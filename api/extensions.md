# _Extentions_

_[Extensions methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)_
são criados para possibilitar o reaproveitamento de uma mesma lógica e, dependendo do caso, podem ser utilizados por todo o código da API.

### Exemplo de código.

```C#
public static class StringExtensions
{
    public static string OnlyNumbers(this string str)
    {
        if (str == null) return str;
        return Regex.Replace(str, "[^\\d]+", string.Empty);
    }

    public static string RemoveUrlProtocol(this string str)
    {
        return Regex.Replace(str, @"https?:\/\/(www\.)?", string.Empty);
    }
}
```

```C#
public static class DecimalExtensions
{
    public static decimal ToCurrency(this decimal amount)
    {
        return Math.Round(amount, 2);
    }
}
```
