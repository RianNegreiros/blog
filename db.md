# Bancos de dados no desenvolvimento Web

Uma visão geral do uso de bancos de dados em aplicações Web.

Antes de começarmos a falar sobre qual é a finalidade de um banco de dados em um aplicações Web, precisamos primeiro descrever os dois tipos de sites existentes.

## Páginas da Web estáticas vs. dinâmicas

Os sites podem ser classificados em um de dois tipos:

1. Estáticos
2. Dinâmicos

Os **sites estáticos** são aqueles que exibem apenas informações, como texto ou imagens, em suas páginas da Web, com as quais os usuários não podem interagir.

Os **sites dinâmicos**, por outro lado, permitem a interação do usuário, e é aí que os bancos de dados entram em cena. Pense em qualquer site que você tenha visitado hoje. Quantos deles exigiram que você fizesse login ou permitiram que você clicasse em um botão que recuperasse algumas informações, talvez até uma nova página da Web, para você? Essas informações que os aplicações Web podem exibir ou processar para permitir o acesso a outras informações precisam ser armazenadas em algum lugar. Esse local é um banco de dados associado ao aplicação Web.

<div align="center">
    <img src="https://github.com/RianNegreiros/blog-posts-mds/blob/main/assets/how-cache-works.png?raw=true" alt="Estrutura básica de um site dinâmico">
    <p align="center">
      <code>Estrutura básica de um site dinâmico</code>
    </p>
</div>

## DBMS em aplicações Web

DBMS, abreviação de Database Management System (Sistema de gerenciamento de banco de dados), é essencialmente um tipo de nuance adicionada ao desenvolvimento Web que permite que os dados sejam separados da lógica do aplicação e armazenados separadamente para serem recuperados e processados quando necessário. Isso significa que os dados são armazenados em um banco de dados que possui um sistema totalmente autônomo que o rege. O aplicação faz chamadas a esse banco de dados para obter os dados necessários sem precisar integrar grandes quantidades de código de manipulação no próprio código do aplicação. Os sistemas de gerenciamento de banco de dados são, portanto, importantes para os desenvolvedores, pois fornecem um método altamente eficiente para lidar com vários tipos de dados, sem interferir no código do aplicação.

## Tipos de DBMS

Os sistemas de gerenciamento de banco de dados podem ser classificados em dois tipos:

1. SQL
2. NoSQL

## SQL

Os bancos de dados SQL, também conhecidos como bancos de dados relacionais, usam a linguagem de consulta estruturada (SQL) para definir e manipular dados. Por um lado, isso é extremamente útil, pois o SQL é uma das opções mais versáteis e amplamente utilizadas disponíveis, o que o torna uma opção segura e especialmente excelente para consultas complexas. Por outro lado, também pode ser restritivo, pois o SQL exige que você use esquemas predefinidos para determinar a estrutura dos dados antes de trabalhar com eles. Além disso, os dados são sempre armazenados como uma tabela nos bancos de dados SQL, o que leva a recuperações de dados ineficientes e complicadas. Além disso, todos os seus dados devem seguir a mesma estrutura. Isso pode exigir um esforço significativo e pode levar a um código de processamento de dados altamente complicado que pode afetar a qualidade do aplicação geral.

Alguns bancos de dados SQL comuns que discutiremos neste capítulo incluem:

1. MySQL
2. PostgreSQL
3. MariaDB

## NoSQL

Os bancos de dados NoSQL, também conhecidos como bancos de dados não relacionais, têm um esquema dinâmico para dados não estruturados, e os dados são armazenados de várias maneiras, incluindo pares de valores-chave, documentos e até mesmo gráficos. Essa flexibilidade significa que você não precisa definir a estrutura dos dados explicitamente e que cada conjunto de dados pode ter sua própria estrutura exclusiva, sem precisar se preocupar em escrever linhas de código estranhas para processar esses dados de modo que correspondam a uma estrutura predefinida específica. Esses bancos de dados podem crescer dinamicamente, e a estrutura dos dados pode variar de banco de dados para banco de dados.

Os bancos de dados NoSQL populares que abordaremos neste capítulo incluem:

1. MongoDB
2. Apache CouchDB
3. Redis

Isso abrange tudo o que precisamos saber sobre a função dos bancos de dados e dos sistemas de gerenciamento de bancos de dados no desenvolvimento da Web. Agora, vamos examinar exemplos específicos dos dois tipos de bancos de dados e listar os prós e os contras de ambos.

## Bancos de dados SQL

Uma visão geral do sistema de gerenciamento de banco de dados relacional mais comum.

Agora, discutiremos os sistemas populares de gerenciamento de banco de dados SQL que estão disponíveis para uso no desenvolvimento da Web.

### MySQL

O [MySQL](https://www.mysql.com/) está entre os sistemas de gerenciamento de banco de dados mais usados e, no passado, foi parte integrante da maioria das aplicações da Web. O MySQL é apoiado pela Oracle e usa a linguagem de consulta padrão (SQL) para armazenar dados na forma de uma tabela e, para recuperar dados, é necessário fazer consultas em SQL. O MySQL, ao contrário do próprio SQL, funciona em todas as plataformas, incluindo Linux, iOS e Windows, com toda a simplicidade do SQL, pois não exige que você aprenda nenhuma sintaxe nova depois de conhecer o SQL. Vamos dar uma olhada em um exemplo de consulta MySQL que cria uma nova tabela:

```sql
CREATE TABLE [IF NOT EXISTS] table_name(
    column1 DATATYPE,
    column2 DATATYPE,
    column3 DATATYPE,
    column4 DATATYPE,
    PRIMARY KEY (column1)
) ENGINE=storage_engine
```

Como fica claro no exemplo acima, o MySQL usa consultas SQL básicas para criar tabelas e manipular dados. Essa é uma de suas principais vantagens, pois o SQL não é difícil de aprender e é altamente onipresente, o que significa que o MySQL é relativamente fácil de usar para a maioria dos iniciantes que já conhecem SQL.

### PostgreSQL

O [PostgreSQL](https://www.postgresql.org/), também conhecido como Postgres, é um sistema de gerenciamento de banco de dados objeto-relacional de código aberto que pode lidar com grandes quantidades de dados e oferecer suporte contínuo a aplicações Web que lidam com vários usuários simultâneos. É o banco de dados padrão do macOS e oferece gerenciamento eficiente de dados entre plataformas. Além disso, ele também oferece suporte a acessos simultâneos ao banco de dados e permite que os usuários adicionem funções personalizadas desenvolvidas usando várias linguagens de programação, incluindo C, C++ e Java.

Além disso, outro benefício principal do PostgreSQL é que ele foi projetado para ser extensível. Isso significa que os usuários podem definir seus próprios tipos de dados e até mesmo plugins personalizados para alterar partes do sistema de acordo com suas necessidades.

### MariaDB

O [MariaDB](https://mariadb.org/) é um fork do MySQL, que foi criada pelos desenvolvedores originais do MySQL após a aquisição do MySQL. Ele foi concebido para substituir o MySQL e inclui uma ampla seleção de mecanismos de armazenamento para poder trabalhar facilmente com dados de outros sistemas de gerenciamento de dados relacionais. O MariaDB, assim como o MySQL, usa uma linguagem de consulta padrão, o que o torna tão simples de usar quanto o MySQL. Além disso, o MariaDB é executado em vários sistemas operacionais e oferece suporte a uma ampla variedade de linguagens de programação.

Além de ter todos os benefícios que o MySQL apresenta, o MariaDB também oferece muitas operações e comandos que não estão disponíveis no MySQL e substitui recursos que tendem a afetar negativamente o desempenho.

<div align="center">
    <img src="https://github.com/RianNegreiros/blog-posts-mds/blob/main/assets/common-sql-databases.jpeg?raw=true" alt="Database SQL mais comuns" />
    <p align="center">
      <code>Database SQL mais comuns</code>
    </p>
</div>

### A transição para o NoSQL

A velocidade está entre os fatores mais importantes que afetam a experiência do usuário em um aplicação Web. Contar com um número desnecessário de chamadas para um banco de dados geralmente tende a tornar as coisas mais lentas para os usuários e, portanto, é importante que os desenvolvedores da Web mantenham alguns pedaços de dados solicitados com frequência em armazenamento imediato para poderem ser recuperados rapidamente, sem criar grandes latências na exibição de dados. Para isso, os desenvolvedores da Web precisam pensar em como colocar o máximo possível de dados na memória e, em seguida, em como armazenar os dados em cache no nível do sistema de arquivos para evitar fazer chamadas para um banco de dados. O motivo disso é que a recuperação de dados de um banco de dados é o gargalo da maioria das aplicações da Web, e a redução dessas operações pode melhorar significativamente a capacidade de resposta. O processo de selecionar os componentes de dados solicitados com frequência e armazená-los na memória imediata para evitar chamadas estranhas ao banco de dados é chamado de cache, e os bancos de dados NoSQL ganharam popularidade nos últimos tempos devido à sua capacidade de armazenar dados em cache automaticamente para melhorar o desempenho.

Além disso, as próprias chamadas ao banco de dados são mais ineficientes nos bancos de dados SQL do que nos bancos de dados NoSQL. Isso ocorre porque os bancos de dados SQL armazenam dados em forma de tabela, e as consultas exigem iterações abrangentes da tabela. Os bancos de dados NoSQL, por outro lado, usam estruturas de dados para armazenar entradas, o que significa que as pesquisas são sempre otimizadas e as chamadas ao banco de dados são muito mais eficientes. Consequentemente, com os aplicativos modernos da Web exigindo cada vez mais tempos de resposta rápidos para atingir a funcionalidade pretendida, a mudança para o uso de bancos de dados NoSQL tem sido evidente nos últimos tempos.

Isso é tudo sobre os vários sistemas de gerenciamento de bancos de dados SQL disponíveis para uso com seu aplicação Web e como os desenvolvedores da Web passaram a usar os sistemas de gerenciamento de bancos de dados NoSQL.

## Bancos de dados NoSQL

Uma visão geral dos bancos de dados NoSQL comumente usados.

Agora, veremos as opções de NoSQL existentes e como os sistemas de gerenciamento de bancos de dados NoSQL se comparam aos sistemas de gerenciamento de bancos de dados SQL.

### MongoDB

O [MongoDB](https://www.mongodb.com/), que talvez seja o DBMS NoSQL mais popular, é um sistema de gerenciamento de banco de dados não relacional de código aberto que se tornou conhecido como a principal opção quando se trata de desenvolver aplicativos modernos para a Web. A razão para isso é que o MongoDB usa um sistema de armazenamento baseado em documentos que armazena pares de valores-chave e permite pesquisas altamente eficientes, tornando a recuperação de dados muito mais rápida e fácil do que qualquer DBMS SQL normal. Além disso, o modelo de documento garante que os dados possam ser mapeados diretamente para objetos dentro do código do aplicação e, portanto, facilita muito o manuseio de dados, eliminando a necessidade de adicionar código para processar os dados consultados. Além disso, o MongoDB armazena dados de maneira altamente flexível, permitindo que os campos variem de um documento para outro e que a estrutura dos dados esteja aberta as mudanças ao longo do tempo. Os dados também podem ser indexados e consultados conforme os requisitos específicos do usuário e, em seguida, atualizados em tempo real. Isso torna o MongoDB um sistema excepcionalmente poderoso para análise de dados, o que se reflete em sua popularidade. No entanto, o aspecto mais importante da qualidade que o MongoDB abrange é a confiabilidade; o MongoDB é um banco de dados distribuído em sua essência, o que significa que está disponível, é escalável e facilmente distribuível entre locais, o que o torna bem equipado para aplicativos modernos que exigem acesso rápido aos dados em todos os momentos.

Os comandos do MongoDB são bastante simples e fáceis de aprender. Um exemplo que cria um novo banco de dados chamado newDB:

```sql
use newDB
```

A simplicidade e a facilidade de uso do MongoDB mostram por que ele se tornou tão popular nos últimos tempos e, portanto, é uma ferramenta essencial para aprender no desenvolvimento da Web.

### Apache CouchDB

O [CouchDB](http://couchdb.apache.org/) é um sistema de gerenciamento de banco de dados NoSQL de código aberto que tem como objetivo principal oferecer facilidade de uso. O CouchDB combina um modelo intuitivo de armazenamento de documentos com um mecanismo de consulta avançado para permitir que os usuários armazenem seus dados com segurança em servidores pessoais ou com qualquer provedor cloud. Além disso, o CouchDB não só tem a capacidade de armazenar todos os tipos de dados, mas também permite que os aplicações Web extraiam esses dados de forma conveniente, sem a necessidade de adicionar comandos de tradução, já que o CouchDB é compatível com a formatação que os aplicações Web normalmente usam.

### Redis

O [Redis](https://redis.io/) é um armazenamento de estrutura de dados na memória de código aberto que é frequentemente usado como um banco de dados. Ele oferece suporte a todos os tipos de estruturas de dados, desde strings, hashes, listas e conjuntos até conjuntos ordenados com consultas de intervalo, bitmaps, hiperloglogs e índices geoespaciais com consultas de raio e fluxos. Isso significa que o Redis fornece uma ampla variedade de estruturas de dados que podem ser usadas para armazenar os dados de seu aplicação da maneira mais ideal, de acordo com a forma como os dados são estruturados. Além disso, como o Redis armazena dados na memória, ele permite a recuperação rápida de dados e, portanto, acelera significativamente o processo de resposta às solicitações dos usuários.

<div align="center">
    <img src="https://github.com/RianNegreiros/blog-posts-mds/blob/main/assets/common-nosql-databases.png?raw=true" alt="Database NoSQL mais comuns" />
    <p align="center">
      <code>Database NoSQL mais comuns</code>
    </p>
</div>

## Cache na Web

Uma abordagem para otimizar o armazenamento de dados em aplicações Web.

Discutimos a latência que as chamadas frequentes ao banco de dados introduzem nas aplicações Web. Agora, examinaremos o cache da Web como um meio de eliminar essa latência.

### Introdução ao cache da Web

O armazenamento em cache na Web é um recurso de design do protocolo HTTP que visa minimizar a quantidade de tráfego que um aplicação está manipulando em um determinado momento para melhorar a capacidade de resposta do aplicação da Web, conforme percebido pelos usuários, como um todo. Para fazer isso, os caches são usados em cada nível, desde o próprio servidor até os navegadores dos usuários, e cada um deles é destinado a armazenar dados que os usuários provavelmente solicitarão.

Essencialmente, o cache da Web funciona armazenando em cache as respostas HTTP para determinadas solicitações de acordo com uma análise probabilística das solicitações, frequentemente observadas no servidor. As solicitações subsequentes de conteúdo armazenado em cache podem ser atendidas a partir de um cache mais próximo do usuário, em vez de ter que enviar a solicitação de volta ao servidor da Web, que então faz uma chamada ao banco de dados para obter os dados necessários.

<div align="center">
    <img src="https://github.com/RianNegreiros/blog-posts-mds/blob/main/assets/how-cache-works.png?raw=true" alt="Como o cache funciona" />
    <p align="center">
      <code>Como o cache funciona</code>
    </p>
</div>

### Benefícios do armazenamento em cache

Há vários benefícios do armazenamento em cache na Web, cada um deles enumerado abaixo:

1. Os dados podem ser armazenados em cache em vários pontos diferentes no caminho entre o cliente e o servidor. Quando os dados necessários são armazenados em cache mais perto do cliente, as solicitações não aumentam muito o tráfego da rede, pois são resolvidas muito antes no caminho.

2. Em relação ao primeiro ponto, como as solicitações são resolvidas mais cedo no caminho, as respostas também são enviadas de volta mais rapidamente, melhorando assim a capacidade de resposta das aplicações Web.

3. O armazenamento em cache agressivo ao longo da rede também pode permitir que a aplicação sustente cargas maiores de dados, pois uma parte significativa dos dados pode ser armazenada em caches.

4. Caso o servidor tenha problemas para acessar o banco de dados por algum motivo, os dados armazenados em caches ainda poderão ser fornecidos aos usuários finais.

Agora que sabemos como o armazenamento em cache é importante, vamos ver exatamente como você pode armazenar em cache os dados da sua aplicação Web!

### MongoDB

O MongoDB tem mecanismos embutidos para lidar com o armazenamento em cache e mantém os dados usados mais recentemente na RAM. Se os usuários tiverem criado índices para suas consultas e o conjunto de dados de trabalho couber na RAM, o MongoDB atenderá a todas as consultas a partir da memória. Entretanto, o MongoDB não armazena em cache os resultados das consultas para retornar os resultados do cache para todas as futuras consultas idênticas.

### Redis

O Redis, como já vimos, armazena todos os dados na memória por padrão e é frequentemente usado como cache. Portanto, é ideal para ser usado como um sistema de banco de dados em aplicações modernas.

### CouchDB

O CouchDB, assim como os outros dois bancos de dados NoSQL que discutimos, tem como objetivo armazenar em cache todos os dados possíveis. Quanto menor o tamanho do arquivo, mais do arquivo pode ser armazenado em cache pelo CouchDB. Portanto, é uma boa ideia pensar nos dados que você deseja armazenar quando se trata de bancos de dados NoSQL para que sua aplicação possa ser executado sem problemas e sem ser sobrecarregado por quantidades desnecessárias de dados.

### Memcached

O [Memcached](https://memcached.org) é um sistema de cache de objetos de memória distribuída, de código aberto e alto desempenho, destinado a acelerar aplicações dinâmicas, aliviando a carga do banco de dados. O Memcached armazena pares de valores-chave de dados de chamadas ao banco de dados na memória para acelerar o processo de pesquisa no banco de dados. A ideia subjacente do Memcached é que ele permite retirar memória de partes do sistema onde há mais do que o necessário e torná-la acessível a áreas onde há menos do que o necessário. O Memcached é, portanto, uma ferramenta muito útil.

Isso conclui a discussão sobre bancos de dados e como seu uso pode se tornar mais eficiente por meio do armazenamento em cache.

### Alguns conteúdos e cursos para se aprofundar em databases

- [Categoria Database Systems do GitHub repository Computer Science courses with video lectures](https://github.com/Developer-Y/cs-video-courses#database-systems)

- [CS403: Introduction to Modern Database Systems da Saylor acadamy](https://learn.saylor.org/course/view.php?id=93)

- [Stanford University: Databases: Relational Databases and SQL](https://www.edx.org/learn/relational-databases/stanford-university-databases-relational-databases-and-sql)
