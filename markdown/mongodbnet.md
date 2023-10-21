# Usando o MongoDB com C# e .Net Core

## Uma introdução básica

Antes de vermos todas as diferentes maneiras de usar o MongoDB com C# e .NET, vamos examinar alguns dos conceitos básicos desse banco de dados.

## ****Documents****

O MongoDB é um [banco de dados orientado a documentos](https://en.wikipedia.org/wiki/Document-oriented_database) e, tem certas semelhanças com os bancos de dados relacionais.

Em vez de linhas, o MongoDB tem [documentos](https://www.mongodb.com/docs/v2.4/core/document/). Ao contrário dos bancos de dados relacionais, em que as informações de um determinado registro estão espalhadas por várias tabelas, todos os dados de um determinado registro são armazenados em um único documento.

Na essência, os documentos são arquivos BSON, que são a serialização codificada em binário dos arquivos JSON. No entanto, do ponto de vista do programador, o MongoDB manipula arquivos JSON puros.

Exemplo da documentação do MongoDB:

```json
{
  _id: ObjectId("5099803df3f4948bd2f98391"),
  name: { first: "Alan", last: "Turing" },
  birth: new Date('Jun 23, 1912'),
   death: new Date('Jun 07, 1954'),
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   views : NumberLong(1250000)
}
```

Como você pode ver:

Há um campo `_id` no início desse documento JSON.
Esse campo é exclusivo e é gerado pelo MongoDB para cada documento no banco de dados.

Dessa forma, o MongoDB mantém uma das propriedades importantes dos bancos de dados relacionais - consistência forte.

## Collections

Os documentos são armazenados dentro de coleções.

As coleções são grupos de documentos relacionados de alguma forma, mas esses documentos não precisam ter a mesma estrutura.

Esse um dos maiores benefícios do MongoDB: os desenvolvedores não precisam conhecer o esquema do banco de dados de antemão, mas podem modificar o esquema dinamicamente durante o desenvolvimento. Isso é especialmente bom em sistemas em que não conseguimos acertar o esquema no início, ou em que há muitos casos extremos a serem cobertos.

Além disso, dessa forma, todo o problema de incompatibilidade de impedância é evitado (ou seja, a eliminação da camada de mapeamento objeto-relacional).

Como é isso?

Digamos que o documento anterior esteja armazenado na coleção chamada `pessoas`; poderíamos adicionar outro documento a essa coleção, que conteria campos que o documento anterior não tinha, ou poderíamos adicionar um documento que talvez não tivesse os campos que o documento anterior tinha.

Usando o exemplo anterior, poderíamos adicionar o próximo documento à coleção:

```json
{
  _id: ObjectId("5099803df3f4948bd2f98391"),
  name: { first: "Alan", last: "Turing" },
  birth: new Date('Jun 23, 1912'),
   death: new Date('Jun 07, 1954'),
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   views : NumberLong(1250000)
}
```

```json
{
  _id: ObjectId("5099803df3f4948bd2f98391"),
  name: { first: "Alan", last: "Turing" },
  birth: new Date('Jun 23, 1912'),
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   views : NumberLong(1250000),
  location: [99, 99]
}
```

Como você pode ver, esses documentos são semelhantes, mas não iguais. O novo documento não contém o campo `death`, mas contém o campo `location` adicional que o documento adicionado anteriormente não tinha.

## Mapeamento de BSON para objetos C# fortemente tipados

Quando usamos o [driver .NET](https://www.mongodb.com/docs/drivers/csharp/current/), consumimos documentos por meio de [BsonDocument](https://mongodb.github.io/mongo-csharp-driver/2.4/apidocs/html/T_MongoDB_Bson_BsonDocument.htm). O que queremos fazer é mapear esses BsonDocuments para objetos C# fortemente tipados. Mas antes disso, vamos ver como é o nosso banco de dados e suas entidades.

## Exemplo de banco de dados

Usaremos um banco de dados chamado - `blog`.

Esse banco de dados tem uma coleção, `posts`, que contém um documento para cada post.

O documento JSON para um post individual seria algo parecido com isto:

```json
  {
    "_id": ObjectID("64d238115d06bc53ce5c4606"),
    "title": "title",
    "summary": "summary",
    "content": "content",
  "Author": Object
  "UpdatedAt": 2023-08-08T12:41:53.163+00:00
  "CreatedAt": 2023-08-08T12:41:53.163+00:00
  },
```

## Equivalente em C#

Um equivalente disso em C# tem a seguinte aparência:

```csharp
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace MongoDb
{
 public class Post
 {
  [BsonId]
  [BsonRepresentation(BsonType.ObjectId)]
  public string Id { get; set; }
  [BsonElement("title")]
  public string Title { get; set; }
  [BsonElement("summary")]
  public string Summary { get; set; }
  [BsonElement("author")]
  public User Author { get; set; }
  [BsonDateTimeOptions(Kind = DateTimeKind.Local)]
   public DateTime CreatedAt { get; } = DateTime.Now;
   [BsonDateTimeOptions(Kind = DateTimeKind.Local)]
   public DateTime UpdatedAt { get; set; } = DateTime.Now;
 }
}
```

### Explicação

Você perceberá que há atributos para cada propriedade dessa classe. Esses atributos nos permitem mapear automaticamente os dados de BsonDocument para nossa classe.

`BsonId` é usado para mapear um identificador de documento exclusivo. Todo documento no MongoDB tem o elemento:

```csharp
ObjectId.BsonElement(field_name)
```

Seu tipo é usado para mapear outros campos do objeto nas propriedades do objeto.

Como sabemos que os bancos de dados de documentos não têm um esquema rígido, é possível ter mais campos no documento JSON do que realmente queremos usar em nosso aplicativo.

Além disso, é possível que não queiramos pegar todos os campos do banco de dados. Para isso, podemos usar

```csharp
BsonIgnoreExtraElements
```

na própria classe.

## Post repository

O objetivo é criar uma classe que nos dará a capacidade de fazer operações CRUD simples na coleção de `posts`.

A primeira coisa que precisamos fazer é conectar-nos ao banco de dados a partir do nosso aplicativo. A maneira mais fácil de fazer isso é usar a classe [MongoClient](https://mongodb.github.io/mongo-csharp-driver/2.4/apidocs/html/T_MongoDB_Driver_MongoClient.htm), disponível por meio do [driver MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/). (Seu construtor requer uma string de conexão).

Outras classes que precisamos usar são [MongoDatabase](https://mongodb.github.io/mongo-csharp-driver/2.4/apidocs/html/T_MongoDB_Driver_MongoDatabase.htm), para acessar bancos de dados definidos (blog, neste caso), e [MongoCollection](https://mongodb.github.io/mongo-csharp-driver/2.4/apidocs/html/T_MongoDB_Driver_MongoCollection_1.htm), para acessar coleções definidas (posts, neste caso).

Veja como isso no código:

```csharp
public class PostsRepository
{
    private IMongoClient _client;
    private IMongoDatabase _database;
    private IMongoCollection<Post> _postsCollection;

    public PostsRepository(string connectionString)
    {
        _client = new MongoClient(connectionString);
        _database = _client.GetDatabase("blog");
        _postsCollection = _database.GetCollection<Post>("posts");
    }
}
```

## Criar posts

A inserção de um documento no banco de dados é fácil quando as etapas anteriores são seguidas:

```csharp
public async Task InsertPost(Post post)
{
    await _postsCollection.InsertOneAsync(post);
}
```

Obviamente, usei a operação assíncrona `InsertOneAsync`, mas você também pode usar uma operação síncrona.

Novamente, como mapeamos os campos JSON nas propriedades do `post`, é fácil trabalhar com essas operações assíncronas.

### Listar posts

A listagem de posts é feita da seguinte forma:

```csharp
public async Task<List<Post>> GetAllPosts()
{
    return await _postsCollection.Find(new BsonDocument()).ToListAsync();
}

public async Task<List<Post>> GetPostsByField(string fieldName, string fieldValue)
{
    var filter = Builders<Post>.Filter.Eq(fieldName, fieldValue);
    var result = await _usersCollection.Find(filter).ToListAsync();

    return result;
}

public async Task<List<Post>> GetPost(int startingFrom, int count)
{
    var result = await _postsCollection.Find(new BsonDocument())
                                       .Skip(startingFrom)
                                       .Limit(count)
                                       .ToListAsync();

    return result;
}
```

Há três implementações diferentes dessa funcionalidade. Vamos examinar cada uma delas.

Na primeira, a função `GetAllPost` retorna todos os posts do banco de dados.

Para isso, usamos o método `Find` da classe `MongoCollection` e passamos o BsonDocument vazio para ele.
No método seguinte, `GetPostsByField`, podemos ver que o método `Find` recebe o objeto filter, que será usado como critério para obter os dados.

Na primeira função, usamos um filtro vazio e, portanto, recebemos todos os usuários da coleção.

Na segunda função, usamos o `Builder` para criar o filtro que será usado no banco de dados.

Por fim, a última função - `GetPosts` - usa os métodos `Skip` e `Limit` do MongoCollection para obter um pedaço de dados necessário. Essa última função pode ser usada para paginação.

## Atualizar posts

Os documentos podem ser atualizados de maneira semelhante:

```csharp
public async Task<bool> UpdatePost(ObjectId id, string updateFieldName, string updateFieldValue)
{
    var filter = Builders<Post>.Filter.Eq("_id", id);
    var update = Builders<Post>.Update.Set(updateFieldName, updateFieldValue);

    var result = await _postsCollection.UpdateOneAsync(filter, update);

    return result.ModifiedCount != 0;
}
```

Um fato interessante é que, usando essa função, você pode adicionar campos que não estão no "schema". Por exemplo, se quiser adicionar um novo campo no documento do `post`, você pode fazer isso:

```csharp
var posts = await _mongoDbRepo.GetPostsByField("title", "title test");
var post = posts.FirstOrDefault();
var result = await _mongoDbRepo.UpdatePost(post.Id, "coverImageUrl", "url.com");
```

Dessa forma, o MongoDB (e restante dos bancos de dados de documentos) de esquema modular é aproveitado; você não fica bloqueado por objetos definidos e pode armazenar informações dinamicamente, se necessário.

## Deletar posts

Posts podem ser deletados dessa forma:

```csharp
public async Task<bool> DeletePostById(ObjectId id)
{
    var filter = Builders<Post>.Filter.Eq("_id", id);
    var result = await _postsCollection.DeleteOneAsync(filter);
    return result.DeletedCount != 0;
}

public async Task<long> DeleteAllPosts()
{
    var filter = new BsonDocument();
    var result = await _postsCollection.DeleteManyAsync(filter);
    return result.DeletedCount;
}
```

Em resumo, mostramos como usar os recursos do MongoDB com C# em um ambiente .NET, examinamos muitos dos recursos do driver do MongoDB e aprendemos a mapear documentos JSON para objetos .NET, o que nos proporcionou uma maneira fácil de usar os recursos. Os usos das operações CRUD, que são as operações mais comumente usadas, foram demonstrados.
