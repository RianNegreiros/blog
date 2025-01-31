# Construindo um gerador de vídeos curtos com tecnologia de IA

# Gerador de vídeos curtos sobre IA

### **Introdução**

O AI Shorts Video Generator é uma ferramenta projetada para automatizar a criação de vídeos curtos, aproveitando as tecnologias de IA. Ela simplifica o processo de transformação de um prompt de texto em um vídeo completo, incluindo roteiro, narração e montagem de vídeo.

Este artigo fornece um passo a passo de como o aplicativo foi criado.

### **Pré-requisitos**

Para configurar e replicar o AI Shorts Video Generator, verifique se você atende aos seguintes requisitos:

### **Requisitos do sistema**

- **Node.js**: Versão 22.x ou superior.
- **.NET SDK**: Versão 9 ou superior. [Faça o download aqui](https://dotnet.microsoft.com/download/dotnet)
- **PostgreSQL**: Para gerenciamento de banco de dados (por exemplo, [Neon Serverless Postgres](https://neon.tech/)).
- **Contas na**:
    - [Cloudinary](https://cloudinary.com/) para armazenamento e processamento de mídia.
    - [Cloudflare Workers AI](https://developers.cloudflare.com/workers-ai/) para geração de imagens (por exemplo, [flux-1-schnell](https://developers.cloudflare.com/workers-ai/models/flux-1-schnell)).
    - [Google Cloud Text-to-Speech](https://codelabs.developers.google.com/codelabs/cloud-text-speech-csharp) para síntese de áudio.
    - [AssemblyAI](https://www.assemblyai.com/) para geração de legendas.
    - [Gemini API](https://ai.google.dev/) para geração do conteúdo  do vídeo

### **Dependências**

Para os componentes de front-end e back-end, são usadas as seguintes ferramentas e bibliotecas:

- **Frontend**:
    - Next.js, TailwindCSS, Remotion, Axios e componentes shadcn.
- **Backend**:
    - Google.Cloud.TextToSpeech.V1, AssemblyAI SDK, CloudinaryDotNet SDK e Npgsql para PostgreSQL.

### **Variáveis de ambiente**

Configure as seguintes variáveis de ambiente para permitir a integração adequada com APIs e serviços:

**Frontend (`.env`):**

```
NEXT_PUBLIC_API_URL=<sua URL de API>
REMOTION_AWS_SERVE_URL=<Sua URL do AWS Serve>
REMOTION_AWS_BUCKET_NAME=<Seu nome de bucket do AWS>

```

**Backend (`appsettings.json`):**

```json
{
  “GoogleApi": {
    “GeminiKey": “your-gemini-api-key”,
    “TextToSpeechKey": “your-text-to-speech-api-key”
  },
  “AssemblyAi": {
    “ApiKey": “your-assemblyai-api-key”
  },
  “CloudinaryUrl": “your-cloudinary-url”,
  “Cloudflare": {
    “ApiKey": “your-cloudflare-api-key”,
    “AccountId": “your-cloudflare-account-id”
  },
  “ConnectionStrings": {
    “DefaultConnection” (Conexão Padrão): “your-postgresql-connection-string”
  }
}

```

### **Conhecimento recomendado**

- Familiaridade com React e Next.js para desenvolvimento de front-end.
- Conhecimento básico de .NET Core para desenvolvimento de back-end.
- Experiência com APIs.

### Visão geral da arquitetura

O AI Shorts Video Generator foi projetado para simplificar a criação de vídeos curtos aproveitando vários serviços de IA. Esta seção descreve o fluxo e os principais componentes do sistema.

### Fluxo de trabalho de alto nível

![create-short-video-flow-chart.png](/assets/create-short-video-flow-chart.png)

1. **Input do usuário e geração de conteúdo de vídeo**
    - Os usuários começam preenchendo um formulário para criar um vídeo curto. Esse formulário captura o tópico, o estilo e a duração desejados do vídeo.
    - Usando o **Google Gemini**, o aplicativo gera o script e os prompts de imagem associados necessários para o conteúdo do vídeo.
2. **Geração de áudio**
    - O script gerado é convertido em áudio usando a **Google Text-to-Speech API**.
    - O arquivo MP3 resultante é armazenado no **Cloudinary** para facilitar o acesso e a integração ao vídeo.
3. **Geração de legendas**
    - O arquivo de áudio MP3 é processado usando **AssemblyAI** para gerar legendas.
    - Essas legendas são armazenadas em um banco de dados **PostgreSQL** para recuperação futura e sincronização de vídeo.
4. **Geração de imagens**
    - Com base nos prompts de imagem gerados pelo **Google Gemini**, o aplicativo cria recursos visuais relevantes usando um **modelo de texto para imagem**.
    - Essas imagens também são armazenadas no **Cloudinary**.
5. **Compilação de vídeo**
    - O vídeo final é compilado usando o **Remotion**, incorporando o script, o áudio, as imagens geradas e as legendas.
    - O vídeo concluído fica disponível para visualização, exportação ou exclusão, conforme a necessidade do usuário.

### Tecnologias-chave

- **Frontend**: Desenvolvido com **Next.js**, fornecendo uma interface de usuário perfeita para a criação e o gerenciamento de vídeos.
- **Backend**: Desenvolvido usando o **ASP.NET Core**, manipulando solicitações de API e integrando-se a vários serviços de IA.
- **Banco de dados**: Utiliza o **PostgreSQL** para armazenar metadados, legendas e outros dados essenciais.
- **Serviços de nuvem**:
    - **Cloudinary**: Gerencia o armazenamento e a recuperação de mídia para áudio e imagens.
    - **Google Cloud**: Oferece recursos de IA como Gemini para geração de conteúdo e Text-to-Speech para síntese de áudio.
    - **Cloudflare Workers AI**: Facilita a criação de imagens a partir de solicitações de texto.
    - **AssemblyAI**: Gera legendas para melhorar a acessibilidade.

## **Implementação do backend passo a passo**

Esta seção o orientará na configuração e implementação do AI Shorts Video Generator a partir do zero. Abordaremos os componentes de frontend, backend, integrações de IA e renderização de vídeo.

### 1. Criar projeto backend

```bash
dotnet new webapi --name backend
```

### 2. Criar o projeto frontend

```bash
npx create-next-app@latest
```

Certifique-se de selecionar Tailwind CSS, selecione os outros prompts com base em suas preferências pessoais.

### 3. Geração de conteúdo de vídeo usando o Google Gemini

- O endpoint da API `/generate-content` recebe o input do usuário e gera um script junto com prompts de imagem.

3.1 Obtenha a chave da API do Gemini em sua [docs](https://ai.google.dev/gemini-api/docs).

3.2 Configure sua chave de API adicionando-a ao arquivo `appsettings.json`:

```json
{
  “GoogleApi": {
    “GeminiKey": “your-gemini-api-key”
  }
}
```

3.3 Crie uma classe de serviço para a Gemini API.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;
using Models;

public class GeminiApiService(HttpClient httpClient, IConfiguration configuration)
{
    public async Task<List<VideoContentItem>> CallGoogleApi(string input)
    {
        if (string.IsNullOrWhiteSpace(input))
        {
            throw new ArgumentException("User input cannot be null or empty", nameof(input));
        }

        var apiKey = configuration["GoogleApi:GeminiKey"];

        if (string.IsNullOrEmpty(apiKey))
        {
            throw new InvalidOperationException("API key is not configured");
        }

        string url = $"https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent?key={apiKey}";

        string requestBody = $@"{{
            ""contents"": [
                {{
                    ""role"": ""user"",
                    ""parts"": [
                        {{
                            ""text"": ""{input}""
                        }}
                    ]
                }}
            ],
            ""generationConfig"": {{
                ""temperature"": 1,
                ""topK"": 40,
                ""topP"": 0.95,
                ""maxOutputTokens"": 8192,
                ""responseMimeType"": ""application/json""
            }}
        }}";

        var content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        var response = await httpClient.PostAsync(url, content);

        if (response.IsSuccessStatusCode)
        {
            var responseString = await response.Content.ReadAsStringAsync();
            if (!string.IsNullOrEmpty(responseString))
            {
                var dataJson = JsonSerializer.Deserialize<JsonElement>(responseString);

                if (dataJson.TryGetProperty("candidates", out var candidates))
                {
                    var contentText = candidates[0]
                        .GetProperty("content")
                        .GetProperty("parts")[0]
                        .GetProperty("text")
                        .GetString();

                    if (!string.IsNullOrEmpty(contentText))
                    {
                        var videoContentList = JsonSerializer.Deserialize<List<VideoContentItem>>(contentText);

                        if (videoContentList != null)
                        {
                            return videoContentList;
                        }
                    }
                }
            }
        }

        var errorContent = await response.Content.ReadAsStringAsync();
        throw new Exception($"Error calling Google API: {response.StatusCode}, Content: {errorContent}");
    }
}
```

Adicione ao `Program.cs`:

```csharp
builder.Services.AddHttpClient<GeminiApiService>();
```

3.4 Configure a chave da API atualizando o arquivo `appsettings.Development.json`:

```json
{
  “GoogleApi": {
    “GeminiKey": “your-gemini-api-key”
  }
}
```

3.5 Crie o enpoint `/generate-content`:

```csharp
app.MapPost(“/generate-content”, async (GeminiApiService googleApiService, [FromBody] JsonElement body) =>
    {
        try
        {
            se (!body.TryGetProperty(“input”, out var userInputJson) || string.IsNullOrWhiteSpace(userInputJson.GetString()))
            {
                return Results.BadRequest(new { success = false, message = “O parâmetro obrigatório ‘input’ está faltando ou é inválido”. });
            }

            var apiKey = builder.Configuration[“GoogleApi:GeminiKey”];
            se (string.IsNullOrEmpty(apiKey))
            {
                return Results.BadRequest(new { success = false, message = “A chave da API está faltando ou não está configurada.” });
            }

            var userInput = userInputJson.GetString()!
            var result = await googleApiService.CallGoogleApi(userInput);

            return Results.Ok(result);
        }
        catch (Exception ex)
        {
            return Results.BadRequest(new { success = false, message = ex.Message });
        }
    })
    .WithName(“GenerateContent”)
    .Produces<List<VideoContentItem>>()
    .Produces(400);
```

### 4. Conversão de texto em fala

- O endpoint `/generate-audio` converte o script em um arquivo MP3

4.1 Crie um projeto no Google Cloud e ative a [Google Cloud Text-to-Speech AI](https://cloud.google.com/text-to-speech)

4.2 Crie uma chave de API: Pesquise Google Cloud Text-to-Speech > Manage > Credentials > CREATE CREDENTIALS > API key

4.3 Adicione os pacotes NuGet `Google.Cloud.TextToSpeech.V1` e `CloudinaryDotNet` ao projeto:

```bash
dotnet add pacote Google.Cloud.TextToSpeech.V1
dotnet add package CloudinaryDotNet
```

4.4 Atualize o `appsettings.Development.json` :

```json
{
  “GoogleApi": {
    “GeminiKey": “your-gemini-api-key”,
    “TextToSpeechKey": “your-text-to-speech-api-key”
  },
  “CloudinaryUrl": “your-cloudinary-url”,
}
```

4.5 Crie uma classe de serviço para a API Text-to-Speech do Google Cloud

```csharp
using Google.Cloud.TextToSpeech.V1;

namespace AiShortsGenerator.Services;

public class TextToSpeechService(IConfiguration configuration)
{
    public async Task<byte[]> SynthesizeTextToSpeech(string inputText)
    {
        if (string.IsNullOrWhiteSpace(inputText))
        {
            throw new ArgumentException("Input text cannot be null or empty", nameof(inputText));
        }

        var client = await new TextToSpeechClientBuilder
        {
            ApiKey = configuration["GoogleApi:TextToSpeechKey"]
        }.BuildAsync();

        var input = new SynthesisInput
        {
            Text = inputText
        };

        var voiceSelection = new VoiceSelectionParams
        {
            LanguageCode = "en-US",
            SsmlGender = SsmlVoiceGender.Neutral
        };

        var audioConfig = new AudioConfig
        {
            AudioEncoding = AudioEncoding.Mp3
        };

        var response = await client.SynthesizeSpeechAsync(input, voiceSelection, audioConfig);
        return response.AudioContent.ToArray();
    }
}
```

Adicione ao `Program.cs`:

```csharp
builder.Services.AddScoped<TextToSpeechService>();
```

4.6 Crie uma classe de serviço para a API do Cloudinary:

Precisamos que o arquivo de áudio MP3 esteja acessível na Internet para a geração de legendas com o Assembly AI.

```csharp
using CloudinaryDotNet;
using CloudinaryDotNet.Actions;

namespace AiShortsGenerator.Services;

public class CloudinaryService(IConfiguration configuration)
{
    private readonly Cloudinary _cloudinary = new(configuration["CloudinaryUrl"]);

    public async Task<string> UploadAudio(byte[] audioContent)
    {
        var uploadParams = new AutoUploadParams
        {
            File = new FileDescription(Guid.NewGuid().ToString(), new MemoryStream(audioContent)),
            Folder = "audio-files"
        };

        var uploadResult = await _cloudinary.UploadAsync(uploadParams);

        if (uploadResult.StatusCode == System.Net.HttpStatusCode.OK)
        {
            return uploadResult.SecureUrl.ToString();
        }

        throw new Exception("Audio upload failed: " + uploadResult.Error?.Message);
    }

    public async Task<string> UploadImage(byte[] imageContent)
    {
        var uploadParams = new ImageUploadParams
        {
            File = new FileDescription(Guid.NewGuid().ToString(), new MemoryStream(imageContent)),
            Folder = "image-files"
        };

        var uploadResult = await _cloudinary.UploadAsync(uploadParams);

        if (uploadResult.StatusCode == System.Net.HttpStatusCode.OK)
        {
            return uploadResult.SecureUrl.ToString();
        }

        throw new Exception("Image upload failed: " + uploadResult.Error?.Message);
    }
}
```

Adicione ao `Program.cs`:

```csharp
builder.Services.AddSingleton<CloudinaryService>();
```

4.7 Crie o endpoint `/generate-audio` :

```csharp
app.MapPost("/generate-audio", async (TextToSpeechService textToSpeechService, CloudinaryService cloudinary, [FromBody] JsonElement body) =>
    {
        if (!body.TryGetProperty("input", out var inputJson) || string.IsNullOrWhiteSpace(inputJson.GetString()))
        {
            return Results.BadRequest(new { success = false, message = "Required parameter 'input' is missing or invalid." });
        }

        var input = inputJson.GetString()!;
        try
        {
            var mp3Data = await textToSpeechService.SynthesizeTextToSpeech(input);
            var audioUrl = await cloudinary.UploadAudio(mp3Data);
            return Results.Ok(audioUrl);
        }
        catch (Exception ex)
        {
            return Results.Problem(detail: ex.Message);
        }
    })
    .WithName("GenerateAudio")
    .Produces<string>()
    .Produces(400);
```

### Geração de legendas usando o AssemblyAI

- O ponto de extremidade `/generate-captions` processa o arquivo MP3 e cria legendas.

5.1 Crie uma conta no [site do AssemblyAI](https://www.assemblyai.com/) e obtenha sua chave de API

5.2 Atualize o arquivo `appsettings.Development.json` :

```csharp
{
  "GoogleApi": {
    "GeminiKey": "your-gemini-api-key",
    "TextToSpeechKey": "your-text-to-speech-api-key"
  },
  "AssemblyAi": {
    "ApiKey": "your-assemblyai-api-key"
  }
}
```

5.3 Adicione o `AssemblyAI` NuGet package ao projeto:

```bash
dotnet add package AssemblyAI
```

5.4 Crie uma classe de serviço para o AssemblyAI:

```csharp
using AssemblyAI;
using AssemblyAI.Transcripts;

namespace AiShortsGenerator.Services;

public class AssemblyAiService(IConfiguration configuration)
{
    public async Task<IEnumerable<TranscriptWord>> Transcribe(string fileUrl)
    {
        var apiKey = configuration["AssemblyAi:ApiKey"];

        if (string.IsNullOrEmpty(apiKey))
        {
            throw new InvalidOperationException("API key is not configured");
        }

        var client = new AssemblyAIClient(apiKey);

        var transcriptParams = new TranscriptParams
        {
            AudioUrl = fileUrl
        };

        var transcript = await client.Transcripts.TranscribeAsync(transcriptParams);

        transcript.EnsureStatusCompleted();

        if (transcript.Words == null)
        {
            throw new InvalidOperationException("Transcript contains no words.");
        }

        return transcript.Words;
    }
}
```

Adicione ao `Program.cs`:

```csharp
builder.Services.AddScoped<AssemblyAiService>();
```

5.5 Crie o endpoint `/generate-captions` :

```csharp
app.MapPost("/generate-captions", async (AssemblyAiService assemblyAiService, [FromBody] JsonElement body) =>
    {
        if (!body.TryGetProperty("fileUrl", out var inputJson) || string.IsNullOrWhiteSpace(inputJson.GetString()))
        {
            return Results.BadRequest(new { success = false, message = "Required parameter 'fileUrl' is missing or invalid." });
        }

        var fileUrl = inputJson.GetString()!;
        try
        {
            var transcript = await assemblyAiService.Transcribe(fileUrl);

            return Results.Ok(transcript);
        }
        catch (Exception ex)
        {
            return Results.BadRequest(new { success = false, message = ex.Message });
        }
    })
    .WithName("GenerateCaptions")
    .Produces<string>()
    .Produces(400);
```

### 6. Geração de imagens usando a IA do Cloudflare Workers

O endpoint `/generate-image` converte prompts de texto em imagens.
6.1 Obtenha a chave da API do Cloudflare e o ID da conta necessários para usar o [Workers AI](https://developers.cloudflare.com/workers-ai/).

6.2 Atualize o arquivo `appsettings.Development.json` :

```json
{
  "GoogleApi": {
    "GeminiKey": "your-gemini-api-key",
    "TextToSpeechKey": "your-text-to-speech-api-key"
  },
  "AssemblyAi": {
    "ApiKey": "your-assemblyai-api-key"
  },
  "CloudinaryUrl": "your-cloudinary-url",
  "Cloudflare": {
    "ApiKey": "your-cloudflare-api-key",
    "AccountId": "your-cloudflare-account-id"
  }
}
```

6.3 Crie uma classe para a resposta da API do Cloudflare e a requisição do endpoint:

```csharp
namespace AiShortsGenerator.DTOs
{
    public class CloudflareApiResponse
    {
        public CloudflareResult Result { get; set; }
        public bool Success { get; set; }
        public List<string> Errors { get; set; }
        public List<string> Messages { get; set; }
    }

    public class CloudflareResult
    {
        public string Image { get; set; }
    }
}
```

```csharp
namespace AiShortsGenerator.DTOs;

public class GenerateImageRequest
{
    public string Prompt { get; set; }
}
```

6.4 Crie a classe de serviço para a API do Cloudflare Workers AI:

```csharp
using System.Net.Http.Headers;
using System.Text;
using System.Text.Json;
using AiShortsGenerator.DTOs;

namespace AiShortsGenerator.Services;

public class CloudflareApiService
{
    private readonly HttpClient _httpClient;
    private readonly string _apiUrl;

    public CloudflareApiService(HttpClient httpClient, IConfiguration configuration)
    {
        _httpClient = httpClient;
        _apiUrl = $"https://api.cloudflare.com/client/v4/accounts/{configuration["Cloudflare:AccountId"]}/ai/run/@cf/black-forest-labs/flux-1-schnell";

        var apiKey = configuration["Cloudflare:ApiKey"];
        if (string.IsNullOrEmpty(apiKey))
        {
            throw new ArgumentException("Cloudflare API key is missing in configuration.");
        }

        _httpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", apiKey);
    }

    public async Task<byte[]> GenerateImageAsync(string prompt)
    {
        var payload = new
        {
            prompt
        };

        var jsonPayload = JsonSerializer.Serialize(payload);
        var content = new StringContent(jsonPayload, Encoding.UTF8, "application/json");

        var response = await _httpClient.PostAsync(_apiUrl, content);

        if (!response.IsSuccessStatusCode)
        {
            var errorMessage = await response.Content.ReadAsStringAsync();
            throw new HttpRequestException($"Cloudflare API request failed with status code {response.StatusCode}: {errorMessage}");
        }

        var responseContent = await response.Content.ReadFromJsonAsync<CloudflareApiResponse>();

        if (responseContent is not { Success: true })
        {
            var errors = string.Join("; ", responseContent?.Errors ?? ["Unknown error"]);
            throw new HttpRequestException($"Failed to generate image: {errors}");
        }

        if (responseContent.Result.Image == null)
        {
            throw new HttpRequestException("Failed to generate image: No image returned by Cloudflare API.");
        }

        if (!(responseContent.Messages.Count > 0))
        {
            return Convert.FromBase64String(responseContent.Result.Image);
        }

        Console.WriteLine("Cloudflare API Messages:");
        foreach (var message in responseContent.Messages)
        {
            Console.WriteLine($"- {message}");
        }

        return Convert.FromBase64String(responseContent.Result.Image);
    }
}
```

Adicione ao `Program.cs`:

```csharp
builder.Services.AddHttpClient<CloudflareApiService>();
```

6.5 Crie o endpoint `/generate-image`:

```csharp
app.MapPost("/generate-image", async (CloudflareApiService cloudflareApiService, CloudinaryService cloudinary, [FromBody] GenerateImageRequest request) =>
{
    if (string.IsNullOrWhiteSpace(request.Prompt))
    {
        return Results.BadRequest("Prompt is required.");
    }

    try
    {
        var imageBytes = await cloudflareApiService.GenerateImageAsync(request.Prompt);
        var imageUrl = await cloudinary.UploadImage(imageBytes);
        return Results.Ok(imageUrl);
    }
    catch (HttpRequestException ex)
    {
        return Results.Problem(ex.Message, statusCode: 500);
    }
})
.WithName("GenerateImage")
.Produces<string>()
.Produces(400)
.Produces(500);
```

### 7. Gerenciamento de vídeos no banco de dados

Depois que o conteúdo gerado pela IA (imagens, áudio, legendas) é processado, os vídeos precisam ser armazenados, atualizados e recuperados do banco de dados.

7.1 Crie o model de vídeo:

```csharp
using System.Text.Json.Serialization;

namespace AiShortsGenerator.Models;

public class Video
{
    public int Id { get; set; }

    [JsonInclude]
    public List<VideoContentItem> VideoContent { get; set; } = [];

    public string AudioFileUrl { get; set; }

    [JsonInclude]
    public List<TranscriptSegment> Captions { get; set; } = [];

    public List<string> Images { get; set; }

    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public string? OutputFile { get; set; }
    public string? RenderId { get; set; }
}
```

```csharp
namespace AiShortsGenerator.Models;

public class VideoContentItem(string imagePrompt, string contextText)
{
    public string ImagePrompt { get; set; } = imagePrompt;
    public string ContextText { get; set; } = contextText;
}
```

```csharp
namespace AiShortsGenerator.Models;

public class TranscriptSegment
{
    public double Confidence { get; set; }
    public double Start { get; set; }
    public double End { get; set; }
    public string Text { get; set; }
    public string Channel { get; set; }
    public string Speaker { get; set; }
}
```

```csharp
namespace AiShortsGenerator.Models;

public class Mp3File(string fileName, byte[] fileData)
{
    public string FileName { get; init; } = fileName;
    public byte[] FileData { get; init; } = fileData;
    public DateTime CreatedAt { get; init; }
}
```

7.2 Crie um banco de dados de sua escolha e atualize o arquivo `appsettings.Development.json`:

```bash
{
  "GoogleApi": {
    "GeminiKey": "your-gemini-api-key",
    "TextToSpeechKey": "your-text-to-speech-api-key"
  },
  "AssemblyAi": {
    "ApiKey": "your-assemblyai-api-key"
  },
  "CloudinaryUrl": "your-cloudinary-url",
  "Cloudflare": {
    "ApiKey": "your-cloudflare-api-key",
    "AccountId": "your-cloudflare-account-id"
  },
  "ConnectionStrings": {
    "DefaultConnection": "your-postgresql-connection-string"
  }
}
```

7.3 Adicione o PostgreSQL data e EF Core packages:

```bash
dotnet add package Npgsql
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Microsoft.EntityFrameworkCore.Design
```

7.4 Crie e aplique as migrations:

```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

7.5 Configure o DbContext:

```csharp
using AiShortsGenerator.Models;
using Microsoft.EntityFrameworkCore;

namespace AiShortsGenerator.Data;

public class AppDbContext(DbContextOptions<AppDbContext> options) : DbContext(options)
{
    public DbSet<Video> Videos { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<Video>(entity =>
        {
            entity.Property(v => v.VideoContent)
                .HasColumnType("jsonb");

            entity.Property(v => v.Captions)
                .HasColumnType("jsonb");
        });
    }
}
```

Adicione ao `Program.cs`:

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
{
    var dataSourceBuilder = new NpgsqlDataSourceBuilder(builder.Configuration.GetConnectionString("DefaultConnection"));
    dataSourceBuilder.EnableDynamicJson();
    options.UseNpgsql(dataSourceBuilder.Build());
});
```

7.6 Crie o endpoint para salvar um vídeo:

```csharp
app.MapPost("/save-video", async ([FromBody] Video video, AppDbContext context) =>
{
    await context.Videos.AddAsync(video);
    await context.SaveChangesAsync();

    return Results.Ok(new { message = "Video saved successfully", videoId = video.Id });
})
.WithName("SaveVideo")
.Produces(200);
```

7.7 Crie o endpoint para atualizar um vídeo:

```csharp
app.MapPut("/videos/{id:int}", async (int id, AppDbContext context, [FromBody] UpdateVideoRequest request) =>
{
    var video = await context.Videos.FirstOrDefaultAsync(v => v.Id == id);

    if (video == null)
    {
        return Results.NotFound();
    }

    if (string.IsNullOrEmpty(request.OutputFile))
    {
        return Results.Ok(video);
    }

    video.OutputFile = request.OutputFile;
    video.RenderId = request.RenderId;
    await context.SaveChangesAsync();

    return Results.Ok(video);
})
.WithName("UpdateVideo")
.Produces<Video>()
.Produces(404);
```

7.8 Crie a DTO de requisição de atualização do vídeo:

```csharp
namespace AiShortsGenerator.DTOs;

public class UpdateVideoRequest
{
    public string OutputFile { get; set; }
    public string RenderId { get; set; }
}
```

7.9 Crie o endpoint para listar vídeos:

```csharp
app.MapGet("/videos", async (AppDbContext context) =>
{
    var videos = await context.Videos.ToListAsync();

    return Results.Ok(videos);
})
.WithName("GetVideos")
.Produces<List<Video>>();
```

7.10 Crie o endpoint para excluir um vídeo:

```csharp
app.MapDelete("/videos/{id:int}", async (int id, AppDbContext context) =>
{
    var video = await context.Videos.FirstOrDefaultAsync(v => v.Id == id);

    if (video == null)
    {
        return Results.NotFound();
    }

    context.Videos.Remove(video);
    await context.SaveChangesAsync();

    return Results.NoContent();
})
.WithName("DeleteVideo")
.Produces(204)
.Produces(404);
```

### **8. Finalizando o backend**

### **8.1. Configuração do CORS (compartilhamento de recursos entre origens)**

Como nosso frontend (Next.js) e backend (ASP.NET Core) são executados em portas diferentes durante o desenvolvimento, precisamos configurar o **CORS** para permitir a comunicação entre eles.

### **Configure o CORS no `Program.cs`**

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowSpecificOrigins", policy =>
    {
        policy.WithOrigins("http://localhost:3000") // Allow frontend
            .AllowAnyHeader()
            .AllowAnyMethod();
    });

    options.AddPolicy("AllowAll", policy =>
    {
        policy.AllowAnyOrigin()
            .AllowAnyHeader()
            .AllowAnyMethod();
    });
});

var app = builder.Build();

// Apply CORS based on the environment
app.UseCors(app.Environment.IsDevelopment() ? "AllowAll" : "AllowSpecificOrigins");
```

### **Explicação:**

- Durante o **desenvolvimento**, ele permite **todas as origens**, facilitando a depuração.
- Na **produção**, ele restringe o acesso **apenas ao URL do frontend**.

### **8.2. Configuração da migração do banco de dados**

Para garantir que o esquema do banco de dados esteja atualizado, executamos as **migrações doEntity Framework Core** na inicialização do aplicativo.

### **Aplicar automaticamente as migrações**

```csharp
using (var scope = app.Services.CreateScope())
{
    var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    dbContext.Database.Migrate(); // Applies pending migrations automatically
}
```

### **Comandos de migração manual**

Se você preferir executar migrações manualmente, use:

```csharp
# Create a new migration
dotnet ef migrations add InitialCreate 

# Apply migrations
dotnet ef database update
```

### Por que automatizar migrações?

- Garante uma **implementação tranquila** sem intervenção manual.
- Evita erros causados por um **esquema de banco de dados** desatualizado.

### **9. Executando o backend**

Com tudo configurado, inicie o backend com:

```csharp
dotnet run
```

## **Implementação passo a passo do frontend**

O front-end foi criado com o Next.js e fornece a interface do usuário para o gerenciamento dos vídeos.

### 1. Configuração do projeto

1.1 Instale as dependências

```bash
npm install axios lucide-react
```

1.2 Adicione os componentes **button, card, dialog, dropdown-menu, input, label, progress, select, separator, sheet, sidebar, skeleton, sonner, textarea** e tooltip de [shadcn/ui](https://ui.shadcn.com/)

1.3 Instale o Remotion seguindo este guia: [**Instalar o Remotion em um projeto existente**](https://www.remotion.dev/docs/brownfield)

1.4 **Configure as variáveis de ambiente** (em `.env.local`)

```bash
NEXT_PUBLIC_API_URL=http://localhost:5211
```

### 2. Implementar a Landing Page

### **2.1. Página inicial (`app/(site)/page.tsx`)**

- Página de destino com um botão CTA (Call To Action).

```tsx
import Link from 'next/link'
import { Button } from '@/components/ui/button'

export default function Home() {
  return (
    <div className='flex min-h-screen flex-col items-center justify-center px-4 text-center'>
      <div className='w-full max-w-3xl'>
        <h1 className='text-4xl font-bold sm:text-5xl md:text-6xl'>Create AI Shorts Instantly</h1>
        <p className='mt-4 text-lg text-muted-foreground sm:text-xl'>
          AI-generated short videos with subtitles and voiceovers—no editing needed!
        </p>
        <Button asChild className='mt-8 w-full sm:w-auto'>
          <Link href='/dashboard/create-new'>Start Generating</Link>
        </Button>
      </div>
    </div>
  )
}
```

### **2.2. Navbar Component (`app/components/NavBar.tsx`)**

- Crie uma **barra de navegação** reutilizável.

```tsx
import Link from 'next/link'

export default function Navbar() {
  return (
    <nav className='p-4 shadow-md'>
      <div className='container mx-auto flex justify-between'>
        <Link href='/' className='text-xl font-bold'>AI Shorts</Link>
        <Link href='/dashboard' className='text-gray-600 hover:underline'>Dashboard</Link>
      </div>
    </nav>
  )
}
```

### **2.3. Layout do site (`app/(site)/layout.tsx`)**

- Envolve a landing page com uma **barra de navegação**.

```tsx
import Navbar from '../components/NavBar'

export default function SiteLayout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <Navbar />
      <main className='grow'>{children}</main>
    </>
  )
}
```

### **3. Implementar o painel**

Implementei o painel de controle seguindo este tutorial: [**A maneira mais fácil de criar um menu de barra lateral no NextJs 15**](https://www.youtube.com/watch?v=rLf6-RHE5W8)

### **3.1. Layout da dashboard(`app/dashboard/layout.tsx`)**

```tsx
import DashboardSidebar from '../components/DashboardSidebar'

export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className='flex'>
      <DashboardSidebar />
      <main className='flex-grow p-4'>{children}</main>
    </div>
  )
}
```

### 3.2 DashboardSider Component(`app/dashboard/_components/DashboardSidebar.tsx`)

```tsx
import Link from 'next/link'
import { ArrowLeft, FileVideo, LayoutDashboard } from 'lucide-react'

import {
  Sidebar,
  SidebarContent,
  SidebarGroup,
  SidebarGroupContent,
  SidebarGroupLabel,
  SidebarHeader,
  SidebarMenu,
  SidebarMenuButton,
  SidebarMenuItem,
  SidebarRail,
} from '@/components/ui/sidebar'

// Menu items.
const items = [
  {
    title: 'Dashboard',
    url: '/dashboard',
    icon: LayoutDashboard,
  },
  {
    title: 'Create new',
    url: '/dashboard/create-new',
    icon: FileVideo,
  },
]

export default function DashboardSidebar() {
  return (
    <Sidebar collapsible='icon' variant='inset'>
      <SidebarHeader>
        <SidebarMenu>
          <SidebarMenuItem>
            <SidebarMenuButton asChild>
              <Link href='/' className='text-sky-700 hover:text-sky-600'>
                <ArrowLeft />
                <span>Back to site</span>
              </Link>
            </SidebarMenuButton>
          </SidebarMenuItem>
        </SidebarMenu>
      </SidebarHeader>

      <SidebarContent>
        <SidebarGroup>
          <SidebarGroupLabel>Dashboard</SidebarGroupLabel>

          <SidebarGroupContent>
            <SidebarMenu>
              {items.map((item) => (
                <SidebarMenuItem key={item.title}>
                  <SidebarMenuButton asChild>
                    <Link href={item.url}>
                      <item.icon />
                      <span>{item.title}</span>
                    </Link>
                  </SidebarMenuButton>
                </SidebarMenuItem>
              ))}
            </SidebarMenu>
          </SidebarGroupContent>
        </SidebarGroup>
      </SidebarContent>

      <SidebarRail />
    </Sidebar>
  )
}
```

### **3.3. Página da dashboard(`app/dashboard/page.tsx`)**

- Exibe a **lista de vídeos** e um botão **"Criar novo vídeo ”**.

```tsx
import Link from 'next/link'
import { PlusCircle } from 'lucide-react'
import { Button } from '@/components/ui/button'
import ShortVideoGrid from './_components/ShortVideoGrid'

export default function DashboardPage() {
  return (
    <div className='p-6'>
      <div className='flex justify-between'>
        <h1 className='text-2xl font-bold'>Dashboard</h1>
        <Link href='/dashboard/create-new'>
          <Button>
            <PlusCircle className='mr-2' /> Create New Video
          </Button>
        </Link>
      </div>
      <ShortVideoGrid />
    </div>
  )
}
```

### 3.4 Short Video Grid Component **(`app/dashboard/_components/ShortVideoGrid.tsx`)**

```tsx
'use client'

import { useEffect, useState } from 'react'
import { PlusCircle } from 'lucide-react'
import axios from 'axios'
import Link from 'next/link'
import { Thumbnail } from '@remotion/player'

import { MyComposition } from '@/remotion/Composition'
import type { VideoData } from '@/app/lib/interface'
import { Button } from '@/components/ui/button'

import { SkeletonCard } from './SkeletonCard'

type ShortVideoGridData = {
  createdAt: string
  id: number // Assuming each video has a unique id
} & VideoData

export default function ShortVideoGrid() {
  const [loading, setLoading] = useState(false)
  const [videos, setVideos] = useState<ShortVideoGridData[]>([])

  const GetVideos = async () => {
    setLoading(true)
    const resp = await axios.get(`${process.env.NEXT_PUBLIC_API_URL}/videos`)
    if (resp.data) {
      const sortedVideos = resp.data.sort(
        (a: ShortVideoGridData, b: ShortVideoGridData) =>
          new Date(b.createdAt).getTime() - new Date(a.createdAt).getTime(),
      )
      setVideos(sortedVideos)
    }

    setLoading(false)
  }

  useEffect(() => {
    GetVideos()
  }, [])

  if (loading) {
    return <SkeletonCard count={10} />
  }

  if (videos.length === 0) {
    return (
      <div className='flex h-64 flex-col items-center justify-center'>
        <p className='mb-4 text-xl'>No short videos yet</p>
        <Link href={'/dashboard/create-new'}>
          <Button>
            <PlusCircle className='mr-2 size-4' /> Create New Short Video
          </Button>
        </Link>
      </div>
    )
  }

  return (
    <>
      <div className='grid grid-cols-1 gap-4 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5'>
        {videos.map((video) => (
          <button
            key={video.id} // Use a unique identifier as the key
            className='relative aspect-square h-[450px] w-[300px] overflow-hidden rounded-lg transition-all duration-300 ease-in-out hover:scale-105 focus:outline-none focus:ring-2 focus:ring-primary'
          >
            <Thumbnail
              component={MyComposition}
              compositionWidth={300}
              compositionHeight={450}
              frameToDisplay={30}
              durationInFrames={120}
              fps={30}
              inputProps={{
                ...video,
                setDurationInFrame: (a: number) => console.log(a),
              }}
            />
          </button>
        ))}
      </div>
    </>
  )
}
```

### 3.5 Skeleton Card Component **(`app/dashboard/_components/SkeletonCard.tsx`)**

```tsx
import { Skeleton } from '@/components/ui/skeleton'

export const SkeletonCard = ({ count = 5 }) => {
  return (
    <div className='grid grid-cols-1 gap-4 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5'>
      {Array.from({ length: count }).map((_, index) => (
        // eslint-disable-next-line react/no-array-index-key
        <Skeleton key={index} className='h-[450px] w-[300px] rounded-xl' />
      ))}
    </div>
  )
}

```

### 4. Implementar a geração de vídeo

- Trata a entrada do usuário** (tópico, estilo, duração).
- Busca o conteúdo gerado pela IA** (script, áudio, legendas, imagens).
- Reproduz o vídeo gerado**.

### 4.1 Crie types se estiver usando TypeScript:

```tsx
export type VideoContentItem = {
  imagePrompt: string
  contextText: string
}

export type TranscriptSegment = {
  confidence: number
  start: number
  end: number
  text: string
  channel: string | null
  speaker: string | null
}

export type VideoData = {
  id?: number
  videoContent: VideoContentItem[]
  audioFileUrl: string
  captions: TranscriptSegment[]
  images: string[]
  outputFile?: string
  renderId?: string
}
```

### 4.2 Select Topic components `app/dashboard/create-new/_components/SelectTopic.tsx`:

```tsx
'use client'

import { useState } from 'react'

import { Label } from '@/components/ui/label'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select'
import { Textarea } from '@/components/ui/textarea'

const options = [
  'Custom Prompt',
  'Random AI Story',
  'Historical Facts',
  'Fun Facts',
  'Science Facts',
  'Motivational',
  'Scary Story',
  'Adventure Story',
  'Fantasy Story',
  'Sci-Fi Story',
  'Steampunk Story',
  'Romance Story',
  'Mystery/Thriller Story',
  'Historical Fiction',
  'Poems',
  'Tech Trends',
  'Philosophical Quotes',
  'Space Exploration',
  'Mythology',
]

type SelectTopicProps = {
  // eslint-disable-next-line no-unused-vars
  onUserSelect: (fieldName: string, fieldValue: string) => void
}

export default function SelectTopic({ onUserSelect }: SelectTopicProps) {
  const [contentType, setContentType] = useState('')
  return (
    <div className='space-y-2'>
      <Label htmlFor='content-type' className='text-lg font-semibold'>
        Content Type
      </Label>
      <Select
        onValueChange={(value) => {
          setContentType(value)
          if (value !== 'Custom Prompt') {
            onUserSelect('topic', value)
          }
        }}
        value={contentType}
      >
        <SelectTrigger id='content-type' name='topic'>
          <SelectValue placeholder='Select content type' />
        </SelectTrigger>
        <SelectContent>
          {options.map((item) => (
            <SelectItem key={item} value={item}>
              {item}
            </SelectItem>
          ))}
        </SelectContent>
      </Select>
      {contentType === 'Custom Prompt' && (
        <Textarea
          onChange={(e) => onUserSelect('topic', e.target.value)}
          placeholder='Write your custom prompt'
        />
      )}
    </div>
  )
}
```

### 4.3 Select Style components `app/dashboard/create-new/_components/SelectStyle.tsx`:

```tsx
'use client'

import { useState } from 'react'
import Image from 'next/image'

import { Card, CardTitle } from '@/components/ui/card'

const options = [
  {
    name: 'Realistic',
    image: '/images/realistic.png',
  },
  {
    name: 'Cartoon',
    image: '/images/cartoon.png',
  },
  {
    name: 'Comic',
    image: '/images/comic.png',
  },
  {
    name: 'WaterColor',
    image: '/images/watercolor.png',
  },
  {
    name: 'Drawing',
    image: '/images/drawing.png',
  },
  {
    name: 'Monochrome',
    image: '/images/monochrome.png',
  },
  {
    name: 'Oil Painting',
    image: '/images/oil-painting.png',
  },
  {
    name: 'Pixel Art',
    image: '/images/pixel-art.png',
  },
  {
    name: 'retro',
    image: '/images/retro.png',
  },
  {
    name: 'Surreal',
    image: '/images/surreal.png',
  },
]

type SelectStyleProps = {
  // eslint-disable-next-line no-unused-vars
  onUserSelect: (fieldName: string, fieldValue: string) => void
}

export default function SelectStyle({ onUserSelect }: SelectStyleProps) {
  const [selectOption, setSelectOption] = useState('')
  return (
    <div className='space-y-2'>
      <legend className='mb-4 text-lg font-semibold'>Image Style</legend>
      <div
        id='style'
        className='grid grid-cols-2 gap-5 md:grid-cols-3 lg:grid-cols-5 xl:grid-cols-6'
      >
        {options.map((item) => (
          <Card
            onClick={() => {
              setSelectOption(item.name)
              onUserSelect('imageStyle', item.name)
            }}
            key={item.name}
            className={`cursor-pointer transition-all ${
              selectOption === item.name
                ? 'border-4 border-black dark:border-white'
                : 'hover:scale-105'
            }`}
          >
            <div className='relative aspect-square w-full'>
              <div className='absolute inset-0'>
                <Image
                  alt='Image'
                  className={`h-auto w-full rounded-lg object-cover ${
                    selectOption === item.name ? 'opacity-50' : 'opacity-100'
                  }`}
                  height='1024'
                  src={item.image}
                  width='1024'
                  priority
                />
              </div>
              <div className='absolute inset-x-0 bottom-0 rounded-b-lg bg-black/75 p-2'>
                <CardTitle className='text-2xl font-bold text-white'>
                  {item.name}
                </CardTitle>
              </div>
            </div>
          </Card>
        ))}
      </div>
    </div>
  )
}
```

As imagens foram criadas usando a IA do Cloudflare Workers.

### 4.4 Selecionar componentes de duração `app/dashboard/create-new/_components/SelectDuration.tsx`:

```tsx
import { Label } from '@/components/ui/label'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select'

type SelectDurationProps = {
  // eslint-disable-next-line no-unused-vars
  onUserSelect: (fieldName: string, fieldValue: string) => void
}

export default function SelectDuration({ onUserSelect }: SelectDurationProps) {
  return (
    <div className='space-y-2'>
      <Label htmlFor='video-duration' className='text-lg font-semibold'>
        Video Duration
      </Label>
      <Select onValueChange={(value) => onUserSelect('duration', value)}>
        <SelectTrigger id='video-duration' name='duration'>
          <SelectValue placeholder='Select duration' />
        </SelectTrigger>
        <SelectContent>
          <SelectItem value='15 seconds'>15 seconds</SelectItem>
          <SelectItem value='30 seconds'>30 seconds</SelectItem>
          <SelectItem value='60 seconds'>60 Seconds</SelectItem>
        </SelectContent>
      </Select>
    </div>
  )
}
```

### 4.5 Loading Component `app/components/Loading.tsx`

```tsx
import { Progress } from '@/components/ui/progress'

type LoadingProps = {
  loading: boolean
  progress?: number
  message: string
  showProgress?: boolean
}

export default function Loading({
  loading,
  progress,
  message,
  showProgress = true,
}: LoadingProps) {
  if (!loading) return null

  return (
    <div
      className={`fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50 ${loading ? 'flex' : 'hidden'}`}
    >
      <div className='flex flex-col items-center space-y-4'>
        <div className='size-12 animate-spin rounded-full border-4 border-gray-400 border-t-transparent' />
        <p className='text-white dark:text-gray-300'>{message}</p>

        {showProgress && progress !== undefined && (
          <Progress
            value={progress}
            max={100}
            className='mt-4 h-2 w-64 rounded-full bg-gray-300 dark:bg-gray-700'
          />
        )}
      </div>
    </div>
  )
}

```

### 4.6 Page create new`app/dashboard/create-new/page.tsx`:

```tsx
'use client'

import { useEffect, useState } from 'react'
import axios from 'axios'

import { Button } from '@/components/ui/button'
import {
  Card,
  CardContent,
  CardFooter,
  CardHeader,
  CardTitle,
} from '@/components/ui/card'
import Loading from '@/app/components/Loading'
import type { VideoContentItem, VideoData } from '@/app/lib/interface'

import SelectTopic from './_components/SelectTopic'
import SelectStyle from './_components/SelectStyle'
import SelectDuration from './_components/SelectDuration'

type FormData = {
  topic: string
  imageStyle: string
  duration: string
}

export default function CreateNew() {
  const [isLoading, setIsLoading] = useState(false)
  const [loadingMessage, setLoadingMessage] = useState(
    'Generating your video...',
  )
  const [progress, setProgress] = useState(0)
  const [formData, setFormData] = useState<FormData>({} as FormData)
  const [videoData, setVideoData] = useState<VideoData>({
    videoContent: [],
    audioFileUrl: '',
    captions: [],
    images: [],
  })

  useEffect(() => {
    if (
      videoData.videoContent.length > 0 &&
      videoData.audioFileUrl &&
      videoData.captions.length > 0 &&
      videoData.images.length > 0
    ) {
      SaveVideoToDatabase(videoData)
    }
  }, [videoData])

  const onHandleInputChange = (fieldName: string, fieldValue: string) => {
    setFormData((prev) => ({
      ...prev,
      [fieldName]: fieldValue,
    }))
  }

  const onCreateSubmitHandler = (e: React.FormEvent) => {
    e.preventDefault()
    getVideoContent()
  }

  const getVideoContent = async () => {
    setIsLoading(true)
    setLoadingMessage('Generating video content...')
    setProgress(20)

    const prompt = `Generate a script for a video lasting ${formData.duration} seconds on the topic '${formData.topic}'. For each scene, provide the following in JSON format: [{'ContextText': '<Description of the scene (concise and fitting the duration)>','ImagePrompt': '<AI image generation prompt in ${formData.imageStyle} style>'}] Ensure all fields are well-structured, and do not include plain text outside the JSON.`

    const resp = await axios.post(
      `${process.env.NEXT_PUBLIC_API_URL}/generate-content`,
      {
        input: prompt,
      },
    )

    if (resp.data) {
      setVideoData((prev) => {
        return {
          ...prev,
          videoContent: resp.data,
        }
      })
      await GenerateAudioFile(resp.data)
    }
  }

  const GenerateAudioFile = async (videoContentData: VideoContentItem[]) => {
    setLoadingMessage('Generating audio file...')
    setProgress(50)
    let script = ''
    videoContentData.forEach((item) => {
      script = script + item.contextText + ''
    })
    const resp = await axios.post(
      `${process.env.NEXT_PUBLIC_API_URL}/generate-audio`,
      {
        input: script,
      },
    )
    if (resp.data) {
      setVideoData((prev) => {
        return {
          ...prev,
          audioFileUrl: resp.data,
        }
      })
      await GenerateCaptions(resp.data, videoContentData)
    }
  }

  const GenerateCaptions = async (
    fileUrl: string,
    videoContentData: VideoContentItem[],
  ) => {
    setLoadingMessage('Generating captions...')
    setProgress(75)
    const resp = await axios.post(
      `${process.env.NEXT_PUBLIC_API_URL}/generate-captions`,
      {
        fileUrl,
      },
    )
    if (resp.data) {
      setVideoData((prev) => {
        return {
          ...prev,
          captions: resp.data,
        }
      })
      await GenerateImage(videoContentData)
    }
  }

  const SaveVideoToDatabase = async (videoData: VideoData) => {
    try {
      await axios.post(`${process.env.NEXT_PUBLIC_API_URL}/save-video`, {
        videoContent: videoData.videoContent,
        captions: videoData.captions,
        images: videoData.images,
        audioFileUrl: videoData.audioFileUrl,
      })
    } catch (error) {
      console.error('Error saving video:', error)
    }
  }

  const GenerateImage = async (videoContent: VideoContentItem[]) => {
    setLoadingMessage(
      'Generating images... This part can take a minute or two.',
    )
    setProgress(90)
    const responseImages: string[] = []
    for (const item of videoContent) {
      try {
        const resp = await axios.post(
          `${process.env.NEXT_PUBLIC_API_URL}/generate-image`,
          {
            prompt: item.imagePrompt,
          },
        )
        responseImages.push(resp.data)
      } catch (e) {
        console.log('Error:' + e)
      }
    }
    setVideoData((prev) => {
      return {
        ...prev,
        images: responseImages,
      }
    })
    setProgress(100)
    setIsLoading(false)
  }

  return (
    <div className='container mx-auto px-4 py-8'>
      <Card className='mx-auto max-w-full'>
        <CardHeader>
          <CardTitle className='text-center text-2xl font-bold'>
            Create New Short Video
          </CardTitle>
        </CardHeader>
        <form onSubmit={onCreateSubmitHandler}>
          <CardContent className='space-y-4'>
            <SelectTopic onUserSelect={onHandleInputChange} />
            <SelectStyle onUserSelect={onHandleInputChange} />
            <SelectDuration onUserSelect={onHandleInputChange} />
          </CardContent>
          <CardFooter>
            <Button type='submit' className='w-full'>
              Generate
            </Button>
          </CardFooter>
        </form>
      </Card>
      <Loading
        loading={isLoading}
        progress={progress}
        message={loadingMessage}
      />
    </div>
  )
}

```

### 5. Criar a caixa de diálogo com o player de vídeo do Remotion

Depois que tudo for gerado com sucesso, reproduz o vídeo.

### 5.1 Componente VideoPlayerDialog `app/components/VideoPlayerDialog.tsx`

```tsx
import { useEffect, useState } from 'react'
import { Player } from '@remotion/player'
import axios from 'axios'
import { useRouter } from 'next/navigation'
import { Download, Trash, X } from 'lucide-react'

import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from '@/components/ui/dialog'
import { Button } from '@/components/ui/button'
import { MyComposition } from '@/remotion/Composition'
import type { VideoData } from '@/app/lib/interface'

import Loading from './Loading'

type VideoPlayerDialogProps = {
  video: VideoData | null
  isOpen: boolean
  onClose: () => void
  refreshVideos?: () => void
}

export function VideoPlayerDialog({
  video,
  isOpen,
  onClose,
  refreshVideos,
}: VideoPlayerDialogProps) {
  const router = useRouter()
  const [isLoading, setIsLoading] = useState(false)
  const [outputFileUrl, setOutputFileUrl] = useState<string | null>(null)

  useEffect(() => {
    if (video?.outputFile) {
      setOutputFileUrl(video.outputFile)
    } else {
      setOutputFileUrl(null)
    }
  }, [video])

  if (!video) {
    return null
  }

  const durationInFrame =
    video.captions.length > 0
      ? Math.ceil((video.captions[video.captions.length - 1].end / 1000) * 30)
      : 700

  const handleCancel = () => {
    onClose()
    router.push('/dashboard')
  }

  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className='max-w-xl p-0'>
        <DialogHeader className='border-b p-4'>
          <DialogTitle className='text-lg font-semibold'>
            Generated Video
          </DialogTitle>
          <DialogDescription>
            Preview your generated video. You can export, delete or cancel to go
            back the dashboard.
          </DialogDescription>
        </DialogHeader>

        <Loading
          loading={isLoading}
          showProgress={false}
          message='Rendering video, please wait...'
        />
        <div className='flex justify-center p-4'>
          {video && (
            <Player
              className='h-auto w-full rounded-md shadow-md'
              component={MyComposition}
              durationInFrames={Number(durationInFrame.toFixed(0))}
              compositionWidth={300}
              compositionHeight={450}
              fps={30}
              controls
              inputProps={{
                ...video,
              }}
            />
          )}
        </div>
        
         <DialogFooter className='gap-4 border-t p-4 sm:justify-between'>
          <div className='flex space-x-2'>
            <Button type='button' variant='secondary' onClick={handleCancel}>
              <X className='mr-2 size-4' />
              Cancel
            </Button>
          </div>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  )
}

```

### 5.2 Adicione a caixa de diálogo do player de vídeo ao `dashboard/create-new/page.tsx`:

```tsx
export default function CreateNew() {
  const [playVideo, setPlayVideo] = useState(false)
```

- O `playVideo` é um **estado booleano** que determina se o `VideoPlayerDialog` deve ser aberto (`true`) ou fechado (`false`).
- Ele é inicialmente definido como `false`, o que significa que a caixa de diálogo **não é exibida** até que o vídeo esteja pronto.

```tsx
    setPlayVideo(true)
  }

  return (
```

Quando as imagens são geradas com êxito, `playVideo` é definido como `true`, **acionando a caixa de diálogo do player de vídeo**.

```tsx
      <VideoPlayerDialog
        isOpen={playVideo}
        onClose={() => setPlayVideo(false)}
        video={videoData}
      />
    </div>
  )
}
```

5.3 Adicione também a caixa de diálogo com o player de vídeo ao arquivo `/dashboard/_components/ShortVideoGrid.tsx`:

```tsx
export default function ShortVideoGrid() {
  const [selectedVideo, setSelectedVideo] = useState<VideoData | null>(null)
```

```tsx
          <button
            key={video.id} // Use a unique identifier as the key
            className='relative aspect-square h-[450px] w-[300px] overflow-hidden rounded-lg transition-all duration-300 ease-in-out hover:scale-105 focus:outline-none focus:ring-2 focus:ring-primary'
            onClick={() => setSelectedVideo(video)}
          >
```

```tsx
      <VideoPlayerDialog
        isOpen={!!selectedVideo}
        onClose={() => setSelectedVideo(null)}
        video={selectedVideo}
        refreshVideos={GetVideos}
      />
    </>
  )
}
```

5.4 Style o Remotion Player em remotion/Composition.tsx que você criou seguindo este guia: [**Installing Remotion in an existing project**](https://www.remotion.dev/docs/brownfield)

```tsx
import { TranscriptSegment, VideoData } from '@/app/lib/interface'
import { useEffect, useState } from 'react'
import {
  AbsoluteFill,
  Audio,
  Img,
  interpolate,
  Sequence,
  useCurrentFrame,
  useVideoConfig,
} from 'remotion'

export const MyComposition = ({
  audioFileUrl,
  captions,
  images,
}: VideoData) => {
  const { fps } = useVideoConfig()
  const frame = useCurrentFrame()
  const [durationFrame, setDurationFrame] = useState<number | null>(null)

  useEffect(() => {
    if (captions.length > 0) {
      const lastSegment = captions[captions.length - 1]
      const calculatedDurationFrame = (lastSegment.end / 1000) * fps
      setDurationFrame(calculatedDurationFrame)
    }
  }, [captions, fps])

  if (durationFrame === null) {
    return null
  }

  const getCurrentCaption = () => {
    const currentTime = (frame / 30) * 1000
    const currentCaption = captions.find(
      (word: TranscriptSegment) =>
        currentTime >= word.start && currentTime <= word.end,
    )
    return currentCaption ? currentCaption.text : ''
  }

  return (
    <AbsoluteFill>
      {images.map((item, index) => {
        const key = item || `image-${index}`
        const startTime = (index * durationFrame) / images.length
        const duration = durationFrame
        const scale = interpolate(
          frame,
          [startTime, startTime + duration / 2, startTime + duration],
          [1, 1.2, 1],
          { extrapolateLeft: 'clamp', extrapolateRight: 'clamp' },
        )

        return (
          <Sequence key={key} from={startTime} durationInFrames={durationFrame}>
            <AbsoluteFill
              style={{
                display: 'flex',
                justifyContent: 'center',
                alignItems: 'center',
              }}
            >
              <Img
                src={item}
                style={{
                  width: '100%',
                  height: '100%',
                  objectFit: 'cover',
                  transform: `scale(${scale})`,
                }}
              />
              <AbsoluteFill
                style={{
                  display: 'flex',
                  justifyContent: 'center',
                  alignItems: 'center',
                  textAlign: 'center',
                  fontSize: '1.25rem',
                  color: 'white',
                  position: 'absolute',
                  top: undefined,
                  bottom: 0,
                  height: '200px',
                }}
              >
                <p>{getCurrentCaption()}</p>
              </AbsoluteFill>
            </AbsoluteFill>
          </Sequence>
        )
      })}
      <Audio src={audioFileUrl} />
    </AbsoluteFill>
  )
}
```

### 6. Renderização de um vídeo

- Usar o **Remotion Lambda** para gerar quadros e codificar o vídeo.
- Armazenamento do output URL e atualização do nosso banco de dados.

6.1 Obtenha o URL e o bucket do AWS Serve seguindo este [guia da documentação do Remotion](https://www.remotion.dev/docs/lambda/setup) e configure as seguintes variáveis de ambiente no seu arquivo `.env`

```
  NEXT_PUBLIC_API_URL=<Your API URL>
  REMOTION_AWS_SERVE_URL=<Your AWS Serve URL for Remotion>
  REMOTION_AWS_BUCKET_NAME=<Your AWS Bucket URL for Remotion>
```

6.2 Rota da API (`app/api/render-video/route.ts`)

```tsx
import {
  getFunctions,
  getRenderProgress,
  renderMediaOnLambda,
} from '@remotion/lambda/client'
import type { NextRequest } from 'next/server'
import { NextResponse } from 'next/server'
import axios from 'axios'
import type { VideoData } from '@/app/lib/interface'

const serveUrl = process.env.REMOTION_AWS_SERVE_URL

export const POST = async (req: NextRequest) => {
  if (!serveUrl) {
    return NextResponse.json({ error: 'Serve URL not defined' }, { status: 500 })
  }

  const { id, audioFileUrl, captions, images }: VideoData = await req.json()

  try {
    // Fetch available Lambda functions
    const functions = await getFunctions({
      region: 'us-east-1',
      compatibleOnly: true,
    })

    if (functions.length === 0) {
      throw new Error('No compatible Lambda functions found.')
    }

    const functionName = functions[0].functionName
    const captionsDuration = captions[captions.length - 1].end / 1000
    const fps = 30
    const durationInFrames = Math.ceil(captionsDuration * fps)

    // Start rendering process on AWS Lambda
    const { renderId, bucketName } = await renderMediaOnLambda({
      region: 'us-east-1',
      functionName,
      serveUrl,
      composition: 'shortVideo',
      inputProps: { audioFileUrl, captions, images, durationInFrames },
      codec: 'h264',
      maxRetries: 1,
      framesPerLambda: 100,
      privacy: 'public',
    })

    // Poll for render progress
    while (true) {
      await new Promise((resolve) => setTimeout(resolve, 1000))
      const progress = await getRenderProgress({ renderId, bucketName, functionName, region: 'us-east-1' })

      if (progress.done) {
        const outputFile = progress.outputFile
        console.log('Render finished!', outputFile)

        // Save the rendered video output URL
        await axios.put(`${process.env.NEXT_PUBLIC_API_URL}/videos/${id}`, {
          outputFile,
          renderId,
        })

        return NextResponse.json({ outputFile })
      }

      if (progress.fatalErrorEncountered) {
        console.error('Error encountered', progress.errors)
        return NextResponse.json({ error: progress.errors }, { status: 500 })
      }
    }
  } catch (error) {
    console.error('Error rendering video:', error)
    return NextResponse.json({ error }, { status: 500 })
  }
}
```

6.2 Pegue os dados passados durante o processo de renderização em `remotion/Root.tsx`:

```tsx
import { Composition, getInputProps } from 'remotion'
import { MyComposition } from './Composition'
import { VideoData } from '@/app/lib/interface'

const { video, durationInFrames } = getInputProps() as {
  video: VideoData
  durationInFrames: number
}

export const RemotionRoot = () => {
  return (
    <Composition
      id='shortVideo'
      component={MyComposition}
      durationInFrames={durationInFrames}
      width={300}
      height={450}
      fps={30}
      defaultProps={{
        ...video,
      }}
    />
  )
}

```

6.3 Crie a função de exportação de vídeo em `app/components/VideoPlayerDialog.tsx`:

```tsx
const exportVideo = async () => {
  if (!video) return
  setIsLoading(true)

  try {
    const result = await axios.post('/api/render-video', {
      id: video.id,
      audioFileUrl: video.audioFileUrl,
      captions: video.captions,
      images: video.images,
    })

    console.log('Video Rendered:', result.data)

    if (result.data.outputFile) {
      setOutputFileUrl(result.data.outputFile)
      window.open(result.data.outputFile, '_blank')
    }
  } catch (error) {
    console.error('Error rendering video:', error)
  } finally {
    setIsLoading(false)
  }
}
```

6.4 Adicione o botão para renderizar o vídeo ou abrir o output URL se ele já estiver sido renderizado:

```tsx
        <DialogFooter className='gap-4 border-t p-4 sm:justify-between'>
          <div className='flex space-x-2'>
            <Button type='button' variant='secondary' onClick={handleCancel}>
              <X className='mr-2 size-4' />
              Cancel
            </Button>
            {outputFileUrl ? (
              <Button
                type='button'
                onClick={() => window.open(outputFileUrl, '_blank')}
              >
                Open
              </Button>
            ) : (
              <Button type='button' onClick={exportVideo}>
                <Download className='mr-2 size-4' />
                Export
              </Button>
            )}
          </div>
        </DialogFooter>
```

## 7. Exclusão de um vídeo

- Remova o arquivo de vídeo do AWS S3.
- Exclua os dados do nosso banco de dados.

7.1 Rota da API (`app/api/delete-video/route.ts`)

```tsx
import { deleteRender } from '@remotion/lambda/client'
import axios from 'axios'
import type { NextRequest } from 'next/server'
import { NextResponse } from 'next/server'

const bucketName = process.env.REMOTION_AWS_BUCKET_NAME

export const DELETE = async (req: NextRequest) => {
  if (!bucketName) {
    return NextResponse.json({ error: 'BucketName not defined' }, { status: 500 })
  }

  const { renderId, videoId } = await req.json()

  try {
    // Delete the video render from AWS S3
    if (renderId) {
      await deleteRender({ bucketName, region: 'us-east-1', renderId })
    }

    // Remove video metadata from the database
    await axios.delete(`${process.env.NEXT_PUBLIC_API_URL}/videos/${videoId}`)

    return NextResponse.json({ message: 'Video deleted successfully' }, { status: 200 })
  } catch (error) {
    console.error('Error deleting video:', error)
    return NextResponse.json({ error: 'Failed to delete video', details: error }, { status: 500 })
  }
}

```

7.2 Crie a função para deletar o vídeo:

```tsx
  const handleDelete = async () => {
    onClose()
    try {
      await axios.delete('/api/delete-video', {
        data: {
          videoId: video.id,
          renderId: video.renderId,
        },
      })
      if (refreshVideos) {
        refreshVideos()
      }
    } catch (error) {
      console.error(error)
    }
  }
```

7.3 Adicione o botão de deletar:

```tsx
        <DialogFooter className='gap-4 border-t p-4 sm:justify-between'>
          <Button type='button' variant='destructive' onClick={handleDelete}>
            <Trash className='mr-2 size-4' />
            Delete
          </Button>
          <div className='flex space-x-2'>
            <Button type='button' variant='secondary' onClick={handleCancel}>
              <X className='mr-2 size-4' />
              Cancel
            </Button>
            {outputFileUrl ? (
              <Button
                type='button'
                onClick={() => window.open(outputFileUrl, '_blank')}
              >
                Open
              </Button>
            ) : (
              <Button type='button' onClick={exportVideo}>
                <Download className='mr-2 size-4' />
                Export
              </Button>
            )}
          </div>
        </DialogFooter>
```

### **Conclusão**

A criação de um gerador de vídeos curtos com tecnologia de IA envolve a integração de várias tecnologias, incluindo **Next.js, Remotion, AWS Lambda e transcrição/legendas orientadas por IA**. Abordamos os principais aspectos da implementação de renderização e exclusão de vídeo.

Ao aproveitar a **computação sem servidor** com AWS Lambda e os **recursos de renderização do Remotion**, alcançamos uma maneira escalável e eficiente de gerar vídeos de alta qualidade. Além disso, a capacidade de excluir vídeos garante que gerenciemos o armazenamento de forma eficiente e cumpramos as necessidades de privacidade do usuário.

Esta implementação é apenas uma base — há espaço para mais melhoria, como:

- **Adicionar autenticação de usuário**
- **Transformá-lo em um SaaS**
- **Aprimorar UI/UX**
- **Adicionar efeitos e animações de vídeo adicionais**

Com essas melhorias, o aplicativo pode se tornar uma ferramenta poderosa para **criação automatizada de vídeos curtos.**

### **Código-fonte**

Você pode encontrar a implementação completa no repositório do GitHub:

🔗 [**Repositório do GitHub**](https://github.com/RianNegreiros/AiShortsVideosGenerator)
