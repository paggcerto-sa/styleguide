# _Models_

_Models_ são um conjunto classes responsáveis pela validação de dados e regras de negócio e, também, pela persistência em banco de dados,
arquivo ou através da comunicação com outras APIs.

## _Entity model_

São classes que representam entidades persistidas e seu mapeamento para o banco de dados. Por exemplo:

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

_Pendente._

## _Result model_

_Pendente._

## _Service model_

_Pendente._

## _Validations_

_Pendente._

## _View models_

_Pendente._
