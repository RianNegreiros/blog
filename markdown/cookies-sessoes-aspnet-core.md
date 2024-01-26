# Cookies e Sessões no ASP.NET Core

O gerenciamento de sessão no ASP.NET Core depende de cookies HTTP, portanto, vamos examiná-los primeiro. Os cookies foram inicialmente concebidos pelos desenvolvedores do então popular navegador da Web Netscape em 1994. Uma especificação preliminar foi publicada, e o Internet Explorer também a implementou posteriormente. Existe também uma norma adequada, [RFC 6265](https://datatracker.ietf.org/doc/html/rfc6265), mas o processo geral não se alterou nos últimos 25 anos ou mais.

Atualmente, os cookies são ferramentas indispensáveis para a criação de aplicações web. Isso se deve ao fato de que o armazenamento de dados específicos do usuário não era uma preocupação quando a Web e o HTTP foram criados. De fato, a especificação HTTP afirma desde o início que o protocolo é sem estado. Isso significa que ele não pode armazenar nada - não há "estado" que o cliente mantenha quando comunica com o servidor. Como quase todas as aplicações web complexas precisam armazenar informações, mesmo que seja apenas sobre quem está registrado, não há alternativa viável para os cookies (discutiremos brevemente o armazenamento local e armazenamento de sessão mais adiante neste capítulo). A Figura 3.4 mostra como os cookies funcionam em geral.

![session-cookie-process.png](/assets/session-cookie-process.png)

## O Processo de Envio e Recebimento de Cookies

O servidor é a entidade que envia os cookies utilizando o cabeçalho HTTP Set-Cookie. Este cabeçalho contém o nome do cookie, seu valor e atributos opcionais, como a data de expiração (se não houver nenhuma, o cookie expira quando o navegador é fechado; caso contrário, expira no máximo na sua data de expiração).

```
Set-Cookie: PreferredLanguage=en-US; expires=Fri, 26 Jan 2026 11:34:56 GMT
```

O navegador tem então três opções conforme sua configuração:

- Armazenar ("aceitar") o cookie.
- Não armazenar ("negar") o cookie.
- Perguntar ao usuário o que fazer.

Hoje em dia, há cookies em todo lugar, então os navegadores removeram a opção de perguntar ao usuário. A primeira opção é quase sempre a que está em vigor. Desativar os cookies basicamente impede o uso de muitas aplicações web, algo que ninguém faz atualmente. A maioria dos navegadores oferece configurações específicas para cookies de terceiros, os quais podem ser bloqueados sem afetar a funcionalidade de um site.

Quando o cookie é armazenado, ele é enviado de volta assim que o cliente envia o próximo pedido HTTP para o servidor. Desta vez, o cabeçalho Cookie é utilizado, e apenas o nome e o valor do cookie são enviados:

```
Cookie: PreferredLanguage=en-US
```

<aside>
💡 Nota:

Partimos do princípio de que a flag de domínio do cookie não está definida. Os cookies de terceiros estão fora do âmbito desta discussão. Como alguns clientes implementam sua funcionalidade de recuperação de sessão, pode também dar aos cookies sem uma data de expiração uma vida após o fechamento do navegador.

</aside>

Uma vez que os cookies são armazenados no navegador, seus valores são fáceis de manipular. Obviamente, não faz sentido armazenar informações sensíveis em um cookie, como o nome de usuário atualmente logado ou privilégios específicos do usuário. Mas como armazenar informações vitais enquanto um usuário está interagindo com nossa aplicação? Entra a gestão de sessões. Este mecanismo baseia-se nos cookies, mas reduz os riscos de manipulação de dados, pelo menos até certo ponto. A Figura mostra o processo.

![Screenshot.png](/assets/session-store-process.png)

## O Processo de Gestão de Sessões com Cookies

O servidor armazena algumas informações sobre a sessão atual do usuário. A figura visualiza uma base de dados, mas esta poderia muito bem ser um armazenamento na memória ou outro meio de persistência de dados. Cada conjunto de dados tem um identificador único, o ID da sessão. Este ID de sessão é então enviado para o cliente como um cookie. O ID da sessão é gerado no servidor, é bastante longo e é virtualmente impossível de adivinhar. Portanto, a manipulação de dados no navegador é menos preocupante do que quando o cookie continha informações reais.

O navegador envia os cookies de volta para o servidor com cada pedido subsequente, para que o servidor possa sempre identificar a sessão e acessar os dados de sessão corretos. Assim, desde que o ID da sessão não seja roubado, o processo parece ser seguro. Por outro lado, quando um invasor obtém um ID de sessão, ele pode se passar pelo usuário ao falar com o servidor. Tudo o que o servidor precisa e espera é o ID da sessão. Felizmente, o ASP.NET Core suporta algumas precauções para manter o ID da sessão seguro.

### Armazenamento de Sessão e Armazenamento Local

Existem duas outras tecnologias para armazenar dados no cliente: armazenamento local e armazenamento de sessão. Os dados persistidos aqui só podem ser lidos e escritos a partir do código JavaScript, utilizando os objetos localStorage e sessionStorage. Neste caso, o servidor está fora de cena. É por isso que o armazenamento local e o armazenamento de sessão são utilizados principalmente por aplicações de página única (SPAs). Os dados do armazenamento de sessão são eliminados quando o separador do navegador é fechado, enquanto o armazenamento local não tem uma data de expiração. Se estiver a armazenar dados sensíveis em uma das duas opções, a principal ameaça é o XSS, uma vez que o código JavaScript pode aceder ao armazenamento local e ao armazenamento de sessão. Embora seja possível proteger os cookies do acesso do JavaScript com o sinalizador HttpOnly, não existe tal mecanismo para armazenamento local e armazenamento de sessão, já que estes são explicitamente APIs JavaScript.

## Cookie do ASP.NET Core e Configurações de Sessão

As versões anteriores do ASP.NET vinham com suporte de gerenciamento de sessão fora da caixa, sem qualquer configuração obrigatória adicional. Isso mudou com o ASP.NET Core, onde todos os recursos precisam ser habilitados explicitamente. No arquivo Program.cs, precisamos de uma chamada para `builder.Services.AddSession()` e `app.UseSession()`. Se você tiver um projeto existente que foi iniciado com uma versão do .NET anterior à 6 e a configuração do middleware reside no arquivo Startup.cs, adicione `services.AddSession()` ao método `ConfigureServices()` e `app.UseSession()` no método `Configure()` (esta é a primeira e última vez que mencionaremos esta abordagem herdada). As páginas ou controladores que usam a funcionalidade como `Session.SetString()` e `Session.GetString()` podem precisar de importar o arquivo `Microsoft.AspNetCore.Http` para ter acesso a esses métodos e outros relacionados.

Existem três abordagens principais para um atacante roubar um ID de sessão:

- No cliente, geralmente com cross-site scripting.
- No servidor, o que é difícil de conseguir. A injeção de SQL vem à mente, mas apenas no caso da informação da sessão estar armazenada numa base de dados. O Capítulo 6 dá-lhe cobertura.
- Durante o transporte entre o cliente e o servidor, através de sniffing do tráfego (normalmente só é possível se for usado HTTP em vez de HTTPS). É assim que a extensão Firesheep funcionava. O criador da especificação dos cookies previu o terceiro vetor de ataque e acrescentou uma salvaguarda à sua técnica. Alguns anos mais tarde, a equipa responsável pelo Internet Explorer 6 adicionou uma proteção para o primeiro vetor de ataque (e outros navegadores seguiram o exemplo prontamente). Estas duas defesas são na forma de sinalizadores de cookie:
    - HttpOnly: Esta bandeira esconde o cookie do acesso direto do JavaScript, tornando o XSS menos útil para o roubo de cookies.
    - Secure: Este sinalizador garante que este cookie seja apenas enviado do cliente de volta para o servidor por uma conexão (HTTPS), impedindo a escuta de cookies.

<aside>
💡 Digo "menos útil" porque existe um vetor de ataque que, pelo menos hipoteticamente funciona. Se o método HTTP TRACE estiver ativado no servidor Web (por defeito, não está, pelo menos na configuração predefinida dos servidores Web modernos), então um ataque XSS pode injetar código JavaScript que emite um pedido TRACE ao servidor. O navegador envia automaticamente todos os cookies ao longo deste pedido. O servidor retorna todos os cabeçalhos HTTP, incluindo esses cookies - é assim que o TRACE funciona. Neste cenário específico, o atacante pode então aceder aos cookies da sessão.

</aside>

A definição dos sinalizadores do cookie de sessão pode ser efetuada em Program.cs desta forma:

```csharp
builder.Services.AddSession(options => {
  options.Cookie.HttpOnly = true;
  options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
});
```

É necessário definir explicitamente ambas as opções se optar por usar esses recursos de segurança (e eu recomendo que você o faça!). Por padrão, o sinalizador HttpOnly está desativado, e o sinalizador secure só está sendo utilizado se aceder à aplicação usando HTTPS (o valor padrão para esta opção é `CookieSecurePolicy.SameAsRequest`, e o enum faz parte do espaço de nomes `Microsoft.AspNetCore.Http`).

<aside>
💡 Ao ativar a flag Secure, certifique-se de que irá sempre usar o aplicativo via HTTPS, mesmo durante o desenvolvimento local. Caso contrário, o utilizador não verá o cookie quando usar HTTP simples.

</aside>

## O Cookie de Sessão é Invisível para o JavaScript.

A variável JavaScript `document.cookie` não contém o cookie de sessão, embora ele esteja lá.

<aside>
💡 Também existe outra flag de cookie chamado SameSite, que acrescenta proteção contra ataques, chamado falsificação de pedidos entre sites (CSRF).

</aside>

### Protegendo Cookies

A adição dessas definições na chamada `AddSession()` em `Program.cs` aplica-as a todos os cookies de sessão. Se quiser ter a mesma proteção para os cookies personalizados que está definindo, pode utilizar uma abordagem semelhante:

```csharp
var cookieOptions = new CookieOptions()
{
  HttpOnly = true,
  Secure = true
};
Response.Cookies.Append("PreferredLanguage", "en-US", cookieOptions);
```

Primeiro, crie algumas opções de cookie (a propriedade Boolean Secure é traduzida para o sinalizador seguro) e, em seguida, aplique-as ao adicionar um novo cookie à resposta HTTP.

No caso de uma sessão ser efetivamente roubada, é útil limitar a quantidade de tempo que uma sessão pode ser explorada. Por padrão, cada sessão será abandonada após 20 minutos de inatividade (ou seja, nenhuma solicitação HTTP com esse ID de sessão). Este intervalo de tempo pode ser definido globalmente na chamada `AddSession()` em Program.cs também:

```csharp
builder.Services.AddSession(options => {
  options.IdleTimeout = TimeSpan.FromMinutes(5);
}
```

Especialmente em áreas críticas para a segurança, use um intervalo mais curto. Mas, os usuários podem precisar de iniciar sessão com mais frequência, ou podem mesmo perder trabalho não salvo devido à expiração da sessão, por isso tente encontrar um compromisso entre segurança e usabilidade.

1. Schulz, Marius. "Securing Authentication Cookies in ASP.NET Core."
[Link](https://mariusschulz.com/blog/securing-authentication-cookies-in-asp-net-core)
2. Macoratti. "ASP .NET Core - Usando Cookies"
[Link](https://macoratti.net/21/09/aspc_cookie1.htm)
3. Microsoft Docs. "Cookie Authentication in ASP.NET Core."
[Link](https://learn.microsoft.com/pt-br/aspnet/core/security/authentication/cookie?view=aspnetcore-8.0)
4. Macoratti. "ASP .NET Core 3.1 - Autenticação com cookies"
[Link](https://www.macoratti.net/20/03/aspc_cookauth1.htm)
5. C# Corner. "ASP.NET Core: Working with Cookie."
[Link](https://www.c-sharpcorner.com/article/asp-net-core-working-with-cookie/)
6. Microsoft Docs. "ASP.NET Core Fundamentals: App State."
[Link](https://learn.microsoft.com/pt-br/aspnet/core/fundamentals/app-state?view=aspnetcore-8.0)