# _Filters_

_[Filters](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-2.1)_
são métodos executados antes e/ou depois da _action_ ser executada. Um filtro fundamental é o gerenciador de erros da API,
responsável por logar todas as exceções lançadas durante o processamento das requisições.

## Exemplo de código

```C#
public class ErrorHandlerAttribute : ExceptionFilterAttribute
{
    private IHostingEnvironment _env;
    private IErrorLogger _logger;

    public ErrorHandlerAttribute(IHostingEnvironment env, IErrorLogger logger)
    {
        _env = env;
        _logger = logger;
    }

    public override async Task OnExceptionAsync(ExceptionContext context)
    {
        if (!_env.IsDevelopment())
        {
            await _logger.LogAsync(context.Exception);
        }

        context.Result = new StatusCodeResult(500);
    }
}
```
