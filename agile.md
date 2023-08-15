# DESENVOLVIMENTO ÁGIL DE SOFTWARE

Áte meados dos anos 1990, os processos de desenvolvimento de software prescritivos exigiam um detalhado planenjamento de projeto, um sistema de garantia da qualidade estabelecido, a aplicação de métodos de análise e projeto, tal como a modelagem de caso de uso, apoiados for ferramentas “CASE” ([Computer-Aided Software Engineering](https://pt.wikipedia.org/wiki/Ferramenta_CASE)) e controlados por um displinado e formal modelo de processo de desenvolvimento de software.

Essa visão era influenciada por equipes de engenheiros de software engajados em sistemas de software complexos que exigiam um esforço significativo de planejamento, projeto e documentação de sistema.

## MAS ESSE ESFORÇO JUSTIFICA-SE EM SISTEMAS DE MENOR GRAU DE COMPLEXIDADE?

Em 2001, um grupo de 17 metodologistas e outros profissionais da indústria de software se reuiniram no estado norte-americano de Utah para discutir e propor uma alternativa aos processos prescetivos dominates á época. Ao final da reunião, esse grupo lançou as bases para um novo conceito de processo de software, as quais foram registradas em um documento que chamaram de [Manisfesto para o Desenvolvimento Ágil de Software](https://agilemanifesto.org/iso/ptbr/manifesto.html), que inclui quatro valores:

- Indivíduos e interações, mais do que processos e ferramentas.
- Software em funcionamento, mais do que documentação abragente.
- Colaboração com o cliente, mais do que negociação de contratos.
- Resposta a mudanças, mais do que seguir um plano.

A maioria dos métodos ágeis de desenvolvimento de software têm foco no próprio software, em vez de em seu projeto e documentação, assumem um fluxo de processo iterativo e incremental para entegra def software e apoiam aplicações de negócios nas quais os requisitos possuem alta volatilidade durante o desenvolvimento.

Um projeto de software ágil busca a capacidade de implantar um nova versão a cada incremento, enfatizando o software funcional como uma medida primária de progresso.

A entrega de software rápida permite que os clientes validem de form imediata o incremento, indicando possíveis erros ou alterações de requisitos, bem como descrevendo novos requisitos para as próximas iterações.

<aside>
💡 Métodos ágeis também enfatizam comunicações em tempo real entre equipe de desenvolvimento e clientes.

</aside>

## EXTREME PROGRAMMING - XP

A Extreme Programming, ou simplesmente XP, é considerada um processo de desnvolvimento de software ágil, tendo sido proposta por **Kent Beck** em 1999.  A XP adota valores que servem como critérios que norteiam as partes interessadas de um projeto, incluindo: comunicação, simplicidade, feedback, coragem e respeito.

<aside>
💡 **Kent Beck** é um engenheiro de software americano criador do Extreme Programming e Test Driven Development. Beck foi um dos 17 signatários originais do Agile Manifesto (Manifesto ágil) em 2001, que trata-se de uma declaração de princípios que fundamentam o desenvolvimento ágil de software.

</aside>

Vejamos uma breve descrição de cada valor:

- **COMUNICAÇÃO**
    
    A **comunicação** objetiva construir um entendimento dos requisitos com o mínimo de documentação formal eo máximo de interação entre equipe de desenvolvimento e clientes.
    
- **SIMPLICIDADE**
    
    A **simplicidade** sugere que a equipe adote soluções mais simples, criando um ambiente onde o custo de mudanças no futuro seja baixo.
    
- **FEEDBACK**
    
    O **feedback** permite que os programadores também tenham feedback quando da execução dos testes e quando da validação funcional por parte dos clientes, permitindo a correção de erros e alterações nos requisitos.
    
- **CORAGEM**
    
    A **coragem** é necessária para lidar com o risco de erro, que deve ser considerado natural e se traduz em confiança nos seus mecanismos de prevenção e proteção
    
- **RESPEITO**
    
    O **respeito** éo valor mais básico e que dá sustentação a todos os demais, sem o qual não haverá nada que possa salvar um projeto de software do insucesso.
    

Assim como um segundo nível de abstração, a XP possui cinco princípios que servem para ajudar a equipe de software na escolha de alternaltivas de solução de problemas durante o projeto. Os princípios incluem: feedback rápido, assumir simplicidade, mudanças incremental, abraçando mudanças e trabalho de qualidade.

A XP possui [doze práticas](https://www.tutorialspoint.com/extreme_programming/extreme_programming_practices.htm) que se enquadram nos valores e princípios descritos na table a seguir:

| Prática | Descrição |
| --- | --- |
| Jogo de Planejamento | Os requisitos são registados em caroes, ou fichas, denominados “história de usuários”, sendo a descrição e a priorização realizadas pelos clientes. A equipe de desenvolvimento estima a duração de cada release. |
| Pequenas Releases | Cada release dever ser a menor possível, incluindo os requisitos mais para o negócio. As releases frequentes permitem um maior feedback para lcientes e equipe de desnevolvimento, facilitando o aprendizado ea correção dos de feitos do software. |
| Metáfora | Facilita a comunicação com o cliente, entendendo a sua realidade, isto é, procura traduzir as palavras do úsuario para o significado que ele espera dentro do projeto. Como exemplo, a metáfoa “Estoque 4.0” para um subsistema de gestão virtual dos estoques no contexto de um sistema de logística. |
| Projeto Simples | Projeto simples inclui projetar somente as histórias de usuários, ou funcionalidades, previamente definidas, bem como se concentrar em soluções de projeto simples e bem estruturadas, tal como menor número possível de classes e métodos. |
| Cliente no local de Trabalho | Incluir um cliente na equipe de desenvolvimento para responder perguntas, ou alquém da parte do cliente com conhecimento do negócio. O cliente é protagonista em um projeto XP. |
| Semana de 40 horas | Estimula a busca de um ritmo sustentado de trabalho, tal como 40 horas semanais. Horas extras são permitidass quando trouxeram produtividade para a execução do projeto, devendo estar restringidas a duas semanas seguidas. |
| Programação Pareada | Todo código deve ser gerado por dois programadores trabalhando em um único computador. O programador codifica e o parceiro revisa e sugere. Dessa forma, o programa sempre é revisto por duas pessoas, evitando e diminuindo, assim, a possibilidade de defeitos. Com isso, busca-se sempre a evolução da equipe, melhorando a qualidade do código fonte gerado. |
| Propriedade Coletiva | O código fonte pode ser alterado por qualquer membro da equipe de desenvolvimento, de modo que esta detém a propriedade do código. O objetivo com isso é fazer a equipe conhecer todas as partes do sistema. Os testes de unidade e integração garantem a estabilidade dessa prática. |
| Padronização do Código | A prática da propriedade coletiva de código exige o estabelecimento de regras para programar a serem seguidas por todos. O objetivo é facilitar o entendimento do código. |
| Desenvolvimento Orientado a Testes | Um framework automatizado é sugerido para a escrita dos testes unitários, antes que determinada funcionalidade seja implementada, técnica denominada “test first”. Os testes de integração também são realizados sistematicamente. Os testes de validação são especificados pelos clientes a partir das histórias de usuários. |
| Refatoração | A refatoração é um processo de modificar a estrutura interna do software sem alterar o seu comportamento externo, permitindo a melhoria contínua da programação com o mínimo de introdução de erros e mantendo a compatibilidade com o código já existente. |
| Integração Contínua | O sistema deverá ser integrado sempre que produzir uma nova funcionalidade. Essa continuidade permite a detecção imediata de erros de integração, evitando problemas de compatibilidade e interface. |

## PROCESSO XP

A XP adota o paradigma orientado a objetos, aplicando seus valores, princípios e práticas em um processo com quatro atividades metodológicas: planejamento, projeto, codificação e testes. A Figura ilustra essas atividades metodológicas propostas por Pressman (2016).

![Fonte: [https://blog.grancursosonline.com.br/t-i-em-foco-vamos-entender-a-engenharia-de-software](https://blog.grancursosonline.com.br/t-i-em-foco-vamos-entender-a-engenharia-de-software/)](https://blog-static.infra.grancursosonline.com.br/wp-content/uploads/2020/05/20154428/PU.png)

Fonte: [https://blog.grancursosonline.com.br/t-i-em-foco-vamos-entender-a-engenharia-de-software](https://blog.grancursosonline.com.br/t-i-em-foco-vamos-entender-a-engenharia-de-software/)

## O PROCESSO ÁGIL EXTREME PROGRAMMING (XP)

Veja mais detalhes sobre o processo [XP e suas etapas](https://en.wikipedia.org/wiki/Extreme_programming#:~:text=XP%20describes%20four%20basic%20activities,testing%2C%20listening%2C%20and%20designing.):

### PLANEJAMENTO

A primeira atividade é ouvir o cliente, realizando um levantamento de requisitos que possibilite o entendimento do escopo do problema. Essa atividade gera um conjunto de histórias de usuários, escritas pelos clientes, juntamente à equipe de desenvolvimento que, de forma simplificada, compõem as necessidades destes. Cada história é colocada em uma ficha ou cartão, sendo priorizadas pelos clientes em função da agregação de valor ao negócio. Após a elaboração dos cartões, a equipe de desenvolvimento avalia as histórias e as divide em tarefas, estimando o esforço e os recursos necessários à implementação. Caso o esforço exija mais do que três semanas de desenvolvimento, é preciso que o cliente fatore a história analisada em histórias menores. As histórias que farão parte de cada incremento são selecionadas de forma conjunta por equipe de desenvolvimento e clientes, considerando os seguintes critérios: as histórias de maior risco serão priorizadas, em seguida, as que agregam maior valor ao negócio. Após a implantação da primeira versão do software, é calculada a velocidade do projeto que é o número de histórias de usuários implementadas na primeira release. Essa velocidade serve para avaliar se o compromisso firmado está compatível e auxiliar na estimativa de prazos das versões seguintes.

### PROJETO

Aqui temos a máxima: “Não complique!” A XP estimula a modelagem com cartões [CRC (Classe-Responsabilidade-Colaborador)](https://www.inf.ufpr.br/silvia/ES/requisitos/pdf/CRCAl.pdf), que permitem identificar as classes orientadas a objetos relevantes para a iteração corrente do software. Os cartões CRC são os únicos artefatos de projeto gerados durante o processo XP. Caso algum requisito necessite ser validado em função de sua complexidade, é recomendada a prototipação, como solução pontual, a fim de reduzir o risco dessa especificação no projeto. A refatoração também é estimulada, tendo em vista uma melhor organização do código interno do software sem impactar a sua funcionalidade, o que minimiza as possibilidades de erros.

### CODIFICAÇÃO

A XP sugere que dois programadores trabalhem juntos em um mesmo computador a fim de criarem código para uma história, fornecendo um mecanismo de solução de problemas emtempo real, cabendo a máxima de que duas cabeças pensam melhor que uma, além de garantir a qualidade do código, pois a sua revisão é imediata. Ambos desenvolvedores têm papéis distintos, tal como, uma pessoa implementa o código, outro verifica os padrões de codificação. Quando da conclusão da codificação, esse código é integrado aos demais. A estratégia de integração contínua possibilita a identificação precoce de erros.

### TESTE

Como afirmado anteriormente, a equipe de desenvolvimento avalia cada história e a divide em tarefas, que representa uma característica discreta do sistema. Antes da codificação, a equipe de desenvolvimento elabora os testes de unidades a serem aplicados nas tarefas. A automação desses testes é fortemente recomendada, o que estimula a realização dos **testes de regressão** toda vez que o código for alterado. A estratégia de integração contínua está vinculada aos testes de integração, realizados de forma sistemática a cada novo código gerado a ser integrado, o que permite lançar alertas logo no início, evitando propagações indesejadas. Os testes de aceitação são elaborados pelos clientes com base nas histórias de usuários especificadas anteriormente e que serão implementadas na próxima release. O foco está nas funcionalidades externas visíveis aos clientes. Em outras palavras, os testes verificam se o sistema atende às reais necessidades dos usuários.

<aside>
💡 Teste de Regressão é a reexecução de algum subconjunto de testes que já foram conduzidos para garantir que as modificações não propagaram efeitos colaterais indesejáveis.

</aside>

## RESUMINDO

Compreendemos as principais características das metodologias ágeis e, em particular, da Extreme Programming – XP. A partir do questionamento do excesso de formalismo dos processos prescritivos dominantes em meados da década de 1990, foi redigido o Manifesto para o Desenvolvimento Ágil de Software, com quatro valores:

1. Indíviduos e interações, mais do que processos e ferramentas
2. Software em fucionamento, mais do que documentação abrangente
3. Colaboração com o cliente mais do que negociação de contratos
4. Resposta a mudanças, mais do que sequir um plano.

A maioria dos métodos ágeis de desenvolvimento de software tem como foco o software, em vez de formalismos de projeto e documentação, assumem um fluxo de processo iterativo e incremental e apoiam aplicações com requisitos altamente voláteis durante o desenvolvimento. A XP possui um conjunto de valores, princípios e práticas como base aos que assumem a metodologia XP. O processo XP possui quatro atividades metodológicas:

1. O planejamento está fundamentado nas histórias de usuários.
2. O projeto tem um mínimo de formalismo em função de gerar um único tipo de artefato, os cartões CRC, sendo sugeridas a prototipação e refatoração nessa etapa.
3. A codificação sugere o trabalho em par de programadores alocados em um único computador, permitindo uma revisão de código em tempo real.
4. Os testes têm uma característica peculiar, são escritos antes da codificação e aplicados de forma sistemática em ambiente automatizado; na sequência, temos os testes unitários, de integração e aceitação.

# METODOLOGIA ÁGIL SCRUM

O termo Scrum, cujo significado se reporta a um método de reinício de jogada no esporte rugby, foi concebido fora da indústria de software como um estilo de gerenciamento de produtos. A partir de conceitos relacionados com este termo, Jeffrey Sutherland e Ken Schwaber, no início da década de 1990, desenvolveram o denominado [Processo de Desenvolvimento SCRUM](https://scrumguides.org/docs/scrumguide/v1/Scrum-Guide-Portuguese-BR.pdf), publicado em 1995. Desde então, esse processo passou a ser aplicado no desenvolvimento de software em todo o mundo.

A metodologia Scrum sofreu, posteriormente, desenvolvimentos adicionais abrangendo o gerenciamento de projetos, não necessariamente relacionados a projetos de software. Entretanto, o Scrum é empregado de forma sistemática no gerenciamento de projetos de desenvolvimento de software, utilizando-se dos valores e princípios do manifesto ágil junto aos conceitos definidos em seu framework. Tal metodologia tem sua aplicação recomendada principalmente quando, em função da complexidade do projeto, não é possível estabelecer todos os requisitos nas fases iniciais do planejamento.

O Scrum possui três pilares fundamentais:

### TRANSPARÊNCIA

A equipe de desenvolvedores deve dispor de um mesmo entendimento dos aspectos significativos do processo com base num padrão, tal como uma linguagem comum referente ao processo.

### INSPEÇÃO

A máxima é “inspeção constante de todos os artefatos”, ou seja, a equipe de desenvolvedores deve sistematicamente inspecionar os artefatos Scrum e o progresso desses, de modo a detectar não conformidades; entretanto, a inspeção não deve ser um entrave à própria execução das tarefas.

### ADAPTAÇÃO

Inclui a adaptação do produto software, que pode sofrer mudanças em função da volatilidade dos requisitos e/ou mudanças da tecnologia, bem como a adaptação do processo em função de alguma falha; os ajustes devem ser realizados o mais rápido possível de forma a evitar a propagação de desvios.

## PAPÉIS NO SCRUM

Uma equipe Scrum é composta pelo **Product Owner** (Dono de Produto), **Scrum Master** e **Scrum team**. 

O Dono do Produto, ou **Product Owner**, faz o papel do cliente, determinando os requisitos e funcionalidades que deverão ser entregues, incluindo as suas mudanças, além de manter e comunicar às partes interessadas uma visão clara do projeto e estar disponível para esclarecer eventuais questionamentos.

O **Scrum Master** é o responsável por garantir que as regras do método Scrum estejam sendo seguidas, desempenhando, também, funções de um facilitador na resolução de problemas ou nas interferências externas. O Scrum Master é o mantenedor das regras e dos procedimentos do Scrum durante a execução do projeto, auxiliando a todos da equipe aentenderem seus papéis no processo.

A **Scrum Team**, ou equipe scrum, é multidisciplinar, incluindo os especialistas necessários para desenvolver o produto de forma a não depender de membros externos, tais como programadores, administradores de banco de dados, web designers etc.

## ARTEFATOS SCRUM

Os Artefatos Scrum são concebidos com a finalidade de garantir que as equipes Scrum sejam bem-sucedidas na geração de um incremento. Vejamos os principais artefatos:

- **PRODUCT BACKLOG**
    
    Lista incluindo todos os requisitos ou funcionalidades levantados para o produto, estando sob responsabilidade do product owner; no início do desenvolvimento, são estabelecidos os requisitos inicialmente conhecidos e mais bem compreendidos, evoluindo à medida que o produto e o ambiente em que ele será utilizado também evoluem, ou seja, o product backlog é dinâmico.
    
- **SPRINT BACKLOG**
    
    Subconjunto de requisitos do product backlog selecionados para determinada iteração, denominada de Sprint; inclui, também, o plano para entregar um incremento do produto e atingir a meta da Sprint, isto é, estabelece quais funcionalidades estarão na próxima versão e o trabalho necessário para a referida entrega, também denominada de “Done”. A equipe de desenvolvimento é a responsável, quando necessário, pelas modificações no sprint backlog.
    

## EVENTOS SCRUM

Os eventos permitem otimizar a organização das inspeções e iterações, sendo por meio deles que os conceitos descritos são postos em prática de uma forma ágil e produtiva. Oseventos permitem, também, a minimização da necessidade de reuniões não contempladas no Scrum. Os eventos possuem um tempo pré-definido com uma duração máxima estipulada denominada de timebox, o que garante uma quantidade apropriada de tempo no planejamento. Vejamos os eventos Scrum.

- **SPRINT**
    
    O **Sprint** corresponde a uma unidade de iteração em Scrum, tendo uma duração (timebox) em torno de um mês, ao final do qual é gerado um incremento de produto em condições de uso pelos usuários finais. Ele possui duração consistente durante todo o desenvolvimento. Cada Sprint inclui as atividades típicas de um processo de desenvolvimento de software, tais como, levantamento de requisitos, análise, projeto etc.
    
- **REUNIÃO DE PLANEJAMENTO DO SPRINT**
    
    A **Reunião de planejamento do Sprint** permite planejar o trabalho a ser realizado em dado Sprint, sendo esse plano resultado de trabalho colaborativo da equipe scrum. A reunião é limitada a 8h para um Sprint de um mês, dividida em duas partes que, respectivamente, buscam respostas às seguintes perguntas: O que será entregue no incremento resultante do Sprint planejado? Como será alcançado o trabalho necessário para entregar o incremento?
    
- **REUNIÃO DIÁRIA DO SPRINT (DAILY SCRUM)**
    
    A **Reunião diária do Sprint (Daily Scrum)**, como diz o próprio nome, é um evento diário limitado a 15 minutos, no qual a equipe scrum ajusta as atividades e cria um plano para as próximas 24 horas. Isso é feito inspecionando o trabalho desde a última daily scrum e pela previsão do trabalho a ser feito antes da próxima reunião diária.
    
- **REVISÃO DO SPRINT**
    
    A **Revisão do Sprint** é realizada no final do sprint para inspecionar o incremento e adaptar, se necessário, o product backlog. Essa é uma reunião informal, e a apresentação do incremento destina-se a obter feedback e a fomentar a colaboração na equipe, não devendo ultrapassar quatro horas para sprints de um mês.
    
- **RETROSPECTIVA DO SPRINT**
    
    A **Retrospectiva do Sprint** é uma oportunidade formal para a equipe scrum inspecionar ecriar um plano de melhorias a ser seguido durante o próximo Sprint, sendo uma reunião limitada a 3h para sprints de um mês. Os objetivos dessa retrospectiva incluem verificar como correu o último sprint no que diz respeito às pessoas, relações, processos e ferramentas; identificar e ordenar os itens que correram de forma satisfatória e potenciais ajustes; e criar um plano para implementar melhorias no modo como a equipe scrum faz o seu trabalho.
    

## FLUXO DE PROCESSO DO SCRUM

![Fonte : [Lakeworks](https://commons.wikimedia.org/w/index.php?title=User:Lakeworks&action=edit&redlink=1) - Wikimedia Commons](https://upload.wikimedia.org/wikipedia/commons/5/58/Scrum_process.svg)

Fonte : [Lakeworks](https://commons.wikimedia.org/w/index.php?title=User:Lakeworks&action=edit&redlink=1) - Wikimedia Commons

O fluxo de processo Scrum, ilustrado na Figura. Os requisitos que definem as funcionalidades a serem entregues são agrupadas pelo product owner no product backlog.

- **REUNIÃO DE PLANEJAMENTO DE SPRINT**
    
    No início de cada sprint, é realizada a reunião de planejamento de sprint, com a participação do product owner, scrum master e scrum teams, a fim de que sejam priorizados e selecionados os requisitos do product backlog que serão implementados no sprint em planejamento. Esses requisitos são decompostos em tarefas, para cada tarefa é alocado um responsável que realiza a sua estimativa. Ao final da reunião, essas tarefas passam a compor o sprint backlog.
    
- **DESENVOLVIMENTO DO SPRINT**
    
    Na sequência, a scrum team inicia o desenvolvimento do sprint de acordo com o planejado. No início de cada dia do sprint, a daily scrum permite o compartilhamento, entre os membros da equipe, do que foi concluído no dia anterior e quais as prioridades do dia atual. O scrum master conduz essa reunião.
    
- **REUNIÃO DE REVISÃO DE SPRINT**
    
    Ao final do sprint, ocorre a reunião de revisão de sprint que permite a geração de um product backlog revisto, sendo definidas as prováveis exigências para o próximo sprint; os requisitos podem ser ajustados em função de sua volatilidade ou de novas tecnologias. Participam dessa reunião o project owner, o scrum master e a scrum team.
    
- **REUNIÃO DE RETROSPECTIVA DO SPRINT**
    
    A última atividade da iteração sprint é a reunião de retrospectiva do sprint com a finalidade de levantar as lições aprendidas nessa iteração, incluindo os pontos positivos e o que precisa ser aprimorado nas próximas reuniões.
    

## PROCESSO UNIFICADO ÁGIL - AUP

O Processo Unificado Ágil, ou [Agile Unified Process](https://en.wikipedia.org/wiki/Agile_unified_process) (AUP), é uma versão simplificada do [Rational Unified Process](https://pt.wikipedia.org/wiki/IBM_Rational_Unified_Process) (RUP). O AUP baseia-se nos seguintes princípios:

- A equipe sabe o que está fazendo. O AUP disponibiliza links de acesso à documentação com muitos detalhes, isso para os que estiverem interessados.
- Simplicidade. Tudo é descrito de forma concisa maximizando a simplicidade.
- Agilidade. O AUP está em conformidade com os valores e princípios da metodologia ágil de desenvolvimento de software.
- Concentre-se em atividades de alto valor. O foco está nas atividades que realmente agregam valor ao cliente.
- Independência de ferramenta. A recomendação é utilizar as ferramentas mais adequadas, que muitas vezes são ferramentas simples.
- Customização. O produto AUP é facilmente customizável às necessidades dos seus usuários.

O AUP adota uma natureza serial para o que é amplo, e iterativo para o que é particular. Vejamos a natureza serial do AUP que inclui as mesmas quatro fases adotadas pelo RUP:

- **CONCEPÇÃO**
    
    Tem como objetivos a identificação do escopo do projeto, de uma arquitetura em potencial para o sistema e a viabilidade do projeto.
    
- **ELABORAÇÃO**
    
    Tem foco na comprovação da arquitetura do sistema.
    
- **CONSTRUÇÃO**
    
    Visa a construir um software funcional de forma iterativa e incremental que atenda às necessidades das partes interessadas do projeto.
    
- **TRANSIÇÃO**
    
    Tem como objetivo validar e implantar o sistema em ambiente de produção.
    

Vejamos o processo iterativo do AUP. As atividades que são realizadas de forma iterativa pelos membros da equipe de desenvolvimento para construir, validar e entregar software operacional são as seguintes:

- **MODELAGEM**
    
    Inclui o entendimento do negócio em questão e do problema a ser resolvido pelo software, identificando uma solução; representações UML para os modelos são utilizadas, entretanto, devem ser suficientemente boas e adequadas, ou seja, o mínimo de formalismo.
    
- **IMPLEMENTAÇÃO**
    
    Transformação dos modelos em código executável, realizando os respectivos testes básicos, tais como testes unitários.
    
- **TESTES**
    
    Realização de uma avaliação objetiva a fim de garantir a qualidade, incluindo a detecção de defeitos, a validação funcional e a verificação de que os requisitos são cumpridos.
    
- **IMPLANTAÇÃO**
    
    Planejar e executar a entrega do software operacional aos usuários finais e na obtenção de feedback dos usuários.
    
- **GERENCIAMENTO DE CONFIGURAÇÃO**
    
    Garantir o acesso aos artefatos do projeto, incluindo o rastreamento das diversas versões,bem como o controle e gerenciamento de suas alterações.
    
- **GESTÃO DE PROJETOS**
    
    Planejar, monitorar e controlar as atividades do projeto a fim de que ele seja entregue no prazo, dentro do orçamento e atendendo as expectativas dos usuários.
    
- **AMBIENTE**
    
    Atua em apoio às demais atividades, garantindo a infraestrutura do processo que inclui padrões, ferramentas e outras tecnologias de suporte disponíveis à equipe. Importante destacar que a adoção do AUP requer a aceitação prévia dos conceitos, valores e princípios relacionados com o desenvolvimento ágil.
    

## RESUMINDO

Destacamos os processos de desenvolvimento ágil Scrum e Processo Unificado Ágil (AUP). O Scrum é uma metodologia que possui uma destacada aplicação em projetos de software. Existe a possibilidade de aplicarmos a metodologia Scrum no gerenciamento de projeto ágil de software ou como processo ágil de desenvolvimento de software. Como todo processo ágil, aplica o fluxo de processo iterativo, sendo cada iteração denominada de sprint e, ao final de cada um destes, é gerada uma entrega, isto é, um software operacional. O método AUP tem suas origens no Processo Unificado (RUP), podendo ser considerado uma versão simplificada em função de sua adesão aos valores e princípios das metodologias ágeis. As fases do projeto permanecem as mesmas do RUP, tais como, concepção, elaboração, construção e transição. As mudanças significativas estão nas atividades iterativas compostas de modelagem, implementação, testes, implantação, gerenciamento de configurações, gestão de projetos e ambiente.

Para saber mais sobre os assuntos tratados neste tema, leia:

- [Engenharia de Software, Sommerville](https://www.facom.ufu.br/~william/Disciplinas%202018-2/BSI-GSI030-EngenhariaSoftware/Livro/engenhariaSoftwareSommerville.pdf).
- [Princípios de Análise e Projeto de Sistemas com UML](https://www.tecgraf.puc-rio.br/ftp_pub/lfm/EduardoBezerra-PrincipiosAnaliseProjetoSistemasComUML-2aEd.pdf)
- [Engenharia de Software Moderna](https://engsoftmoderna.info/)

Cursos gratuitos:

[Princípios de Desenvolvimento Ágil de Software](https://www.coursera.org/learn/principios-de-desenvolvimento-agil-de-software)

[Desenvolvimento Ágil com Padrões de Projeto](https://www.coursera.org/learn/desenvolvimento-agil-com-padroes-de-projeto)

[Desenvolvimento Ágil com Java Avançado](https://www.coursera.org/learn/desenvolvimento-agil-com-java-avancado)

[Software Engineering: Introduction](https://www.edx.org/learn/software-engineering/university-of-british-columbia-software-engineering-introduction)