# Convenção de nomes

Seguir o _naming convention_ definidos nos guidelines elaborados pela própria Microsoft.

| Convenção             | Notação                                  |
|:----------------------|:-----------------------------------------|
| Classe                | `PascalCase`                             |
| Atributo              | `camelCase` iniciando com underline `_`  |
| Propriedade           | `camelCase`                              |
| Método                | `PascalCase`                             |
| Parâmentro de método  | `camelCase`                              |
| Variável local        | `camelCase`                              |
| Constante             | `PascalCase`                             |
| Enum                  | `PascalCase` no singular                 |

Outras regras:

- O código deve ser escrito em inglês;
- Deve-se evitar abreviações;
- A declaração dos elementos da classe (quando houver) deve respeitar a seguinte sequência:
  1. Constantes;
  2. Atributos;
  3. Construtores;
  4. Propriedades;
  5. Métodos: `public`, `protected` e `private` (necessáriamente nessa ordem).
- Respeitar a quebra de linhas (seguindo o exemplo).

Exemplo de código:

```C#
public class PaymentProcessing
{
    public const MaximumInstallment = 12;

    private ApiDbContext _dbContext { get; set; }

    public PaymentProcessing(ApiDbContext dbContext)
    {
        _dbContext = dbContext;
    }

    public bool CardNotProcessed { get; set; }

    private decimal CalculateFees()
    {
        // Do something
    }

    protected decimal AquirerProcessing()
    {
        // Do something
    }

    public void Process(Payment payment)
    {
        // Do something
    }
}
```
