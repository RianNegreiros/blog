# Como funciona o HTTP

O HTTP segue um modelo de solicitação/resposta em que um cliente conectado ao servidor emite uma solicitação e o servidor responde a ela.

Uma mensagem HTTP (seja uma solicitação ou uma resposta) contém várias partes:

- start line
- headers
- body

## Solicitações HTTP

Em uma solicitação, a linha inicial indica o verbo usado pelo cliente, o caminho do recurso desejado e a versão do protocolo que será usada.

```http
GET /api/v2/facts/random HTTP/1.1
```

Nesse caso, o cliente está tentando `GET` o recurso em `/api/v2/facts/random` por meio da versão `1.1` do protocolo.

## Headers

Após a start line, o HTTP nos permite adicionar metadados à mensagem por meio de headers que assumem a forma de pares de valores-chave separados por dois pontos.

```http
GET /api/v2/facts/random HTTP/1.1
Host: uselessfacts.jsph.pl
Accept: */*
Coolness: 9000
```

Nessa solicitação, por exemplo, o cliente anexou três headers adicionais à solicitação: `Host`, `Accept` e `Coolness`.

Os headers não precisam usar nomes específicos e reservados, mas geralmente é recomendável usar os nomes padronizados pela especificação HTTP. Quanto mais você se desviar dos padrões, menos a outra parte na troca o entenderá.

`Cache-Control` é, por exemplo, um cabeçalho usado para definir se uma resposta pode ser armazenada em cache. A maioria dos proxies e proxies reversos entende isso, pois seguem à risca a especificação HTTP. Se você renomeasse o cabeçalho `Cache-Control` para `Awesome-Cache-Control`, os proxies não teriam ideia de como armazenar a resposta em cache, pois não foram criados para seguir a especificação que você acabou de criar.

A especificação HTTP considera vários cenários e criou headers para lidar com uma infinidade de situações. O `Cache-Control` ajuda você a escalonar melhor por meio do armazenamento em cache. `Stale-If-Error` torna seu site disponível mesmo se houver um tempo de inatividade. Esse é um daqueles headers que devem ser muito bem compreendidos, pois pode evitar muitos problemas. `Accept` permite que o cliente negocie qual `Content-Type` é mais adequado para a resposta.

Para obter uma lista completa de headers, recomendo dar uma olhada neste [artigo da Wikipedia](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

## Headers personalizados

Às vezes, pode fazer sentido incluir um header personalizado na mensagem, pois você pode querer adicionar metadados que não fazem parte da especificação HTTP. Um servidor pode optar por incluir informações técnicas em sua resposta para que o cliente possa executar solicitações simultaneamente e obter informações importantes sobre o status do servidor que está respondendo.

Ao usar headers personalizados, é sempre preferível prefixá-los com uma chave para não entrarem em conflito com outros headers que possam se tornar padrão no futuro. Historicamente, isso funcionou até que todos começaram a usar prefixos `X`, que, se tornaram a norma. Os headers `X-Forwarded-For` e `X-Forwarded-Proto` são exemplos de headers personalizados que são amplamente usados e compreendidos por balanceadores de carga e proxies, embora não façam parte do padrão HTTP.

Se você precisar adicionar seu próprio cabeçalho personalizado, geralmente é melhor usar um prefixo de fornecedor, como `Acme-Custom-Header` ou `A-Custom-Header`.

Após os headers, uma solicitação pode conter um body, separado dos headers por uma linha em branco.

```http
POST /api/v2/facts HTTP/1.1
Host: uselessfacts.jsph.pl
Accept: */*
Coolness: 9000

Venus is the only planet that rotates clockwise.
```

Nossa solicitação agora está completa com uma linha inicial (informações de local e protocolo), cabeçalhos e um corpo. Observe que o corpo é totalmente opcional e, geralmente, é usado somente quando queremos enviar dados ao servidor. É por isso que o exemplo acima usou o verbo `POST`.

## Respostas HTTP

Uma resposta não é muito diferente:

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: private, max-age=3600

{ "id": "3b3dd7608388f9341fe1d2878a5c7f5b", "text": "Virginia Woolf wrote all her books standing.", "source": "djtech.net", ... }
```

A primeira informação que a resposta anuncia é a versão do protocolo que ela usa e o status dessa resposta. Os headers seguem o exemplo e, se necessário, uma quebra de linha, seguida pelo corpo.

## A evolução do HTTP

O HTTP passou por duas mudanças semânticas: HTTP/1.0 e HTTP/1.1.

"É o HTTPS e [HTTP2](https://httpwg.org/specs/rfc7540.html) ?"

O HTTPS e o HTTP2 (abrev. H2) são mudanças técnicas, pois introduziram novas maneiras de entregar mensagens pela Internet sem afetar muito a semântica do protocolo.

O HTTPS é uma extensão segura do HTTP. Ele envolve o estabelecimento de um segredo comum entre um cliente e um servidor, garantindo que estamos nos comunicando com a parte correta e criptografando as mensagens trocadas com o segredo comum. Falaremos mais sobre isso posteriormente.

Enquanto o HTTPS tinha como objetivo melhorar a segurança do protocolo HTTP, o H2 tinha como objetivo acelerar o processo. O H2 usa mensagens binárias em vez de mensagens de texto simples, é compatível com multiplexação e usa o algoritmo HPACK para compactar cabeçalhos. Para resumir a história, o H2 foi um aumento de desempenho para o HTTP/1.1.

Os proprietários de sites relutavam em mudar para o HTTPS, pois isso envolvia viagens de ida e volta adicionais entre o cliente e o servidor (como mencionado, um segredo comum precisa ser estabelecido entre as duas partes), tornando a experiência do usuário mais lenta. Com o H2, criptografado por padrão, não há mais desculpas, pois recursos como multiplexação e push de servidor fazem com que ele tenha um [desempenho melhor do que o HTTP/1.1 simples](https://www.troyhunt.com/i-wanna-go-fast-https-massive-speed-advantage/).

## HTTPS

O objetivo do HTTPS (HTTP Secure) é permitir que clientes e servidores conversem com segurança por meio do TLS (Transport Layer Security), o sucessor do SSL (Secure Socket Layer).

O problema que o TLS resolve é bastante simples e pode ser ilustrado com uma metáfora simples: sua cara-metade liga para você no meio do dia, enquanto você está em uma reunião, e pede que você informe a senha da sua conta bancária on-line, pois ela precisa transferir dinheiro para a mensalidade do seu filho. É fundamental que você comunique isso agora mesmo, caso contrário, você enfrentará a possibilidade de seu filho ser rejeitado na escola na manhã seguinte.

Agora você se depara com dois desafios:

- **Autenticação**: garantir que você está realmente falando com a pessoa amada e não com alguém que finge ser ela.

- **Criptografia**: comunicar a senha sem que seus colegas de trabalho consigam entendê-la e anotá-la.

O que você faz? Esse é o problema que o HTTPS tenta resolver.

Neste artigo, vimos o HTTP, sua evolução e como sua extensão segura integra autenticação e criptografia para permitir que clientes e servidores se comuniquem por meio de um canal seguro. Os headers de segurança HTTP oferecem uma maneira de melhorar a postura de segurança da nossa aplicação.
