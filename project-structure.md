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
- Controllers:
- Extensions:
- Filters:
- Infrastructure: Mantém as classes que a infraestrutura da API ex.: segurança, log de erros, etc.
- Migrations:
- Model: Camada `Controller` do MVC.
  - EntityModel: Classes de persistência e mapeamento para o banco de dados da API.
  - IntegrationModel: Classes de persistência e comunicação através de integração com outras APIs.
  - ResultModel: Classes que representam a serialização do JSON.
  - ServiceModel: Classes que representam a serialização do JSON.
