# Web Servers e Networking em Go

# Um servidor TCP

Implementação e explicação de um servidor e cliente TCP em Go.

## Um aplicativo cliente-servidor

Desenvolveremos uma aplicação cliente-servidor simples usando o [protocolo TCP](https://pt.wikipedia.org/wiki/Protocolo_de_controle_de_transmiss%C3%A3o) e o paradigma [goroutine](https://aprendagolang.com.br/2021/11/19/o-que-sao-e-como-funcionam-as-goroutines/). Uma aplicação de servidor (web) precisa responder a solicitações de muitos clientes simultaneamente. No Go, para cada solicitação do cliente, uma goroutine é gerada para lidar com a solicitação. Precisaremos do pacote [net](https://pkg.go.dev/net) para funcionalidade de comunicação em rede. Ele contém métodos para trabalhar com protocolos **TCP/IP** e **UDP**, resolução de nomes de domínio e assim por diante.

### O lado do servidor

Código do servidor:

```go
package main
import (
"fmt"
"net"
)

func main() {
  fmt.Println("Iniciando o servidor ...")
  // criar listener:
  listener, err := net.Listen("tcp", "localhost:50000")
  if err != nil {
    fmt.Println("Erro listener", err.Error())
    return // encerra o programa
  }
  // escuta e aceita conexões de clientes:
  for {
    conn, err := listener.Accept()
    if err != nil {
      return // encerra o programa
    }
    go fazerLeituraDados(conn)
  }
}

func fazerLeituraDados(conn net.Conn) {
  for {
    buf := make([]byte, 512)
    _, err := conn.Read(buf)
    if err != nil {
      fmt.Println("erro de leitura", err.Error())
      return // encerra o programa
    }
    fmt.Printf("Dados recebidos: %v", string(buf))
  }
}
```

Em `main()`, criamos uma variável listener `net.Listener`, que é a função básica de um servidor: ouvir e aceitar solicitações de clientes que chegam (no endereço IP **0.0.0.0** na porta **3001** por meio do protocolo TCP). Essa função `Listen()` pode retornar uma variável `err` do tipo erro. A espera por solicitações de clientes é realizada em um loop for infinito com `listener.Accept()`. Uma solicitação de cliente cria uma variável de conexão `conn` do tipo `net.Conn`. Nessa conexão, é iniciada uma goroutine separada `fazerLeituraDados()` que lê os dados recebidos em um [buffer](https://pkg.go.dev/bytes#Buffer) de 512 bytes e os envia para o terminal do servidor; quando todos os dados do cliente tiverem sido lidos, a goroutine para. Para cada cliente, é criada uma goroutine separada. O código do servidor deve ser executado antes que qualquer cliente possa ser executado.

Código do cliente em uma pasta e arquivo separado:

```go
package main
import (
"fmt"
"os"
"net"
"bufio"
"strings"
)

func main() {
    // abre conexão:
    conn, err := net.Dial("tcp", ":3001")
    if err != nil {
        // Nenhuma conexão pôde ser feita porque a máquina de destino a recusou.
        fmt.Println("Erro dialing", err.Error())
        return // encerra o programa
    }
    inputReader := bufio.NewReader(os.Stdin)
    fmt.Println("Qual é o seu nome?")
    clientName, _ := inputReader.ReadString('\n')
    // fmt.Printf("CLIENTNAME %s",clientName)
    trimmedClient := strings.Trim(clientName, "\r\n") // "\r\n" no Windows, "\n" no Linux
    // envie informações para o servidor até sair:
    for {
        fmt.Println("O que enviar para o servidor? Digite Q para sair.")
        input, _ := inputReader.ReadString('\n')
        trimmedInput := strings.Trim(input, "\r\n")
        // fmt.Printf("input:--%s--",input)
        // fmt.Printf("trimmedInput:--%s--",trimmedInput)
        if trimmedInput == "Q" {
            return
        }
        _, err = conn.Write([]byte(trimmedClient + " diz: " + trimmedInput+"\n"))
    }
}
```

Inicie o servidor com: `go run server.go`. Em seguida, abra 2 ou 3 janelas de console separadas. Em cada janela, um processo de cliente pode ser iniciado com: `go run client.go`.

Obviamente, o servidor deve ser iniciado primeiro. Se ele não estiver escutando, não poderá ser discado por um cliente. Se um processo cliente for iniciado sem que o servidor esteja escutando, o cliente será interrompido com a seguinte mensagem de erro: `Erro dialing dial tcp :3001: connect: connection refused`

Inicie o servidor com: `go run server/server.go`. Em seguida, abra 2 ou 3 janelas de console separadas. Em cada janela, um processo de cliente pode ser iniciado com: `go run client/client.go`.

A seguinte mensagem é exibida no console: Iniciando o servidor ... Esse processo pode ser interrompido com CTRL/C. O erro: `Erro listening listen tcp :3001: bind: Only one usage of each socket address (protocol/network address/port) is normally permitted` significa que a porta 3001 já está ocupada em seu sistema. Altere o número da porta para 3002, por exemplo, e inicie o servidor novamente.

Aqui estão alguns resultados do servidor:

```bash
Iniciando o servidor ...
Dados recebidos: Rian disse: Hello World
```

O cliente estabelece uma conexão com o servidor por meio do `net.Dial`. Ele recebe entrada do teclado `os.Stdin` em um loop infinito até que Q seja inserido. Observe o corte de **\n** e **\r** (ambos necessários apenas no Windows). A entrada cortada é então transmitida para o servidor por meio do método `Write` da conexão.

Quando um cliente entra em Q e para, o servidor emite a seguinte mensagem: `Error reading read EOF`.

A função `net.Dial` é uma das funções mais importantes da rede. Quando você disca para um sistema remoto, a função retorna um tipo de interface `Conn`, que pode ser usado para enviar e receber informações. A função `Dial` abstrai perfeitamente a família de rede e o transporte. Portanto, `IPv4` ou `IPv6`, `TCP` ou `UDP` podem compartilhar uma interface comum. A discagem de um sistema remoto na porta 80 sobre TCP, depois UDP e, por último, TCP sobre IPv6 tem a seguinte aparência:

```go
package main
import (
  "fmt"
  "net"
  "os"
)

func main() {
  conn, err:= net.Dial("tcp", "192.0.32.10:80") // tcp ipv4
  checkConnection(conn, err)
  conn, err = net.Dial("udp", "192.0.32.10:80") // udp
  checkConnection(conn, err)
  conn, err = net.Dial("tcp", "[2620:0:2d0:200::10]:80") // tcp ipv6
  checkConnection(conn, err)
}

func checkConnection(conn net.Conn, err error) {
  if err!= nil {
    fmt.Printf("erro conectando: %v")
    os.Exit(1)
  }
  fmt.Println("A conexão é feita com %v", conn)
}
```

O programa a seguir é outra ilustração do uso do pacote `net` para abrir, gravar e ler em um `[soquete](https://pt.wikipedia.org/wiki/Soquete_de_rede)`:

```go
package main
import (
"fmt"
"net"
"io"
)

func main() {
  var (
    host = "www.apache.org"
    port = "80"
    remote = host + ":" + port
    msg string = "GET / \n"
    data = make([]uint8, 4096)
    read = true
    count = 0
  )
  // crie o soquete
  con, err := net.Dial("tcp", remote)
  // envie nossa mensagem. uma solicitação HTTP GET neste caso
  io.WriteString(con, msg)
  // ler a resposta do servidor web
  for read {
    count, err = con.Read(data)
    read = (err == nil)
    fmt.Printf(string(data[0:count]))
  }
  con.Close()
}
```

# Um servidor Web simples

O HTTP é um protocolo de nível superior ao TCP e descreve como um servidor da Web se comunica com os navegadores clientes. Especificamente para essa finalidade, o Go tem o pacote `net/http`, que exploraremos agora.

Vamos escrever um servidor Web **Hello world!** Importamos `http`, e nosso servidor Web é iniciado com a função `http.ListenAndServe("0.0.0.0:3000", nil)`, que retorna nil se tudo estiver OK ou um erro caso contrário (0.0.0.0 pode ser omitido do endereço, 3000 é o número da porta escolhida).

Um endereço da Web é representado pelo tipo `http.URL`, que tem um campo `Path` que contém o `URL` como uma cadeia de caracteres. As solicitações de clientes são descritas pelo tipo `http.Request`, que tem um campo URL.

Se a solicitação `req` for um POST de um formulário Html e `var1` for o nome de um campo de entrada Html nesse formulário, o valor inserido pelo usuário poderá ser capturado com: `req.FormValue("var1")`. Isso também funciona quando `var1` é um parâmetro fornecido por meio do URL: `http://localhost:3000/?var1=valor`. Uma alternativa é chamar primeiro `request.ParseForm()`, e o valor pode ser recuperado como o primeiro parâmetro de retorno de `request.Form["var1"]`, como em:

```go
var1, found := request.Form["var1"]
```

O segundo parâmetro `found` é então verdadeiro. Se `var1` não estiver no formulário, `found` se tornará falso. O campo `Form` é, do tipo `map[string][]string`. O servidor Web envia um `http.Response`, cuja saída é enviada em um objeto `http.ResponseWriter`. Esse objeto reúne a resposta do servidor HTTP; ao escrever nele, enviamos dados ao cliente HTTP. Agora ainda temos que programar o que o servidor Web deve fazer, como ele lida com uma solicitação. Isso é feito por meio da função `http.HandleFunc`. Neste exemplo, ele diz que, se a raiz "/" for solicitada (ou qualquer outro endereço nesse servidor), a função `HelloServer` será chamada. Essa função é do tipo `http.HandlerFunc` e, na maioria das vezes, é denominada `Prefhandler` com algum prefixo `Pref`. A `http.HandleFunc` registra uma função de manipulador (aqui `HelloServer`) para solicitações recebidas em /. O / pode ser substituído por URLs mais específicos, como **/create**, **/edit** e assim por diante; para cada URL específico, você pode definir a função de manipulador correspondente.

Essa função tem como segundo parâmetro a solicitação `req`. Seu primeiro parâmetro é o `ResponseWriter`, no qual ele grava uma string composta por `Hello` e `r.URL.Path[1:]`. O `[1:]` à direita significa criar uma *slice* do Path do primeiro caractere até o final. Ele elimina o / inicial do nome do caminho. Isso e feito com a função `fmt.Fprintf()`; outra possibilidade é `io.WriteString(w, "hello, world!\n")`

O primeiro parâmetro é um caminho solicitado e o segundo parâmetro é uma referência a uma função a ser chamada quando o caminho for solicitado.

```go
package main
import (
"fmt"
"net/http"
"log"
)

func HelloServer(w http.ResponseWriter, req *http.Request) {
  fmt.Println("Dentro do handler HelloSever")
  fmt.Fprint(w, "Hello, " + req.URL.Path[1:])
}

func main() {
  http.HandleFunc("/", HelloServer)
  err := http.ListenAndServe("localhost:8080", nil)
  if err != nil {
    log.Fatal("erro ListenAndServe: ", err.Error())
  }
}
```

abra seu navegador com o endereço (URL): [http://localhost:8080](http://localhost:8080/) e acrescente **/world** no final do URL. Após recarregar, na janela do navegador aparece o texto: **Hello, world**

# Polling e leitura de páginas da Web

Como verificar o status de um site com o Go e como ler uma página na Web.

## Introdução

Enviar a um site uma solicitação muito simples e ver como o site responde é conhecido como [**polling**](https://en.wikipedia.org/wiki/Polling_(computer_science)) de um site.

## Explicação

Considere o exemplo a seguir, em que uma solicitação inclui apenas um cabeçalho HTTP.

```go
package main
import (
"fmt"
"net/http"
)

var urls = []string{
  "http://www.google.com/",
  "http://golang.org/",
  "http://blog.golang.org/",
}

func main() {
  // Executa uma solicitação HTTP HEAD para todas as URLs
  // e retorna a string de status HTTP ou uma string de erro.
  for _, url := range urls {
    resp, err := http.Head(url)
    if err != nil {
    fmt.Println("Error:", url, err)
  }
  fmt.Println(url, ": ", resp.Status)
  }
}
```

No programa acima, importamos o pacote `net/http` (veja a **linha 4**). Todos os URLs em uma matriz de strings `urls` (definida na **linha 7**) são pesquisados. Na **linha 16**, iniciamos uma iteração sobre os `urls` com um loop for-range. Na **linha 17**, uma solicitação simples `http.Head()` é enviada a cada url para ver como eles reagem. A assinatura da função é: `func Head(url string) (r *Response, err error)`. Quando há um erro, nós o imprimimos na **linha 19**. Se não houver erro, o `status` de `resp` é impresso na **linha 21**.

```bash
package main
import (
"fmt"
"net/http"
"io/ioutil"
"log"
)

func main() {
  res, err := http.Get("http://www.google.com")
  VerificarErro(err)
  data, err := ioutil.ReadAll(res.Body)
  VerificarErro(err)
  fmt.Printf("Recebeu: %q", string(data))
}

func VerificarErro(err error) {
  if err != nil {
    log.Fatalf("Retornou: %v", err)
  }
}
```

No programa acima, mostramos o conteúdo Html de uma página da Web chamando `http.Get()` na página inicial do Google na **linha 10**. `Get` retorna um resultado e um possível erro. O tratamento de erros aqui é realizado chamando uma função `VerificarErro(err)` na **linha 11**, passando o erro como parâmetro.

Agora, observe o cabeçalho da função `VerificarErro(err error)` na **linha 17**. Quando há um erro (**linha 18**), nós o registramos como fatal, o que interrompe o programa (veja a **linha 19**). Quando o programa chega à **linha 12**, não há erro. A resposta `res` retornada do `Get` tem o conteúdo em um campo `Body`. Lemos esse conteúdo em uma fatia de bytes chamada data com ioutil.ReadAll na **linha 12**. Um possível erro é novamente tratado por `VerificarErro` na **linha 13**. Em seguida, convertemos a fatia em uma string e a imprimimos na **linha 14**.

Outras funções úteis do pacote `http` que usaremos são:

- `http.Redirect(w ResponseWriter, r *Request, url string, code int)`: redireciona o navegador para o url (pode ser um caminho relativo ao caminho da solicitação) e um código de `statuscode`.
- `http.NotFound(w ResponseWriter, r *Request)`: responde à solicitação com um erro HTTP 404 não encontrado.
- `http.Error(w ResponseWriter, error string, code int)`: responde à solicitação com a mensagem de erro e o código HTTP especificados.
- Um campo útil de um objeto `http.Request req é: req.Method`, que é uma cadeia de caracteres que contém **GET** ou **POST**, de acordo com a forma como a página da Web foi solicitada.

Todos os códigos de status HTTP são definidos como Go-constants, por exemplo:

```go
http.StatusContinue = 100
http.StatusOK = 200
http.StatusFound = 302
http.StatusBadRequest = 400
http.StatusUnauthorized = 401
http.StatusForbidden = 403
http.StatusNotFound = 404
http.StatusInternalServerError = 500
```

Você pode definir o header do conteúdo com `w.Header().Set("Content-Type", "../..."),` por exemplo, ao enviar strings Html em um aplicativo da Web, execute `w.Header().Set("Content-Type", "text/html")` antes da saída, mas normalmente isso não é necessário.

# Um aplicativo Web simples

No programa a seguir, da **linha 7** à **linha 10**, vemos como o HTML necessário para nosso formulário da web (que é um formulário de entrada simples com caixa de texto e botão de envio) é armazenado em um formato constante de string multilinha.

O programa inicia um servidor web na porta **3000** na **linha 39**. Ele usa uma instrução `if` combinada para que, sempre que ocorrer um erro, o servidor web pare com uma instrução `panic` (veja a **linha 40**). Antes de iniciar um servidor web, vemos duas chamadas instruções de roteamento:

- `http.HandleFunc("/teste1", SimpleServer)` na linha 37
- `http.HandleFunc("/teste2", FormServer)` na linha 38

Isso significa que sempre que a URL terminar com `/teste1`, o servidor web executará a função `SimpleServer`, e o mesmo para `/teste2` com `FormServer`.

Agora, observe o cabeçalho de `SimpleServer()` na **linha 13**. Ele gera uma string hello world no navegador escrevendo a string HTML correspondente em io com o método `WriteString` na **linha 14**.

Agora, observe o header de `FormServer()` na **linha 19**. O navegador pode solicitar dois métodos HTML diferentes: **GET** e **POST**. O código do `SimpleServer`uma opção (começando na **linha 22**) para distinguir entre as 2 possibilidades. Se esta URL for solicitada inicialmente pelo navegador, então a solicitação possui um método `GET`, e a resposta é a forma constante, conforme indicado na **linha 25**. Ao inserir algo na caixa de texto e clicar no botão, uma solicitação `POST` é emitida. No caso `POST`, o conteúdo da caixa de texto com um nome é recuperado com `request.FormValue("in")`, como você pode ver na **linha 32**, e gravado de volta na página do navegador.

Inicie o programa em um console e abra um navegador com a URL `http://localhost:8088/teste2` (no seu caso a URL será diferente) para testar este programa:

```go
package main
import (
"net/http"
"io"
)

const form = `<html><body><form action="#" method="post" name="bar">
<input type="text" name="in"/>
<input type="submit" value="Submit"/>
</form></html></body>`

/* solicitação get simples */
func SimpleServer(w http.ResponseWriter, request *http.Request) {
  io.WriteString(w, "<h1>hello, world</h1>")
}

/* Processa um formulário, tanto o GET que exibe o formulário
e o POST que o processa.*/
func FormServer(w http.ResponseWriter, request *http.Request) {
  w.Header().Set("Content-Type", "text/html")

  switch request.Method {
    case "GET":
      /* exibir o formulário para o usuário */
      io.WriteString(w, form );
    case "POST":
      /* Processa os dados do formulário, observe que ParseForm deve
       ser chamado antes de podermos extrair dados do formulário com Form */
      io.WriteString(w, request.FormValue("in"))
  }
}

func main() {
  http.HandleFunc("/teste1", SimpleServer)
  http.HandleFunc("/teste2", FormServer)
  if err := http.ListenAndServe(":8088", nil); err != nil {
    panic(err)
  }
}
```

Ao usar uma string para representar o html, é importante incluir o

```html
<html><body>... </html></body>
```

para que o navegador saiba que está recebendo html. Ainda mais seguro é definir o cabeçalho com o tipo de conteúdo text/html no handler:

```go
w.Header().Set("Content-Type", "text/html")
```

O tipo de conteúdo que o navegador acredita que pode ser recuperado com a função:

```go
http.DetectContentType([]byte(form))
```

# **Chamada de procedimento remoto com RPC**

Como o Go permite que duas máquinas diferentes (uma máquina servidor e uma máquina cliente) se comuniquem com outra usando chamadas remotas.

Os programas Go podem se comunicar uns com os outros por meio do pacote `[net/rpc](https://pkg.go.dev/net/rpc)`; portanto, essa é outra aplicação do paradigma cliente-servidor. Ele fornece um meio conveniente de fazer chamadas de função em uma conexão de rede. Obviamente, isso só é útil quando os programas são executados em máquinas diferentes. O pacote `rpc` se baseia no pacote `[gob](https://pkg.go.dev/encoding/gob)` para transformar sua automação de codificação/decodificação em transporte para chamadas de métodos pela rede.

Um servidor registra um objeto, tornando-o visível como um serviço com o nome de tipo do objeto. Isso fornece acesso aos métodos exportados desse objeto em uma rede ou em outra conexão de E/S para clientes remotos. Trata-se da exposição de métodos em tipos em uma rede. O pacote usa o protocolo Http e TCP e o pacote `gob` para o transporte de dados. Um servidor pode registrar vários objetos (serviços) de tipos diferentes, mas é um erro registrar vários objetos do mesmo tipo.

Aqui um exemplo simples:

```go
package main
import (
	"net/http"
	"log"
	"net"
	"net/rpc"
	"time"
	"objectosrpc"
)

func main() {
	calc := new(obejctosrpc.Args)
	rpc.Register(calc)
	rpc.HandleHTTP()
	listener, e := net.Listen("tcp", "localhost:1234")
	if e != nil {
		log.Fatal("erro iniciando RPC-server:", e)
	}
	go http.Serve(listener, nil)
	time.Sleep(1000e9)
}
```

Na pasta objectosrpc:

```go
package objectosrpc

type Args struct {
	N, M int
}

func (t *Args) Multiplicar(args *Args, reply *int) error {
	*reply = args.N * args.M
	return nil
}
```

Esse é o código do lado do servidor. No **main.go**, precisamos do pacote `net/rpc`, que é importado na **linha 6**. Também importamos o pacote `objectosrpc` (consulte a **linha 8**). Na **linha 12**, construímos uma nova instância do tipo `objectosrpc.Args`, chamada `calc`. Na **linha 13**, isso é registrado como um método `rpc`.

A **linha 14** diz ao `rpc` para lidar com o protocolo HTTP. Em seguida, na **linha 15**, criamos um ouvinte de servidor RPC com `net.Listen`. Da **linha 16** à **linha 18**, executamos o tratamento de erros usual. A **linha 19** inicia o servidor RPC em uma goroutine e, em seguida, a rotina `main()` aguarda por um segundo.

`objectosrpc.go` é um pacote que criamos e é importado na **linha 8**. Esse código do lado do servidor define o tipo `Args` como consistindo de dois inteiros exportados `N` e `M` (da **linha 3** à **linha 5**). Ele também define um método `Multiplicar` no Args (da **linha 7** à **linha 10**) que recebe ponteiros para `Args` e a resposta, que é o produto dos inteiros.

Aqui está o código do cliente:

```go
package main
import (
"fmt"
"log"
"net/rpc"
"./rpc_objects"
)

const serverAddress = "localhost"
func main() {
  client, err := rpc.DialHTTP("tcp", serverAddress + ":1234")
  if err != nil {
    log.Fatal("erro rpc.DialHTTP:", err)
  }
  // Chamada síncrona
  args := &rpc_objects.Args{7, 8}
  var reply int
  err = client.Call("Args.Multiplicar", args, &reply)
  if err != nil {
    log.Fatal("Args erro:", err)
  }
  fmt.Printf("Args: %d * %d = %d", args.N, args.M, reply)
}
```

Esse é o código do lado do cliente, que acompanha o código do servidor anterior. O endereço do servidor (pode ser seu nome ou endereço IP) é armazenado na constante `serverAddress` na **linha 9** (para facilitar o teste, usamos localhost aqui).

O computador cliente precisa conhecer a definição do tipo de objeto (`Args`, no nosso caso) e seus métodos (`Multiplicar`, neste exemplo). Ele chama `rpc.DialHTTP()` na **linha 11**, com tratamento de erros da **linha 11** à **linha 14**.

Na **linha 16**, `args := &objetos_rpc.Args{7, 8`}, o cliente inicializa uma estrutura `Args` com os valores **7** e **8**. Quando a conexão do cliente é feita, os métodos remotos podem ser invocados com `client.Call("Tipo.Metodo", args, &reply)`, como feito na linha 18, em que `Tipo.Metodo` aqui se torna `Args.Multiplicar`.

Agora, vamos executar o cliente e o servidor.

Novamente, após o tratamento de erros (da **linha 19** à **linha 21**), a resposta vem do servidor remoto e é exibida na **linha 22**.

Primeiro, inicie o servidor com: `go run server.go` e, em seguida, um cliente em outra janela de console com: `go run client.go`.

O resultado é o seguinte:

```bash
Args: 7 * 8 = 56
```

Essa chamada é síncrona, portanto, ela aguarda o retorno do resultado. Uma chamada assíncrona pode ser feita da seguinte forma:

```bash
call1 := client.Go("Args.Multiplicar", args, &reply, nil)
replyCall := <- call1.Done
```

Se o último argumento tiver um valor nulo, um novo canal será alocado quando a chamada for concluída.

[Creating A Simple Web Server With Golang](https://tutorialedge.net/golang/creating-simple-web-server-with-golang/)

[RPC with Go, what is it?](https://dev.to/iamelesq/rpc-with-go-what-is-it-p41)

[Trabalhando com o protocolo TCP/IP usando Go - iMasters - We are Developers](https://imasters.com.br/back-end/trabalhando-com-o-protocolo-tcpip-usando-go)

[Making HTTP requests in Go](https://tutorialedge.net/golang/making-http-requests-in-go-tutorial/)
