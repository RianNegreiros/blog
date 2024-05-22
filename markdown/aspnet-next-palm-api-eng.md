# Creating a Chatbot with ASP.NET Core, Next.js and PaLM API

![chatbot-demo.gif](/assets/chatbot-demo.gif)

Let's analyze the code step by step to understand how it works and what each part does.

### Project Structure

The project is structured as follows:

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

This is the main entry point of the application. It sets up the web application, configures services, and defines the middleware pipeline.

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

### Key Sections:

1. **Service Configuration**:
    - `builder.Services.AddEndpointsApiExplorer()`: Adds support for API endpoint exploration.
    - `builder.Services.AddServicesExtension()`: Adds custom services defined in `BuilderExtensions.cs`.
    - `builder.Services.AddSwaggerExtension()`: Adds Swagger for API documentation.
    - `builder.Services.AddCorsExtension(builder.Configuration)`: Adds CORS policy.
    - `builder.Services.AddHealthChecksExtension(builder.Configuration)`: Adds health checks.
    - `builder.Services.AddRateLimiter(...)`: Configures rate limiting to prevent abuse.
2. **Middleware Pipeline**:
    - `app.UseSwagger()`: Enables Swagger middleware.
    - `app.UseSwaggerUI(...)`: Configures Swagger UI.
    - `app.MapHealthChecks("/health")`: Maps the health check endpoint.
    - `app.UseHttpsRedirection()`: Redirects HTTP requests to HTTPS.
    - `app.UseRateLimiter()`: Enables rate limiting middleware.
    - `app.MapPost("/prompt/{text}", ...)`: Defines an endpoint to generate a response from the PaLM API.

### Extensions/BuilderExtensions.cs

This file contains extension methods to add various services to the DI container.

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

### Key Methods:

1. **AddSwaggerExtension**: Configures Swagger for API documentation.
2. **AddCorsExtension**: Configures CORS to allow requests from specified origins.
3. **AddHealthChecksExtension**: Adds health checks, including a custom check for the Google API.
4. **AddServicesExtension**: Adds HTTP client services.

### Extensions/GoogleCloudLanguageApiHealthCheck.cs

This file defines a custom health check for the Google API.

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

### Key Points:

- **CheckHealthAsync**: This method sends a request to the Google API and checks if the response is successful. If the API responds with a success status code, it returns `HealthCheckResult.Healthy()`. Otherwise, it returns `HealthCheckResult.Unhealthy()` with the error details.

### appsettings.json

This file contains configuration settings for the application.

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

### Key Settings:

- **ClientUrl**: Specifies the URL of the client application.
- **LANGUAGE_MODEL:URL**: Placeholder for the API URL to make requests to the PaLM API.
- **LANGUAGE_MODEL:API_KEY**: Placeholder for the API key to access the PaLM API.

You can get the the API Key and the REST API URL here:

[Tutorial: Get started with the Gemini API](https://ai.google.dev/gemini-api/docs/get-started/tutorial?lang=rest)

### Using.cs

This file contains global using directives to simplify the code.

```csharp
global using API.Extensions;
```

### Summary

- **Program.cs**: Sets up the web application, configures services, and defines the middleware pipeline.
- **BuilderExtensions.cs**: Contains extension methods to add various services to the DI container.
- **GoogleCloudLanguageApiHealthCheck.cs**: Defines a custom health check for the Google API.
- **appsettings.json**: Contains configuration settings for the application.
- **Using.cs**: Contains global using directives.

This setup provides a robust foundation for building and extending a chatbot application using ASP.NET Core 8 and the PaLM API.

## Client

Now let's analyze the code for the chatbot client.

### Project Setup

1. **Creating the Project**:
    
    ```bash
    cd src
    npx create-next-app@latest client --typescript
    cd client
    ```
    
2. **Installing TailwindCSS**:
    
    ```bash
    npm install -D tailwindcss postcss autoprefixer
    npx tailwindcss init -p
    ```
    
3. **Configuring TailwindCSS**:
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
        
4. **Setting Up Environment Variables**:
    - `.env.local`:
        
        ```
        NEXT_PUBLIC_API_URL=http://localhost:8080
        ```
        

### Components

### `components/chat.tsx`

This is the main chat component that handles user input, displays messages, and shows loading and error states.

The styling is a modification of this Flowbite component:

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

- **useChat Hook**: This hook manages the chat state, including the text input, messages, loading state, and error handling.
- **useRef**: Used to keep references to the input field and the end of the messages list for scrolling.
- **useEffect**: Used to scroll to the latest message and focus the input field when not loading.

### `components/loading.tsx`

This component displays a loading animation while waiting for the bot's response.

The styling is a modification of this Flowbite component:

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

- **Loading Animation**: Uses TailwindCSS classes to create a pulsing animation effect.

### `components/danger-error.tsx`

This component displays an error message when something goes wrong.

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

- **Error Handling**: Displays an error message with a dismiss button.

### Hook

### `hooks/useChat.ts`

This hook manages the chat state, including fetching responses from the API.

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

- **State Management**: Manages the state for text input, messages, loading state, and error messages.
- **useEffect**: Loads saved messages from `sessionStorage` on initial render.
- **handleSubmit**: Handles form submission, updates messages, and triggers the bot response.
- **getResponse**: Fetches the bot's response from the API and updates the messages.

### Main Page

### `app/page.tsx`

This is the main page that renders the `Chat` component.

```tsx
import Chat from '../components/chat'

export default function Home() {
  return <Chat />
}
```

### Running the Application

Finally, run your application:

```bash
npm run dev
```

Navigate to `http://localhost:3000` in your browser to see your chatbot client in action.

### Conclusion

This code sets up a Next.js application with TypeScript and TailwindCSS, and implements a chatbot client using the provided components and hooks. The `Chat` component handles user input, displays messages, and manages loading and error states. The `useChat` hook manages the chat state and interacts with the chatbot API.

[Code Repository](https://github.com/RianNegreiros/DotNetChatBot)

[Inspired by - PaLM 2 API Course – Build Generative AI Apps by freeCodeCamp](https://www.youtube.com/watch?v=LHbtSrkTsIE)