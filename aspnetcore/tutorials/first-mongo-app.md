---
title: ASP.NET Core ve MongoDB ile Web API 'SI oluşturma
author: prkhandelwal
description: Bu öğreticide, MongoDB NoSQL veritabanı kullanarak ASP.NET Core Web API 'sinin nasıl oluşturulacağı gösterilmektedir.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 46607fc92670bb46a155ddf3248bc8a36b600a4a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452258"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>ASP.NET Core ve MongoDB ile Web API 'SI oluşturma

By [pratik Khandelwal](https://twitter.com/K2Prk) ve [Scott Ade](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * MongoDB 'yi yapılandırma
> * MongoDB veritabanı oluşturma
> * MongoDB koleksiyonu ve şeması tanımlama
> * Web API 'sinden MongoDB CRUD işlemleri gerçekleştirme
> * JSON serileştirmesini özelleştirme

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code için C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Mac için Visual Studio sürüm 7,7 veya üzeri](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>MongoDB 'yi yapılandırma

Windows kullanıyorsanız MongoDB varsayılan olarak *C: \\ Program Files \\ MongoDB* konumunda yüklüdür. Ortam değişkenine *C: \\ Program Files \\ MongoDB \\ sunucu \\ \<version_number> \\ kutusu* ekleyin `Path` . Bu değişiklik geliştirme makinenizde her yerden MongoDB erişimine izin vermez.

Bir veritabanı oluşturmak, koleksiyonları yapmak ve belgeleri depolamak için aşağıdaki adımlarda Mongo kabuğunu kullanın. Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Verileri depolamak için geliştirme makinenizde bir dizin seçin. Örneğin, *C: Windows üzerinde \\ booksdata* . Mevcut değilse dizini oluşturun. Mongo kabuğu yeni dizinler oluşturmaz.
1. Bir komut kabuğu açın. Varsayılan bağlantı noktası 27017 ' de MongoDB 'ye bağlanmak için aşağıdaki komutu çalıştırın. `<data_directory_path>`Önceki adımda seçtiğiniz dizinle değiştirmeyi unutmayın.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Başka bir komut kabuğu örneği açın. Aşağıdaki komutu çalıştırarak Varsayılan test veritabanına bağlanın:

   ```console
   mongo
   ```

1. Komut kabuğu 'nda aşağıdakileri çalıştırın:

   ```console
   use BookstoreDb
   ```

   Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur. Veritabanı mevcutsa, bağlantısı işlemler için açılır.

1. `Books`Aşağıdaki komutu kullanarak bir koleksiyon oluşturun:

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
   > Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.

1. Aşağıdaki komutu kullanarak veritabanında bulunan belgeleri görüntüleyin:

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

   Şema, `_id` her belge için türünde bir otomatik olarak oluşturulan özellik ekler `ObjectId` .

Veritabanı hazırlanıyor. ASP.NET Core Web API 'sini oluşturmaya başlayabilirsiniz.

## <a name="create-the-aspnet-core-web-api-project"></a>ASP.NET Core Web API projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Dosya** > **Yeni** > **Proje**' ye gidin.
1. **ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.
1. Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.
1. **.NET Core** hedef çerçevesini ve **3,0 ASP.NET Core**seçin. **API** proje şablonunu seçin ve **Oluştur**' u seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) . **Paket Yöneticisi konsol** penceresinde, proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Komut kabuğu 'nda aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Yeni bir ASP.NET Core Web API projesi hedefleme .NET Core Visual Studio Code oluşturulur ve açılır.

1. Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?**. **Evet**' i seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) . **Tümleşik Terminal** ' i açın ve proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Sürüm 8,6 ' den önceki Mac için Visual Studio, **File**  >  kenar çubuğundan Dosya**yeni çözüm**  >  **.NET Core**  >  **App** ' i seçin. Sürüm 8,6 veya sonraki sürümlerde **File**  >  kenar çubuğundan Dosya**yeni çözüm**  >  **Web ve konsol**  >  **uygulaması** ' nı seçin.
1. **ASP.NET Core** > **API** C# proje şablonunu seçin ve **İleri**' yi seçin.
1. **Hedef çerçeve** açılır listesinden **.NET Core 3,1** ' i seçin ve **İleri**' yi seçin.
1. **Proje adı**Için *booksapı* girin ve **Oluştur**' u seçin.
1. **Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.
1. Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.
1. **Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.

---

## <a name="add-an-entity-model"></a>Varlık modeli ekleme

1. Proje köküne *modeller* dizini ekleyin.
1. `Book` *Modeller* dizinine aşağıdaki kodla bir sınıf ekleyin:

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

   Önceki sınıfta, `Id` özelliği:

   * Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.
   * , [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Bu özelliği belgenin birincil anahtarı olarak belirlemek için ile birlikte açıklanmış.
   * , [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) Parametresinin `string` [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür olarak geçirilmesine izin vermek için ile birlikte açıklanmış. Mongo, ' den ' `string` ye dönüştürmeyi işler `ObjectId` .

   `BookName`Özelliği özniteliğiyle açıklama eklenir [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) . Özniteliğinin değeri, `Name` MongoDB koleksiyonundaki özellik adını temsil eder.

## <a name="add-a-configuration-model"></a>Yapılandırma modeli ekleme

1. Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. *Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Yukarıdaki `BookstoreDatabaseSettings` sınıf, *appSettings. JSON* dosyasının özellik değerlerini depolamak için kullanılır `BookstoreDatabaseSettings` . JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.

1. Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   Yukarıdaki kodda:

   * *AppSettings. JSON* dosyasının bölüm bağlandığı yapılandırma örneği, `BookstoreDatabaseSettings` bağımlılık ekleme (dı) kapsayıcısına kaydedilir. Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appSettings. JSON*içindeki özelliği ile doldurulur.
   * `IBookstoreDatabaseSettings`Arabirim, tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir. Eklenen arabirim örneği bir nesne olarak çözümlenir `BookstoreDatabaseSettings` .

1. Ve başvurularını çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>CRUD işlemleri hizmeti ekleme

1. Proje köküne bir *Hizmetler* dizini ekleyin.
1. `BookService` *Hizmetler* dizinine aşağıdaki kodla bir sınıf ekleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır. Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.

1. Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek IÇIN dı ile kaydedilir. Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir bağımlılık alır `MongoClient` . Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir HIZMET ömrü ile dı 'ye kaydedilmelidir.

1. Başvuruyu çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookService` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService`Sınıfı, `MongoDB.Driver` veritabanına karşı CRUD işlemlerini gerçekleştirmek için aşağıdaki üyeleri kullanır:

* [Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur. Bu sınıfın oluşturucusuna MongoDB bağlantı dizesi verilmiştir:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): işlemleri gerçekleştirmek Için Mongo veritabanını temsil eder. Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır. Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin. `GetCollection<TDocument>(collection)`Yöntem çağrısında:

  * `collection`koleksiyon adını temsil eder.
  * `TDocument`Koleksiyonda depolanan CLR nesne türünü temsil eder.

`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür. Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:

* [Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.
* [Bul \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): koleksiyonda, belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.
* [Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.
* [Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.

## <a name="add-a-controller"></a>Denetleyici ekleme

`BooksController` *Controllers* dizinine aşağıdaki kodla bir sınıf ekleyin:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Önceki Web API denetleyicisi:

* `BookService`CRUD işlemleri gerçekleştirmek için sınıfını kullanır.
* HTTP isteklerini al, gönder, koy ve SIL desteği için eylem yöntemleri içerir.
* <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` [Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için eylem yöntemindeki çağrılar. Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır. `CreatedAtRoute`Ayrıca yanıta bir `Location` üst bilgi ekler. `Location`Üst bilgi, yeni oluşturulan KITABıN URI 'sini belirtir.

## <a name="test-the-web-api"></a>Web API 'sini test etme

1. Uygulamayı derleyin ve çalıştırın.

1. `http://localhost:<port>/api/books`Denetleyicinin parametresiz eylem yöntemini sınamak için öğesine gidin `Get` . Aşağıdaki JSON yanıtı görüntülenir:

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

1. `http://localhost:<port>/api/books/{id here}`Denetleyicinin aşırı yüklenmiş eylem yöntemini sınamak için öğesine gidin `Get` . Aşağıdaki JSON yanıtı görüntülenir:

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

[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:

* ' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.
* `bookName`Özelliği olarak döndürülmelidir `Name` .

Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:

1. JSON.NET, ASP.NET paylaşılan çerçevesinden kaldırılmıştır. Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .

1. ' De `Startup.ConfigureServices` , aşağıdaki vurgulanmış kodu yöntem çağrısına zincirle `AddControllers` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir. Örneğin, `Book` sınıfın `Author` özelliği olarak serileştirir `Author` .

1. *Modeller/Book. cs*' de, `BookName` aşağıdaki özniteliğiyle özelliğe not ekleyin [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]`Özniteliğinin değeri, `Name` Web API 'SININ serileştirilmiş JSON yanıtında özellik adını temsil eder.

1. Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. [Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin. JSON Özellik adlarındaki farka dikkat edin.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * MongoDB 'yi yapılandırma
> * MongoDB veritabanı oluşturma
> * MongoDB koleksiyonu ve şeması tanımlama
> * Web API 'sinden MongoDB CRUD işlemleri gerçekleştirme
> * JSON serileştirmesini özelleştirme

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code için C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Mac için Visual Studio sürüm 7,7 veya üzeri](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>MongoDB 'yi yapılandırma

Windows kullanıyorsanız MongoDB varsayılan olarak *C: \\ Program Files \\ MongoDB* konumunda yüklüdür. Ortam değişkenine *C: \\ Program Files \\ MongoDB \\ sunucu \\ \<version_number> \\ kutusu* ekleyin `Path` . Bu değişiklik geliştirme makinenizde her yerden MongoDB erişimine izin vermez.

Bir veritabanı oluşturmak, koleksiyonları yapmak ve belgeleri depolamak için aşağıdaki adımlarda Mongo kabuğunu kullanın. Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Verileri depolamak için geliştirme makinenizde bir dizin seçin. Örneğin, *C: Windows üzerinde \\ booksdata* . Mevcut değilse dizini oluşturun. Mongo kabuğu yeni dizinler oluşturmaz.
1. Bir komut kabuğu açın. Varsayılan bağlantı noktası 27017 ' de MongoDB 'ye bağlanmak için aşağıdaki komutu çalıştırın. `<data_directory_path>`Önceki adımda seçtiğiniz dizinle değiştirmeyi unutmayın.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Başka bir komut kabuğu örneği açın. Aşağıdaki komutu çalıştırarak Varsayılan test veritabanına bağlanın:

   ```console
   mongo
   ```

1. Komut kabuğu 'nda aşağıdakileri çalıştırın:

   ```console
   use BookstoreDb
   ```

   Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur. Veritabanı mevcutsa, bağlantısı işlemler için açılır.

1. `Books`Aşağıdaki komutu kullanarak bir koleksiyon oluşturun:

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
   > Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.

1. Aşağıdaki komutu kullanarak veritabanında bulunan belgeleri görüntüleyin:

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

   Şema, `_id` her belge için türünde bir otomatik olarak oluşturulan özellik ekler `ObjectId` .

Veritabanı hazırlanıyor. ASP.NET Core Web API 'sini oluşturmaya başlayabilirsiniz.

## <a name="create-the-aspnet-core-web-api-project"></a>ASP.NET Core Web API projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Dosya** > **Yeni** > **Proje**' ye gidin.
1. **ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.
1. Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.
1. **.NET Core** hedef çerçevesini ve **2,2 ASP.NET Core**seçin. **API** proje şablonunu seçin ve **Oluştur**' u seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) . **Paket Yöneticisi konsol** penceresinde, proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Komut kabuğu 'nda aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Yeni bir ASP.NET Core Web API projesi hedefleme .NET Core Visual Studio Code oluşturulur ve açılır.

1. Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?**. **Evet**' i seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) . **Tümleşik Terminal** ' i açın ve proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Sürüm 8,6 ' den önceki Mac için Visual Studio, **File**  >  kenar çubuğundan Dosya**yeni çözüm**  >  **.NET Core**  >  **App** ' i seçin. Sürüm 8,6 veya sonraki sürümlerde **File**  >  kenar çubuğundan Dosya**yeni çözüm**  >  **Web ve konsol**  >  **uygulaması** ' nı seçin.
1. **ASP.NET Core Web API** C# proje şablonunu seçin ve **İleri**' yi seçin.
1. **Hedef çerçeve** açılır listesinden **.NET Core 2,2** ' i seçin ve **İleri**' yi seçin.
1. **Proje adı**Için *booksapı* girin ve **Oluştur**' u seçin.
1. **Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.
1. Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.
1. **Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.

---

## <a name="add-an-entity-model"></a>Varlık modeli ekleme

1. Proje köküne *modeller* dizini ekleyin.
1. `Book` *Modeller* dizinine aşağıdaki kodla bir sınıf ekleyin:

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

   Önceki sınıfta, `Id` özelliği:

   * Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.
   * , [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Bu özelliği belgenin birincil anahtarı olarak belirlemek için ile birlikte açıklanmış.
   * , [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) Parametresinin `string` [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür olarak geçirilmesine izin vermek için ile birlikte açıklanmış. Mongo, ' den ' `string` ye dönüştürmeyi işler `ObjectId` .

   `BookName`Özelliği özniteliğiyle açıklama eklenir [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) . Özniteliğinin değeri, `Name` MongoDB koleksiyonundaki özellik adını temsil eder.

## <a name="add-a-configuration-model"></a>Yapılandırma modeli ekleme

1. Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. *Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Yukarıdaki `BookstoreDatabaseSettings` sınıf, *appSettings. JSON* dosyasının özellik değerlerini depolamak için kullanılır `BookstoreDatabaseSettings` . JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.

1. Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Yukarıdaki kodda:

   * *AppSettings. JSON* dosyasının bölüm bağlandığı yapılandırma örneği, `BookstoreDatabaseSettings` bağımlılık ekleme (dı) kapsayıcısına kaydedilir. Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appSettings. JSON*içindeki özelliği ile doldurulur.
   * `IBookstoreDatabaseSettings`Arabirim, tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir. Eklenen arabirim örneği bir nesne olarak çözümlenir `BookstoreDatabaseSettings` .

1. Ve başvurularını çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>CRUD işlemleri hizmeti ekleme

1. Proje köküne bir *Hizmetler* dizini ekleyin.
1. `BookService` *Hizmetler* dizinine aşağıdaki kodla bir sınıf ekleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır. Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.

1. Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek IÇIN dı ile kaydedilir. Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir bağımlılık alır `MongoClient` . Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir HIZMET ömrü ile dı 'ye kaydedilmelidir.

1. Başvuruyu çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookService` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService`Sınıfı, `MongoDB.Driver` veritabanına karşı CRUD işlemlerini gerçekleştirmek için aşağıdaki üyeleri kullanır:

* [Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur. Bu sınıfın oluşturucusuna MongoDB bağlantı dizesi verilmiştir:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): işlemleri gerçekleştirmek Için Mongo veritabanını temsil eder. Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır. Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin. `GetCollection<TDocument>(collection)`Yöntem çağrısında:

  * `collection`koleksiyon adını temsil eder.
  * `TDocument`Koleksiyonda depolanan CLR nesne türünü temsil eder.

`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür. Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:

* [Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.
* [Bul \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): koleksiyonda, belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.
* [Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.
* [Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.

## <a name="add-a-controller"></a>Denetleyici ekleme

`BooksController` *Controllers* dizinine aşağıdaki kodla bir sınıf ekleyin:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Önceki Web API denetleyicisi:

* `BookService`CRUD işlemleri gerçekleştirmek için sınıfını kullanır.
* HTTP isteklerini al, gönder, koy ve SIL desteği için eylem yöntemleri içerir.
* <xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` [Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için eylem yöntemindeki çağrılar. Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır. `CreatedAtRoute`Ayrıca yanıta bir `Location` üst bilgi ekler. `Location`Üst bilgi, yeni oluşturulan KITABıN URI 'sini belirtir.

## <a name="test-the-web-api"></a>Web API 'sini test etme

1. Uygulamayı derleyin ve çalıştırın.

1. `http://localhost:<port>/api/books`Denetleyicinin parametresiz eylem yöntemini sınamak için öğesine gidin `Get` . Aşağıdaki JSON yanıtı görüntülenir:

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

1. `http://localhost:<port>/api/books/{id here}`Denetleyicinin aşırı yüklenmiş eylem yöntemini sınamak için öğesine gidin `Get` . Aşağıdaki JSON yanıtı görüntülenir:

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

[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:

* ' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.
* `bookName`Özelliği olarak döndürülmelidir `Name` .

Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:

1. ' De `Startup.ConfigureServices` , aşağıdaki vurgulanmış kodu yöntem çağrısına zincirle `AddMvc` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir. Örneğin, `Book` sınıfın `Author` özelliği olarak serileştirir `Author` .

1. *Modeller/Book. cs*' de, `BookName` aşağıdaki özniteliğiyle özelliğe not ekleyin [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]`Özniteliğinin değeri, `Name` Web API 'SININ serileştirilmiş JSON yanıtında özellik adını temsil eder.

1. Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. [Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin. JSON Özellik adlarındaki farka dikkat edin.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Web API 'sine kimlik doğrulama desteği ekleme

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Sonraki adımlar

ASP.NET Core Web API 'Leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Bu makalenin YouTube sürümü](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
