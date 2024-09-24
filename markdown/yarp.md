![YARP Reverse Proxy Diagram](/assets/YARP.gif)

**YARP: Yet Another Reverse Proxy**

No mundo da internet, a segurança e a performance dos sites são fundamentais para garantir uma boa experiência ao usuário. Para atingir esses objetivos, é comum utilizar ferramentas de proxy reverso, que permitem controlar o tráfego de rede e proteger os servidores originais de ataques mal-intencionados.

No entanto, a escolha da melhor ferramenta de proxy reverso pode ser uma tarefa desafiadora. Nesse sentido, YARP (Yet Another Reverse Proxy) surge como uma solução inovadora e escalável para atender às necessidades mais rigorosas.

**O que é YARP?**

YARP é um software de código aberto que fornece uma implementação de proxy reverso escalável e seguro. Foi desenvolvido pela Microsoft, em colaboração com a comunidade de desenvolvedores, e está disponível sob a licença MIT (Massachusetts Institute of Technology).

**Características**

YARP é uma ferramenta robusta e fácil de usar que oferece várias características únicas, incluindo:

* **Escalabilidade**: YARP é projetado para ser escalável e pode lidar com volumes grandes de tráfego.
* **Segurança**: O software inclui recursos avançados de segurança, como autenticação e autorização, para proteger os servidores originais.
* **Flexibilidade**: YARP suporta vários protocolos, incluindo HTTP, HTTPS, TCP e UDP, o que permite sua integração em diferentes ambientes.
* **Integração com outros sistemas**: O software pode ser facilmente integrado com outros sistemas, como load balancers e firewalls.

**Funcionalidades**

YARP oferece uma ampla gama de funcionalidades, incluindo:

* **Reverse Proxy**: Permite que o tráfego do cliente seja redirecionado para um servidor original.
* **Load Balancing**: Distribui o tráfego entre vários servidores originais para garantir a escalabilidade e a disponibilidade.
* **Caching**: Armazena respostas de servidores originais em cache, reduzindo a carga no servidor e melhorando a performance.
* **SSL/TLS Termination**: Permite que o SSL/TLS seja desacelerado no proxy reverso, eliminando a necessidade de certificados SSL/TLS nos servidores originais.

**Vantagens**

YARP oferece várias vantagens em relação às outras ferramentas de proxy reverso. Algumas das principais são:

* **Escalabilidade**: YARP é projetado para ser escalável e pode lidar com volumes grandes de tráfego.
* **Segurança**: O software inclui recursos avançados de segurança, como autenticação e autorização, para proteger os servidores originais.
* **Flexibilidade**: YARP suporta vários protocolos e pode ser facilmente integrado com outros sistemas.

## Exemplo de Configuração do YARP em uma API ASP.NET Core 8

Vamos passo a passo entender como se configura o YARP ([Yet Another Reverse Proxy](https://microsoft.github.io/reverse-proxy/)) em uma aplicação [ASP.NET Core](https://dotnet.microsoft.com/en-us/apps/aspnet) 8.

### 1. Importações Necessárias

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Yarp.ReverseProxy.Configuration;

```

Esses `using` directives importam os namespaces necessários para configurar e usar o YARP e os serviços do [ASP.NET](http://asp.net/) Core.

### 2. Criação do Builder da Aplicação

```csharp
var builder = WebApplication.CreateBuilder(args);

```

Aqui, estamos criando um `WebApplicationBuilder`, que é uma nova maneira de configurar e inicializar uma aplicação [ASP.NET](http://asp.net/) Core.

### 3. Adicionando Serviços do YARP

```csharp
builder.Services.AddReverseProxy()
    .LoadFromMemory(new[]
    {
        new RouteConfig
        {
            RouteId = "route1",
            ClusterId = "cluster1",
            Match = new RouteMatch
            {
                Path = "{**catch-all}"
            }
        }
    },
    new[]
    {
        new ClusterConfig
        {
            ClusterId = "cluster1",
            Destinations = new Dictionary<string, DestinationConfig>
            {
                { "destination1", new DestinationConfig { Address = "<https://example.com>" } }
            }
        }
    });

```

Aqui, estamos adicionando o serviço de proxy reverso do YARP ao contêiner de serviços da aplicação. Vamos detalhar cada parte:

- `AddReverseProxy()`: Adiciona os serviços necessários para o YARP.
- `LoadFromMemory()`: Carrega a configuração do YARP diretamente da memória. Estamos passando duas configurações principais:
    - **RouteConfig**: Define uma rota para o proxy.
        - `RouteId`: Identificador único da rota.
        - `ClusterId`: Identificador do cluster ao qual esta rota está associada.
        - `Match`: Define como a rota deve ser correspondida. Aqui, `{**catch-all}` significa que qualquer caminho será correspondido.
    - **ClusterConfig**: Define um cluster de destinos.
        - `ClusterId`: Identificador único do cluster.
        - `Destinations`: Um dicionário de destinos para o cluster. Cada destino tem um identificador e uma configuração de endereço. Neste caso, estamos direcionando para `https://example.com`.

### 4. Construção da Aplicação

```csharp
var app = builder.Build();

```

Aqui, estamos construindo a aplicação com base na configuração definida anteriormente.

### 5. Mapeamento do Middleware do YARP

```csharp
app.MapReverseProxy();

```

Este método adiciona o middleware do YARP ao pipeline de processamento de requisições da aplicação. Isso significa que todas as requisições que correspondem à rota definida serão processadas pelo YARP e redirecionadas para o destino configurado.

### 6. Execução da Aplicação

```csharp
app.Run();

```

Finalmente, este método inicia a aplicação e começa a escutar por requisições HTTP.

### Resumo

Este código configura um proxy reverso simples usando YARP em uma aplicação [ASP.NET](http://asp.net/) Core 8. Ele define uma rota que captura todas as requisições (`{**catch-all}`) e as redireciona para `https://example.com`. O YARP é adicionado como um serviço e configurado para ser usado como middleware no pipeline de requisições da aplicação.

**Conclusão**

YARP é uma ferramenta inovadora e escalável de proxy reverso que oferece várias vantagens em relação às outras soluções. Com sua robusta implementação de segurança, flexibilidade e escalabilidade, YARP é uma escolha ideal para ambientes que necessitam de uma solução de proxy reverso confiável e eficiente.

Fonte: <https://microsoft.github.io/reverse-proxy/>

**Recomendações**

Para aprender mais sobre como usar o YARP, é recomendável assistir aos seguintes vídeos:

1. "How To Build an API Gateway for Microservices with YARP" - https://www.youtube.com/watch?v=UidT7YYu97s
2. "Introduction to YARP a .NET Reverse Proxy" - https://www.youtube.com/watch?v=EfVVvEtfgpI

E para entender melhor sobre o conceito de proxy reverso e sua aplicação em diferentes cenários, recomendo os seguintes vídeos:

1. "Descubra como utilizar Proxy Reverso para proteger sua aplicação e melhorar a performance" - https://www.youtube.com/watch?v=E51dIa0ZcGs
2. "Proxy vs Reverse Proxy (Real-world Examples)" - https://www.youtube.com/watch?v=4NB0NDtOwIQ
