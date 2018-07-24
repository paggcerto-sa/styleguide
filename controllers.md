# _Controllers_

Os _controllers_ devem ser magros. Apesar de poderem realizar consultas e consumir APIs (dependendo do caso), não devem manter regras de negócio na sua implementação.

## Definição das rotas

Algumas regras para definição das rotas devem ser seguidas:
- A rota declarada no _controller_ deve ser prefixada com `api` e a versão: `api/v2`;
- As rotas devem ser declaradas em letra minúscula;
- Para rotas com nomes compostos, cada palavra deve ser separada por hífen.

## Exemplo de código

```C#
[Route("api/v2/payments")]
public class PaymentsController : Controller
{
    private ApiDbContext _dbContext;
    private AccountApi _accountApi;
  
    public PaymentsController(ApiDbContext dbContext, AccountApi _accountApi)
    {
        _dbContext = dbContext;
        _accountApi = accountApi;
    }

    [HttpGet]
    public async Task<IActionResult> List([FromQuery] PaymentListModel model)
    {
        var paymentQuery = _dbContext.Payments
             .WhereDateFrom(model.StartDate)
             .WhereDateUntil(model.EndDate)
             .WherePayerName(model.PayerName)
             .WherePayerTaxDocument(model.PayerTaxDocument)
             .WherePaymentMethod(model.PaymentMethod)
             .WhereStatus(model.Status);

         var payments = await paymentQuery
             .OrderByDescendingCreatedAt()
             .Skip(model.Index.Value)
             .Take(model.Length.Value)
             .ToListAsync();

         var periodAmount = await paymentQuery.SumAsync(payment => payment.Amount);
         var count = await paymentQuery.LongCountAsync();

         return new PaymentListJson(payments, count, periodAmount);
    }
    
    [HttpGet, Route("{paymentId:long}")]
    public async Task<IActionResult> Find([FromRoute] long paymentId)
    {
        var payment = await _dbContext.Payments
            .WhereId(paymentId)
            .IncludeCardPayment()
            .IncludeBankSlipPayment()
            .SingleOrDefaultAsync();

        if (payment == null)
        {
            return new PaymentNotFoundJson();
        }

        return new PaymentJson(payment);
    }

    [HttpPost, Route("pay-with-card")]
    public async Task<IActionResult> PayWithCard([FromBody] CardPaymentModel model)
    {
        var cardTransactionProcessing = new CardTransactionProcessing(_dbContext, _accountApi);
        var payment = model.Map();

        if (!await cardTransactionProcessing.Process(payment))
        {
            return new CardTransactionProcessingErrorJson(cardTransactionProcessing);
        }

        return new PaymentJson(cardPaymentProcessing.Payment);
    }
    
    [HttpGet, Route("pay-with-bank-slip")]
    public async Task<IActionResult> PayWithBankSlip([FromBody] BankSlipPaymentModel model)
    {
        var bankSlipProcessing = new BankSlipProcessing(_dbContext, _accountApi);
        var payment = model.Map();

        if (!await bankSlipProcessing.Process(payment))
        {
            return new BankSlipProcessingErrorJson(bankSlipProcessing);
        }

        return new PaymentListJson(bankSlipProcessing.Payments);
    }
}
```
