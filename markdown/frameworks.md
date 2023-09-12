# O que são frameworks?

Um framework web é um pacote formado por uma estrutura de arquivos e pastas padronizados (arquivos HTML, CSS, JS, etc.), que pode ser utilizado para auxiliar no desenvolvimento de aplicações web.

A maioria dos sites compartilha uma estrutura muito similar, e o objetivo dos frameworks e fornecer essa estrutura em comum para que os desenvolvedores não precisem refazê-la do zero, assim facilitando o processo de desenvolvimento.

# Arquitetura MVC

A maioria dos frameworks usa a arquitetura Model View Controller. Esse padrão serve para separar a lógica do aplicativo da interface do usuário.

## Model

O Model armazena todas as informações sobre o conteúdo de um aplicativo. Ao receber os dados de entrada do Controller, ele comunica qualquer atualizações de interface necessárias ao componente View.

## View

A View se refere ao front-end da aplicação, ou interface do usuário. Ele contém informações sobre o layout e a maneira como os usuários podem interagir com qualquer uma de suas partes. A View recebe a entrada do usuário, comunica essa entrada para o Controller para processamento e se atualiza conforme o Model.

## Controller

O Controller é um intermediário entre o Model e a View. Ele recebe a entrada do usuário da View, processa essa entrada e faz a mediação das mudanças necessárias entre as duas.

# Tipos de frameworks

Existem frameworks para front-end e back-end.

## Front-end (client-side) frameworks

1. React
2. Bootstrap
3. Angular
4. Next.js
5. Svelte

## Back-end (server-side) frameworks

1. Express (JavaScript)
2. Ruby On Rails (Ruby)
3. ASP.NET Core (C#)
4. Django (Python)
5. Laravel (PHP)

## React

[React](https://react.dev/) é uma library JavaScript para construir interfaces de usuário. Embora haja dúvidas sobre React realmente ser um framework, é uma library altamente popular, usada para construir a camada View de uma aplicação MVC. React é baseada em componentes que permite os usuários construir componentes encapsulados, cada um dos quais gerencia seu próprio estado, e depois compô-los para criar interfaces de usuário complexas. O benefício disso é que, quando os dados mudam, o React garante que apenas os componentes em questão sejam atualizados.
Um exemplo de como é um componente React

## Angular

[Angular](https://angular.io/) é uma estrutura JavaScript criada pelo Google que foi projetada especificamente para criar aplicativos dinâmicos da web. Angular é usado principalmente para atualizar a interface do usuário em tempo real e fornecer um site altamente interativo.

## Bootstrap

[Bootstrap](https://getbootstrap.com/docs/5.3/getting-started/introduction/) foi criado por desenvolvedores do Twitter e serviu principalmente para facilitar o processo de adição de CSS ao HTML. O Bootstrap, como qualquer outro framework front-end, inclui componentes CSS, HTML e JavaScript. Ele adere aos padrões de web design responsivo e, portanto, permite que os usuários desenvolvam sites responsivos de todas as complexidades e tamanhos.

## Next.js

[Next.js](https://nextjs.org/) é um framework front-end de código aberto baseado em React que é projetado para facilitar a construção de aplicações web modernas e escaláveis. Ele é focado em oferecer a melhor experiência para desenvolvedores, fornecendo uma série de recursos avançados prontos para uso. O Next.js é conhecido por seu suporte a renderização do lado do servidor (Server-Side Rendering - SSR) e renderização do lado do cliente (Client-Side Rendering - CSR). Essa combinação permite que os desenvolvedores otimizem o carregamento das páginas, melhorando o desempenho e a experiência do usuário. Além disso, o framework possui suporte nativo a rotas, permitindo a criação de aplicativos com várias páginas de forma intuitiva. Com uma comunidade ativa e suporte contínuo da equipe de desenvolvimento, o Next.js tornou-se uma escolha popular para projetos web ambiciosos.

## Svelte

[Svelte](https://svelte.dev/) é uma abordagem diferente para o desenvolvimento front-end. Enquanto a maioria dos frameworks JavaScript se baseia na interpretação em tempo de execução, o Svelte toma uma abordagem de compilação. Isso significa que o código Svelte é compilado para JavaScript otimizado durante a fase de construção, reduzindo a carga no navegador do usuário. O resultado é um código menor, mais eficiente e de alto desempenho. O Svelte simplifica a criação de componentes reutilizáveis, fornecendo uma sintaxe clara e concisa. Além disso, ele oferece recursos nativos para o gerenciamento de estado, permitindo que os desenvolvedores criem aplicações complexas com facilidade. Como o Svelte não precisa ser incluído na fase de execução, os aplicativos desenvolvidos com esse framework costumam ter tempos de carregamento mais rápidos e uma experiência mais suave para os usuários. Com sua abordagem inovadora e eficiente, o Svelte ganhou uma base de fãs dedicada e está rapidamente ganhando popularidade na comunidade de desenvolvimento front-end.

## Frameworks Back-end

### Express (JavaScript)

[Express](https://expressjs.com/en/starter/installing.html) é um framework web leve e flexível para Node.js, uma plataforma que permite a execução de JavaScript fora do navegador. O Express é amplamente conhecido por sua simplicidade e facilidade de uso, tornando-o uma escolha popular para desenvolvedores que desejam criar aplicativos web rápidos e eficientes. Ele fornece uma camada de abstração sobre o servidor HTTP, o que significa que os desenvolvedores podem lidar com rotas, requisições e respostas de maneira mais fácil e organizada. Além disso, o Express permite a integração perfeita de middlewares, que são funções que podem ser executadas em uma sequência específica durante o processamento das requisições. Essa flexibilidade torna o Express ideal para a criação de APIs RESTful e aplicativos web com uma arquitetura baseada em microserviços. Com uma grande comunidade de desenvolvedores e uma ampla gama de recursos e extensões disponíveis, o Express continua sendo um dos frameworks mais populares para o desenvolvimento web em JavaScript.

### Ruby On Rails (Ruby)

[Ruby on Rails](https://rubyonrails.org/), também conhecido como Rails, é uma estrutura de aplicativo da Web do lado do servidor escrita em Ruby. O Rails é construído sobre a arquitetura Model View Controller que falamos anteriormente e fornece estruturas padrão para tudo o que está em cada componente, incluindo bancos de dados, vários serviços da Web comuns, bem como as próprias páginas da Web. Ruby on Rails, portanto, separa o processo de desenvolvimento web em componentes simplificados e fornece uma estrutura básica para cada elemento de qualquer componente, tornando assim imensamente conveniente para os usuários construir sua própria funcionalidade específica sobre essas estruturas provisórias sem ter que se preocupar em começar do básico.

### ASP.NET Core (C#)

[ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-7.0) é uma estrutura de desenvolvimento da Web de código aberto do lado do servidor para criar aplicativos e serviços da Web modernos usando .NET Core ou qualquer linguagem compatível com .NET Core. O ASP.NET Core foi desenvolvido pela Microsoft para facilitar o processo de desenvolvimento de páginas web dinâmicas e, portanto, simplificou consideravelmente o processo de criação de sites baseados em HTML5, CSS e JavaScript. Como resultado, possibilitou aos usuários criar sites que não são apenas simples e rápidos, mas também têm a capacidade de escalar para milhões de usuários.

### Django (Python)

[Django](https://www.djangoproject.com/start/) é um framework web Python de alto nível que permite o rápido desenvolvimento de websites seguros e de fácil manutenção. O Django, como o Express, cuida de reduzir o código para funcionalidades básicas a instruções simples, para que você possa se concentrar em escrever seu aplicativo e implementar funcionalidades mais específicas sem precisar reinventar a roda!

### Laravel (PHP)

[Laravel](https://laravel.com/docs/10.x#why-laravel) é um framework web PHP de código aberto que oferece uma experiência de desenvolvimento elegante e expressiva. Ele foi projetado para ser fácil de aprender e usar, permitindo que os desenvolvedores criem rapidamente aplicativos web robustos e seguros. Uma das características mais distintas do Laravel é o uso extensivo de sua sintaxe chamada "Eloquent ORM", que permite aos desenvolvedores interagir com o banco de dados usando uma linguagem de consulta fluente, tornando a manipulação de dados mais intuitiva e eficiente. Além disso, o Laravel oferece recursos poderosos para autenticação, criação de APIs e gerenciamento de tarefas em segundo plano, tornando-o uma escolha sólida para projetos de todos os tamanhos. O ecossistema do Laravel também é muito ativo, com uma ampla gama de pacotes e extensões criados pela comunidade para estender ainda mais as funcionalidades do framework. Com uma documentação detalhada e suporte ativo da comunidade, o Laravel se estabeleceu como um dos principais frameworks PHP para o desenvolvimento web moderno.

Agora, com esses resumos adicionados ao seu artigo, você tem uma visão geral dos frameworks Express em JavaScript e Laravel em PHP. Continue a aprofundar seus pontos com exemplos e comparações para fornecer aos leitores uma visão completa dessas poderosas ferramentas de desenvolvimento web. Lembre-se de verificar suas fontes e garantir que o conteúdo seja preciso e atualizado antes de finalizar o artigo. Boa sorte com a continuação da redação!

# Stacks de Software para desenvolvimento web

Uma stack de software refere-se a uma combinação de ferramentas que são usadas juntas para criar software.

Vários frameworks precisem ser usados em conjunto com outras ferramentas como uma stack para criar um aplicativo da web e, nesse caso, cada um deles precisa ser explicitamente incluído e vinculado pelos próprios usuários. Já falamos os frameworks front-end e back-end anteriormente; agora veremos como usá-los juntos como uma stack de software para desenvolver aplicações web.

## MEANStack

MEANStack refere-se à integração dos seguintes frameworks e ferramentas para criar uma aplicação web:

1. MongoDB
2. Express
3. Angular
4. Node.js

O MongoDB serve como banco de dados e, portanto, forma o componente de armazenamento do site. Para colocá-lo em termos MVC, ele atua como o componente Model do aplicativo. O Express é usado para desenvolver o back-end da aplicação Web e é usado para fazer tudo, desde a criação do servidor no back-end até o tratamento de todos os tipos de solicitações provenientes do cliente e processamento de dados do banco de dados de acordo. O Express, portanto, forma o Controller da aplicação MVC. O Angular, neste caso, é utilizado para desenvolver o front-end da aplicação web, e serve basicamente para programar tudo o que acontece dentro do navegador do lado do cliente. O Angular é, portanto, o componente View da sua aplicação MVC. Node.js, neste caso, refere-se ao ambiente de execução do back-end da aplicação.
O express roda no ambiente Node.js e seu objetivo é simplificar as operações que podem ser complexas no Node.js.

## MERNStack

As aplicações MERN têm a mesma estrutura da MEAN; um banco de dados, um framework do lado do servidor e um framework do lado do cliente. A única diferença, neste caso, é que o framework front-end utilizado é diferente. O front-end dos aplicativos MERN é desenvolvido usando React em vez de Angular. Não há benefícios marcantes do React em comparação com o Angular ou vice-versa, portanto, a pilha de software que você seleciona depende muito dos requisitos específicos do seu aplicativo, e é principalmente uma questão de preferência ao escolher entre os dois.

Isso conclui este artigo sobre frameworks, como elas podem ser combinadas para formar stacks de software. A escolha da estrutura depende muito dos requisitos do aplicativo que está sendo desenvolvido.
