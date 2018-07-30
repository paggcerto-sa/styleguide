# Convenções de nomenclatura

Seguir o _naming convention_ definido nos [guidelines](https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/index) elaborados pela própria Microsoft.

| Convenção             | Notação                                  |
|:----------------------|:-----------------------------------------|
| Classe                | `PascalCase`                             |
| Atributo              | `camelCase` iniciando com underline `_`  |
| Propriedade           | `PascalCase`                              |
| Método                | `PascalCase`                             |
| Parâmentro de método  | `camelCase`                              |
| Variável local        | `camelCase`                              |
| Constante             | `PascalCase`                             |
| Enum                  | `PascalCase` no singular                 |

## Outras regras:

- O código deve ser escrito em inglês;
- As declarações devem estar semanticamente relacionadas aos termos utilizados no mundo real;
- Deve-se evitar abreviações;
- As classes declaradas devem possuir uma única responsabilidade;
- A declaração dos elementos da classe (quando houver) deve respeitar a seguinte sequência:
  1. Constantes;
  2. Atributos;
  3. Construtores;
  4. Propriedades;
  5. Métodos: `public`, `protected` e `private` (necessariamente nessa ordem);
- Respeitar a quebra de linhas (seguindo o exemplo);
- Os _usings_ devem ser ordenados alfabeticamente.

### Por que?

Adotar um padrão de organização e estrutura facilita a navegação do código e melhora a manutenibilidade.

## Exemplo de código:

```C#
using System.IO;
using System.Threading.Tasks;

namespace api.Models.ServiceModel
{
    public class PaymentProcessing
    {
        public const MaximumInstallment = 12;

        private ApiDbContext _dbContext;

        public PaymentProcessing(ApiDbContext dbContext)
        {
            _dbContext = dbContext;
        }

        public bool CardNotProcessed { get; set; }

        public void Process(Payment payment)
        {
            // Do something
        }

        protected void AcquirerProcessing()
        {
            // Do something
        }

        private decimal CalculateFees()
        {
            // Do something
        }
    }
}
```
