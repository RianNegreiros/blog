# O que é o Back-End?

O Back-End de um site consiste, em geral, em um servidor, bancos de dados e APIs.

## O que são servidores da Web?

Um servidor da Web é um sistema que fornece conteúdo e serviços aos usuários pela Internet. Os servidores da Web são exatamente como os computadores comuns, exceto pelo fato de serem mais potentes e robustos. Além disso, a maioria dos servidores não tem um monitor ou uma tela como a maioria dos PCs, mas você pode se conectar a eles com SSH. Para simplificar, o SSH é uma maneira de obter o terminal de outra máquina por conta própria. Um servidor da Web pode "hospedar" um site, ou seja, o servidor da Web terá todo o código do site nele: front e back, e qualquer pessoa com uma conexão à Internet pode acessá-lo com sua máquina para visualizar o site.

Tecnicamente, você pode abrir a porta 80 em seu PC e hospedar seu site lá. No entanto, isso geralmente nunca é feito porque um PC comum seria incapaz de lidar com a carga de potencialmente milhares de conexões de clientes, colocaria seus dados pessoais em risco e você teria que mantê-lo ligado com o servidor funcionando 24 horas por dia, 7 dias por semana. Portanto, os serviços de hospedagem na Web resolvem esse problema.

## Serviços de hospedagem na Web

As empresas de hospedagem na Web oferecem espaço em um servidor próprio ou alugado para uso dos clientes, além de fornecer conectividade com a Internet, geralmente em um data center. Normalmente, elas cobram uma taxa mensal por esse serviço. Um data center é um edifício que abriga vários servidores. Os data centers geralmente exigem refrigeração e manutenção extensivas para manter a temperatura de suas máquinas baixa, o que reduz as taxas de falhas e, portanto, aumenta os lucros. O Facebook, por exemplo, tem alguns dos maiores data centers do mundo!

<figure align="center">
    <img src="./assets/data-center.jpeg" alt="Servidores de rack em um data center" />
    <figcaption>
      <code>Servidores de rack em um data center</code>
    </figcaption>
</figure>

No entanto, a seleção do serviço adequado de hospedagem na Web desempenha um papel fundamental na implementação da Web e é uma decisão crítica.

## Sistemas operacionais de servidor da Web

Assim como seu computador pessoal tem um sistema operacional, Windows, Mac OS ou Linux, os servidores também precisam de um sistema operacional para funcionar. No entanto, a maioria dos sistemas operacionais de servidor é especializada para servidores e, portanto, ligeiramente diferente dos sistemas operacionais de desktop.

### Linux

O Linux não é um sistema operacional. É um grupo de sistemas operacionais gratuitos e de código aberto. Normalmente, o Linux é empacotado em um formato conhecido como distribuição Linux (ou distro, para abreviar) para uso em desktops e servidores. Há muitas variantes ou distros do Linux para uso em servidores. Por exemplo,

### CentOS

A vantagem de usar o CentOS é que ele é estável e raramente trava. Ele também é a distribuição mais popular para servidores atualmente, portanto, também tem a vantagem de contar com um excelente suporte da Web, o que significa que você não ficará preso a nenhum problema por muito tempo.

Além disso, a maioria das distribuições Linux é atualizada regularmente, mas o CentOS não. A vantagem disso é que, após colocar seu servidor em funcionamento, ele estará em boas condições por muito tempo sem precisar atualizar regularmente para uma versão mais recente.

Quanto aos contras, você terá dificuldades para executar pacotes mais novos e usar as tecnologias mais recentes. Se estiver procurando usar a tecnologia mais recente, o CentOS pode não ser o ideal para você.

### Debian

Assim como o CentOS, o Debian também não é atualizado com tanta frequência. O Debian também é uma distribuição comunitária, o que significa que as novas versões do Debian são lançadas somente quando a comunidade chega a um consenso. Portanto, cada versão do Debian é completamente testada e confiável. Há apenas um tipo de Debian disponível para download e instalação, que funciona tanto no servidor quanto no desktop.

### Ubuntu

O Ubuntu é a distribuição Linux mais popular para desktop. Talvez você queira usar o Ubuntu em seu servidor se quiser ter a vantagem de estar familiarizado com ele!

Aqui estão algumas outras que você talvez queira considerar,

### [Windows Server 2019](https://www.microsoft.com/en-us/cloud-platform/windows-server)

### [Mac OS X Server](https://www.apple.com/lae/macos/server/)

### [Solaris](https://www.google.com/search?q=solaris+server)

## Arquitetura de microsserviços

## Microsserviços

No entanto, a maioria dos sites modernos não se baseia nesse estilo de hospedagem na Web, ou seja, o código de todas as funcionalidades do site inteiro não existe em um único servidor. Em vez disso, os sites são hospedados em máquinas virtuais (pense nelas como computadores dentro de computadores). As máquinas virtuais podem nos permitir ter várias "máquinas" em um servidor, cada uma com sistemas operacionais diferentes. Portanto, o site é hospedado em um servidor, e esse servidor delega tarefas a outros servidores.

<figure align="center">
    <img src="./assets/conventional-client-server-connection.png" alt="Convencional" />
    <figcaption>
      <code>Convencional</code>
    </figcaption>
</figure>

Na imagem acima, um cliente se conecta a um servidor, que cuida de tudo e responde.

<figure align="center">
    <img src="./assets/microservice-client-connection.png" alt="Arquitetura de microsserviços" />
    <figcaption>
      <code>Arquitetura de microsserviços</code>
    </figcaption>
</figure>

Na arquitetura de microsserviços, no entanto, existem muitas máquinas virtuais em um servidor físico, e cada uma delas tem um trabalho separado. Por exemplo, uma delas pode ser a "interface" com a qual o usuário interage, e pode se conectar a outra para o banco de dados.

Segundo o [site Microservices](https://microservices.io/),

"Microsserviços - também conhecidos como arquitetura de microsserviços - é um estilo arquitetônico que estrutura um aplicativo como uma coleção de serviços que são

- Altamente manuteníveis e testáveis
- Pouco acoplados
- Podem ser implantados de forma independente
- Organizados em torno de recursos comerciais.

A arquitetura de microsserviços permite a entrega/implantação contínua de aplicativos grandes e complexos. Ela também permite que uma organização desenvolva sua pilha de tecnologia."

## Programação Back-End

Veremos quais são as funções básicas de um engenheiro Back-End

### O que fazem os engenheiros de back-end?

Os engenheiros de back-end programam servidores para processar solicitações de usuários e responder corretamente com o recurso solicitado. Eles também escrevem códigos para processar e armazenar dados do usuário.

### Armazenamento e fornecimento eficientes de informações

Os dados relativos a um site precisam ser armazenados em um banco de dados e entregues mediante solicitação. Considere o banco de dados de itens da Amazon; se o sistema de consulta do banco de dados se tornar ineficiente em algum momento, eles perderão clientes.

### Experiência de usuário personalizada

Os servidores geralmente armazenam e usam informações sobre os clientes para oferecer uma experiência de usuário personalizada. Por exemplo, muitos sites armazenam detalhes de cartões de crédito para que as informações não precisem ser inseridas novamente.

### Acesso controlado ao conteúdo

A programação de back-end envolve a restrição adequada do acesso às informações. Por exemplo, em um aplicativo de carona, como o Uber, um usuário não deve poder visualizar o histórico de viagens de carro de outro.

### Armazenar informações de sessão/estado

Os engenheiros de back-end também lidam com a sessão do usuário, ou seja, uma cadeia de caracteres é associada a cada usuário que visita o site, e os dados associados à cadeia de caracteres, como e-mails ou histórico de pedidos, são salvos e exibidos quando o usuário visita novamente o site. Outro exemplo é salvar o estado de um jogo simples para que o usuário possa acessar o site novamente e continuar de onde parou.

### Notificações

Os servidores podem ser programados para enviar notificações gerais ou específicas ao usuário por meio do próprio site, ou por e-mail, SMS, mensagens instantâneas, conversas por vídeo ou outros serviços de comunicação.

Alguns exemplos incluem:

- O Facebook e o Twitter enviam e-mails e mensagens SMS para notificá-lo sobre novas comunicações.

- A Amazon envia regularmente e-mails de produtos que sugerem produtos semelhantes aos já comprados ou visualizados, nos quais você pode estar interessado.

- Um servidor da Web pode enviar mensagens de aviso aos administradores do site, alertando-os sobre pouca memória no servidor ou atividade suspeita do usuário.

### Análise de dados

Um site pode coletar muitos dados sobre os usuários: o que eles pesquisam, o que compram, o que recomendam, quanto tempo permanecem em cada página. A programação no lado do servidor pode ser usada para refinar as respostas com base em uma análise desses dados.

Por exemplo, a Amazon e o Google anunciam produtos com base em pesquisas (e compras) anteriores.

### Alguns recursos gratuitos para aprender desenvolvimento Back-End:

- [Rest API Básico](https://oliveira-michel.github.io/artigos/2020/02/10/rest-api-basico---guia-visual.htm)

- [Curso Back End Development and APIs](https://www.freecodecamp.org/learn/back-end-development-and-apis/)

- [Back-end Roadmap](https://roadmap.sh/backend)
