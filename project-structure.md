# Estrutura de diretórios do projeto

Abaixo está descrito a estrutura de diretórios das nossas APIs:

```
src
├──api
|  ├──Controllers
|  ├──Extensions
|  ├──Filters
|  ├──Infrastructure
|  ├──Migrations
|  ├──Models
|  |  ├──EntityModel
|  |  ├──IntegrationModel
|  |  ├──ResultModel
|  |  ├──ServiceModel
|  |  ├──Validations
|  |  └──ViewModel
|  └──api.csproj
└──tests
|  ├──Factories
|  |  ├──EntityModel
|  |  └──ViewModel
|  ├──Fakes
|  ├──Functional
|  ├──Http
|  ├──Unit
|  └──tests.csproj
└──Project.sln
CHANGELOG.md
README.md
```

Diretório `api`:
- Controllers: Camada `Controller` do MVC, a qual o usuário interage, controla o fluxo e decide a resposta.
- Extensions: [Extension methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods) utilizados por toda a API.
- Filters: [MVC Core filters](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-2.1) globais ou utilizados indivualmente por cada _action_ dos _controllers_.
- Infrastructure: Mantém as classes que a infraestrutura da API ex.: segurança, log de erros, etc.
- Migrations: Código gerado automaticamente pelo [Entity framework](https://docs.microsoft.com/en-us/ef/core/) para controle de versão do banco de dados.
- Model: Camada `Model` do MVC, responsável pela lógica de negócio e persistência.
  - EntityModel: Classes de mapeamento e persistência para o banco de dados da API.
  - IntegrationModel: Classes de comunicação e persistência através de integração com outras APIs.
  - ResultModel: Classes que representam a serialização para JSON, PDF, etc.
  - ServiceModel: Classes responsáveis pela execução dos processos de negócio e validação das regras de negócio.
  - Validations: Validação reutilizadas nos parâmetros dos _view models_.
  - ViewModel: Classes que representam o JSON da requisição e responsáveis pela validação de dados.

Diretório `tests`:
- Factories: _Pendente_.
  - EntityModel: _Pendente_.
  - ViewModel: _Pendente_.
- Fakes: _Pendente_.
- Functional: _Pendente_.
- Http: _Pendente_.
- Unit: _Pendente_.
