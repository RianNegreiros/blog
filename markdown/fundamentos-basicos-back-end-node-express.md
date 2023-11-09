# Fundamentos Básicos de Back-end com Node.js e Express.js

Desenvolver o back-end de um aplicativo é uma tarefa crucial para permitir a interação dinâmica dos usuários com o front-end, possibilitando a realização de ações que gerem resultados reais. Nesse contexto, a escolha do framework de back-end adequado para o desenvolvimento em JavaScript desempenha um papel fundamental no sucesso geral do aplicativo.

![Comparativo entre Back-end e Front-end](../assets/front-end-back-end-comparativo.png)

## Fundamentos Básicos de Back-end

Antes de explorarmos o Node.js e o framework Express.js, é essencial compreender a distinção entre desenvolvimento front-end e back-end. O front-end refere-se à criação da interface interativa de um site, utilizando ferramentas populares como JavaScript, CSS e HTML. Por outro lado, o back-end, também conhecido como lado do servidor, é responsável por implementar a lógica do programa, o design de dados e a gestão do sistema.

O desenvolvedor back-end utiliza linguagens como JavaScript, Python, Java, C#, PHP e Ruby para criar aplicações dinâmicas, envolvendo a interação com bancos de dados e a integração com o front-end. A escolha do framework de back-end é crucial, e é nesse contexto que exploraremos o Node.js e o Express.js.

## Introdução à Plataforma Node.js

O Node.js é um ambiente de runtime JavaScript do lado do servidor, amplamente utilizado para criar servidores web. Lançado em 2009, o Node.js executa o motor JavaScript V8 fora do navegador, proporcionando alta performance. Um destaque do Node.js é sua abordagem assíncrona, permitindo lidar com milhares de conexões simultâneas sem bloquear o código.

O ambiente de desenvolvimento pode ser configurado facilmente utilizando a plataforma CodeSandbox, um IDE online e compartilhável. O CodeSandbox suporta a execução de aplicativos Node.js, proporcionando acesso ao runtime, aos scripts e a um terminal de linha de comando.

## Configuração do Ambiente de Desenvolvimento com CodeSandbox

O CodeSandbox, um ambiente de desenvolvimento integrado online, simplifica o desenvolvimento com sua interface amigável e suporte tecnológico eficiente. Pode-se criar projetos Node.js e executá-los utilizando containers Docker. A autenticação via GitHub ou Google facilita o acesso gratuito sem a necessidade de instalações locais.

## Introdução ao Framework Express.js

O Node.js, por si só, é uma plataforma de baixo nível. Para simplificar o desenvolvimento, o framework Express.js foi introduzido em 2010. Express.js é um framework rápido e leve para criação de servidores web em Node.js, seguindo a arquitetura Model-View-Controller (MVC). Ele simplifica o roteamento, gerenciamento de sessões, tratamento de erros e integração com bancos de dados.

### Conhecendo o Express.js

O Express.js acelera o desenvolvimento de aplicativos web, sendo amplamente utilizado em conjunto com o Node.js. Suas principais vantagens incluem a facilidade de desenvolvimento, suporte a aplicativos móveis e da web (SPA), arquitetura MVC, integração com bancos de dados e facilidade de configuração.

## Roteamento no Express.js

No Express.js, o roteamento é fundamental para determinar o comportamento específico do aplicativo em resposta a solicitações do cliente. Utilizando métodos HTTP como GET, POST, PUT e DELETE, o roteamento define como o aplicativo responde a diferentes requisições.

## Middleware no Express.js

As funções de middleware no Express.js têm acesso aos objetos de solicitação e resposta, permitindo a execução de código, modificação de solicitações e respostas, encerramento do ciclo de solicitação-resposta e chamada do próximo middleware. São essenciais para a execução de tarefas específicas durante o processamento das solicitações.

## Testando Rotas com Postman

Para testar as rotas criadas no exemplo, utilizamos a ferramenta Postman. Esta aplicação permite fazer solicitações a uma API de forma gráfica, facilitando os testes de métodos HTTP. Os testes foram realizados para as rotas de adicionar, atualizar, deletar e listar, proporcionando uma compreensão prática do funcionamento do Express.js.

## Conclusão

Exploramos os fundamentos do desenvolvimento back-end com Node.js e Express.js, desde a configuração do ambiente de desenvolvimento até a criação e teste de rotas. O Node.js e o Express.js oferecem uma combinação poderosa para o desenvolvimento eficiente de servidores web e aplicações dinâmicas.

Esse guia fornece uma base sólida para quem deseja aprofundar seus conhecimentos em desenvolvimento back-end, destacando a importância da escolha adequada de frameworks e a aplicação prática dos conceitos apresentados. Avance para adquirir novos aprendizados e aprimorar suas habilidades no emocionante mundo do desenvolvimento de software!

REFERÊNCIAS

CLARK, J. Backend meaning and everything you need to know about it. Back4App Blog, 28 fev. 2021. Disponível em: [https://blog.back4app.com/?s=backend+meaning](https://blog.back4app.com/?s=backend+meaning). Acesso em: 28 ago. 2022.

ELMASRI, R.; NAVATHE, S. B. Sistemas de banco de dados. 6. ed. São Paulo (SP): Pearson, 2011.

EXPRESSJS. Express: framework web rápido, flexível e minimalista para Node.js. Fundação Node.js, s. d. Disponível em: [http://expressjs.com/pt-br](http://expressjs.com/pt-br). Acesso em: 20 jul. 2022.

FERNANDES, H. M. What is a frontend developer and what does it do? TechBlog, 16 ago. 2020. Disponível em: [https://marquesfernandes.com/en/technology/what-is-a-frontend-developer-and-what-does-he-do/](https://marquesfernandes.com/en/technology/what-is-a-frontend-developer-and-what-does-he-do/). Acesso em: 10 ago. 2022.

GASPAR, L. Aprenda o que é NPM e como utilizá-lo. Blog HostGator, 19 fev. 2021. Disponível em: [https://www.hostgator.com.br/blog/o-que-e-npm](https://www.hostgator.com.br/blog/o-que-e-npm). Acesso em: 30 jul. 2022.

HASHDORK. CodePen vs CodeSandbox vs StackBlitz. Blog HashDork, 5 out. 2021. Disponível em: [https://hashdork.com/pt/codepen-codesandbox-stackblitz/](https://hashdork.com/pt/codepen-codesandbox-stackblitz/). Acesso em: 31 jul. 2022.

NODEJS. Introduction to Node.js. OpenJS Foundation, s. d. Disponível em: [https://nodejs.dev/learn/introduction-to-nodejs](https://nodejs.dev/learn/introduction-to-nodejs). Acesso em: 26 jul. 2022.

POSTMAN. Introduction. Postman HQ, San Francisco (CA, USA), 5 jul. 2022. Disponível em: [https://learning.postman.com/docs/getting-started/introduction](https://learning.postman.com/docs/getting-started/introduction). Acesso em: 05 ago. 2022.

RUBY, S.; RICHARDSON, L. Restful web services. Sebastopol (CA, USA): O'Reilly Media, 2007.

SAUDATE, A. REST: construa APIs inteligentes de maneira simples. São Paulo (SP): Casa do Código, 2014.

SINGLA, L./NET SOLUTIONS. MEAN Stack and Startups: Are they Made for Each Other? DZone - Web Dev Zone Tutorial, 27 fev. 2019. Disponível em: [https://dzone.com/articles/mean-stack-amp-startups-are-they-made-for-each-oth](https://dzone.com/articles/mean-stack-amp-startups-are-they-made-for-each-oth). Acesso em: 10 ago. 2022.
