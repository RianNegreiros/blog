# Introdução a C #

# O que é o C#?

Embora o C# seja derivado da [linguagem de programação C](https://pt.wikipedia.org/wiki/C_(linguagem_de_programa%C3%A7%C3%A3o)), ele tem recursos como a [coleta de lixo](https://pt.wikipedia.org/wiki/Coletor_de_lixo_(inform%C3%A1tica)) que permitem que os iniciantes se tornem proficientes em C# mais rapidamente do que em [C](https://pt.wikibooks.org/wiki/Programar_em_C) ou [C++](https://pt.wikibooks.org/wiki/Programar_em_C%2B%2B). Semelhante ao [Java](https://pt.wikipedia.org/wiki/Java_(linguagem_de_programa%C3%A7%C3%A3o)), ele é

- [Orientada a objetos](https://pt.wikipedia.org/wiki/Programa%C3%A7%C3%A3o_orientada_a_objetos)
- Vem com uma extensa biblioteca de classes
- Oferece suporte ao tratamento de exceções
- Vários tipos de [polimorfismo](https://pt.wikipedia.org/wiki/Polimorfismo_(ci%C3%AAncia_da_computa%C3%A7%C3%A3o))
- Separação de interfaces de implementações

Os recursos mencionados acima, combinados com suas poderosas ferramentas de desenvolvimento, suporte multiplataforma e genéricos, fazem do C# uma boa opção para muitos tipos de projetos de desenvolvimento de software, como

## Usos do C #

- Projetos de [desenvolvimento rápido de aplicações](https://pt.wikipedia.org/wiki/Desenvolvimento_r%C3%A1pido_de_aplica%C3%A7%C3%B5es)
- Aplicações de Internet com [ASP.NET Core](https://learn.microsoft.com/pt-br/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-7.0)
- Estruturas de teste, como o [NUnit](https://docs.nunit.org/articles/nunit/intro.html), tornam o C# adequado ao desenvolvimento orientado por testes e, portanto, uma boa linguagem para uso com o [Extreme Programming](https://pt.wikipedia.org/wiki/Programa%C3%A7%C3%A3o_extrema) (XP).

Sua [tipagem estática](https://www.alura.com.br/artigos/o-que-sao-as-tipagens-estatica-e-dinamica-em-programacao) ajuda a evitar muitos erros de programação comuns em linguagens com tipagem fraca. Devido a essas semelhanças com outras linguagens, é possível apresentar o **C#** como uma linguagem com recursos do C++, além de ter o estilo de programação do **Java**.

Grande parte do poder do **C#** (assim como o de outras linguagens **.NET**) vem com a [API comum do **.NET**](https://learn.microsoft.com/pt-br/dotnet/api/), que fornece um grande conjunto de classes, incluindo aquelas para criptografia, programação de soquete, **TCP/IP** e gráficos. Dessa forma, os desenvolvedores podem escrever parte de uma aplicação em **C#** e outra parte em outra linguagem **.NET** (por exemplo, Visual Basic .NET), mantendo as ferramentas, a biblioteca e o modelo de desenvolvimento orientado a objetos, mas tendo apenas que aprender a nova sintaxe da linguagem.

Devido às semelhanças entre o **C#** e a família de linguagens **C**, bem como Java, um desenvolvedor com experiência em linguagens orientadas a objetos, como **C++**, pode achar a estrutura e a sintaxe do **C#** intuitivas.

Agora vamos nos aprofundar nos conceitos básicos do C# e aprender a escrever um simples programa "**Hello World!**".

# ****Hello World!****

Há elementos básicos que todos os programas executáveis em C# têm, e é isso que nos ver agora, começando com um programa simples em C#.

💡 **Aviso**: O C# é [case-sensitive](https://pt.wikipedia.org/wiki/Case-sensitive)

Abaixo está uma aplicação C# muito simples.

É uma aplicação de console que imprime a mensagem `"Hello, World!"`, para iniciar uma aplicação de console, de uma olhada na [documentação oficial](https://learn.microsoft.com/pt-br/dotnet/core/tutorials/with-visual-studio-code?pivots=dotnet-7-0).

```csharp
// Declaração de namespace
using System;

// Classe de início do programa
class HelloWorld
{
    // Main inicia a execução do programa
    static void Main()
    {
        // Escreve no console
      Console.WriteLine("Hello, World!"); 
    }
}
```

Resultado:

```bash
Hello, World!
```

O programa tem **quatro** elementos principais:

- uma declaração de *namespace*
- uma classe
- o método `Main`
- uma declaração de programa

💡 **Observação**: Como o C# diferencia maiúsculas de minúsculas, a palavra Main não é a mesma que sua grafia em minúsculas, main.

A linha 2 do programa acima está usando uma diretiva

### A Diretiva "using”

A linha 2 do programa acima está usando uma

- **Diretiva**
  - Ela declara que o arquivo atual pode usar membros do namespace indicado sem usar o nome completo.

Sem essa diretiva, todas as referências ao identificador `Console` teriam de ser precedidas por `System` e um ponto, porque `Console` é um membro do namespace `System`.

### Definição de classe

A declaração de classe, class `HelloWorld`, contém

- Dados
- Definições de método que seu programa usa para executar
- Um único método estático chamado `Main`
- O **método** é o ponto de entrada da aplicação
- O método `Main` pode ser declarado com um parâmetro para aceitar uma matriz de cadeias de caracteres e pode retornar um valor inteiro.
- A matriz de cadeias de caracteres passada para o método representa os argumentos da linha de comando usados na execução do programa
  - Esse programa não usa argumentos de linha de comando e, portanto, o método não foi declarado para aceitar **nenhum** argumento

### Corpo do método principal

- O método `Main` especifica seu comportamento com a instrução `Console.WriteLine(...)`
- O `Console` é uma classe do namespace `System`
- WriteLine(...) é um método da classe `Console`
- Usamos o operador ".", ponto, para separar elementos subordinados do programa.

💡 Observe que também poderíamos escrever como System.Console.WriteLine(...).

- Isso segue o padrão **namespace.class.method** como uma declaração totalmente qualificada. Se tivéssemos deixado de fora a declaração using `System` na parte superior do programa, teria sido obrigatório usar o caminho completo: `System.Console.WriteLine(...)`. Essa declaração é o que faz com que a string "`Hello, World!`" seja impressa na tela do console.

Agora que você viu o básico sobre como escrever um programa simples em C#, vamos dar uma olhada em outro exemplo um pouco mais complexo.

## Um programa interativo simples

No exemplo anterior, você simplesmente executa o programa e ele produz uma saída. Mas, muitos programas são escritos para aceitar a entrada da linha de comando.

Abaixo está um exemplo de um programa de amostra mais interativo que recebe entrada usando a linha de comando.

Neste exemplo, será pedido que você digite seu nome, que será exibido quando você executar o código.

```csharp
// Declaração de namespace
using System;

// Classe de início do programa
class HelloWorld
{
    // Main inicia a execução do programa
    static void Main()
    {
        // Escreve no console
      Console.WriteLine("Digite seu nome:");
    // Ler do console
   string name = Console.ReadLine();
    // escrevendo a string e o argumento adicional no console
   Console.WriteLine("Oi, {0}", name);
    // manter o programa em execução até que o usuário pressione Enter
   Console.ReadLine();
    }
}
```

O método `WriteLine` é, na verdade, um pouco mais complicado do que pode parecer à primeira vista. Na verdade, o método `WriteLine` funciona de forma semelhante à função [printf](https://cplusplus.com/reference/cstdio/printf/) do C++.

- Ele recebe uma string e um número variável de argumentos adicionais.
- O primeiro argumento é conhecido como string de formato.
- O método `WriteLine` pesquisa a string de formato em busca de tokens do formato **{index}**, como "**{0}**", conhecidos como itens de formato (os itens de formato podem, na verdade, ter uma sintaxe mais elaborada do que a indicada aqui, mas isso será ignorado por enquanto para manter a simplicidade).
- A função `WriteLine` substitui cada item de formato pelo argumento especificado pelo índice do item de formato. Assim, quando `Console.WriteLine("Oi, {0}", name)` é chamada, o "**{0}**" é substituído pelo nome inserido pelo usuário.

A linha final do método é uma espécie de hack que garante que o programa continue em execução até que o usuário pressione a tecla "**Enter**".

💡 **OBSERVAÇÃO IMPORTANTE**: Todas as declarações terminam com `;`, ponto e vírgula. As classes e os métodos começam com `{`, uma chave à **esquerda**, e terminam com `}`, uma chave à **direita**. Todas as instruções dentro e incluindo `{` e `}` definem um bloco. Os blocos definem o escopo (ou tempo de vida e visibilidade) dos elementos do programa.

Agora que você conhece a estrutura básica de um programa C#, vamos nos aprofundar em mais detalhes.

## O que são variáveis?

Em qualquer linguagem de programação, uma variável é uma parte nomeada da memória do computador, que contém algumas informações em seu interior.

> Pense em uma variável como uma caixa com um nome, onde podemos "armazenar" algo.
>

Criamos, editamos e excluímos variáveis, sempre que necessário em nossas tarefas. Elas são **dispositivos** usados para armazenar dados, como um número ou uma cadeia de caracteres.

### Como nomear suas variáveis

Programadores usam nomes *simbólicos* para descrever variáveis, como:

"a quantidade de dinheiro na conta bancária do cliente".

> **Observação**: O compilador auxilia os programadores gerenciando a relação entre as representações simbólicas e numéricas dos locais das variáveis para reduzir o número de erros que os programadores certamente cometeriam se fossem obrigados a se referir a cada variável que têm em mente apenas por seu local atual nas memórias de seus computadores.
>

O C# exige que o programador use nomes construídos com letras escolhidas de **a a z**, de **A a Z**, e os números são escolhidos de **0 a 9**. O C# considera que as letras maiúsculas são diferentes das letras minúsculas.

> **Observação importante**: é uma boa prática de programação iniciar os nomes das variáveis com letras minúsculas.
>

Assim, o C# permite:

`a`

seja usado como um nome de variável. Alguns exemplos de tipos de nomes de variáveis permitidos pelo C# incluem:

```csharp
using System;

class Program {
  static void Main() {
    int a=2;
    char A='b';
    float rt345=2.3F;
    double areaQuadrada=6.5; //camelCase
    Console.WriteLine("Valor de a é {0}",a);
    Console.WriteLine("Valor de A é {0}",A);
    Console.WriteLine("Valor de rt345 é {0}",rt345);
    Console.WriteLine("Valor de areaQuadrada é {0}",areaQuadrada);    
  }
}
```

Resultado:

```bash
Valor de a é 2
Valor de A é b
Valor de rt345 é 2.3
Valor de areaQuadrada é 6.5
```

A convenção normalmente seguida para a nomeação de variáveis é camelCase, o que significa que a primeira letra permanece minúscula e todo alfabeto para a nova palavra na frase começa com uma letra maiúscula; por exemplo: areaQuadrada.

O **saldo da conta do cliente** não é um nome de variável válido no C# porque contém espaços, que não são permitidos, e porque contém um apóstrofo, que também não é permitido pelo C# em nomes de variáveis.

Agora que você tem informações básicas sobre variáveis em C#, vamos aprender como as variáveis são armazenadas na memória.

## Onde são armazenadas as variáveis?

Uma variável é um local na memória onde o aplicativo pode armazenar algum tipo de informação.

> Você pode pensar na memória como uma "matriz de bytes". Cada byte possui um endereço de memória.
>

Após a declaração da variável, um local de memória é reservado para essa variável.

### Exemplo

Por exemplo, declaramos uma variável `int` que ocupa 4 bytes e uma variável `char` que ocupa **2 bytes** de memória:

```csharp
using System;

namespace VariaveisExemploUm
{
    class Program
    {
        static void Main(string[] args)
        {
            int meuInt;
            char meuChar;
        }
    }
}
```

![Representação de memória para armazenamento de variáveis](assets/meu_int.png)

Representação de memória para armazenamento de variáveis

💡 **Observação**: 1 byte = 8 bits

## Sintaxe de variável

Explicação resumida sobre declaração e inicialização de uma variável em C#.

### Declaração

A sintaxe da declaração de variáveis é a seguinte:

```csharp
<variavel_tipo> <variavel_nome>;
```

```csharp
int varInt;       //declaração simples do tipo int
float varFloat;   //declarando uma variável do tipo float
```

### Inicialização

A sintaxe para a inicialização de variáveis também é simples! Basta seguir o seguinte:

```csharp
<variavel_tipo> <variavel_nome> = <valor>;
```

```csharp
int varInt = 5;   //declarando uma variável int e atribuir valor a ela
```

Você também pode separar a declaração e a inicialização:

```csharp
int varInt;   //Declaração
varInt = 5;   //Inicialização
```

## Tipos de variáveis

Uma visão geral de todos os tipos de variáveis em C#, como `int`, `bool`, `double`, `char` e `float`

### Inteiro

Um número inteiro é um número que não tem nenhuma casa decimal. É um número inteiro, por exemplo, **1,2,3,4** são todos inteiros. **4,3** não é. Se você tentasse colocar o número **4,3** em um número inteiro, o número seria truncado em **4**.

Também há outros tipos diferentes em um número inteiro. Todos eles diferem em tamanho, o que discutiremos mais adiante na lição.

Vamos dar uma olhada em cada um deles:

```csharp
using System;

namespace VariaveisExemploUm
{
    class Program
    {
        static void Main(string[] args)
        {
            sbyte a = -128; 
            Console.WriteLine("A variável a contém " + a);
            byte b = 255;
            Console.WriteLine("A variável b contém " + b);
            short c = 32767;
            Console.WriteLine("A variável c contém " + c);
            ushort d = 65535;
            Console.WriteLine("A variável d contém " + d);
            int e = 2147483647;
            Console.WriteLine("A variável e contém " + e);
            uint f = 4294967295;
            Console.WriteLine("A variável f contém " + f);
            long g = 9223372036854775807;
            Console.WriteLine("A variável g contém " + g);
            ulong h = 18446744073709551615;
            Console.WriteLine("A variável h contém " + h);
        }
    }
}
```

Resultado:

```bash
A variável a contém -128
A variável b contém 255
A variável c contém 32767
A variável d contém 65535
A variável e contém 2147483647
A variável f contém 4294967295
A variável g contém 9223372036854775807
A variável h contém 18446744073709551615
```

### Float

Os `float` são números de ponto flutuante com um tamanho de armazenamento de `4 bytes`, o que significa que esses números podem conter casas decimais. Isso nos permite armazenar números como "**8,344**" e "**34353,24123**".

Vamos ver os diferentes tipos de `float`:

```csharp
using System;

namespace VariaveisExemploUm
{
    class Program
    {
        static void Main(string[] args)
        {
            float i = 256.4788f; 
            Console.WriteLine("A variável i contém " + i);
            double j = 2545645645.6647; 
            Console.WriteLine("A variável j contém " + j);
            decimal k = 0.33333333333333333333333333357m; 
            Console.WriteLine("A variável k contém " + k);
        }
    }
}
```

Resultado:

```bash
A variável i contém 256.4788
A variável j contém 2545645645.6647
A variável k contém 0.3333333333333333333333333336
```

### Char

Um `char` é um tipo de dados de **16 bits**. Isso significa que um `char` pode armazenar entre **U+0000 e U+FFFF**. Os `char` são comumente usados para armazenar texto no formato UTF-16 (Unicode). Um char pode ser inicializado para conter um número ou um caractere, mas armazenará somente o valor Unicode.

```csharp
using System;

namespace VariaveisExemploUm
{
    class Program
    {
        static void Main(string[] args)
        {
            char a = 'a'; 
            Console.WriteLine("A variável a contém " + a);
        }
    }
}
```

Resultado:

```bash
A variável a contém a
```

> **Observação**: ASCII é um sistema em que um valor numérico é atribuído a cada caractere que você possa imaginar. Para obter uma tabela de conversão completa, acesse [http://ascii-code.com/](http://ascii-code.com/)
>

### Boolean

O tipo `bool` (booleano) é um tipo de dados de 1 byte que é true ou false. Verdadeiro é qualquer número diferente de zero e falso é zero. A palavra-chave true usa o valor 1 para atribuir true.

```csharp
using System;

class ExemploBooleano{
  static void Main() {
    bool podePular = false;
    bool podeFazer = true;
    Console.WriteLine("Valor de podePular é: {0}", podePular);
    Console.WriteLine("Valor de podeFazer é: {0}", podeFazer);

  }
}
```

Resultado:

```bash
Valor de podePular é: False
Valor de podeFazer é: True
```

### Tamanhos

Essa tabela mostra como os tamanhos das variáveis variam de acordo com os tipos de variáveis.

| Alias em C# | Tamanho (bits) | Alcance |
| --- | --- | --- |
| sbyte | 8 | -128 a 127 |
| byte | 8 | 0 a 255 |
| short | 16 | -32,768 a 32,767 |
| ushort | 16 | 0 a 65,535 |
| char | 16 | Um caractere unicode de código 0 a 65.535 |
| int | 32 | 2,147,483,648 a 2,147,483,647 |
| uint | 32 | 0 a 4,294,967,295 |
| long | 64 | 9,223,372,036,854,775,808 a 9,223,372,036,854,775,807 |
| ulong | 64 | 0 a 18,446,744,073,709,551,615 |
| float | 32 | $1.5× 10−4510−45 to 3.4×3.4× 10381038$ |
| double | 64 | $5.0× 10−32410−324 to 1.7×1.7× 1030810308$ |
| decimal | 128 | $1.0× 10−2810−28 to 7.9×7.9× 10281028$ |
| bool | 8 | true ou false |
| string | 16*comprimento | Uma cadeia de caracteres unicode sem limite especial. |
| object | 32/64 | Dependente da plataforma (um ponteiro para um objeto). |

### Evite qualquer possível erro

Se você tentar armazenar algum valor que exceda o alcance específico de um tipo de variável, receberá um erro de tempo de execução:

```csharp
using System;

namespace VariaveisExemplo
{
    class Program
    {
        static void Main(string[] args)
        {
            sbyte a = -129; 
            Console.WriteLine("A variável a contém " + a);
        }
    }
}
```

Resultado:

```bash
main.cs(9,24): error CS0031: Constant value `-129' cannot be converted to a `sbyte'
```

Aqui, o tipo `sbyte` pode armazenar números inteiros de **-128 a 127**. O valor de **-129** está fora de seu limite e, portanto, gerará um erro.

## Varável Casting

Como as variáveis podem ser convertidas de um tipo para outro por meio de casting.

## O que é Casting?

Muitas vezes é importante poder converter entre diferentes tipos de dados.

Por exemplo, o usuário pode solicitar um valor de precisão dupla ao seu programa, mas o programa os armazena como `floats`. Isso requer um método de conversão entre os tipos. Essa conversão é realizada por meio de **casting**.

A sintaxe da conversão é a seguinte:

```csharp
(type)name
```

Em que type é o tipo a ser convertido e name é o nome da variável a ser convertida.

### Exemplo

```csharp
using System;

namespace VariaveisExemploUm
{
    class Program
    {
        static void Main(string[] args)
        {
            float meuFloat = 76.467f;
            Console.WriteLine("A variável meuFloat contém " + meuFloat);
            int meuFloat2 = (int)meuFloat;
            Console.WriteLine("O cast para inteiro do meyFloat é " + meuFloat2);
        }
    }
}
```

Resultado:

```bash
A variável meuFloat contém 76.467
O cast para inteiro do meyFloat é 76
```

Essa linha 9 declara uma variável do tipo float z. A linha 11 do código executa uma conversão de tipo na variável de ponto flutuante chamada meuFloat. É importante observar que a conversão não altera o conteúdo da variável meuFloat.

Em vez disso, um novo valor inteiro de meuFloat2 é criado e armazenado na variável **truncated**.

> O nome **truncated** vem do fato de que esse tipo de conversão (de float para int) remove qualquer precisão após a casa decimal. Isso é chamado de **truncating**.
>