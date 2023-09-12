# Criação de URLs amigáveis (slugs) a partir de strings no ASP.NET Core

Essa classe que chamei de SlugHelper contém métodos para auxiliar na criação de URLs amigáveis (slugs) a partir de strings.

```c#
public static class SlugHelper
{
    public static string RemoveAccents(this string text)
    {
        if (string.IsNullOrWhiteSpace(text))
            return text;

        text = text.Normalize(NormalizationForm.FormD);
        char[] chars = text
            .Where(c => CharUnicodeInfo.GetUnicodeCategory(c)
            != UnicodeCategory.NonSpacingMark).ToArray();

        return new string(chars).Normalize(NormalizationForm.FormC);
    }

    public static string Slugify(this string phrase)
    {
    string output = phrase.RemoveAccents().ToLower();

    output = Regex.Replace(output, @"[^A-Za-z0-9\s\-_]", "");

    output = Regex.Replace(output, @"[\s\-_]+", "-");

    output = output.Trim('-');
  
    return output;
    }
}
```

A principal função dessa classe é transformar uma string em um formato mais adequado para ser usada como parte de uma URL, removendo acentos, caracteres especiais e espaços em branco, substituindo-os por hífens e tornando tudo em letras minúsculas.

Vamos analisar o código linha por linha para entender o que ele faz:

1 - `public static string RemoveAccents(this string text)`

  Declara um método de extensão para a classe string chamado RemoveAccents, que remove os acentos da string.

2 - `if (string.IsNullOrWhiteSpace(text)) return text;`

  Verifica se a string de entrada é nula, vazia ou contém apenas espaços em branco. Se for o caso, retorna a própria string sem modificação.

3 - `text = text.Normalize(NormalizationForm.FormD);`

  Normaliza a string de entrada usando a forma de normalização Unicode D, que separa os caracteres diacríticos (como acentos) dos caracteres-base.

4 - `char[] chars = text.Where(c => CharUnicodeInfo.GetUnicodeCategory(c) != UnicodeCategory.NonSpacingMark).ToArray();`

  Usa LINQ para filtrar os caracteres da string normalizada. Remove todos os caracteres que não são marcas de espaço não espaçadoras (basicamente, remove os acentos).

5 - `return new string(chars).Normalize(NormalizationForm.FormC);`

  Cria uma nova string a partir dos caracteres filtrados e a normaliza novamente usando a forma de normalização Unicode C. Isso garante que a string resultante esteja em uma forma normalizada e sem acentos.

6 - `public static string Slugify(this string phrase)`

  Declara um método de extensão para a classe string chamado Slugify, que transforma a string em um slug.

7 - `string output = phrase.RemoveAccents().ToLower();`

  Chama o método RemoveAccents para remover os acentos e normalizar a string, e então converte a string resultante para letras minúsculas.

8 - `output = Regex.Replace(output, @"[^A-Za-z0-9\s\-_]", "");`

  Usa uma expressão regular para remover todos os caracteres que não são letras, números, espaços, hífens ou underscores.

9 - `output = Regex.Replace(output, @"[\s\-_]+", "-");`

  Usa uma expressão regular para substituir sequências de espaços, hífens e underscores por um único hífen.

10 - `output = output.Trim('-');`

  Remove hífens no início e no final da string, se houver.

11 - `return output;`

  Retorna a string resultante, que agora está no formato de slug, pronto para ser usado em URLs.

## Classe de teste usando `xunit`

```c#
public class SlugHelperTests
{
    [Theory]
    [InlineData("Hello, World!", "hello-world")]
    [InlineData("This is a test.", "this-is-a-test")]
    [InlineData("Slûg Exåmplé", "slug-example")]
    [InlineData("", "")]
    [InlineData("----", "")]
    [InlineData(" Testing  Spaces  ", "testing-spaces")]
    [InlineData("ALL CAPS", "all-caps")]
    [InlineData("Mixed CaSe", "mixed-case")]
    [InlineData("123_456", "123-456")]
    [InlineData("Special #Chars!", "special-chars")]
    public void Slugify_ShouldGenerateCorrectSlugs(string input, string expected)
    {
        // Act
        string actual = SlugHelper.Slugify(input);

        // Assert
        Assert.Equal(expected, actual);
    }
}
```

Em resumo, esse código define uma classe com dois métodos de extensão para strings: `RemoveAccents` que remove acentos e normaliza a string, e `Slugify` que gera um slug a partir da string, removendo acentos, caracteres especiais, espaços em branco e substituindo-os por hífens, e convertendo tudo para letras minúsculas. Isso serve para criar URLs mais amigáveis para SEO e para melhorar a usabilidade em aplicações web.
