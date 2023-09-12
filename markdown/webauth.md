# Introdução à autenticação na Web

## Como aplicações Web se tornam seguros?

Muitas aplicações Web são totalmente funcionais sem fazer login de usuários, criar contas ou proteger dados. Por exemplo, a pesquisa do Google, a Wikipedia e o Stack Overflow não exigem contas. Por outro lado, não conseguiremos ir muito longe com a Amazon, o Facebook ou o Quora sem criar uma conta, mesmo que possamos ver algum conteúdo sem ela. Criar e gerenciar contas é um pouco mais complicado do que criar um site normal. Precisamos personalizar o site para cada usuário, e suas informações devem ser protegidas de outros usuários. Toda interação específica do usuário deve ser autenticada. Isso significa que o servidor deve saber quem é o usuário e ter certeza de que não se trata de outra pessoa. Em geral, isso é feito por meio de uma senha. Quando a senha é inserida, o servidor pode ter certeza de que está se conectando com o usuário legítimo. Uma aplicação Web requer muitas interações individuais. Um usuário se cansará rapidamente de reinserir sua senha para cada interação.

As perguntas a seguir se enquadram no domínio da segurança de computadores:

1. A senha deve ser armazenada no cliente e reenviada todas às vezes?
2. Como a API verifica se ela está correta?
3. Quais abordagens oferecem a melhor escala?
4. Que preocupações são levantadas por essas abordagens?

## JSON Web Token

A segurança de computadores pode ser um assunto intimidador, cheio de algoritmos, acrônimos e abstrações complicadas. Felizmente, não é necessário dominar a teoria da segurança para criar uma aplicação Web segura. Existem vários métodos bons para autenticação segura. Um dos métodos mais simples é usar tokens efêmeros que permitem que qualquer solicitação que apresente o token obtenha acesso. Essa abordagem é chamada de **[JSON Web Token (JWT)](https://jwt.io/introduction)**. Um JWT refere-se ao mecanismo de implementação dessa abordagem, e a abordagem em si seria melhor denominada "tokens de acesso efêmeros".

A descrição resumida é que um JWT é um objeto JSON assinado criptograficamente, criado quando o usuário faz login e usado posteriormente até a expiração. Para entender essa breve descrição, precisamos entender a diferença entre assinar e criptografar, como as assinaturas podem ser verificadas, como um token pode ser "usado", o que significa um token expirar e que segurança isso oferece (e não oferece).

## A função das assinaturas digitais

Como as [assinaturas digitais](https://pt.wikipedia.org/wiki/Assinatura_digital) são usadas durante o acesso a um aplicação Web.

### **Assinatura digital**

A criptografia de dados é o processo de conversão de dados originais em uma nova sequência de bytes da qual os dados originais não podem ser lidos sem acesso a uma chave secreta. A criptografia impede que as pessoas leiam os dados se não tiverem a chave. Essa proteção é chamada de **confidencialidade dos dados**. O JWT não envolve criptografia, mas usa assinaturas. A expectativa é que a confidencialidade seja fornecida pelo TLS ([**Transport Layer Security**](https://pt.wikipedia.org/wiki/Transport_Layer_Security)), que é o que o "S" em **HTTPS**.

Diferentemente da criptografia, as assinaturas de dados não substituem os dados originais. Os dados e um segredo são transformados em [hash](https://pt.wikipedia.org/wiki/Fun%C3%A7%C3%A3o_hash) para criar uma nova cadeia de bytes de aparência aleatória chamada assinatura, anexada aos dados. O hash de dados pode ser considerado como uma mistura de todos os bits e sua condensação em um tamanho menor. O importante é que se impossibilita criar uma assinatura falsa se não soubermos o segredo, ou mesmo encontrar uma parte diferente dos dados com a mesma assinatura. O benefício disso é a proteção, que chamamos de integridade dos dados. Se alguns dados estiverem acompanhados de uma assinatura válida, saberemos que os dados não foram modificados.

Para resumir, o JWT usa assinaturas para fornecer integridade de dados, em vez de usar criptografia para fornecer confidencialidade de dados.

Então, o que isso tem a ver com tokens? É melhor pensar em duas fases diferentes.

1. Na primeira fase, um token é gerado.
2. Na segunda fase, o token é usado para autenticação em uma API.

## Acesso a uma aplicação Web

Quando um usuário chega ao sua aplicação Web:

1. A primeira coisa que ele precisa fazer é o login. Ele insere seu nome de usuário e senha em um formulário e o envia.
2. O servidor recebe as informações e verifica se o nome de usuário e a senha correspondem ao que está no banco de dados. Normalmente, a senha não é armazenada diretamente. Apenas um hash dela é armazenado, de modo que mesmo as pessoas com acesso ao banco de dados de senhas não conseguiriam fazer login. Até o momento, não há tokens envolvidos.
3. Se o nome de usuário e a senha estiverem corretos, o servidor gerará um objeto JSON com as principais informações da sessão, como o nome de usuário, o acesso que o usuário tem, um ID de sessão e por quanto tempo o acesso será válido, ou seja, a expiração.
4. Essas informações são enviadas de volta ao usuário juntamente com uma assinatura. A assinatura garante que o usuário não possa enganar o servidor posteriormente com um objeto modificado. O objeto modificado teria uma assinatura inconsistente e, portanto, inválida. Para fins de integridade, o token também indica o algoritmo usado para produzir a assinatura, mas o segredo usado para criar a assinatura não é fornecido. O segredo é a base da segurança da aplicação Web e deve ser protegido o tempo todo.

![Acessando um aplicação Web](assets/Acesso_a_uma_aplicao_Web.png)

## ****JWT: JSON Web Tokens****

Como as assinaturas de dados são usadas durante o acesso a um aplicação Web.

### Atualização de informações em uma aplicação Web

A coleção do nome do algoritmo, o objeto JSON e a assinatura juntos formam o JWT. Ele é enviado como um cookie para o usuário, que agora o reenviará a cada solicitação subsequente. Portanto, suponha que o usuário queira atualizar suas informações em uma aplicação Web. Ele agora envia a solicitação de API regular para essa atualização com o token de autenticação JWT em um cookie.

O servidor examina o token e verifica se ele ainda é válido. Para isso, é necessário verificar a assinatura, reescrevendo os dados com o mesmo segredo de antes, e verificar novamente se o token ainda não expirou.

- Somente se o token for válido, o servidor executará a ação solicitada.
- Se o token for inválido, isso também deverá ser tratado de forma elegante, por exemplo, redirecionando o usuário de volta à tela de login, onde um novo token válido poderá ser criado.

Em uma aplicação dinâmica de página única, a abordagem mais comum seria responder com uma mensagem indicando que o token expirou. O cliente deve reconhecer esse fato e adquirir um novo token.

## O servidor é seguro?

O servidor agora pode garantir que somente os usuários com tokens válidos usem a API, protegendo assim o servidor. É extremamente improvável que alguém "decifre" a função hash, mas duas coisas notáveis podem dar errado com os tokens. A mais notável é que qualquer pessoa com uma cópia do token pode se passar pelo usuário legítimo. Portanto, o usuário e o navegador do usuário devem manter o token em segredo. Normalmente, isso é feito permitindo que ele seja transferido somente por HTTPS, o que criptografa a solicitação e impede que ela seja lida por intermediários. Além disso, uma boa segurança no navegador, fornecida pelos navegadores modernos, pode ajudar a impedir que scripts de terceiros acessem as informações.

A outra preocupação principal é que a segurança depende totalmente do sigilo do segredo usado no hashing. Qualquer pessoa que conheça esse segredo poderá forjar assinaturas para qualquer dado. Para proteger o segredo, ele nunca deve ser armazenado no próprio código, mas lido em um ambiente ou em um banco de dados. Em geral, essa é uma proteção suficiente, mas também é uma boa prática atualizar o segredo de tempos em tempos.

## Vantagens

O uso do JWT oferece uma grande vantagem: os servidores podem responder às solicitações de API sem acessar o banco de dados de credenciais. Em outras palavras, qualquer número de serviços pode verificar os direitos de acesso após receber o valor de hash secreto. Em abordagens de autenticação mais primitivas, cada serviço precisaria verificar o nome de usuário e a senha em um banco de dados, o que pode se tornar um ponto único de falha à medida que a aplicação é dimensionado. A possibilidade de verificar os tokens sem acessar o banco de dados reduz a comunicação e torna a aplicação mais dimensionável.

## Desvantagens

A principal desvantagem é que a revogação de acesso não é bem tratada. A revogação de acesso é quando você deseja negar o acesso de um usuário após fornecer um token, mas antes que o token tenha expirado. Isso pode ocorrer com datas de expiração longas, se um usuário cancelar repentinamente um serviço ou se você descobrir que uma conta foi invadida. Para a maioria das aplicações Web, esse não é um problema sério. Em geral, as aplicações Web não são atacados pelo roubo de tokens, mas pelo roubo de senhas. Nesse ponto, a revogação não é realmente a solução, mas são necessários métodos mais avançados, como a detecção de logins anômalos.

Autenticação de dois fatores, que envolve o envio de um código de verificação para um telefone ou e-mail registrado, ou a realização de perguntas de segurança secundárias, por exemplo, o nome da escola primária do usuário. Métodos mais avançados como esses estão fora do escopo deste texto.

## Adendo: JWTs são seguros?

Nos últimos anos, as JWTs têm sido alvo de muita atenção, em parte devido a algumas falhas de projeto que precisaram ser corrigidas, como o [suporte a um algoritmo "Nenhum",](https://auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/) que efetivamente permitiria forjar tokens sem nenhum conhecimento prévio dos segredos e chaves usados para assiná-los. Luciano Mammino, um pesquisador da Itália, conseguiu até mesmo publicar um [cracker de JWT](https://github.com/lmammino/jwt-cracker) para ilustrar como poderia ser fácil quebrar JWTs por meio de força bruta, desde que o algoritmo e os segredos usados fossem fracos.

Sinceramente, os JWTs são muito úteis quando você deseja trocar dados entre duas partes. Por exemplo, você poderia enviar a um cliente o URL `https://exemplo.com/verificar?token=＄JWT` para que ele pudesse acessar os dados contidos no token e saber que eles vêm de uma fonte confiável. Como IDs de sessão, muitas vezes há mecanismos mais simples nos quais você pode confiar, pois só precisa emitir um ID criptograficamente aleatório que identifique um cliente.

Isso significa que os JWTs não são seguros? Na verdade, não, pois isso depende de como você os utiliza. O Google, por exemplo, permite a [autenticação em suas APIs por meio de JWTs](https://developers.google.com/identity/protocols/oauth2/service-account?hl=pt-br#jwt-auth). O truque é usar segredos longos e seguros ou um algoritmo de assinatura criptograficamente seguro e entender o caso de uso que lhe é apresentado. Os JWTs também não fazem nenhum esforço para criptografar os dados que armazenam e só se preocupam em validar sua autenticidade. Entenda essas compensações e faça sua própria escolha com conhecimento de causa.

Mais informações:

- [paragonie.com/blog/2017/03/jwt-json-web-tokens-is-bad-standard-that-everyone-should-avoid](https://paragonie.com/blog/2017/03/jwt-json-web-tokens-is-bad-standard-that-everyone-should-avoid)
- [https://tiagotavares.io/2020/06/insegurança-em-tokens-jwt/](https://tiagotavares.io/2020/06/inseguran%C3%A7a-em-tokens-jwt/)
- [https://fgodoy.me/2016/05/jwt-hole/#analysis](https://fgodoy.me/2016/05/jwt-hole/#analysis)
- [kevin.burke.dev/kevin/things-to-use-instead-of-jwt](https://kevin.burke.dev/kevin/things-to-use-instead-of-jwt/)
