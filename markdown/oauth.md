# OAuth: Simplificando a Gestão de Identidade e Credenciais 

![Animação.gif](/assets/google-oauth.gif)

No mundo da tecnologia, a autenticação e autorização são fundamentais para garantir a segurança e integridade dos dados e serviços online. Nesse sentido, o OAuth (Open Authorization) é um padrão de autenticação que permitiu simplificar e integrar logins em múltiplos serviços na Internet. Desenvolvido em conjunto pela Google e pelo Twitter, o OAuth é uma solução de autenticação baseada em tokens, que permite que os desenvolvedores criem aplicativos que podem acessar recursos de terceiros sem revelar as credenciais do usuário.

**O Processo de Autenticação**

Para entender como funciona o OAuth, vamos analisar um exemplo prático. Imagine que você está usando uma aplicação web que deseja acesso ao seu perfil no Google Drive ou Google Photos. Nesse caso, a aplicação Web solicita autorização para acessar seus dados do Google. O processo de autenticação é o seguinte:

1. A aplicação Web (client) solicita autorização para o usuário (resource owner);
2. O usuário concede autorização ao client;
3. O client envia uma solicitação de autorização ao servidor de autorização (authorization server);
4. O authorization server emite um token de acesso (access token) para o client;
5. O client utiliza o access token para acessar os recursos do usuário protegidos;

**Os Papéis Básicos**

Para entender melhor como funciona o OAuth, é importante conhecer os quatro papéis básicos envolvidos:

1. **Resource Owner**: É a pessoa que concede acesso aos seus dados. No exemplo anterior, é o usuário que concede autorização ao client;
2. **Resource Server**: É a camada de serviço/integração disponibilizada pelo provedor de identidades. Nesse caso, é o Google Drive ou Google Photos que fornecem os recursos protegidos;
3. **Authorization Server**: Responsável por autenticação e emissão dos tokens de acesso (access token) para os clients;
4. **Client**: É a aplicação que interage com o Resource Owner. Nesse caso, é a aplicação Web que deseja acessar os dados do usuário.

**A Execução dos Papéis**

A imagem abaixo representa bem a execução desses papéis e respectivos fluxos:

![Animação2.gif](/assets/abstract-flow.gif)

ETAPA 1 - A aplicação (client) solicita autorização para o usuário, para que a aplicação possa interagir e solicitar informações de suas credenciais junto ao provedor de identidade.

ETAPA 2 - O Dono do Recurso (resource owner) realiza a autorização.

ETAPA 3 - De posse da autorização, esta é encaminhada pelo cliente ao Servidor de Autorização, responsável por viabilizar a passagem das credenciais de acesso aos serviços do provedor.

ETAPA 4 - O provedor de credenciais passa o TOKEN, por meio de uma comunicação segura. De posse desse token, a aplicação poderá acessar os recursos do usuário requisitante.

ETAPA 5 - Passa-se o token aos provedores de serviços que detêm os recursos protegidos dos usuários. Nesse caso, é o Google Drive ou Google Photos que passa a ser acessado pelo Client com a devida autorização do usuário Dono do Recurso, realizado no passo 2.

ETAPA 6 - As informações e recursos protegidos são compartilhados com o Cliente. É nessa etapa que é possível, por exemplo, já ter a sua foto integrada com o serviço web requisitado, outras informações, como e-mail, dados de telefone, recursos específicos no Drive, lista de amigos e contatos, entre muitos outros.

**Conclusão**

Nesse artigo, discutimos o modelo de autenticação OAuth e seus papéis básicos. Vimos como funciona o processo de autenticação e autorização, desde a solicitação da autorização até a entrega do token de acesso. Com esse conhecimento, os desenvolvedores podem criar aplicativos seguros que possam acessar recursos de terceiros sem revelar as credenciais do usuário.

- [What is OAuth? | SAML vs. OAuth | Cloudflare](https://www.cloudflare.com/learning/access-management/what-is-oauth/)

- [Modern Guide - What is OAuth 2.0 and How Does It Work?](https://fusionauth.io/articles/oauth/modern-guide-to-oauth)

- [OAuth 2 Explained In Simple Terms](https://www.youtube.com/watch?v=ZV5yTm4pT8g)

- [O que você deveria saber sobre Oauth 2.0 e OpenID!](https://www.youtube.com/watch?v=68azMcqPpyo)