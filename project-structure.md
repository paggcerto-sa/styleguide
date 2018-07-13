# Project Structure

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
.gitignore
CHANGELOG.md
README.md
```

Diretório `api`:
- Controllers: Camada `Controller` do MVC, a qual o usuário interage, controla o fluxo e decide de resposta.
- Extensions: _[Extension methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)_ utilizados por toda a API.
- Filters: 
- Infrastructure: Mantém as classes que a infraestrutura da API ex.: segurança, log de erros, etc.
- Migrations:
- Model: Camada `Model` do MVC, responsável pela lógica de negócio e persistência.
  - EntityModel: Classes de mapeamento e persistência para o banco de dados da API.
  - IntegrationModel: Classes de comunicação e persistência através de integração com outras APIs.
  - ResultModel: Classes que representam a serialização do JSON.
  - ServiceModel: Classes que representam a serialização do JSON.

Diretório `tests`:
- Factories:
  - EntityModel:
  - ViewModel:
- Fakes:
- Functional:
- Http:
- Unit:
