# Como a Injeção de Dependência Facilita Testes Unitários em .NET

A injeção de dependência é uma prática que envolve escrever classes de forma que elas não controlem suas dependências, mas sim que essas dependências sejam fornecidas a elas (injetadas). Vamos explorar um exemplo prático usando a classe Greeter e como a injeção de dependência torna os testes unitários mais simples.

## Exemplo Prático

Considere a classe `Greeter`, cuja responsabilidade é exibir uma saudação. Ela possui duas dependências: `IGreetingProvider` e `IGreetingWriter`. Essas dependências são injetadas no construtor da classe `Greeter`, permitindo que as versões simuladas (mock) delas sejam injetadas durante os testes unitários.

```csharp
public class Greeter
{
    private readonly IGreetingProvider _greetingProvider;
    private readonly IGreetingWriter _greetingWriter;

    public Greeter(IGreetingProvider greetingProvider, IGreetingWriter greetingWriter)
    {
        _greetingProvider = greetingProvider;
        _greetingWriter = greetingWriter;
    }

    public void Greet()
    {
        var greeting = _greetingProvider.GetGreeting();
        _greetingWriter.WriteGreeting(greeting);
    }
}

public interface IGreetingProvider
{
    string GetGreeting();
}

public interface IGreetingWriter
{
    void WriteGreeting(string greeting);
}
```

Neste exemplo, as implementações reais de `IGreetingProvider` e `IGreetingWriter` podem interagir com APIs ou bancos de dados. No entanto, durante os testes unitários, versões simuladas como `TestGreetingProvider` e `TestGreetingWriter` podem ser injetadas para isolar o comportamento da classe `Greeter`.

**Testando a Classe Greeter**

```csharp
[TestClass]
public class GreeterTests
{
    [TestMethod]
    public void Greeter_WritesGreeting()
    {
        var greetingProvider = new TestGreetingProvider();
        var greetingWriter = new TestGreetingWriter();
        var greeter = new Greeter(greetingProvider, greetingWriter);
        
        greeter.Greet();
        
        Assert.AreEqual(greetingWriter[0], TestGreetingProvider.TestGreeting);
    }
}
```

Neste teste, não nos preocupamos com o comportamento específico de `IGreetingProvider` e `IGreetingWriter`. O objetivo é garantir que a classe `Greeter` interaja corretamente com essas dependências. A injeção de dependência simplifica esse processo, permitindo a substituição fácil de implementações reais por versões simuladas durante os testes.

# Por que Usamos Contêineres de Injeção de Dependência (Containers IoC)

A injeção de dependência significa escrever classes de modo que elas não controlem suas dependências, mas sim que essas dependências sejam fornecidas a elas (injetadas).

O uso de um contêiner de injeção de dependência, também conhecido como "DI container" ou "IoC container," simplifica o processo de injeção de dependência. Ele é especialmente útil quando lidamos com várias classes que dependem umas das outras e têm diferentes requisitos de configuração.

## Problema da Construção Manual de Instâncias

Quando as classes dependem de várias interfaces e valores específicos, criar instâncias manualmente se torna complicado. O exemplo abaixo ilustra a complexidade:

```csharp
public CustomerData GetCustomerData(string customerNumber)
{
    var customerApiEndpoint = ConfigurationManager.AppSettings["customerApi:customerApiEndpoint"];
    var logFilePath = ConfigurationManager.AppSettings["logwriter:logFilePath"];
    var authConnectionString = ConfigurationManager.ConnectionStrings["authorization"].ConnectionString;

    using (var logWriter = new LogWriter(logFilePath))
    {
        using (var customerApiClient = new CustomerApiClient(customerApiEndpoint))
        {
            var customerService = new CustomerService(
                new SqlAuthorizationRepository(authConnectionString, logWriter),
                new CustomerDataRepository(customerApiClient, logWriter),
                logWriter
            );

            // ... Resto do código ...
        }
    }
}
```

Essa abordagem manual torna-se impraticável à medida que as dependências aumentam. Além disso, lidar com classes que implementam `IDisposable` pode se tornar um desafio.

## Uso de Contêineres de Injeção de Dependência

Os contêineres de injeção de dependência simplificam esse processo, permitindo configurar quais classes ou valores devem ser usados para satisfazer cada dependência. No exemplo abaixo, usamos o Castle Windsor como contêiner de injeção de dependência:

```csharp
var container = new WindsorContainer();
container.Register(
    Component.For<CustomerService>(),
    Component.For<ILogWriter, LogWriter>()
        .DependsOn(Dependency.OnAppSettingsValue("logFilePath", "logWriter:logFilePath")),
    Component.For<IAuthorizationRepository, SqlAuthorizationRepository>()
        .DependsOn(Dependency.OnValue(connectionString, ConfigurationManager.ConnectionStrings["authorization"].ConnectionString)),
    Component.For<ICustomerDataProvider, CustomerApiClient>()
        .DependsOn(Dependency.OnAppSettingsValue("apiEndpoint", "customerApi:customerApiEndpoint"))
);
```

Isso simplifica a criação de instâncias, permitindo que o contêiner resolva automaticamente as dependências e suas configurações.

```csharp
var customerService = container.Resolve<CustomerService>();
var data = customerService.GetCustomerData(customerNumber);
container.Release(customerService);
```

Ao solicitar uma dependência ao contêiner, ele a resolve, criando todas
as instâncias necessárias até que seja possível retornar uma instância
de `CustomerService`. Se uma classe exigir uma dependência
não registrada, o contêiner lançará uma exceção indicando que não há
nada registrado para atender a esse requisito.

- "Dependency Injection Principles, Practices, and Patterns" de Mark Seemann.
- [Microsoft Docs - Dependency Injection in .NET](https://docs.microsoft.com/en-us/dotnet/core/extensions/dependency-injection) - A documentação oficial da Microsoft oferece informações detalhadas sobre injeção de dependência no ecossistema .NET.
- [Castle Windsor Documentation](https://github.com/castleproject/Windsor/blob/master/docs/README.md) - Se você estiver interessado em contêineres de injeção de dependência específicos, a documentação do Castle Windsor fornece insights valiosos.
- [Martin Fowler - Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html) - Um artigo clássico de Martin Fowler que explora o conceito de contêineres de inversão de controle e injeção de dependência.
