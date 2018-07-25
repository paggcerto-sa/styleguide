# _Models_

_Models_ são um conjunto classes responsáveis pela validação de dados e regras de negócio e, também, pela persistência em banco de dados,
arquivo ou através da comunicação com outras APIs.

## _Entity model_

_Entity models_ são classes que representam entidades persistidas e seu mapeamento para o banco de dados. Por exemplo:

- `Payment.cs`: Classe representativa de uma entidade do negócio. O nome deve ser semanticamente relacionado ao termo de negócio utilizado no mundo real.
- `PaymentMap.cs`: Classe que configura o mapeamento da tabela do banco de dados para uma instância da entidade do negócio.
- `PaymentQuery.cs`: Classe composta apenas de _extension methods_ para encapsulamento das queries SQL.

Também podem ser incluídas classes que representam entidades não persistidas quando necessário.

### Exemplo de código

```C#
// Payment.cs
public class Payment
{
  public long Id { get; set; }
  public DateTime PaitAt { get; set; }
  public DateTime? CanceledAt { get; set; }
  public decimal Amount { get; set; }
  public BankSlip BankSlip { get; set; }
  public CardTransaction CardTransaction { get; set; }
}
```

```C#
// PaymentMap.cs
public static class PaymentMap
{
    public static void Map(this EntityTypeBuilder<Payment> entity)
    {
        entity.ToTable(nameof(Payment));
        
        entity.HasKey(p => p.Id);
        
        entity.Property(p => p.Id).ValueGeneratedOnAdd();
        entity.Property(p => p.PaitAt).IsRequired();
        entity.Property(p => p.CanceledAt);
        entity.Property(p => p.Amount).HasColumnType("decimal(8,2)");
        
        entity.HasOne(p => p.BankSlip)
            .WithOne(p => p.Payment)
            .HasForeignKey<BankSlip>(p => p.PaymentId)
            .OnDelete(DeleteBehavior.Restrict);

        entity.HasOne(p => p.CardTransaction)
            .WithOne(p => p.Payment)
            .HasForeignKey<CardTransaction>(p => p.PaymentId)
            .OnDelete(DeleteBehavior.Restrict);
    }
}
```

```C#
// PaymentQuery.cs
public static class PaymentQuery
{
    public static IQueryable<Payment> IncludeCardTransaction(this IQueryable<Payment> payments)
    {
        return payments.Include(payment => payment.CardTransaction);
    }

    public static IQueryable<Payment> IncludeBankSlip(this IQueryable<Payment> payments)
    {
        return payments.Include(payment => payment.BankSlip);
    }
    
    public static IQueryable<Payment> WhereCanceled(this IQueryable<Payment> payments)
    {
        return payments.Include(payment => payment.CanceledAt != null);
    }
    
    public static IQueryable<Payment> WherePaidAt(this IQueryable<Payment> payments, DateTime date)
    {
        return payments.Include(payment => payment.PaitAt == date.Date);
    }
}
```

## _Integration model_

_Integration models_ são classes que realizam a comunicação com outras APIs (RESTful ou SOAP). Por exemplo:

- `IAccountApi.cs`: Interface da API utilizada para implementação real e _fake_ para testes;
- `AccountApi.cs`: Responsável pela comunicação de fato com a API de destino;
- `AccountApiOptions.cs`: Opções de configuração da API obtidas a partir do `appsettings.json`.

### Exemplo de código

```C#
// IAccountApi.cs
public interface IAccountApi
{
    Task<WhoAmI> WhoAmI(string token);
}
```

```C#
// AccountApi.cs
public class AccountApi : IAccountApi
{
    private AccountApiOptions _options;
    private HttpClient _httpClient;

    public AccountApi(IOptions<AccountApiOptions> options)
    {
        _options = options.Value;

        _httpClient = new HttpClient();
        _httpClient.DefaultRequestHeaders.Accept.Clear();
        _httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    }
    
    public async Task<WhoAmI> WhoAmI(string token)
    {
        _httpClient.DefaultRequestHeaders.Add("Authorization", token);

        var response = await _httpClient.PostAsJsonAsync(_options.WhoAmIUrl);
        return response.IsSuccessStatusCode ? await response.Content.ReadAsJsonAsync<WhoAmI>() : null;
    }
}
```

```C#
// AccountApiOptions.cs
public class AccountApiOptions
{
    public string WhoAmIUrl { get; set; }
}
```

## _Result model_

_Result models_ são classes de resposta da API e representam a serialização para JSON, PDF, etc. Por exemplo:

- `PaymentListJson.cs`: Representa uma coleção em JSON:
  ```json
  {
    "payments": [],
    "count": 0
  }
  ```

- `PaymentJson.cs`: Representa um objeto `Payment` em JSON:
  ```json
  {
    "id": 1,
    "paitAt": "2018-04-10T14:14:20.1394168",
    "canceledAt": null,
    "amount": 90.5
  }
  ```
  
### Exemplo de código

```C#
public class PaymentListJson: IActionResult
{
    public PaymentListJson() { }

    public PaymentListJson(IEnumerable<Payment> payments, long count)
    {
        Payments = payments.Select(payment => new PaymentJson(payment)).ToList();
        Count = count;
    }
    
    public IEnumerable<PaymentJson> Payments { get; set; }
    public long Count { get; set; }

    public Task ExecuteResultAsync(ActionContext context)
    {
        return new JsonResult(this).ExecuteResultAsync(context);
    }
}
```

```C#
public class PaymentJson: IActionResult
{
    public PaymentJson() { }

    public PaymentJson(Payment payment)
    {
        Id = payment.Id;
        CreatedAt = payment.CreatedAt;
        CanceledAt = payment.CanceledAt;
        Amount = payment.Amount;
    }

    public string Id { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? CanceledAt { get; set; }
    public decimal Amount { get; set; }

    public Task ExecuteResultAsync(ActionContext context)
    {
        return new JsonResult(this).ExecuteResultAsync(context);
    }
}
```

## _Service model_

_Service models_ são classes que abstraem a execução do processo de negócio e validação de suas regras (não inclui validação de dados). Por exemplo:

- `PaymentProcessing`: Classe responsável pelo processamento de um pagamento.

### Exemplo de código

```C#
public class PaymentProcessing
{
    private ApiDbContext _dbContext;
    private IAcquirerApi _acquirerApi;
    
    public CardPaymentProcessing(ApiDbContext dbContext, IAcquirerApi acquirerApi)
    {
        _dbContext = dbContext;
        _acquirerApi = _acquirerApi;
    }
    
    public Payment Payment { get; private set; }
    public bool Reproved { get; private set; }
    public bool CardNotSupported { get; private set; }
    
    public async Task Process(Payment payment)
    {
        Payment = payment;
        
        await _acquirerApi.Process(Payment);
        
        CardNotSupported = Payment.CardTransaction == null;
        if (CardNotSupported) return false;
        
        Reproved = Payment.CardTransaction.ReprovedAt != null;
        if (Reproved) return false;
        
        _dbContext.Payments.Add(Payment);
        await _dbContext.SaveChangesAsync();
    }
}
```

## _Validations_

_Pendente._

## _View models_

_Pendente._
