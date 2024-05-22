# Criando um chatbot com ASP.NET Core, Next.js e API PaLM

![chatbot-demo.gif](/assets/chatbot-demo.gif)

Vamos analisar o código passo a passo para entender como ele funciona e o que cada parte faz.

### Estrutura do projeto

O projeto está estruturado da seguinte forma:

```
├── ChatBot.sln
├── compose.yaml
└── src
    ├── API
    │   ├── Dockerfile
    │   ├── Extensions
    │   │   ├── BuilderExtensions.cs
    │   │   └── GoogleCloudLanguageApiHealthCheck.cs
    │   ├── Program.cs
    │   ├── Using.cs
    │   ├── appsettings.json
    └── client
        ├── Dockerfile
        ├── app
        │   ├── components
        │   │   ├── chat.tsx
        │   │   ├── danger-error.tsx
        │   │   └── loading.tsx
        │   ├── hooks
        │   │   └── useChat.ts
        │   ├── layout.tsx
        │   └── page.tsx
```

### Program.cs

Este é o principal ponto de entrada da aplicação. Estabelece a aplicação Web, configura os serviços e define o pipeline de middleware.

```csharp
using System.Text;
using System.Text.Json;
using System.Threading.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddServicesExtension();
builder.Services.AddSwaggerExtension();
builder.Services.AddCorsExtension(builder.Configuration);
builder.Services.AddHealthChecksExtension(builder.Configuration);
builder.Services.AddRateLimiter(options =>
{
    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

    options.AddPolicy("fixed", httpContext =>
            RateLimitPartition.GetFixedWindowLimiter(
                partitionKey: httpContext.Connection.RemoteIpAddress?.ToString(),
                factory: partition => new FixedWindowRateLimiterOptions
                {
                    PermitLimit = 10,
                    Window = TimeSpan.FromSeconds(10)
                }));
});

var app = builder.Build();

// Configure the HTTP request pipeline.
app.UseSwagger();
app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "API for a Chat Bot"));

app.MapHealthChecks("/health");

app.UseHttpsRedirection();

app.UseRateLimiter();

app.MapPost("/prompt/{text}", async (
    string text,
    IHttpClientFactory factory,
    HttpContext httpContext) =>
{
    var languageModelApiKey = app.Configuration["LANGUAGE_MODEL:API_KEY"];
    var languageModelUrl = $"{app.Configuration["LANGUAGE_MODEL:URL"]}?key={languageModelApiKey}";

    var payload = new
    {
        prompt = new { messages = new[] { new { content = text } } },
        temperature = 0.1,
        candidate_count = 1
    };

    try
    {
        using var httpClient = factory.CreateClient();
        var content = new StringContent(JsonSerializer.Serialize(payload), Encoding.UTF8, "application/json");
        var response = await httpClient.PostAsync(languageModelUrl, content);
        var data = await response.Content.ReadAsStringAsync();

        app.Logger.LogInformation($"Response received from the API: {data}");

        await httpContext.Response.WriteAsync(data);
    }
    catch (Exception ex)
    {
        app.Logger.LogError(ex, "An error occurred while contacting the API.");
    }
})
.WithName("Generate Language Model Response")
.WithSummary("Return a Language Model Response")
.WithDescription("Return a Language Model Response from PaLM API")
.WithOpenApi(generatedOperation =>
{
    var parameter = generatedOperation.Parameters[0];
    parameter.Description = "The text to be processed by the language model";
    return generatedOperation;
})
.Produces(StatusCodes.Status200OK)
.Produces(StatusCodes.Status400BadRequest)
.Produces(StatusCodes.Status500InternalServerError)
.RequireRateLimiting("fixed");

app.UseCors("AllowClient");

app.Run();
```

### Partes Chaves:

1. **Configuração do serviço**:
    - `builder.Services.AddServicesExtension()`: Adiciona serviços personalizados definidos em `BuilderExtensions.cs`.
    - `builder.Services.AddSwaggerExtension()`: Adiciona Swagger para documentação da API.
    - `builder.Services.AddCorsExtension(builder.Configuration)`: Adiciona política CORS.
    - `builder.Services.AddHealthChecksExtension(builder.Configuration)`: Adiciona os health checks.
    - `builder.Services.AddRateLimiter(...)`: Configura o rate limiting para evitar abusos.
2. **Middleware Pipeline**:
    - `app.UseSwagger()`: Habilita o middleware Swagger.
    - `app.UseSwaggerUI(...)`: Configura o Swagger UI.
    - `app.MapHealthChecks(“/health”)`: Mapeia o endpoint de heath check.
    - `app.UseRateLimiter()`: Habilita o middleware de rate limiting.
    - `app.MapPost(“/prompt/{text}”, ...)`: Define um endpoint para gerar uma resposta da API PaLM.

### Extensions/BuilderExtensions.cs

Esse arquivo contém os métodos para adicionar os serviços para o contêiner de injeção de dependência

```csharp
using Microsoft.OpenApi.Models;

namespace API.Extensions;

public static class BuilderExtensions
{
    public static void AddSwaggerExtension(this IServiceCollection services)
    {
        services.AddSwaggerGen(options => options.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "Chat bot API",
            Description = "A ASP.NET Core 8 minimal API to generate messages for a chat bot using PaLM 2 API",
        }));
    }

    public static void AddCorsExtension(this IServiceCollection services, IConfiguration config)
    {
        var clientUrl = config["Client_Url"] ?? "http://localhost:3000";

        services.AddCors(options => options.AddPolicy(name: "AllowClient", policy =>
        policy.WithOrigins(clientUrl)
        .AllowAnyHeader()
        .WithMethods("POST")));
    }

    public static void AddHealthChecksExtension(this IServiceCollection services, IConfiguration config)
    {
        services.AddHttpClient();
        services.AddHealthChecks()
          .AddCheck("google-api", new GoogleColabHealthCheck(services.BuildServiceProvider().GetRequiredService<IHttpClientFactory>(), config, services.BuildServiceProvider().GetRequiredService<ILogger<GoogleColabHealthCheck>>()));
    }

    public static void AddServicesExtension(this IServiceCollection services)
    {
        services.AddHttpClient();
    }
}
```

### Métodos chave

1. **AddSwaggerExtension**: Configura o Swagger para a documentação da API.
2. **AddCorsExtension**: Configura o CORS para permitir requisições dos clientes especificadas.
3. **AddHealthChecksExtension**: Adiciona verificações de integridade, incluindo uma verificação personalizada para a API do Google.
4. **AddServicesExtension**: Adiciona serviços de cliente HTTP.

### Extensions/GoogleCloudLanguageApiHealthCheck.cs

Este arquivo define um controller de health check personalizado para a API do Google.

```csharp
using System.Text;

using Microsoft.Extensions.Diagnostics.HealthChecks;

namespace API.Extensions;

public class GoogleColabHealthCheck(IHttpClientFactory httpClientFactory, IConfiguration configuration, ILogger<GoogleColabHealthCheck> logger) : IHealthCheck
{
    private readonly IHttpClientFactory _httpClientFactory = httpClientFactory;
    private readonly IConfiguration _configuration = configuration;
    private readonly ILogger<GoogleColabHealthCheck> _logger = logger;

    public async Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, CancellationToken cancellationToken)
    {
        try
        {
            var httpClient = _httpClientFactory.CreateClient();
            var languageModelApiKey = _configuration["LANGUAGE_MODEL:API_KEY"];
            var request = new HttpRequestMessage(HttpMethod.Post, $"https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key={languageModelApiKey}")
            {
                Content = new StringContent(
                "{ \"contents\":[{\"parts\":[{\"text\":\"hi\"}]}] }",
                Encoding.UTF8,
                "application/json")
            };

            HttpResponseMessage response = await httpClient.SendAsync(request, cancellationToken);

            if (response.IsSuccessStatusCode)
            {
                _logger.LogInformation("Google API responded with success status code.");
                return HealthCheckResult.Healthy();
            }
            else
            {
                _logger.LogError($"Google API responded with: {response.StatusCode} - {response.ReasonPhrase}");
                return HealthCheckResult.Unhealthy($"Google API responded with: {response.StatusCode} - {response.ReasonPhrase}");
            }
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred while contacting Google API.");
            return HealthCheckResult.Unhealthy("An error occurred while contacting Google API: " + ex.Message);
        }
    }
}
```

### Pontos chave

- **CheckHealthAsync**: Este método envia uma request para a API do Google e verifica se a resposta é bem sucedida. Se a API responder com um código de sucesso, devolve `HealthCheckResult.Healthy()`. Caso contrário, devolve `HealthCheckResult.Unhealthy()` com os detalhes do erro.

### appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "LANGUAGE_MODEL": {
    "URL": "https://generativelanguage.googleapis.com/v1beta1/models/chat-bison-001:generateMessage",
    "API_KEY": ""
  }
  "AllowedHosts": "*",
  "ClientUrl": "http://localhost:3000"
}
```

### Definições de chave:

- **ClientUrl**: Especifica o URL da aplicação cliente.
- **LANGUAGE_MODEL:URL**: URL da API para fazer requests à API do PaLM.
- **LANGUAGE_MODEL:API_KEY**: chave para dar acesso à API do PaLM.

Pode-se obter a chave da API e o URL da API REST aqui:

[Tutorial: Get started with the Gemini API](https://ai.google.dev/gemini-api/docs/get-started/tutorial?lang=rest)

### Using.cs (Opcional)

Esse arquivo contém diretivas de uso globais para simplificar o código.

```csharp
global using API.Extensions;
```

### Resumo

- **Program.cs**: Configura o aplicativo da Web, configura os serviços e define o pipeline de middleware.
- **BuilderExtensions.cs**: Contém métodos de extensão para adicionar vários serviços ao contêiner DI.
- **GoogleCloudLanguageApiHealthCheck.cs**: Define um health check personalizada para a API do Google.

## Cliente

Agora vamos analisar o código do cliente do chatbot.

### Configuração do projeto

1. **Criando o projeto**:
    
    ```bash
    cd src
    npx create-next-app@latest client --typescript
    cd client
    ```
    
2. **Instalando o TailwindCSS**:
    
    ```bash
    npm install -D tailwindcss postcss autoprefixer
    npx tailwindcss init -p
    ```
    
3. **Configurando o TailwindCSS**:
   - `tailwind.config.js`:
        
        ```jsx
        import type { Config } from 'tailwindcss'
        
        const config: Config = {
          content: [
            './pages/**/*.{js,ts,jsx,tsx,mdx}',
            './components/**/*.{js,ts,jsx,tsx,mdx}',
            './app/**/*.{js,ts,jsx,tsx,mdx}',
          ],
          theme: {
            extend: {
              backgroundImage: {
                'gradient-radial': 'radial-gradient(var(--tw-gradient-stops))',
                'gradient-conic':
                  'conic-gradient(from 180deg at 50% 50%, var(--tw-gradient-stops))',
              },
              animation: {
                'pulse-slow': 'pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                'pulse-normal': 'pulse 1.5s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                'pulse-fast': 'pulse 1s cubic-bezier(0.4, 0, 0.6, 1) infinite',
              },
            },
          },
          plugins: [],
        }
        export default config
        ```
        
    - `styles/globals.css`:
        
        ```css
        @tailwind base;
        @tailwind components;
        @tailwind utilities;
        ```

4. **Configuração de variáveis de ambiente**:
    - `.env.local`:
        
        ```
        NEXT_PUBLIC_API_URL=http://localhost:8080
        ```

### Componentes

### `components/chat.tsx`

Este é o componente principal do chat que lida com a entrada do usuário, exibe mensagens e mostra os estados de carregamento e erro.

O styling desse component é feito a partir desse componente da **Flowbite**:

[Tailwind CSS Textarea - Flowbite](https://flowbite.com/docs/forms/textarea/#chatroom-input)

```tsx
'use client'

import React, { useEffect, useRef } from 'react'
import Loading from './loading'
import ReactMarkdown from 'react-markdown'
import DangerError from './danger-error'
import { useChat } from '../hooks/useChat'

export default function Chat() {
  const {
    text,
    setText,
    messages,
    isLoading,
    errorMessage,
    handleSubmit,
    dismissError,
  } = useChat()

  const inputRef = useRef<HTMLInputElement>(null);
  const messagesEndRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' })
  }, [messages])

  useEffect(() => {
    if (!isLoading) {
      inputRef.current?.focus();
    }
  }, [isLoading]);

  return (
    <div className='p-6'>
      {errorMessage && (
        <DangerError message={errorMessage} dismissError={dismissError} />
      )}
      <div className='mb-4 rounded-lg border border-gray-200 p-4 dark:border-gray-700 dark:bg-gray-900'>
        {messages.map((message, index) => (
          <div
            key={index}
            className={`mb-4 ${message.author === 'Bot' ? 'text-blue-500' : 'text-green-500'}`}
          >
            <strong>{message.author}</strong>:{' '}
            <ReactMarkdown>{message.content}</ReactMarkdown>
          </div>
        ))}
        <div ref={messagesEndRef} />
        {isLoading && <Loading />}
      </div>
      <form onSubmit={handleSubmit}>
        <label htmlFor='chat' className='sr-only'>
          Your message
        </label>
        <div className='flex items-center rounded-lg bg-gray-50 px-3 py-2 dark:bg-gray-700'>
          <input
            id='chat'
            disabled={isLoading}
            ref={inputRef}
            className='mx-4 block w-full rounded-lg border border-gray-300 bg-white p-2.5 text-sm text-gray-900 focus:border-blue-500 focus:ring-blue-500 dark:border-gray-600 dark:bg-gray-800 dark:text-white dark:placeholder-gray-400 dark:focus:border-blue-500 dark:focus:ring-blue-500'
            placeholder='Your message...'
            value={text}
            onChange={(e) => setText(e.target.value)}
          />
          <button
            type='submit'
            disabled={isLoading}
            className='inline-flex cursor-pointer justify-center rounded-full p-2 text-blue-600 hover:bg-blue-100 dark:text-blue-500 dark:hover:bg-gray-600'
          >
            <svg
              className='h-5 w-5 rotate-90 rtl:-rotate-90'
              aria-hidden='true'
              xmlns='<http://www.w3.org/2000/svg>'
              fill='currentColor'
              viewBox='0 0 18 20'
            >
              <path d='m17.914 18.594-8-18a1 1 0 0 0-1.828 0l-8 18a1 1 0 0 0 1.157 1.376L8 18.281V9a1 1 0 0 1 2 0v9.281l6.758 1.689a1 1 0 0 0 1.156-1.376Z' />
            </svg>
            <span className='sr-only'>Send message</span>
          </button>
        </div>
      </form>
    </div>
  )
}
```

- **UseChat Hook**: Esse hook gerencia o estado do chat, incluindo a entrada de texto, as mensagens, o estado de carregamento e o tratamento de erros.
- **UseRef**: Usado para manter referências ao campo de entrada e ao final da lista de mensagens para rolagem.
- **useEffect**: Usado para rolar até a última mensagem e focar o campo de entrada quando não estiver carregando.

### `components/loading.tsx`

Esse componente exibe uma animação de carregamento enquanto aguarda a resposta do bot.

Esse componente é uma modificação refatorada da **Flowbite**:

[Tailwind CSS Skeleton - Flowbite](https://flowbite.com/docs/components/skeleton/#text-placeholder)

```tsx
interface LoadingBarProps {
  width: string;
  animation: string;
  bgColor: string;
  marginLeft: string;
}

const LoadingBar: React.FC<LoadingBarProps> = ({ width, animation, bgColor, marginLeft }) => (
  <div className={`h-2.5 ${width} ${animation} rounded-full ${bgColor} ${marginLeft}`}></div>
);

export default function Loading() {
  const loadingBars = [
    [
      { width: 'w-32', animation: 'animate-pulse-slow', bgColor: 'bg-gray-200 dark:bg-gray-700', marginLeft: '' },
      { width: 'w-24', animation: 'animate-pulse-normal', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
      { width: 'w-full', animation: 'animate-pulse-fast', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
    ],
    [
      { width: 'w-full', animation: 'animate-pulse-normal', bgColor: 'bg-gray-200 dark:bg-gray-700', marginLeft: '' },
      { width: 'w-full', animation: 'animate-pulse-slow', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
      { width: 'w-24', animation: 'animate-pulse-fast', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
    ],
    [
      { width: 'w-full', animation: 'animate-pulse-fast', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: '' },
      { width: 'w-80', animation: 'animate-pulse-normal', bgColor: 'bg-gray-200 dark:bg-gray-700', marginLeft: 'ms-2' },
      { width: 'w-full', animation: 'animate-pulse-slow', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
    ],
    [
      { width: 'w-full', animation: 'animate-pulse-slow', bgColor: 'bg-gray-200 dark:bg-gray-700', marginLeft: 'ms-2' },
      { width: 'w-full', animation: 'animate-pulse-normal', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
      { width: 'w-24', animation: 'animate-pulse-fast', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
    ],
    [
      { width: 'w-32', animation: 'animate-pulse-normal', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
      { width: 'w-24', animation: 'animate-pulse-slow', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
      { width: 'w-full', animation: 'animate-pulse-fast', bgColor: 'bg-gray-200 dark:bg-gray-700', marginLeft: 'ms-2' },
    ],
    [
      { width: 'w-full', animation: 'animate-pulse-fast', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
      { width: 'w-80', animation: 'animate-pulse-normal', bgColor: 'bg-gray-200 dark:bg-gray-700', marginLeft: 'ms-2' },
      { width: 'w-full', animation: 'animate-pulse-slow', bgColor: 'bg-gray-300 dark:bg-gray-600', marginLeft: 'ms-2' },
    ],
  ];

  return (
    <div role='status' className='mb-4 space-y-2.5 p-6 dark:bg-gray-900'>
      {loadingBars.map((bars, index) => (
        <div key={index} className='flex w-full items-center'>
          {bars.map((bar, idx) => (
            <LoadingBar key={idx} {...bar} />
          ))}
        </div>
      ))}
      <span className='sr-only'>Loading...</span>
    </div>
  );
}
```

- **Loading Animation**: Usa as classes TailwindCSS para criar um efeito de animação pulsante.

### `components/danger-error.tsx`

Esse componente exibe uma mensagem de erro quando algo dá errado.

O styling desse component é feito a partir desse componente da **Flowbite**:
[Tailwind CSS Alerts - Flowbite](https://flowbite.com/docs/components/alerts/#border-accent)

```tsx
interface DangerErrorTypes {
  message: string
  dismissError: () => void
}

export default function DangerError({
  message,
  dismissError,
}: DangerErrorTypes) {
  return (
    <div
      id='alert-border-2'
      className='mb-4 flex items-center border-t-4 border-red-300 bg-red-50 p-4 text-red-800 dark:border-red-800 dark:bg-gray-800 dark:text-red-400'
      role='alert'
    >
      <svg
        className='h-4 w-4 flex-shrink-0'
        aria-hidden='true'
        xmlns='<http://www.w3.org/2000/svg>'
        fill='currentColor'
        viewBox='0 0 20 20'
      >
        <path d='M10 .5a9.5 9.5 0 1 0 9.5 9.5A9.51 9.51 0 0 0 10 .5ZM9.5 4a1.5 1.5 0 1 1 0 3 1.5 1.5 0 0 1 0-3ZM12 15H8a1 1 0 0 1 0-2h1v-3H8a1 1 0 0 1 0-2h2a1 1 0 0 1 1 1v4h1a1 1 0 0 1 0 2Z' />
      </svg>
      <div className='ms-3 text-sm font-medium'>{message}</div>
      <button
        type='button'
        onClick={dismissError}
        className='-mx-1.5 -my-1.5 ms-auto inline-flex h-8 w-8 items-center justify-center rounded-lg bg-red-50 p-1.5 text-red-500 hover:bg-red-200 focus:ring-2 focus:ring-red-400 dark:bg-gray-800 dark:text-red-400 dark:hover:bg-gray-700'
        data-dismiss-target='#alert-border-2'
        aria-label='Close'
      >
        <span className='sr-only'>Dismiss</span>
        <svg
          className='h-3 w-3'
          aria-hidden='true'
          xmlns='<http://www.w3.org/2000/svg>'
          fill='none'
          viewBox='0 0 14 14'
        >
          <path
            stroke='currentColor'
            strokeLinecap='round'
            strokeLinejoin='round'
            strokeWidth='2'
            d='m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6'
          />
        </svg>
      </button>
    </div>
  )
}
```

- **Tratamento de erros**: Exibe uma mensagem de erro com um botão de ignorar.

### Hook

### `hooks/useChat.ts`

Esse hook gerencia o estado do chat, incluindo as respostas da API.

```tsx
import { useState, useEffect } from 'react'
import axios, { AxiosError } from 'axios'

const API_URL = process.env.NEXT_PUBLIC_API_URL

type Message = {
  author: string
  content: string
}

export const useChat = () => {
  const [text, setText] = useState('')
  const [messages, setMessages] = useState<Message[]>([])
  const [isLoading, setIsLoading] = useState(false)
  const [errorMessage, setErrorMessage] = useState<string | null>(null)

  useEffect(() => {
    const savedMessages = sessionStorage.getItem('messages')
    if (savedMessages) {
      setMessages(JSON.parse(savedMessages))
    }
  }, [])

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    setMessages((prevMessages) => {
      const newMessages = [...prevMessages, { author: 'User', content: text }]
      sessionStorage.setItem('messages', JSON.stringify(newMessages))
      return newMessages
    })
    setText('')
    setTimeout(getResponse, 0)
  }

  const getResponse = async () => {
    setIsLoading(true)
    try {
      const response = await axios.get(`${API_URL}/prompt/${text}`)
      const data = response.data
      setMessages((prevMessages) => {
        const newMessages = [
          ...prevMessages,
          { author: 'Bot', content: data.candidates[0].content },
        ]
        sessionStorage.setItem('messages', JSON.stringify(newMessages))
        return newMessages
      })
    } catch (error) {
      const axiosError = error as AxiosError
      let errorMessage = 'An unexpected error occurred'
      if (axiosError.response) {
        errorMessage = axiosError.message
      }
      setErrorMessage(errorMessage)
    }
    setIsLoading(false)
  }

  const dismissError = () => {
    setErrorMessage(null)
  }

  return {
    text,
    setText,
    messages,
    isLoading,
    errorMessage,
    handleSubmit,
    dismissError,
  }
}
```

- **Gerenciamento de estado**: Gerencia o estado da entrada de texto, das mensagens, do estado de carregamento e das mensagens de erro.
- **UseEffect**: Carrega mensagens salvas do `sessionStorage` na renderização inicial.
- **handleSubmit**: Lida com o envio de formulários, atualiza mensagens e aciona a resposta do bot.
- **getResponse**: Obtém a resposta do bot da API e atualiza as mensagens.

### Página principal

### `app/page.tsx`

Renderizamos o componente `Chat`.

```tsx
import Chat from '../components/chat'

export default function Home() {
  return <Chat />
}
```

### Executando o aplicativo

Por fim, executamos a aplicação:

```bash
npm run dev
```

Vá até `http://localhost:3000` no navegador para ver o client do chatbot em funcionamento.

### Conclusão

Esse código implementa um cliente Next.js com TypeScript e TailwindCSS para um chatbot. O componente `Chat` manipula a entrada do usuário, exibe mensagens e gerencia os estados de carregamento e erro. O hook `useChat` gerencia o estado do chat e interage com a API do chatbot.

[Repositório do código](https://github.com/RianNegreiros/DotNetChatBot)

[Inspirado pelo vídeo - PaLM 2 API Course – Build Generative AI Apps da freeCodeCamp](https://www.youtube.com/watch?v=LHbtSrkTsIE)