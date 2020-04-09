---
page_type: sample
description: Bu öğretici, MongoDB NoSQL veritabanı nı kullanarak ASP.NET Core web API'sini nasıl oluşturacağımı gösterir.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 09d73e25667822b8748a00cc76ad6d4f0e5fe290
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511411"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>ASP.NET Core ve MongoDB ile bir web API oluşturma

Bu öğretici, [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini gerçekleştiren bir web API'si oluşturur.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

* MongoDB'yi yapılandır
* MongoDB veritabanı oluşturma
* Bir MongoDB koleksiyonu ve şeması tanımlayın
* Bir web API'den MongoDB CRUD işlemlerini gerçekleştirin
* JSON serileştirmeyi özelleştir

## <a name="prerequisites"></a>Ön koşullar

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) **ASP.NET ve web geliştirme** iş yükü ile önizleme
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a>MongoDB'yi yapılandır

Windows kullanıyorsanız, MongoDB varsayılan olarak *C:\\Program Files\\MongoDB'ye* yüklenir. *C ekle:\\\\Program\\Dosyaları MongoDB Server\\\<version_number>\\bin* çevre değişkenine. `Path` Bu değişiklik, geliştirme makinenizin herhangi bir yerinden MongoDB erişimine olanak tanır.

Bir veritabanı oluşturmak, koleksiyonlar oluşturmak ve belgeleri depolamak için aşağıdaki adımlardaki mongo Shell'i kullanın. Mongo Shell komutları hakkında daha fazla bilgi için [mongo Shell ile çalışma bilgisine](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)bakın.

1. Verileri depolamak için geliştirme makinenizde bir dizin seçin. Örneğin, *C:\\* Windows'da Kitaplar Verileri. Yoksa dizini oluşturun. Mongo Shell yeni dizinler yaratmaz.
1. Komut kabuğunu açın. Varsayılan bağlantı noktası 27017'de MongoDB'ye bağlanmak için aşağıdaki komutu çalıştırın. Önceki adımda seçtiğiniz dizini değiştirmeyi `<data_directory_path>` unutmayın.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Başka bir komut kabuğu örneği açın. Aşağıdaki komutu çalıştırarak varsayılan test veritabanına bağlanın:

    ```console
    mongo
    ```

1. Aşağıdakileri bir komut kabuğunda çalıştırın:

    ```console
    use BookstoreDb
    ```

    Zaten yoksa, *BookstoreDb* adında bir veritabanı oluşturulur. Veritabanı varsa, bağlantısı hareketler için açılır.

1. Aşağıdaki `Books` komutu kullanarak bir koleksiyon oluşturun:

    ```console
    db.createCollection('Books')
    ```

    Aşağıdaki sonuç görüntülenir:

    ```console
    { "ok" : 1 }
    ```

1. Koleksiyon için bir şema tanımlayın `Books` ve aşağıdaki komutu kullanarak iki belge ekleyin:

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    Aşağıdaki sonuç görüntülenir:

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > Bu makalede gösterilen kimlik, bu örneği çalıştırdığınızda kimliklerle eşleşmez.

1. Aşağıdaki komutu kullanarak veritabanındaki belgeleri görüntüleyin:

    ```console
    db.Books.find({}).pretty()
    ```

    Aşağıdaki sonuç görüntülenir:

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    Şema, her belge `_id` için otomatik `ObjectId` olarak oluşturulmuş bir tür özelliği ekler.

Veritabanı hazır. ASP.NET Core web API'sini oluşturmaya başlayabilirsiniz.

## <a name="create-the-aspnet-core-web-api-project"></a>ASP.NET Core web API projesini oluşturma

1. **Dosya** > **Yeni** > **Proje**git.
1. ASP.NET **Çekirdek Web Uygulaması** proje türünü seçin ve **İleri'yi**seçin.
1. Proje *BooksApi*adını ve **Oluştur'u**seçin.
1. **.NET Core** hedef çerçevesini seçin ve **Core 3.0'ASP.NET.** **API** proje şablonu seçin ve **Oluştur'u**seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin. Paket **Yöneticisi Konsolu** penceresinde proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a>Varlık modeli ekleme

1. Proje köküne bir *Modeller* dizini ekleyin.
1. Aşağıdaki `Book` kodla *Modeller* dizinine bir sınıf ekleyin:

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    Önceki sınıfta, `Id` özellik:

    * Ortak Dil Çalışma Zamanı (CLR) nesnesini MongoDB koleksiyonuyla eşlemek için gereklidir.
    * Bu özelliği belgenin [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) birincil anahtarı olarak belirlemek üzere açıklama yapılır.
    * [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) parametrenin türü `string` olarak geçirilmesine izin vermek için açıklama yapılır. Mongo dönüşüm `string` `ObjectId`yönetir.

    Özellik `BookName` öznitelik ile [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) açıklamalı. Özniteliğin `Name` değeri MongoDB koleksiyonundaki özellik adını temsil eder.

## <a name="add-a-configuration-model"></a>Yapılandırma modeli ekleme

1. *Appsettings.json'a*aşağıdaki veritabanı yapılandırma değerlerini ekleyin:

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. *Modeller* dizinine aşağıdaki kodla *bir BookstoreDatabaseSettings.cs* dosyası ekleyin:

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    Önceki `BookstoreDatabaseSettings` sınıf *appsettings.json* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır. JSON ve C# özellik adları eşleme işlemini kolaylaştırmak için aynı şekilde adlandırılır.

1. Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    Yukarıdaki kodda:

    * *Appsettings.json* dosyasının `BookstoreDatabaseSettings` bölümünün bağladığı yapılandırma örneği Bağımlılık Enjeksiyonu (DI) kapsayıcısında kaydedilir. Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appsettings.json*özelliği ile doldurulur.
    * Arabirim, `IBookstoreDatabaseSettings` singleton [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)yle DI'de kayıtlıdır. Enjekte edildiğinde, arabirim örneği bir `BookstoreDatabaseSettings` nesneye giderilir.

1. Başvuruları ve `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` başvuruları çözmek için aşağıdaki kodu *Startup.cs* üst bölümüne ekleyin:

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>CRUD operasyon hizmeti ekleme

1. Proje köküne *bir Hizmet* dizini ekleyin.
1. Hizmetler `BookService` dizinine *Services* aşağıdaki kodla bir sınıf ekleyin:

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    Önceki kodda, bir `IBookstoreDatabaseSettings` örnek yapı oluşturucu enjeksiyon yoluyla DI alınır. Bu teknik, [yapılandırma modeli ekle](#add-a-configuration-model) bölümüne eklenen *appsettings.json* yapılandırma değerlerine erişim sağlar.

1. Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    Önceki kodda, `BookService` sınıf tüketen sınıflarda yapıcı enjeksiyonu desteklemek için DI'ye kaydedilir. Singleton hizmet ömrü en `BookService` uygundur, çünkü `MongoClient`doğrudan bir bağımlılık alır. Resmi [Mongo Müşteri yeniden](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)yönergeleri `MongoClient` başına, di bir singleton hizmet ömrü ile kayıtlı olmalıdır.

1. Başvuruyu çözmek için Startup.cs üst bölümüne aşağıdaki kodu ekleyin: *Startup.cs* `BookService`


    ```csharp
    using BooksApi.Services;
    ```

Sınıf, `BookService` veritabanına `MongoDB.Driver` karşı CRUD işlemleri gerçekleştirmek için aşağıdaki üyeleri kullanır:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; veritabanı işlemleri gerçekleştirmek için sunucu örneğini okur. Bu sınıfın oluşturucusu MongoDB bağlantı dizesi sağlanır:

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Işlemleri gerçekleştirmek için Mongo veritabanını temsil eder. Bu öğretici, belirli bir koleksiyondaki verilere erişmek için arabirimdeki genel [GetCollection\<TDocument>(toplama)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır. Bu yöntem çağrıldıktan sonra koleksiyona karşı CRUD işlemleri gerçekleştirin. Yöntem `GetCollection<TDocument>(collection)` aramasında:
  * `collection`koleksiyon adını temsil eder.
  * `TDocument`koleksiyonda depolanan CLR nesne türünü temsil eder.

`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür. Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Sağlanan arama ölçütleriyle eşleşen tek bir belgeyi siler.
* [TBelgesi'ni\<bul>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Koleksiyondaki tüm belgeleri sağlanan arama ölçütleriyle eşleşen olarak döndürür.
* [InsertOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; sağlanan nesneyi koleksiyonda yeni bir belge olarak ekler.
* [ReplaceOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; sağlanan arama ölçütleriyle eşleşen tek belgeyi sağlanan nesneyle değiştirir.

## <a name="add-a-controller"></a>Denetleyici ekleme

Denetleyiciler `BooksController` dizinine *Controllers* aşağıdaki kodla bir sınıf ekleyin:

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

Önceki web API denetleyicisi:

* CRUD `BookService` işlemlerini gerçekleştirmek için sınıfı kullanır.
* GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.
* Http <xref:System.Web.Http.ApiController.CreatedAtRoute*> [201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtını döndürmek için eylem yöntemini `Create` çağırır. Durum kodu 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır. `CreatedAtRoute`ayrıca yanıta bir `Location` üstbilgi ekler. Üstbilgi, `Location` yeni oluşturulan kitabın URI'sini belirtir.

## <a name="test-the-web-api"></a>Web API'sini test edin

1. Uygulamayı derleyin ve çalıştırın.

1. Denetleyicinin `http://localhost:<port>/api/books` parametresiz `Get` eylem yöntemini sınamak için gidin. Aşağıdaki JSON yanıtı görüntülenir:

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. Denetleyicinin `http://localhost:<port>/api/books/{id here}` aşırı yüklenmiş `Get` eylem yöntemini sınamak için gidin. Aşağıdaki JSON yanıtı görüntülenir:

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>JSON serileştirme seçeneklerini yapılandırma

[Test web API](#test-the-web-api) bölümünde döndürülen JSON yanıtları hakkında değiştirmek için iki ayrıntı vardır:

* Özellik adlarının varsayılan deve gövdesi, CLR nesnesinin özellik adlarının Pascal kasasına uyacak şekilde değiştirilmelidir.
* Özellik `bookName` olarak `Name`döndürülmelidir.

Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:

1. JSON.NET paylaşılan ASP.NET çerçeveden kaldırıldı. [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)bir paket başvuru ekleyin.

1. In `Startup.ConfigureServices`, `AddMvc` yöntem çağrısı üzerine aşağıdaki vurgulanan kodu zincir:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    Önceki değişiklikle, web API'nin serileştirilmiş JSON yanıtındaki özellik adları CLR nesne türünde karşılık gelen özellik adlarıyla eşleşir. Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileşir.

1. *Modeller/Book.cs'* de, `BookName` özelliği aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) öznitelikle açıklama olarak belirtin:

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    Özniteliğin `[JsonProperty]` `Name` değeri, web API'nin serileştirilmiş JSON yanıtındaki özellik adını temsil eder.

1. Öznitelik başvurularını çözmek `[JsonProperty]` için *Modeller/Book.cs'nin* üst bölümüne aşağıdaki kodu ekleyin:

    ```csharp
    using Newtonsoft.Json;
    ```

1. [Test web API](#test-the-web-api) bölümünde tanımlanan adımları yineleyin. JSON özellik adlarında farka dikkat edin.

## <a name="next-steps"></a>Sonraki adımlar

Core web API'ASP.NET leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Bu makalenin YouTube sürümü](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [ASP.NET Core ile web API'leri oluşturma](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
