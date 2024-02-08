# Criando um chatbot com ASP.NET Core 8 e PaLM 2 API

Este artigo o guiará pelo processo de criação de uma API no ASP.NET Core 8, que utiliza a API PaLM 2 para gerar respostas dinâmicas para um aplicativo de chatbot. Vamos nos concentrar na funcionalidade principal; por agora, vamos ignorar as configurações de CORS e Swagger.

## Entendendo o código

O código descreve uma estrutura básica para a nossa API:

- **Configuração do modelo de linguagem**: A chave da API e o URL de base são recuperados a partir de variáveis de ambiente.
- **Definição do Endpoint**: O endpoint `/prompt/{text}` aceita a entrada do usuário como um path parameter.
- **Interação com a API do PaLM 2**: Um payload é construído com o prompt, a temperature e o candidate count, que é enviado como uma solicitação POST para a API do PaLM 2.
- **Tratamento da resposta**: A resposta da API é processada e enviada de volta para o cliente.

## Melhorias

- **Multi-turn dialogues**: Armazena o histórico e o contexto da conversa para personalizar as respostas em todas as mensagens.
- **Intent recognition**: Integrar ferramentas de [NPL](https://aws.amazon.com/what-is/nlp/) para compreender a intenção do utilizador e gerar respostas relevantes.
- **Personalização**: Permitir que os utilizadores ajustem os parâmetros da API, como a temperatura, para diferentes estilos de resposta.
- **Monitorização e registo**: Acompanhe a utilização da API e analise as interacções dos utilizadores para melhorar.
- **Tratamento de erros**: Implemente um tratamento robusto de erros para falhas de API e solicitações inválidas.
- **Segurança**: Considere medidas de segurança como rate limiter e validação de entrada para evitar mensagens problemáticas.

Este é apenas um ponto de partida. Sinta-se à vontade para personalizar e expandir este código para criar uma interface de chatbot poderosa!

## Recursos

- [ASP.NET Core 8 Documentação](https://learn.microsoft.com/en-us/aspnet/core/?view=aspnetcore-8.0)
- [PaLM 2 API Documentação](https://ai.google/discover/palm2/)

Lembre-se, a chave é experimentar, explorar e melhorar constantemente a sua solução de chatbot para proporcionar uma experiência de usuário agradável.

## Código da aplicação

Aqui está o código da aplicação no qual esta postagem se baseia:

```csharp
using System.Text;
using System.Text.Json;
using Microsoft.OpenApi.Models;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddEndpointsApiExplorer();

var app = builder.Build();

// Configure the HTTP request pipeline.
app.UseHttpsRedirection();

app.MapGet("/prompt/{text}", async (string text, HttpContext httpContext) =>
{
    var languageModelApiKey = app.Configuration["LANGUAGE_MODEL_API_KEY"];
    var languageModelUrl = $"https://generativelanguage.googleapis.com/v1beta1/models/chat-bison-001:generateMessage?key={languageModelApiKey}";

    var payload = new
    {
        prompt = new { messages = new[] { new { content = text } } },
        temperature = 0.1,
        candidate_count = 1
    };

    using var httpClient = new HttpClient();
    var content = new StringContent(JsonSerializer.Serialize(payload), Encoding.UTF8, "application/json");
    var response = await httpClient.PostAsync(languageModelUrl, content);
    var data = await response.Content.ReadAsStringAsync();

    Console.WriteLine(data);
    await httpContext.Response.WriteAsync(data);
})
.WithName("Generate Language Model Response")
.WithSummary("Return a Language Model Response")
.WithDescription("Return a Language Model Response from PaLM 2 API")
.WithOpenApi(generatedOperation =>
{
    var parameter = generatedOperation.Parameters[0];
    parameter.Description = "The text to be processed by the language model";
    return generatedOperation;
})
.Produces(StatusCodes.Status200OK)
.Produces(StatusCodes.Status400BadRequest)
.Produces(StatusCodes.Status500InternalServerError);

app.Run();
```

## Integração do front-end

Para interagir com nossa API de chatbot, criaremos um frontend simples usando Next.js e React. Esse frontend permitirá que os usuários enviem prompts para a API e exibam as respostas geradas.

### Configuração de variáveis de ambiente

Primeiro, precisamos especificar o URL da nossa API em um arquivo `.env`:

```env
NEXT_PUBLIC_API_URL=http://localhost:5111
```

### Chat Component

O `Chat` component é responsável por manipular a entrada do usuário, enviar solicitações à API e exibir o histórico da conversa. Ele usa os hooks `useState` e `useEffect` para gerenciar o estado e os efeitos colaterais.

O input de chat é uma versão modificada do [Flowbite](https://flowbite.com/docs/forms/textarea/#chatroom-input).

```jsx
"use client"

import React, { useEffect, useState } from "react"
import Loading from "./loading";
import ReactMarkdown from "react-markdown";

const API_URL = process.env.NEXT_PUBLIC_API_URL;

type Message = {
  author: string;
  content: string;
}

export default function Chat() {
  const [text, setText] = useState('')
  const [messages, setMessages] = useState<Message[]>([])
  const [isLoading, setIsLoading] = useState(false)

  useEffect(() => {
    const savedMessages = sessionStorage.getItem('messages');
    if (savedMessages) {
      setMessages(JSON.parse(savedMessages));
    }
  }, []);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    setMessages(prevMessages => {
      const newMessages = [...prevMessages, { author: 'User', content: text }];
      sessionStorage.setItem('messages', JSON.stringify(newMessages));
      return newMessages;
    })
    setText('')
    setTimeout(getResponse, 0)
  }

  const getResponse = async () => {
    setIsLoading(true)
    const response = await fetch(`${API_URL}/prompt/${text}`)
    const data = await response.json()
    setMessages(prevMessages => {
      const newMessages = [...prevMessages, { author: 'Bot', content: data.candidates[0].content }];
      sessionStorage.setItem('messages', JSON.stringify(newMessages));
      return newMessages;
    })
    setIsLoading(false)
  }

  return (
    <div className="p-6">
      <h2 className="text-lg lg:text-2xl font-bold text-gray-900 dark:text-white mb-6">Chatbot</h2>
      <div className="border border-gray-200 dark:border-gray-700 dark:bg-gray-900 p-4 rounded-lg mb-4">
        {messages.map((message, index) => (
          <div key={index} className={`mb-4 ${message.author === 'Bot' ? 'text-blue-500' : 'text-green-500'}`}>
            <strong>{message.author}</strong>: <ReactMarkdown>{message.content}</ReactMarkdown>
          </div>
        ))}
      </div>
      {isLoading && <Loading />}
      <form onSubmit={handleSubmit}>
        <label htmlFor="chat" className="sr-only">Your message</label>
        <div className="flex items-center px-3 py-2 rounded-lg bg-gray-50 dark:bg-gray-700">
          <input
            id="chat"
            className="block mx-4 p-2.5 w-full text-sm text-gray-900 bg-white rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-800 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            placeholder="Your message..." value={text} onChange={(e) => setText(e.target.value)}
          />
          <button type="submit" className="inline-flex justify-center p-2 text-blue-600 rounded-full cursor-pointer hover:bg-blue-100 dark:text-blue-500 dark:hover:bg-gray-600">
            <svg className="w-5 h-5 rotate-90 rtl:-rotate-90" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 18 20">
              <path d="m17.914 18.594-8-18a1 1 0 0 0-1.828 0l-8 18a1 1 0 0 0 1.157 1.376L8 18.281V9a1 1 0 0 1 2 0v9.281l6.758 1.689a1 1 0 0 0 1.156-1.376Z" />
            </svg>
            <span className="sr-only">Send message</span>
          </button>
        </div>
      </form>
    </div>
  )
}
```

Estamos usando o package `react-markdown` para formatar as respostas do bot. Isso nos permite incluir a formatação markdown em nossas respostas, o que pode ser útil para exibir dados estruturados, links etc.

### Loading Component

O componente `Loading` exibe uma animação de carregamento enquanto a resposta da API está sendo obtida.

Esse indicador de carregamento é da [Flowbite](https://flowbite.com/docs/components/skeleton/#text-placeholder) com alguns ajustes para combinar com o estilo da aplicação.

```jsx
export default function Loading() {
  return (
    <div role="status" className="p-6 border border-gray-200 dark:border-gray-700 dark:bg-gray-900 rounded-lg mb-4 space-y-2.5 animate-pulse max-w-lg">
      <div className="flex items-center w-full">
        <div className="h-2.5 bg-gray-200 rounded-full dark:bg-gray-700 w-32"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-24"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
      </div>
      <div className="flex items-center w-full max-w-[480px]">
        <div className="h-2.5 bg-gray-200 rounded-full dark:bg-gray-700 w-full"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-24"></div>
      </div>
      <div className="flex items-center w-full max-w-[400px]">
        <div className="h-2.5 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
        <div className="h-2.5 ms-2 bg-gray-200 rounded-full dark:bg-gray-700 w-80"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
      </div>
      <div className="flex items-center w-full max-w-[480px]">
        <div className="h-2.5 ms-2 bg-gray-200 rounded-full dark:bg-gray-700 w-full"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-24"></div>
      </div>
      <div className="flex items-center w-full max-w-[440px]">
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-32"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-24"></div>
        <div className="h-2.5 ms-2 bg-gray-200 rounded-full dark:bg-gray-700 w-full"></div>
      </div>
      <div className="flex items-center w-full max-w-[360px]">
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
        <div className="h-2.5 ms-2 bg-gray-200 rounded-full dark:bg-gray-700 w-80"></div>
        <div className="h-2.5 ms-2 bg-gray-300 rounded-full dark:bg-gray-600 w-full"></div>
      </div>
      <span className="sr-only">Loading...</span>
    </div>
  )
}
```

### Main Page

Por fim, incluímos o componente `Chat` em nossa página principal:

```jsx
import Chat from "./components/chat";

export default function Home() {
  return (
    <Chat />
  );
}
```

mas antes de podermos acessar nosso cliente em `localhost:3000`

### Configuração de CORS

O CORS é uma medida de segurança que permite ou nega que solicitações provenientes de origens diferentes acessem recursos de um servidor. É fundamental configurar o CORS em seu aplicativo para controlar quem pode interagir com seu servidor.

Agora, vamos ver duas maneiras de configurar o CORS na sua aplicação:

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy(name: "AllowLocalClient", policy =>
    policy.WithOrigins(builder.Configuration["Client_Url"] ?? "http://localhost:3000")
    .AllowAnyHeader()
    .WithMethods("GET"));
});

var app = builder.Build();

app.UseCors("AllowLocalClient");

app.Run();
```

No código acima, estamos especificando uma origem específica (`http://localhost:3000`) que tem permissão para acessar o servidor. Esse método é mais seguro, pois restringe o acesso a uma origem específica. O `builder.Configuration["Client_Url"]` é uma forma de obter a URL do cliente a partir de variáveis de ambiente, o que é uma prática mais segura do que colocar direto no código.

```csharp
builder.Services.AddCors(options =>
{
    options.AddDefaultPolicy(
        builder =>
        {
            builder.AllowAnyOrigin()
                   .AllowAnyHeader()
                   .AllowAnyMethod();
        });
});

app.UseCors();
```

No segundo trecho de código, estamos permitindo que qualquer origem, qualquer cabeçalho e qualquer método acesse o servidor. Essa é uma configuração mais genérica e pode expor seu servidor a solicitações indesejadas, portanto, use-a com cautela.

Lembre-se de que o CORS configurado incorretamente pode levar a erros e possíveis riscos de segurança. Se você encontrar problemas, verifique sua configuração de CORS e certifique-se de que as origens, os cabeçalhos e os métodos corretos sejam permitidos.

É isso aí! Agora temos uma interface de bate-papo simples para interagir com nossa API de chatbot.

---

- [Repositório](https://github.com/RianNegreiros/DotNetChatBot)
