# Building a Chatbot with ASP.NET Core 8 and PaLM 2 API

This blog post will guide you through the process of creating a minimalist API in ASP.NET Core 8, which utilizes the PaLM 2 API to generate dynamic responses for a chatbot application. We'll focus on the core functionality; for now, let's ignore the CORS and Swagger configurations.

## Understanding the Code

The provided code outlines a basic structure for our API:

- **Dependencies**: The project is set up with relevant services like Endpoints API Explorer. Swagger is also included for documentation but is currently commented out.
- **Language Model Configuration**: The API key and base URL are retrieved from environment variables.
- **Endpoint Definition**: The `/prompt/{text}` endpoint accepts user input as a path parameter.
- **PaLM 2 API Interaction**: A payload is constructed with the prompt, temperature, and candidate count, which is then sent as a POST request to the PaLM 2 API.
- **Response Handling**: The API response is processed and sent back to the client.

## Building on the Foundation

- **Deployment**: Choose a suitable hosting platform (Azure App Service, AWS Lambda, etc.) to deploy your API.
- **Error Handling**: Implement robust error handling for API failures and invalid requests.
- **Security**: Consider security measures like rate limiting and input validation to prevent abuse.

## Enhancements and Extensions

- **Multi-turn dialogues**: Store conversation history and context to personalize responses across messages.
- **Intent recognition**: Integrate NLP tools to understand user intent and generate relevant responses.
- **Customization**: Allow users to adjust API parameters like temperature for different response styles.
- **Monitoring and Logging**: Track API usage and analyze user interactions for improvement.

This is just a starting point. Feel free to customize and expand upon this code to create a powerful and engaging chatbot interface!

## Further Resources

- [ASP.NET Core 8 Documentation](https://learn.microsoft.com/en-us/aspnet/core/?view=aspnetcore-8.0)
- [PaLM 2 API Documentation](https://ai.google/discover/palm2/)
- [Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/)
- [AWS Lambda](https://aws.amazon.com/lambda/)

Remember, the key is to experiment, explore, and constantly improve your chatbot solution to provide a seamless and delightful user experience.

## Application Code

Here is the application code that this blog post is based on:

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

## Frontend Integration

To interact with our chatbot API, we'll create a simple frontend using Next.js and React. This frontend will allow users to send prompts to the API and display the generated responses.

### Environment Configuration

First, we need to specify the URL of our API in a `.env` file:

```env
NEXT_PUBLIC_API_URL=http://localhost:5111
```

### Chat Component

The `Chat` component is responsible for handling user input, sending requests to the API, and displaying the conversation history. It uses the `useState` and `useEffect` hooks to manage state and side effects.

The chat input is modified version from [Flowbite](https://flowbite.com/docs/forms/textarea/#chatroom-input).

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

We're using the `react-markdown` package to format the bot's responses. This allows us to include markdown formatting in our responses, which can be useful for displaying structured data, links, etc.

### Loading Component

The `Loading` component displays a loading animation while the API response is being fetched.

This loading indicator is from [Flowbite](https://flowbite.com/docs/components/skeleton/#text-placeholder) with a few tweaks to match the application styling.

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

Finally, we include the `Chat` component in our main page:

```jsx
import Chat from "./components/chat";

export default function Home() {
  return (
    <Chat />
  );
}
```

but before we can access our client on `localhost:3000`

### Configuring CORS

CORS is a security measure that allows or denies requests coming from different origins to access resources from a server. It's crucial to configure CORS in your application to control who can interact with your server.

Now, let's look at two ways to configure CORS in your application:

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

In the above code, we're specifying a particular origin (`http://localhost:3000`) that is allowed to access the server. This method is more secure as it restricts access to a specific origin. The `builder.Configuration["Client_Url"]` is a way to get the client URL from environment variables, which is a safer practice than hard-coding values.

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

In the second code snippet, we're allowing any origin, any header, and any method to access the server. This is a more generic configuration and could potentially expose your server to unwanted requests, so use it with caution.

Remember, improperly configured CORS can lead to errors and potential security risks. If you encounter issues, check your CORS configuration and ensure that the correct origins, headers, and methods are allowed.

That's it! We now have a simple chat interface for interacting with our chatbot API.

---

- [The complete code on GitHub](https://github.com/RianNegreiros/DotNetChatBot)
