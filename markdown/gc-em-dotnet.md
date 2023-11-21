# Coleta de Lixo(Garbage Collector) em .Net

No ecossistema .Net, objetos criados com `new()` são alocados no heap gerenciado. Esses objetos nunca são explicitamente finalizados pelo programa que os utiliza; em vez disso, esse processo é controlado pelo Coletor de Lixo (.Net Garbage Collector).

Algumas das exemplos abaixo são "casos de laboratório" para mostrar o Coletor de Lixo em ação e alguns detalhes significativos do seu comportamento, enquanto outros se concentram em como preparar classes para o tratamento adequado pelo Coletor de Lixo.

## Um exemplo básico de (coleta de) lixo

Considere a seguinte classe:

```csharp
public class ObjetoFinalizavel
{
    public ObjetoFinalizavel()
    {
        Console.WriteLine("Instância inicializada");
    }

    ~ObjetoFinalizavel()
    {
        Console.WriteLine("Instância finalizada");
    }
}

```

Um programa que cria uma instância, mesmo sem utilizá-la:

```csharp
new ObjetoFinalizavel(); // Objeto instanciado, pronto para ser usado

```

Produz a seguinte saída:

```csharp
<namespace>.ObjetoFinalizavel inicializado

```

Se nada mais acontecer, o objeto não é finalizado até que o programa termine (o que libera todos os objetos no heap gerenciado, finalizando-os no processo).

É possível forçar o Coletor de Lixo a ser executado em um determinado ponto, da seguinte forma:

```csharp
new ObjetoFinalizavel(); // Objeto instanciado, pronto para ser usado
GC.Collect();

```

O que produz o seguinte resultado:

```csharp
<namespace>.ObjetoFinalizavel inicializado
<namespace>.ObjetoFinalizavel finalizado

```

Dessa vez, assim que o Coletor de Lixo foi invocado, o objeto não utilizado (também conhecido como objeto "morto") foi finalizado e liberado do heap gerenciado.

### Objetos vivos e objetos mortos - o básico

Regra geral: quando a coleta de lixo ocorre, "objetos vivos" são aqueles que ainda estão em uso, enquanto "objetos mortos" são aqueles que não estão mais em uso (qualquer variável ou campo que os referencia, se houver, saiu do escopo antes da coleta ocorrer).

No exemplo a seguir (por conveniência, ObjetoFinalizavel1 e ObjetoFinalizavel2 são subclasses de ObjetoFinalizavel do exemplo acima e herdam o comportamento de mensagens de inicialização / finalização):

```csharp
var obj1 = new ObjetoFinalizavel1(); // Instância de ObjetoFinalizavel1 alocada aqui
var obj2 = new ObjetoFinalizavel2(); // Instância de ObjetoFinalizavel2 alocada aqui
obj1 = null; // Não há mais referências para a instância de ObjetoFinalizavel1
GC.Collect();

```

A saída será:

```csharp
<namespace>.ObjetoFinalizavel1 inicializado
<namespace>.ObjetoFinalizavel2 inicializado
<namespace>.ObjetoFinalizavel1 finalizado

```

No momento em que o Coletor de Lixo é invocado, ObjetoFinalizavel1 é um objeto morto e é finalizado, enquanto ObjetoFinalizavel2 é um objeto vivo e é mantido no heap gerenciado.

## Múltiplos objetos mortos

E se dois (ou vários) objetos mortos referenciarem um ao outro? Isso é mostrado no exemplo abaixo, supondo que OutroObjeto seja uma propriedade pública de ObjetoFinalizavel:

```csharp
var obj1 = new ObjetoFinalizavel1();
var obj2 = new ObjetoFinalizavel2();
obj1.OutroObjeto = obj2;
obj2.OutroObjeto = obj1;
obj1 = null; // Programa não faz mais referência à instância de ObjetoFinalizavel1
obj2 = null; // Programa não faz mais referência à instância de ObjetoFinalizavel2
// Mas os dois objetos ainda se referenciam
GC.Collect();

```

Isso produz a seguinte saída:

```csharp
<namespace>.ObjetoFinalizavel1 inicializado
<namespace>.ObjetoFinalizavel2 inicializado
<namespace>.ObjetoFinalizavel1 finalizado
<namespace>.ObjetoFinalizavel2 finalizado

```

Os dois objetos são finalizados e liberados do heap gerenciado, apesar de se referenciarem mutuamente (porque não existe outra referência a nenhum deles de um objeto realmente vivo).

## Referências Fracas

Referências fracas são... referências, a outros objetos (também conhecidos como "alvos"), mas "fracas" pois não impedem que esses objetos sejam coletados pelo lixo. Em outras palavras, referências fracas não contam quando o Coletor de Lixo avalia objetos como "vivos" ou "mortos".

O código a seguir:

```csharp
var fraca = new WeakReference<ObjetoFinalizavel>(new ObjetoFinalizavel());
GC.Collect();

```

Produz a saída:

```csharp
<namespace>.ObjetoFinalizavel inicializado
<namespace>.ObjetoFinalizavel finalizado

```

O objeto é liberado do heap gerenciado, apesar de ser referenciado pela variável WeakReference (ainda em escopo quando o Coletor de Lixo foi invocado).

Consequência #1: a qualquer momento, não é seguro assumir se um alvo de WeakReference ainda está alocado no heap gerenciado ou não.

Consequência #2: sempre que um programa precisa acessar o alvo de uma WeakReference, o código deve ser fornecido para ambos os casos, do alvo ainda estar alocado ou não. O método para acessar o alvo é TryGetTarget:

```csharp
var alvo = new objeto(); // Qualquer objeto servirá como alvo
var fraca = new WeakReference<objeto>(alvo); // Cria referência fraca nula;
alvo = null; // Descarta a referência forte para o alvo
   // ... Muitas coisas podem acontecer no meio
   // Verifique se o alvo ainda está disponível
   if (fraca.TryGetTarget(out alvo))
 {
  // Use a variável alvo re-inicializada
  // Para fazer o que for necessário com o alvo
 }
else
 {
  // Faça algo quando não houver mais objeto alvo
  // O valor da variável alvo não deve ser usado aqui
 }

```

A versão genérica de WeakReference está disponível desde o .Net 4.5. Todas as versões do framework fornecem uma versão não genérica, sem tipo, que é construída da mesma forma e verificada da seguinte forma:

```csharp
var alvo = new objeto(); // Qualquer objeto servirá como alvo
var fraca = new WeakReference<objeto>(alvo); // Cria referência fraca nula;
alvo = null; // Descarta a referência forte para o alvo
   // ... Muitas coisas podem acontecer no meio
   // Verifique se o alvo ainda está disponível
   if (fraca.IsAlive)
 {
  alvo = fraca.Target;
  // Use a variável alvo re-inicializada
  // Para fazer o que for necessário com o alvo
 }
else
 {
  // Faça algo quando não houver mais objeto alvo
  // O valor da variável alvo não deve ser usado aqui
 }

```

### Dispose() vs. finalizadores

Implemente o método Dispose() (e declare a classe que o contém como IDisposable) como um meio de garantir que quaisquer recursos pesados de memória sejam liberados assim que o objeto não estiver mais em uso. A "pegadinha" é que não há uma garantia forte de que o método Dispose() seria invocado (ao contrário dos finalizadores que sempre são invocados no final da vida do objeto).

Um cenário é um programa chamando Dispose() em objetos que ele cria explicitamente:

```csharp
private void AlgumaFuncao()
{
// Inicializa um objeto que usa recursos externos pesados
var objetoDescartavel = new ClasseQueImplementaIDisposable();

// ... Usa esse objeto

// Descarta assim que não for mais usado
objetoDescartavel.Dispose();

//... Faz outras coisas

// A variável objetoDescartavel sai de escopo aqui
// O objeto será finalizado mais tarde (sem garantia de quando)
// Mas não mantém mais o recurso externo pesado depois de ser descartado
}

```

Outro cenário é declarar uma classe para ser instanciada pelo framework. Nesse caso, a nova classe geralmente herda de uma classe base, por exemplo, em MVC, cria-se uma classe de controlador como uma subclasse de System.Web.Mvc.ControllerBase.

Quando a classe base implementa a interface IDisposable, isso é uma boa indicação de que Dispose() seria invocado adequadamente pelo framework - mas novamente, não há uma garantia forte.

Assim, Dispose() não é um substituto para um finalizador; em vez disso, ambos devem ser usados para propósitos diferentes:

- Um finalizador libera recursos eventualmente para evitar vazamentos de memória que ocorreriam de outra forma
- Dispose() libera recursos (possivelmente os mesmos) assim que esses não forem mais necessários, para aliviar a pressão na alocação de memória geral.

### Descarte e finalização adequados de objetos

Como Dispose() e finalizadores visam propósitos diferentes, uma classe que gerencia recursos externos pesados de memória deve implementar ambos. A consequência é escrever a classe de modo que ela lide bem com dois cenários possíveis:

- Quando apenas o finalizador é invocado
- Quando Dispose() é invocado primeiro e mais tarde o finalizador também é invocado

Uma solução é escrever o código de limpeza de modo que executá-lo uma ou duas vezes produza o mesmo resultado que executá-lo apenas uma vez. A viabilidade depende da natureza da limpeza, por exemplo:

- Fechar uma conexão de banco de dados já fechada provavelmente não terá efeito, então funciona
- Atualizar uma "contagem de uso" é perigoso e produziria um resultado errado quando chamado duas vezes em vez de uma.

Uma solução mais segura é garantir por design que o código de limpeza seja chamado uma vez e apenas uma vez, seja qual for o contexto externo. Isso pode ser alcançado da "maneira clássica" usando uma bandeira dedicada:

```csharp
public class DescartavelFinalizavel1 : IDisposable
{
 private bool descartado = false;

 ~DescartavelFinalizavel1() { Limpeza(); }

 public void Dispose() { Limpeza(); }

 private void Limpeza()
{
 if(!descartado)
{
// O código real para liberar recursos vai aqui, então
descartado = true;
}
}
}

```

Alternativamente, o Coletor de Lixo fornece um método específico SuppressFinalize() que permite pular o finalizador após o Dispose ter sido invocado:

```csharp
public class DescartavelFinalizavel2 : IDisposable
{

 ~DescartavelFinalizavel2() { Limpeza(); }

 public void Dispose() { Limpeza(); }

 public void Dispose()
{
 Limpeza();
 GC.SuppressFinalize(this);
}

 private void Limpeza()
{
// O código real para liberar recursos vai aqui
}
}
```
