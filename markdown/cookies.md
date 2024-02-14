# HTTP Cookies

## O que são cookies?

Imagine que é um programador back-end que precisa de implementar sessões numa aplicação. A primeira coisa que lhe vem à cabeça é emitir um token para os clientes e pedir-lhes que enviem esse token com os seus pedidos subsequentes. A partir daí, será capaz de identificar os clientes com base no token incluído no seu pedido.

Os cookies HTTP nasceram para normalizar este tipo de mecanismo nos navegadores. Não são mais do que uma forma de armazenar dados enviados pelo servidor e enviá-los juntamente com futuros pedidos. O servidor envia um cookie, que contém pequenos pedaços de dados, o navegador armazena-os e envia-os juntamente com futuros pedidos ao mesmo servidor.

A segurança dos cookies é um dos aspectos mais importantes na implementação de sessões na Web. Este texto irá, portanto, dar-lhe uma melhor compreensão dos cookies, como protegê-los e que alternativas podem ser utilizadas.

## O que está por detrás de um Cookie?

Um servidor pode enviar um cookie utilizando o header Set-Cookie.

```bash
HTTP/1.1 200 Ok
Set-Cookie: access_token=1234
...
```

Um cliente armazena então estes dados e envia-os em requests subsequentes através do header Cookie.

```bash
GET / HTTP/1.1
Host: example.com
Cookie: access_token=1234
...
```

Note que os servidores podem enviar vários cookies de uma só vez,

```bash
HTTP/1.1 200 Ok
Set-Cookie: access_token=1234
Set-Cookie: user_id=10
...
```

e os clientes podem fazer o mesmo no request.

```bash
GET / HTTP/1.1
Host: example.com
Cookie: access_token=1234; user_id=10
...
```

Para além da chave e do valor simples, os cookies podem conter diretivas adicionais que limitam o seu tempo de vida e o seu contexto.

### `Expires`

Especifica quando um cookie deve expirar, para que os navegadores não o armazenem e transmitam indefinidamente. Um exemplo claro é um ID de sessão, que normalmente expira após algum tempo. Esta diretiva é expressa como uma data na forma de Date: `<nome-do-dia>, <dia> <mês> <ano> <hora>:<minuto>:<segundo> GMT`, como `Date: Fri, 24 Aug 2018 04:33:00 GMT`. Aqui está um exemplo completo de um cookie: `access_token=1234;Expires=Fri, 24 Aug 2018 04:33:00 GMT`

### `Max-Age`

Semelhante à diretiva `Expires`, `Max-Age` especifica o número de segundos até o cookie expirar. Um cookie que deve durar uma hora teria o seguinte formato: `access_token=1234;Max-Age=3600`

### `Domain`

Esta diretiva define os anfitriões para os quais o cookie deve ser enviado. Lembre-se de que os cookies contêm geralmente dados confidenciais, sendo importante que os navegadores não os divulguem para hosts não confiáveis. Um cookie com a diretiva `Domain=trusted.example.com` não será enviado juntamente com pedidos para qualquer domínio que não seja `trusted.example.com`, nem mesmo o domínio raiz, `example.com`. Eis um exemplo válido de um cookie limitado a um subdomínio específico: `access_token=1234;Domain=trusted.example.com`

### Path

Path é semelhante à diretiva `Domain`, mas aplica-se ao caminho do URL (`/some/path`). Esta diretiva impede que um cookie seja partilhado com caminhos não confiáveis, como no seguinte exemplo: `access_token=1234;Path=/trusted/path.`

## Cookies de sessão e persistentes

### Cookies de sessão

Quando um servidor envia um biscoito sem definir o seu `Expires` ou `Max-Age`, os navegadores tratam-no como um biscoito de sessão. Em vez de adivinhar o seu tempo de vida ou aplicar uma heurística engraçada, o navegador apaga-o quando se desliga.

### Cookies persistentes

Um cookie persistente, pelo contrário, é armazenado no cliente até ao prazo definido pelas suas diretivas `Expires` ou `Max-Age`.

### Restauração de sessões

Vale a pena notar que os navegadores podem empregar um mecanismo conhecido como restauração de sessão, em que os cookies de sessão podem ser recuperados após o cliente se desligar. Os navegadores implementaram este tipo de mecanismo para permitir que os utilizadores retomem uma sessão após uma queda.

A restauração de sessão pode levar a [problemas inesperados](https://stackoverflow.com/questions/777767/firefox-session-cookies) se estivermos à espera que os cookies de sessão expirem em um determinado período (por exemplo, temos a certeza de que uma sessão não duraria mais do que X tempo). Na perspetiva de um navegador, a restauração da sessão é um recurso perfeitamente válido, uma vez que esses cookies são deixados nas mãos do cliente, sem uma data de validade.

O que o cliente faz com esses cookies não afeta o servidor, que não consegue deletar se o cliente se fecha em qualquer altura. Se o cliente quiser manter os biscoitos de sessão vivos para sempre, isso não é motivo de preocupação para o servidor. Seria definitivamente uma implementação questionável, mas não há nada que o servidor possa fazer em relação a isso.

### Cookies de sessão vs cookies persistentes

Não creio que haja um vencedor claro entre cookies de sessão e persistentes, uma vez que ambos podem servir objetivos diferentes muito bem. O que observei, no entanto, é que o Facebook, o Google e serviços semelhantes utilizam cookies persistentes.

Em alguns contextos, pode ser necessário utilizar cookies de sessão devido a requisitos de conformidade. Vi auditores a pedirem para converter todos os cookies persistentes em cookies de sessão. Quando as pessoas me perguntam "devo usar X ou Y?", a minha resposta é "depende do contexto". A construção de um livro de visitas para o seu blog tem diferentes ramificações de segurança do que a construção de um sistema bancário. Como veremos mais adiante no curso, eu recomendaria entender seu contexto e tentar construir um sistema que seja seguro o suficiente: A segurança absoluta é uma utopia, tal como um SLA a 100%.

### ****Host-only****

Quando um servidor não inclui uma diretiva `Domain`, o cookie deve ser considerado como `host-only`, o que significa que a sua validade se restringe apenas ao domínio atual. Trata-se de uma espécie de comportamento padrão dos navegadores quando recebem um cookie que não tem um `Domain` definido.

### ****Supercookies****

E se conseguíssemos definir um cookie num domínio de topo ([TLD](https://pt.wikipedia.org/wiki/Dom%C3%ADnio_de_topo)) como `.com` ou `.org`? Isso seria um enorme problema de segurança, por duas razões:

- **privacidade do utilizador**: todos os sítios Web que funcionassem nesse TLD específico poderiam seguir as informações sobre o utilizador no armazenamento partilhado
- **fuga de informação**: um servidor poderia armazenar por engano um dado sensível num cookie disponível para outros sítios

Pelas razões acima, os TLD-cookies, também conhecidos como supercookies, são desativados pelos navegadores Web. Se tentar definir um supercookie, o navegador simplesmente se recusa.

### Encriptação

Os cookies contêm informações muito sensíveis. Se os atacantes conseguirem obter um ID de sessão, podem fazer-se passar por utilizadores, [sequestrando as suas sessões](https://pt.wikipedia.org/wiki/Session_hijacking).

A maioria dos ataques de sequestro de sessão acontece normalmente por um *[man-in-the-middle](https://pt.wikipedia.org/wiki/Ataque_man-in-the-middle)* que pode ouvir o tráfego não encriptado entre o cliente e o servidor e roubar qualquer informação que tenha sido trocada. Se um cookie for trocado via HTTP, é vulnerável a ataques MITM e ao sequestro de sessão.

Para ultrapassar este problema, podemos utilizar HTTPS quando emitimos o cookie e adicionar-lhe o sinalizador `Secure`. Isto dá instruções aos navegadores para nunca enviarem este cookie em pedidos HTTP simples.

Marca cookies sensíveis como `Secure` é um aspecto incrivelmente importante da segurança dos cookies. Mesmo que sirva todo o seu tráfego para HTTPS, os atacantes podem encontrar uma forma de criar uma página HTTP antiga sob o seu domínio e redirecionar os utilizadores para lá.

### HttpOnly

Considerando que você pode ler o conteúdo do pote de cookies com um simples `document.cookie`, proteger nossos cookies de acesso não confiável ao JavaScript é um aspeto muito importante do fortalecimento dos cookies do ponto de vista da segurança.

Felizmente, a especificação HTTP cuidou disso com a flag `HttpOnly`. Ao utilizar esta diretiva, podemos instruir o navegador para não partilhar o cookie com o JavaScript. O navegador remove então o cookie da variável `window.cookie`, impossibilitando aceder ao cookie via JS.

O sinalizador HttpOnly ajuda a atenuar os ataques XSS, negando o acesso a informações críticas armazenadas num cookie: a sua utilização dificulta o sequestro de uma sessão por parte de um atacante.

<aside>
💡 Como contornar o `HttpOnly`

Em 2003, os investigadores descobriram uma vulnerabilidade interessante em torno do sinalizador `HttpOnly`, o [Cross-Site Tracing](https://owasp.org/www-community/attacks/Cross_Site_Tracing) (XST).

Resumidamente, os navegadores não impediam o acesso aos cookies `HttpOnly` quando utilizavam o método de pedido `TRACE`. Embora a maioria dos navegadores já tenha desativado este método, a minha recomendação seria desativar o `TRACE` ao nível do seu servidor Web, devolvendo o código de estado `405 Not allowed`

</aside>

### ****SameSite****

Introduzido pelo Google Chrome v51, este sinalizador elimina efetivamente a falsificação de pedidos entre sites (CSRF) da Web. `SameSite` é uma inovação simples, mas inovadora, uma vez que as soluções anteriores para ataques CSRF eram incompletas ou demasiado pesadas para os proprietários de sites.

Para entender o `SameSite`, precisamos primeiro dar uma olhada na vulnerabilidade que ele neutraliza. Um CSRF é um pedido não desejado feito pelo site Ao site B enquanto o utilizador está autenticado no site B.

Parece complicado? Deixe-me reformular: suponha que está ligado ao seu sítio Web bancário, com um mecanismo para transferir dinheiro com base num `<form>` HTML e alguns parâmetros adicionais, como a conta de destino e o montante. Quando o sítio Web recebe um pedido `POST` com esses parâmetros e o seu cookie de sessão, processa a transferência. Agora, suponhamos que um sítio Web malicioso de terceiros configura um formulário HTML como este.

```html
<form action="https://bank.com/transfer" method="POST">
<input type="hidden" name="destination" value="attacker@email.com" />
<input type="hidden" name="amount" value="1000" />
<input type="submit" value="CLICK HERE TO WIN A HUMMER" />
</form>
```

Está a ver onde isto vai parar? Se clicar no botão de envio, disfarçado como um prêmio, serão transferidos $1000 da sua conta. Isto é um [cross-site request forgery](https://pt.wikipedia.org/wiki/Cross-site_request_forgery).

Tradicionalmente, existem duas maneiras de se livrar da CSRF:

### ****Origin and Referrer headers****

O servidor pode verificar se esses cabeçalhos vêm de fontes confiáveis (ou seja, `https://bank.com`). A desvantagem desta abordagem é que, como vimos nos capítulos anteriores, nem a `Origin`, nem o `Referrer` são muito fiáveis e podem ser desativados pelo cliente para proteger a privacidade do utilizador.

### Tokens CSRF

O servidor pode incluir um token assinado no formulário e verificar a sua validade assim que o formulário for submetido. Esta é uma abordagem geralmente sólida e tem sido a melhor prática recomendada durante anos. A desvantagem dos tokens CSRF é o facto de serem um fardo técnico para o backend, uma vez que teria de integrar a geração e validação de tokens na sua aplicação Web: isto pode não parecer uma tarefa complicada, mas uma solução mais simples seria mais do que bem-vinda.

Os cookies `SameSite` procuram substituir as soluções acima mencionadas de uma vez por todas. Quando marca um cookie com este sinalizador, diz ao navegador para não incluir o cookie em pedidos gerados por diferentes origens. Quando o navegador inicia um pedido ao seu servidor e um cookie é marcado como `SameSite`, o navegador verifica primeiro se a origem do pedido é a mesma origem que emitiu o cookie. Se não for, o navegador não incluirá o cookie no pedido.

Esta flag tem duas variantes principais, `Lax` e `Strict`. O nosso exemplo utiliza a variante anterior, uma vez que permite a navegação ao mais alto nível num sítio web para incluir o cookie; quando se marca um cookie como `SameSite=Strict`, em vez disso, o navegador não envia o cookie com qualquer pedido entre origens, incluindo a navegação ao mais alto nível: isto significa que se clicar numa ligação para um sítio web que utiliza cookies rigorosos, não terá qualquer sessão iniciada - uma quantidade extremamente elevada de proteção que, por outro lado, pode surpreender os utilizadores. O modo `Lax` permite que estes cookies sejam enviados por pedidos utilizando métodos seguros (como `GET`), criando uma combinação muito útil entre segurança e experiência do usuário.

A última variante desta flag, `None` é uma opção que pode ser usada para desabilitar completamente a funcionalidade `SameSite` em um cookie. Isso é importante porque, ao não especificar uma política `SameSite` para um cookie, os navegadores modernos começaram a aumentar suas medidas de segurança. Por exemplo, o Chrome 80, lançado no primeiro trimestre de 2020, passou a aplicar uma política `SameSite=Lax` por padrão se um cookie não tiver uma política definida. O Firefox também seguiu esse caminho em busca de maior segurança.

No entanto, para contornar essas políticas predefinidas de `SameSite` e permitir que um cookie seja compartilhado em um contexto mais amplo, é necessário usar a flag `SameSite=None`. É importante observar que os navegadores agora [rejeitarão cookies que não utilizem a política SameSite, a menos que sejam explicitamente marcados como](https://chromestatus.com/feature/5633521622188032) `Secure`.

Citando Scott Helme, “[CSRF is (really) dead](https://scotthelme.co.uk/csrf-is-really-dead/)”(CSRF está (realmente) morto).

<aside>
🔑 Cookies flags são importantes

Vamos recapitular o que aprendemos sobre os sinalizadores de cookies, uma vez que são cruciais quando está a armazenar ou a permitir o acesso a dados sensíveis através deles, o que é uma prática muito comum:

- Marcar os cookies como `Secure` garante que não serão enviados por pedidos não encriptados, tornando os ataques man-in-the-middle bastante inúteis.
- Com a flag `HttpOnly` dizemos ao navegador para não partilhar o cookie com o cliente (por exemplo, permitindo o acesso do JavaScript ao cookie), limitando um ataque XSS.
- Marcar o cookie como `SameSite=Lax|Strict` impedirá que o navegador o envie em pedidos de origem cruzada, tornando ineficaz qualquer tipo de ataque CSRF. É importante notar que ainda existem vulnerabilidades CSRF de baixo risco que podem afetar a sua aplicação, como o [login CSRF](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.md#login-csrf).

</aside>

## Alternativas

Os cookies são a sua melhor aposta se quiser implementar algum tipo de mecanismo de sessão sobre HTTP. Mas vamos avaliar alternativas aos cookies, vou tentar resumir dois frequentemente mencionados:

### localStorage

Especialmente no contexto de aplicações de página única (SPA), o localStorage é por vezes mencionado quando se discute onde armazenar tokens sensíveis. O problema com esta abordagem, no entanto, é que o localStorage não oferece qualquer tipo de proteção contra ataques XSS. Se um atacante for capaz de executar um simples `localStorage.getItem('token')` no navegador de uma vítima, o jogo acaba. Os cookies `HttpOnly` ultrapassam facilmente este problema.

### JWT

Os JSON Web Tokens definem uma forma de trocar dados de forma segura entre dois clientes, sob a forma de um token. O JWT é uma especificação que define o aspeto de um token de acesso, mas não define onde o token será armazenado. Por outras palavras, é possível armazenar um JWT num cookie, num localStorage ou mesmo na memória, pelo que não faz sentido considerar os JWT uma alternativa aos cookies.

## Em Resumo

Vimos por que os cookies foram criados, como são estruturados e como pode protegê-los aplicando algumas restrições aos seus atributos `Domain`, `Expires`, `Max-Age` e `Path`, e como outros flags, como `Secure`, `HttpOnly` e `SameSite`, são vitais para proteger os cookies.

Alguns recursos desenvolvedores que querem aprender mais sobre boas práticas de cibersegurança no desenvolvimento web:

[Cybersecurity for Everyone](https://www.coursera.org/learn/cybersecurity-for-everyone)

[Application Security for Developers](https://www.edx.org/learn/computer-programming/ibm-application-security-for-developers)

[Principles of Secure Coding](https://www.coursera.org/learn/secure-coding-principles)
