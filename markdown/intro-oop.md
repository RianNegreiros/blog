# Introdução à Programação Orientada a Objetos

Na jornada de aprendizado em programação, é fundamental explorar e compreender os diferentes paradigmas que guiam o desenvolvimento de softwares. Neste primeiro vamos conhecer duas abordagens distintas: a programação estruturada e a programação orientada a objetos. Antes de nos aprofundarmos nas diferenças entre esses dois enfoques, é crucial compreender que cada um representa uma forma única de abordar e solucionar problemas, semelhante a duas pessoas distintas com o mesmo objetivo de vida: programar.

**Programação Estruturada: Uma Sequência Lógica de Processos**

A programação estruturada segue uma lógica sequencial direcionada à linguagem de máquina, onde as instruções são organizadas em sequência, decisões e iterações. Imagine o desenvolvimento de um programa como a produção de uma pizza, onde cada etapa é um processo isolado. Desde a preparação da massa até retirá-la do forno, cada passo é uma peça fundamental que, ao ser resolvida, contribui para a solução final do problema.

**Programação Orientada a Objetos: Mais Próxima do Raciocínio Humano**

Diferentemente da abordagem estruturada, a programação orientada a objetos organiza o código em torno de objetos, refletindo mais de perto o raciocínio humano. Nesse paradigma, os códigos são estruturados em termos de objetos, não de processos, proporcionando uma visão mais intuitiva e modular do desenvolvimento de software.

Ao explorar os conceitos fundamentais da programação orientada a objetos, mergulhamos nos elementos essenciais: objetos, classes, atributos e métodos. Os objetos são instâncias de classes, cada uma com atributos que descrevem suas características e métodos que representam suas ações.

**Abstração, Encapsulamento, Herança e Polimorfismo: Os Pilares da POO**

Aprofundando-se nesse paradigma, destacamos quatro pilares essenciais: abstração, encapsulamento, herança e polimorfismo.

1. **Abstração:** A abstração simplifica problemas complexos, concentrando-se apenas nas informações essenciais. É como construir um quebra-cabeça, onde criamos uma classe abstrata com métodos abstratos, deixando a implementação específica para as subclasses.
2. **Encapsulamento:** Essa técnica protege o código e o desenvolvedor, estabelecendo padrões e restringindo o acesso direto aos detalhes internos de um objeto. Um exemplo prático é uma máquina de café, onde os detalhes do processo de preparo são encapsulados, permitindo apenas a interação necessária.
3. **Herança:** Semelhante à herança no mundo real, na programação orientada a objetos, uma classe pode herdar atributos e métodos de outra. Isso promove a reutilização de código e economiza tempo no desenvolvimento, já que as novas classes podem incorporar características de suas classes ancestrais.
4. **Polimorfismo:** Permite que diferentes classes implementem o mesmo método de maneiras distintas. Isso promove flexibilidade e ampla aplicação do código, possibilitando que objetos diferentes respondam de maneira única a um comando comum.

**Profundidade da Programação Orientada a Objetos**

Ao compreender os conceitos essenciais da programação orientada a objetos, como abstração, encapsulamento, herança e polimorfismo, você adquire uma base teórica sólida. Estes pilares não são apenas cruciais para a programação orientada a objetos, mas também para sua atuação profissional futura nesta área. Prossiga seus estudos, mergulhe nas linguagens orientadas a objetos e continue a explorar as vastas possibilidades desse poderoso paradigma de programação.

No entanto, ainda é necessário aplicar isso às linguagens utilizadas na orientação ao objeto. Desta forma, neste módulo, você descobrirá algumas linguagens de programação que utilizam o paradigma orientado a objetos, como Java, C# e C++.

### Linguagem Java

Você já ouviu falar em programação Java alguma vez na vida? Esse nome é muito conhecido por ter sido escolhido devido a uma ilha da Indonésia, onde há uma produção de café exótico, com um simbolismo de uma xícara de café fumegante. Ao lado direito, a escrita Java.

Essa linguagem de programação foi criada nos anos 90, baseada na linguagem C. Ela foi desenvolvida pela empresa Sun Microsystems, que tem James Gosling como seu CEO. Mas atualmente, a empresa Oracle comprou a linguagem.

O diferencial da linguagem Java é ser multiplataforma, ou seja, um mesmo programa pode funcionar perfeitamente em Windows, Linux, Android e iOS. Atualmente, ela é muito utilizada em desenvolvimento da Internet das Coisas (IoT), aplicações para celular, além de jogos online, aplicativos para Android, páginas da Internet, documentos interativos, etc.

Observe no código a seguir, um exemplo de uma classe desenvolvida em linguagem
Java. Note que esta linguagem faz uso dos pilares da POO.

```java
public class Exercicio {
public static void main(String args[]) {
int x = 4;
do {
X--;
} while (x > 5);
System.out.println(x);
}
}
```

> Este exemplo mostra uma
classe publica, chamada
exercicio, criada de forma
a mostrar na tela os
numeros decrescentes,
comecando pelo numero 4 .
>

> Perceba que a variavel x
comeca valendo 4
>

> Dentro da repetição
while, e solicitado que a
cada volta no loop (comando
de repetição), seja
retirado um número x, e
depois este resultado deve
ser mostrado na tela
system. Out. Println (x) .
>

Vantagens e desvantagens da linguagem Java:

- Portabilidade: pode ser executada em várias plataformas como Windows, Mac, Linux, Unix, entre outras.
- Multi-Threaded: suporta multiprocessamentos, ou seja, permite realizar mais de um processo ao mesmo tempo.
- Criação de jogos: possui vários recursos que facilitam a produção de inúmeros tipos de jogos.
- Gratuidade: a linguagem e seus ambientes de desenvolvimento são totalmente gratuitos.
- Utilização: sua utilização atualmente tem amplitude mundial, sendo aplicada até mesmo em bancos e instituições do governo.
- Independência: Java é independente de qualquer plataforma, pois ela se conecta em diversas aplicações e sistemas.

Apesar da linguagem Java possuir muitas vantagens, como toda a linguagem, ela também possui algumas desvantagens que vale a pena você conhecer:

- Estrutura da linguagem complexa: para que seja realizada uma simples instrução em seu programa, é necessário que muitas linhas de códigos sejam programadas e escritas.
- Reutilização do código: como a linguagem faz uso do Bytecode, a segurança do código fica comprometida, facilitando para que outros programadores, sem a permissão, reutilizem o código e recuperem o código fonte original.

### Linguagem C++

Essa linguagem é muito conhecida e utilizada desde 1990, principalmente no início de 1980, por Bjarne Stroustrup, no Bell Laboratories. A C++ possui diversos recursos que embelezam a linguagem C, principalmente trazendo a possibilidade da programação orientada a objetos. Com isso, surge a possibilidade da linguagem poder ser utilizada em grandes projetos com diversos programadores colaborando simultaneamente.

C++ é uma linguagem de programação multiparadigma, onde podemos desenvolver um mesmo programa orientado a objetos (POO) ou procedural (linguagem estruturada). Ela nada mais é do que um desvio da linguagem C, sendo sua aplicação a principal.

Outra grande diferença entre as linguagens é que a C++ possui um desempenho, que de acordo com especialistas, é inigualável ao desempenho da linguagem C, pois o C++ é uma linguagem leve, por poder ser utilizada em qualquer plataforma e gerenciar a memória manualmente.

Observe o código a seguir, que apresenta uma classe criada em linguagem C++,
com o nome “minha classe”. classe“. Note que o Loop For é quem vai ajudar na
realização dos cálculos.

```cpp
#include <iostream>
#include <string>
class Minha Classe
{
public:
int ni, n2, n3;
void Calcular(int);
} void Minha Classe::Calcular(int x = 1)
{ n1 = 0 ;
n2 = 1;
std::cout << n1 << “ “ << n2;
for (int i = 1; i<= x-2; i++) {
n3 = n1 + n2;
std::cout << “ “ « n3;
n1 = n2;
n2 = n3;
}
}
int main()
{
int numero = 5;
Minhaclasse objeto;
objeto.Calcular();
}
```

Vamos agora conhecer as diferenças e as semelhanças entre Java e C++.

### Diferenças e semelhanças entre Java e C++

Agora que você conheceu ambas as linguagens, que tal ver no que elas se diferenciam e se assemelham?

| Design |
| --- |
| As duas linguagens são bem diferentes. Em compensação, ambas
são muito boas em relação a sua performance, apesar da C++ ter a
tendência de ser um pouco mais rápida, já que o Java necessita ser
interpretado no momento da execução do código. Esse processo
acaba tornando a linguagem Java um pouco mais lenta, mas, de
qualquer forma, as duas linguagens têm um bom desempenho,
mas de forma diferente. |

| Popularidade |
| --- |
| As duas linguagens são muito utilizadas mundialmente. Porém, a
C++ já era utilizada e reconhecida antes da Java entrar no mercado
por volta de 2012.
Atualmente, a Java é a linguagem vista como mais popular, apesar
do C++ ter sido desenvolvida para uso geral. Cada uma tem sua
legião de fãs. |

### Linguagem C #

Ok, mas como se lê este nome? C o quê?
O nome é pronunciado como C Sharp! E a escolha deste nome causou uma grande confusão entre as pessoas, que acreditavam na semelhança do nome com o C++. Porém, esta linguagem seria uma atualização do C++, como se fosse um C++++, entendeu?

Na verdade, o nome da linguagem foi escolhido, pois o símbolo # é utilizado para se referir ao sustenido, no meio musical. O sustenido indica meio tom acima de uma determinada nota musical. E já que o C# possui uma sintaxe elegante, expressiva e orientada a objetos, foi o nome escolhido pela Microsoft quando foi desenvolvida como parte da plataforma .NET.

A C# foi criada com base na linguagem C++, mas inseriu também alguns percursos de outras linguagens, como a Java e Object Pascal. A C# é uma linguagem multiparadigma e de tipagem (que não realiza conversões automaticamente, não permitindo que um mesmo dado seja tratado como se fosse de outro tipo).

A linguagem C# tem suas raízes em C, C++ e Java, adaptando os melhores recursos de cada linguagem para que seja possível acrescentar novas capacidades próprias. Como ela foi criada com base em linguagens utilizadas mundialmente e muito bem desenvolvidas, os programadores podem aprender facilmente e de forma agradável.

Pois, apesar da sua sintaxe parecer um pouco complexa, ela é muito simples para quem já está acostumado a programar.

No código abaixo, analise uma classe desenvolvida em C#. Observe que as diferenças no código são poucas, mas a lógica da POO é a mesma, pois temos o objeto "cachorro" e os atributos "nome" e "idade".

```csharp
public class Cachorro
{
  public Cachorro(string nome, int idade)
  {
    Nome = nome;
    Idade = idade;
  }

  public string Nome { get; protected set; } = "João";
  public int Idade { get; set; } = 1;

  public void IncrementaIdade() => Idade++;
  public int IdadeDoCachorroEmAnos( => Idade * 7;
}
```

Desta forma, você viu uma breve introdução voltada à linguagem C#, observando como ela foi criada e desenvolvida. Agora, no próximo tópico, vamos nos aprofundar nela, entendendo as suas principais características.

### Características da linguagem C #

A linguagem C# foi desenvolvida em 2000 pela Microsoft e baseada em diversas outras linguagens, entre elas a C++ e a Java. Ela agrupa as principais vantagens dessas duas linguagens, mas suas implementações foram melhoradas e novos recursos surgiram. Com isso, a linguagem se tornou muito interessante para desenvolvedores que desejem mudar para a Microsoft .NET (plataforma única para criação e execução de aplicações e sistemas).

A C# possui uma sintaxe muito simples que se assemelha muito à do Java e do C. Como a linguagem faz o uso do framework .NET, é possível a elaboração de diversas aplicações. Além disso, o ambiente de desenvolvimento gerencia os recursos, permitindo que o desenvolvedor não precise se preocupar com isso. Dessa forma, ele pode apenas se concentrar em seu trabalho principal, que é o desenvolvimento lógico.

Assim, você acompanhou as principais características e singularidades da linguagem C#, sobretudo, no que tange à aplicabilidade dela na programação. Apesar disso, ainda é relevante que você saiba não só as suas vantagens, mas também as desvantagens. Confira a seguir.

### Vantagens e desvantagens da linguagem C #

Como em qualquer outra linguagem de programação, a C# possui suas vantagens e desvantagens ao ser escolhida para o desenvolvimento de qualquer programa. Vamos conhecer algumas vantagens que valem a pena serem destacadas:

- Compatibilidade total com o Windows: é compatível com o ambiente Windows sem necessitar de nenhum programa ou configuração especial para que seja executada no ambiente. Isso serve tanto para ambientes Web, desktop, além de serem facilmente instalados em rede.
- Facilidade de se aprender a linguagem: como visto anteriormente, principalmente para quem já tem familiaridade com outras linguagens de POO, é uma das vantagens da C#. Encontrar programadores especializados pelo mundo se torna algo mais fácil e rápido, além dela ser relacionada com a Java, fazendo com que o desenvolvedor possa trabalhar com as duas ao mesmo tempo, agregando valor ao aplicativo a ser desenvolvido.
- Compatibilidade com desenvolvimentos para nuvem (armazenamento de dados fora da máquina, na Internet): a sua segurança, estabilidade e facilidade que proporciona aos aplicativos em nuvem, crescem a cada ano. Por isso, atualmente, um desenvolvedor deve estar atualizado quando o assunto é desenvolvimento em nuvem.
- Segurança: é uma das principais vantagens do C#. Nessa linguagem não se consegue utilizar uma variável não inicializada, já que isso pode causar estragos em uma aplicação. No C# se isto ocorrer, uma notificação será enviada ao desenvolvedor.

Apesar da linguagem ser muito bem estruturada e utilizada mundialmente, como todas as outras, também possui suas desvantagens, observe-as abaixo:

- Falta de suporte para o .NET: algumas estruturas .NET deixaram de receber suporte pela Windows depois de atualizações do seu sistema operacional.
- Perdas de espaço de memória: ocorre quando um espaço na memória, que já não é mais utilizado, não é liberado para uma nova locação. Apesar do .NET possuir um recurso chamado coletor de lixo, que realiza o serviço de tentar liberar esse espaço, esse problema ainda traz muita preocupação. Quanto mais uma aplicação cresce, mais espaços perdidos na memória são criados.
- Compilação: Apesar de ser uma vantagem, também pode ser uma desvantagem. Como o código precisa ser compilado a cada nova linha de código criada, o aparecimento de bugs (erros no código desenvolvido) pode ser frequente, se uma pequena modificação no código não for cuidadosamente testada.

Como vimos, são muitas as diferenças entre o modo de programar estruturado e a orientada a objetos, sendo que cada um tem sua utilidade e eficácia. Também conhecemos as diferenças entre cada uma das linguagens e como elas podem ajudar a programar em diferentes projetos.

Na orientação a objetos, alguns pilares devem ser conhecidos a fundo para que você consiga fazer o uso de suas ferramentas com eficiência. Por isso, você conheceu os principais que são: encapsulamento, abstração, polimorfismo e herança.

Já que o paradigma de programação orientada a objetos se tornou o mais utilizado, muitas linguagens que fazem o uso desse modo de programar apareceram no mercado, como: a Java, a C#, a C++ entre outras.

1. **HENRIQUE, Carlos.** Aula de Java 032 - abstract, classes abstratas. [https://www.youtube.com/watch?v=M-08A2_Ep4E](https://www.youtube.com/watch?v=M-08A2_Ep4E).
2. **LEITE, Thiago e Carvalho.** Orientação a Objetos: aprenda seus conceitos e suas aplicabilidades de forma efetiva. São Paulo: Casa do código, 2016.
3. **MACHADO, Rodrigo Prestes.** Desenvolvimento de Software III: Programação de Sistemas Web Orientado a Objeto. 3. ed. São Paulo: Bookman, 2016.
4. **MACORATI.** Disponível em: [http://www.macoratti.net/18/08/c_recurs1.htm](http://www.macoratti.net/18/08/c_recurs1.htm), 2021. Acessado em: 09 dez. 2021.
5. **MACORATTI, J. P.** C# - Recursos da Linguagem que você deveria conhecer. Macoratti, 2019. Disponível em: [http://www.macoratti.net/18/08/c_recurs1.htm](http://www.macoratti.net/18/08/c_recurs1.htm).
6. **MARIANO JUNIOR.** Abstração e Encapsulamento - Orientação a Objetos. [https://www.youtube.com/watch?v=X5UkVuJ5np8](https://www.youtube.com/watch?v=X5UkVuJ5np8).
7. **MORETTO, Luiz Augusto.** Programação Orientada a Objetos: Classes Abstratas. [https://morettic.com.br/wp2/poo/poo-classes-abstratas/](https://morettic.com.br/wp2/poo/poo-classes-abstratas/).
8. **BR.CCM.NET.** POO – Herança. Disponível em: [https://br.ccm.net/contents/414-poo-heranca](https://br.ccm.net/contents/414-poo-heranca).
9. **BOZ JUNIOR, Geraldo.** Fluxogramas e programação estruturada. [https://www.youtube.com/watch?v=kcic0UK-MMY](https://www.youtube.com/watch?v=kcic0UK-MMY).
10. **CANAL TI.** O que é programação orientada a objetos. [https://www.youtube.com/watch?v=ojEHf3q7akE](https://www.youtube.com/watch?v=ojEHf3q7akE).
11. **CURSO EM VÍDEO.** Curso POO Teoria #06a - Pilares da POO: encapsulamento [https://www.youtube.com/watch?v=1wYRGFXpVlg](https://www.youtube.com/watch?v=1wYRGFXpVlg).
12. **ESTUDO NA WEB.** Entenda o Conceito Polimorfismo | #5. [https://www.youtube.com/watch?v=DfzAPafCgNY](https://www.youtube.com/watch?v=DfzAPafCgNY)
