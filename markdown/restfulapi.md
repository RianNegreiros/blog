# O que é uma API RESTful e como projetar uma?

Este artigo tentará explicar o que é uma API REST e como podemos utilizar essas APIs para criar nossos próprios sistemas.

## Noções básicas de REST

**REST** - [Representational State Transfer(Transferência de estado representacional).](https://pt.wikipedia.org/wiki/REST)

**API** - [Application Programming Interface(Interface de programação de aplicativo).](https://pt.wikipedia.org/wiki/Interface_de_programa%C3%A7%C3%A3o_de_aplica%C3%A7%C3%B5es)

A maioria, se não todos, os grandes sites populares dependem de alguma forma de **API REST** para fornecer algum conteúdo ou funcionalidade aos seus usuários. Alguns sites, como o Facebook e o Twitter, expõem algumas dessas APIs a desenvolvedores externos para criarem suas próprias ferramentas e sistemas.

Podemos nos comunicar com as **APIs REST** usando solicitações HTTP, da mesma forma que você faria para navegar em um site ou carregar uma imagem. Podemos fazer solicitações HTTP para determinados urls da API e esses urls retornarão as informações necessárias, ou podemos enviar dados para um url da API a fim de alterar alguns dados em um banco de dados.

Normalmente, enviamos solicitações HTTP a um URL que definimos em nossa **API REST** e ele executaria uma determinada tarefa para nós ou retornaria um determinado bit de dados. Atualmente, a maioria das APIs retorna uma resposta para nós na forma de JSON.

> Para saber mais sobre HTTP eu tenho um artigo sobre [como funciona o HTTP](https://www.riannegreiros.dev/post/como-funciona-o-http)
>

## Um simples exemplo

Imagine que você escreveu um pouco de código que fornece as condições climáticas atuais em sua casa. Ele lê a temperatura, a umidade e a precipitação e as armazena localmente. Como poderíamos expor essas informações de forma que sites ou outros aplicativos pudessem visualizá-las?

Uma resposta a essa pergunta é envolvê-la em uma API RESTful.

Poderíamos expor nosso código e envolvê-lo em uma API de modo que, sempre que navegássemos, por exemplo, para `http://localhost:8000/api/weather`, ele nos daria uma resposta JSON que contivesse todas as estatísticas meteorológicas atuais.

## Por que fazemos isso?

**Improved Code Reuse** (Reutilização aprimorada do código) - Ao expor nosso código por meio de APIs REST, basicamente nos damos um grau maior de flexibilidade. Podemos desenvolver nosso software uma vez e, se quisermos usar o mesmo código novamente em um projeto diferente, será fácil: basta enviar solicitações HTTP à nossa API e reduzimos a necessidade de duplicar nossos esforços de trabalho.

**Always Available** (Sempre disponível) - as APIs REST normalmente são coisas em execução e disponíveis o tempo todo. Nós as tornamos muito estáveis e, como resultado, podemos interagir com elas onde quer que estejamos no mundo, desde que tenhamos conectividade com a Internet.

## Projeto de uma API REST

Vamos ver em alto nível os fundamentos do design da API REST.

Vamos dar uma olhada em alguns dos **prós** e **contras** quando se trata de projetar serviços prontos para produção e como podemos projetar nossos sistemas para poderem ser facilmente estendidos, atualizados e mantidos sem nenhum impacto subjacente para as pessoas que consomem seu serviço.

## Controle de versão da API

O primeiro ponto, e francamente o mais importante, que você deve considerar ao projetar uma API REST é que o contrato para todos os seus pontos de extremidade seja **imutável**. Essa é uma maneira muito elegante de dizer que não importa quando, como ou por que você chama essa API, ela deve sempre seguir o mesmo contrato.

O que quero dizer com isso é que, se eu projetar uma API que me permita atualizar os artigos deste site, **deve** sempre garantir que a API não altere o `formato JSON` que espera para atualizar esses artigos.

### Exemplo

Vamos dar uma olhada em um exemplo disso. Digamos que eu tivesse um endpoint que, quando você o acessasse, retornaria um `JSON` com uma resposta.

```bash
## HTTP GET
https://riannegreiros.dev/api/status

{ "resposta": "A API está funcionando" }
```

Imagine que tivéssemos clientes que começassem a usar esse endpoint em seus próprios serviços por qualquer motivo.

Agora, imagine como você ficaria se seus clientes vissem de repente que os sistemas deles estavam quebrando, pois você havia atualizado o endpoint para retornar o seguinte:

```bash
## HTTP GET
https://riannegreiros.dev/api/status

{ "response": "A API está funcionando" }
```

Isso pode parecer uma mudança sutil, mas quem estava consumindo esse endpoint havia criado seu próprio aplicativo em torno da estrutura JSON anterior e espera que isso nunca mude.

### A solução

Esse exemplo destaca a importância do controle de versão dos endpoints da API. Na prática, isso significa garantir que você exponha a primeira versão da sua API usando uma estrutura como essa:

```bash
## HTTP GET
https://riannegreiros.dev/api/v1/status

{ "resposta": "A API está funcionando" }
```

Observe a pequena alteração nos parâmetros de `/api/status` para `/api/v1/status`.

Agora, se quiséssemos modificar a resposta do nosso `JSON` por qualquer motivo para usar a nova chave de `response`, poderíamos simplesmente adicionar um novo endpoint que usasse a `v2` da seguinte forma:

```bash
## HTTP GET
https://riannegreiros.dev/api/v2/status

{ "response": "A API está funcionando" }
```

Essa abordagem garante que nunca interrompamos involuntariamente os [aplicativos downstream](https://medium.com/code-factory-berlin/up-down-stream-what-is-it-a4b4792f5d0b) que estão usando nossa versão original do serviço, mas também nos dá a flexibilidade de estender nosso serviço e oferecer endpoints mais novos ou mais aprimorados!

Agora, se você estiver implementando novas versões o tempo todo, talvez queira usar um número de versão mais granular nos parâmetros de caminho, ou seja, `/api/v1-1/status`, para poder implementar pequenas alterações mais rapidamente sem necessariamente atualizar a versão principal por completo.

### Descontinuidade de versões mais antigas

Em algum momento, talvez você queira começar a depreciar e remover versões mais antigas da sua API. Há muitos motivos para você fazer isso. Desde a remoção de pontos de extremidade adicionais aos quais você precisa dar suporte constante até a possível remoção total do recurso.

Quando se trata de gerenciar isso, é preciso ter cuidado e garantir que você **dê aos seus clientes um grande prazo** para que eles possam migrar seus aplicativos para usar versões mais recentes do sistema. Isso pode variar de algumas semanas a alguns meses, dependendo da complexidade da mudança e do número de clientes com os quais você está lidando.

Ter bons sistemas de monitoramento e registro em suas APIs pode ajudar drasticamente a melhorar esse processo. Se você puder mapear com precisão quais dos seus clientes estão usando qual versão do endpoint, poderá concentrar seus esforços na migração desses clientes e garantir que ninguém ainda esteja usando a versão antes de removê-la completamente.

## Usar verbos HTTP apropriados

Quando se trata de escrever APIs, é extremamente importante usar os verbos HTTP corretos ao criar os endpoints da API `e não incluir verbos` no caminho da API.

Por exemplo, não defina endpoints como este:

```bash
- /api/v1/getposts
- /api/v1/newpost
- /api/v1/updatepost/:id
- /api/v1/deletepost/:id
```

Se outro desenvolvedor for usar a sua API, ele não saberá instintivamente quais endpoints devem ser usados para realizar operações CRUD básicas. Ele se perguntará se o endpoint da API para criar uma postagem está ou não em `/api/v1/createpost`, `/api/v1/addpost` ou `/api/v1/newpost`.

Esse estilo é complexo e deixa muita ambiguidade. A melhor abordagem seria usar os verbos HTTP apropriados:

```bash
- /api/v1/post - HTTP GET request - Todos os Posts
- /api/v1/post/:id - HTTP GET request - Apenas um post
- /api/v1/post/:id - HTTP POST request - Publica um post
- /api/v1/post/:id - HTTP PATCH request - Atualiza um post existente
- /api/v1/post/:id - HTTP DELETE request - Deleta um post
```

Essa é uma abordagem muito mais limpa, pois você sabe instantaneamente, por convenção geral, quais pontos de extremidade precisa atingir e com quais verbos. Não há ambiguidade nessa abordagem.

## Uso de códigos de resposta HTTP

Essa é uma regra importante que você deve seguir. Como a sua API é vista pelos desenvolvedores que consomem o serviço pode ser seriamente prejudicada por um endpoint que retorna um status `200 OK` quando, na verdade, está falhando silenciosamente nos bastidores.

Quando se trata de retornar uma resposta a quem chamou sua API, é melhor utilizar os códigos de status `HTTP` corretos.

```bash
1xx ## Códigos de status informativos
2xx ## Códigos de status de sucesso
3xx ## Códigos de status de redirecionamento
4xx ## Códigos de status de erro do cliente
5xx ## Códigos de status de erro do servidor
```

Ao empregar os códigos de resposta HTTP corretos, melhoramos efetivamente a maneira como nossos serviços interagem entre si. É uma boa ideia consultar [essa lista](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Status) de códigos de status HTTP ao decidir o que retornar a uma solicitação HTTP em uma determinada situação.

Um bom exemplo disso seria imaginar que você tivesse uma API que alimentasse um blog simples. Você teria vários endpoints de API REST diferentes que retornariam coisas como postagens individuais de blog, bem como aquelas que permitiriam atualizar essas postagens de blog. Vamos imaginar que temos dois endpoints em nossa API expostos ao mundo, o primeiro deles é uma solicitação `GET` que simplesmente retorna o `JSON` de um determinado artigo, o segundo é um endpoint `UPDATE` que atualiza o artigo em questão.

```bash
http://servico.com/api/v1/artigo/1 ## GET Endpoint retorna artigo '1'
http://servico.com/api/v1/artigo/1 ## UPDATE Endpoint atualiza artigo '1'
```

Quando um usuário padrão chama `GET` na primeira API, você normalmente retorna a resposta json, bem como um código de status `200 - OK`, que indica que está tudo bem. No entanto, imagine que esse mesmo usuário tente atualizar nosso artigo usando o endpoint `UPDATE`. Como o usuário não é um administrador, queremos retornar um código de status diferente, como `401 - Unauthorized` (Não autorizado), que indicaria que o usuário não tem o nível ou as permissões suficientes. Depois que o usuário for autorizado como administrador e tentar fazer `UPDATE` novamente no mesmo ponto de extremidade, retornaremos um status `200 - OK`, que indica que ele conseguiu atualizar o artigo com êxito.

## Tipos de mídia

Se você estiver criando uma API de uso geral que possa ser usada por uma grande variedade de motivos, precisará considerar a adição de respostas diferentes com base no header `Content-Type` passado com a solicitação.

Isso dá ao desenvolvedor que está tentando interagir com seu aplicativo a opção de solicitar uma resposta em uma variedade de formatos de dados diferentes.

```json
- /api/v1/posts - HTTP GET - Content-Type: application/json

{ "posts": [
    ... todos os posts em JSON
]}
```

Imagine que precisássemos alimentar um pipeline de compilação com as informações, mas precisássemos das postagens em um formato `yaml`. Idealmente, nossa API seria capaz de serializar a resposta em `yaml` e retorná-la se o `Content-Type` fosse definido como `application/yaml`:

```yaml
- /api/v1/posts - HTTP GET - Content-Type: application/yaml

posts:
 ... todos os posts em formato yaml
```

## Swagger

O [Swagger](https://swagger.io/) é algo que facilita os testes rápidos e a validação dos pontos de extremidade da API. Se você estiver criando uma API que será consumida por uma grande variedade de usuários, o fornecimento de uma página que permita que eles interajam instantaneamente com a API oferece um valor enorme para algo que exige um esforço incrivelmente mínimo para ser configurado.

## Conclusão

Neste artigo, analisamos algumas das práticas recomendadas que você deve considerar ao projetar suas próprias APIs REST. Essas são práticas às quais vejo muitos serviços de nível de produção aderirem e que facilitam muito a vida dos desenvolvedores que usam seus serviços.
