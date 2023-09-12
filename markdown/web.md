# O que é a Web?

A Web, em termos simples, é uma rede espalhada por todo o mundo que conecta uma infinidade de dispositivos e permite que eles se comuniquem entre si. Os sites na Internet são hospedados em dispositivos chamados de servidores e, quando você interage com uma página da Web na Internet, o que está fazendo essencialmente é trocar dados com o servidor no qual o site está hospedado. O dispositivo que você está usando para acessar a página da Web é chamado de cliente no contexto da Web. Em resumo, a Web permite a troca de dados entre clientes e servidores por meio de vários mecanismos elaborados.

## Camadas de rede

Como a Web é uma rede imensamente complexa e difundida, as máquinas dentro da rede são normalmente divididas em camadas abstratas, cada uma das quais executa uma tarefa específica que ajuda no processo geral de comunicação. As camadas são enumeradas abaixo:

1. Camada de aplicativos
2. Camada de transporte
3. Camada de rede
4. Camada de enlace de dados
5. Camada física

Cada camada é construída sobre a camada anterior, com protocolos que implementam funcionalidades específicas envolvidas no processo de troca de dados.

## Camada física

A camada física de uma máquina refere-se à fiação física e aos circuitos que tornam a máquina disponível na rede.

## Camada de link de dados

A camada de link de dados é responsável pela transmissão de dados de uma determinada máquina para um dispositivo ou máquina que esteja exatamente a um link de distância.

## Camada de rede

A camada de rede é responsável por conectar duas máquinas quaisquer na Internet. Ela fornece conectividade global e permite que os sistemas finais se comuniquem entre si em grande escala, além do que a camada de enlace de dados tem a oferecer.

## Camada de transporte

A camada de transporte é responsável por conectar os aplicativos na Internet. Ela multiplexara os dados provenientes de uma única fonte e os transmite ao aplicativo a que se destinam. A finalidade básica da camada de transporte no contexto da Web é fornecer comunicação de processo para processo; ela permite que dois processos individuais na mesma máquina ou em máquinas separadas enviem mensagens entre si. Para fazer isso, ela usa soquetes, que são essencialmente apenas o gateway para um processo. Em outras palavras, os soquetes são os meios pelos quais as mensagens são recebidas e enviadas por um processo.

## Camada de aplicativos

A camada de aplicativos é responsável pela comunicação entre processos na Internet. É a camada mais alta da hierarquia, e o próprio aplicativo é construído sobre ela. A camada de aplicativo fornece uma interface de comunicação e serviços de usuário final ao aplicativo para sua comunicação com processos individuais.

## Como a Web permite a comunicação entre dispositivos

### Interruptores para conectar dispositivos

A comunicação é bastante intuitiva. Os clientes enviam mensagens aos servidores solicitando dados, e os servidores respondem com os dados necessários, mas como esses dados são transferidos? A resposta requer primeiro a definição da estrutura da Internet.

A Internet é composta por dispositivos conhecidos como switches que facilitam a conexão de cada dispositivo a todos os outros dispositivos da rede. Os dispositivos em si são chamados de sistemas finais, o que basicamente é apenas um termo sofisticado para o computador que você está usando para acessar esta página da Web neste momento!

Os sistemas finais são conectados aos switches por meio de links e todos os switches são, por sua vez, conectados mutualmente, garantindo assim que cada sistema final na Internet esteja implicitamente conectado a todos os outros sistemas finais.

Além de conectar os sistemas finais entre si, os switches facilitam a comunicação entre dois sistemas finais, encaminhando os pacotes pelo caminho que eles sabem que existe entre a origem e o destino do pacote. Basicamente, os switches armazenam caminhos predeterminados entre os sistemas finais e encaminham os pacotes entre eles.

### Roteadores

Um termo comumente ouvido quando se trata de redes são roteadores. Os roteadores têm a mesma função que os switches em si, pois também conectam os sistemas finais ao restante da Web. Entretanto, os roteadores são, na verdade, muito diferentes dos switches, pois têm o recurso adicional de permitir pesquisas de endereços de destino e determinar o caminho mais curto ou menos movimentado da origem de um pacote até o seu destino. Os roteadores são, portanto, uma versão mais poderosa dos switches e a Internet é composta por uma mistura de roteadores e switches que facilitam a transferência de dados entre sistemas finais.

### Pacotes de dados

Agora que já estabelecemos como a Internet é estruturada para garantir a conectividade, vamos falar sobre como os dados são de fato transportados pela rede. Isso é feito dividindo os dados, que são apenas um conjunto de bits, que precisam ser transferidos em vários pedaços menores de bits conhecidos como pacotes e, em seguida, enviando cada pacote ao seu destino de forma independente. O motivo disso é que nem sempre é possível enviar grandes quantidades de dados em um único pacote porque é muito provável que os dados em pacotes grandes sejam corrompidos no caminho da origem até o destino devido a uma inversão de bits durante as transmissões. Portanto, é mais eficiente e confiável enviar vários pacotes menores.

### TCP

Como é importante que clientes e servidores em toda a rede consigam entender a mesma linguagem, há determinados protocolos que determinam a comunicação entre dispositivos na Internet. O protocolo usado principalmente para a comunicação entre um aplicativo da Web e um navegador é chamado de TCP (Transmission Control Protocol). O TCP é um protocolo de camada de transporte que assume a responsabilidade pela transmissão de dados e garante a transferência confiável de dados entre clientes e servidores na Web. O TCP faz isso acrescentando informações adicionais aos pacotes de dados que permitem a autenticação dos pacotes e a troca de mensagens de confirmação entre o cliente e o servidor para confirmar as transmissões de dados.

O protocolo TCP começa com um 3-way handshake. O handshake permite que ambas as extremidades (servidor e cliente) iniciem e mantenham várias conexões TCP ao mesmo tempo.

### HTTP E HTTPS

Acabamos de aprender que clientes e servidores se comunicam entre si iniciando conexões TCP e enviando mensagens mutualmente. Agora, veremos exatamente como essas mensagens são estruturadas.

O HyperText Transfer Protocol, comumente chamado de HTTP, é um protocolo de camada de aplicativo que determina como as mensagens que um cliente e um servidor trocam na Web são estruturadas e como elas são trocadas. O programa cliente e o programa servidor conversam entre si por meio da troca de mensagens HTTP, e a vantagem do HTTP é que ele garante que as mensagens sejam entregues intactas e no prazo.

Isso pode parecer ambíguo, mas a ideia de alto nível é que o HTTP é construído sobre o TCP e a criação de um servidor HTTP para o seu aplicativo da Web significa basicamente que você está criando um servidor com o qual os clientes criam conexões TCP para transferência confiável de dados. Em termos simples, o HTTP é o meio pelo qual você pode garantir que seu aplicativo esteja usando TCP para transmitir mensagens dos clientes para o servidor e vice-versa. Então, basicamente, quando você digita um URL no navegador, o que acontece é que um comando HTTP é enviado ao servidor que hospeda o aplicativo para buscar e transmitir a página da Web solicitada por meio do TCP.

A estrutura mais comum de URLs é **https**://www.dev.to/. Então, o que significa HTTPS? HTTPS é um acrônimo para HyperText Transfer Protocol Secure (Protocolo de Transferência de Hipertexto Seguro) e é basicamente a versão segura do HTTP. Isso significa que as comunicações entre o navegador e o servidor de hospedagem são criptografadas para que nenhum terceiro na rede possa acessar informações que não devam ser compartilhadas.

### Portas

Até agora, discutimos o protocolo da camada de transporte e o protocolo da camada de aplicativo que garantem a comunicação eficiente entre os sistemas finais na Web. Mas, para onde exatamente as mensagens desses protocolos permitem que os sistemas finais vão? As portas são os pontos finais da comunicação entre clientes e servidores. Ou seja, as portas são onde as mensagens da rede chegam em um sistema final. Discutimos brevemente os soquetes anteriormente e falamos sobre como eles são os gateways para os processos; os soquetes são abertos nas portas para permitir que os processos enviem e recebam mensagens. As portas são designadas por números, e todas as portas abaixo de 1024 são associadas a um protocolo específico por padrão. O número da porta para HTTP, por exemplo, é 80, e isso significa que todas as mensagens que você envia ou recebe na Web entram e saem do computador em um soquete na porta 80. As portas acima de 1024 são portas abertas disponíveis para os programadores usarem em qualquer processo que queiram se comunicar com uma rede. Eles podem criar soquetes nessas portas e definir a estrutura e o tipo de mensagens que esse soquete pode atender por meio da programação de soquetes. A programação de soquetes é um aspecto das redes de computadores que está além do escopo desta discussão, mas é uma habilidade altamente útil a ser adquirida e, sem dúvida, deve ser analisada se você estiver interessado na área.

## Como os dados encontram seu caminho?

Uma visão geral de alto nível de como os pacotes determinam e percorrem o caminho de sua origem até o destino.

### Protocolo da Internet e endereços IP

O Protocolo de Internet, mais comumente conhecido como IP, é um protocolo de camada de rede responsável por atribuir endereços a dispositivos para dar a eles identidades exclusivas que os tornam acessíveis e detectáveis. Cada dispositivo na Internet tem um endereço IP exclusivo que outros dispositivos usam para se conectar a ele. Você pode encontrar seu próprio endereço IP usando a seguinte instrução de linha de comando:

```bash
ifconfig
```

O `ifconfig` é o comando que funciona no Linux. O comando `ipconfig` faz o mesmo no Windows, portanto, experimente-o se estiver executando o comando em um sistema Windows local.

Isso significa que, quando o site estiver ativo em um servidor, os usuários poderão se conectar a ele por meio do endereço IP do servidor e receber dados. Você deve estar se perguntando agora onde entra o endereço IP se você acessa sites por meio de URLs. Bem, é aí que entra o DNS. Os roteadores da Web não armazenam URLs de todos os sites hospedados na Internet; eles só conhecem os endereços IP e encaminham os pacotes para seus destinos com base neles. Os usuários, no entanto, só conhecem as URLs, ou nomes de domínio, dos sites que estão visitando. Para preencher essa lacuna de informações, a Internet faz uso de **servidores DNS**, que servem como intermediários de tradução entre os sistemas finais e os roteadores, que servem para encaminhar os pacotes de dados provenientes deles para seus destinos. O processo de fazer uma pesquisa de um nome de domínio em um servidor DNS e obter o endereço IP correspondente é chamado de **resolução de DNS**.

Para saber mais como a Web funciona recomendo:

- [Como a Web funciona da Mozilla docs](https://developer.mozilla.org/pt-BR/docs/Learn/Getting_started_with_the_web/How_the_Web_works)
- [Computer Networking: A Top-Down Approch](https://gaia.cs.umass.edu/kurose_ross/index.php)
