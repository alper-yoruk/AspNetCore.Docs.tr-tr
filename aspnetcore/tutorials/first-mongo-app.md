---
title: ASP.NET Core ve MongoDB ile bir web API oluşturma
author: prkhandelwal
description: Bu öğretici, MongoDB NoSQL veritabanı nı kullanarak ASP.NET Core web API'sini nasıl oluşturacağımı gösterir.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: d5ce4a1dc3c00b2b12edc12e26f482caa97df6b3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511424"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>ASP.NET Core ve MongoDB ile bir web API oluşturma

Yazar: [Pratik Khandelwal](https://twitter.com/K2Prk) ve [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini gerçekleştiren bir web API'si oluşturur.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * MongoDB'yi yapılandır
> * MongoDB veritabanı oluşturma
> * Bir MongoDB koleksiyonu ve şeması tanımlayın
> * Bir web API'den MongoDB CRUD işlemlerini gerçekleştirin
> * JSON serileştirmeyi özelleştir

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code için C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Mac sürüm 7.7 veya sonrası için Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Dosya** > **Yeni** > **Proje**git.
1. ASP.NET **Çekirdek Web Uygulaması** proje türünü seçin ve **İleri'yi**seçin.
1. Proje *BooksApi*adını ve **Oluştur'u**seçin.
1. **.NET Core** hedef çerçevesini seçin ve **Core 3.0'ASP.NET.** **API** proje şablonu seçin ve **Oluştur'u**seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin. Paket **Yöneticisi Konsolu** penceresinde proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Komut kabuğunda aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   .NET Core'u hedefleyen yeni bir ASP.NET Core web API projesi Visual Studio Code'da oluşturulur ve açılır.

1. Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve hata ayıklama 'BooksApi'den eksiktir. Onları da eklemek mi?** **Evet'i**seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin. **Tümleşik Terminali** açın ve proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **DosyaYa** > Git **Yeni Çözüm** > **.NET Core** > **App**.
1. ASP.NET **Core Web API** C# proje şablonu seçin ve **İleri'yi**seçin.
1. **Hedef Çerçeve** açılır listesinden **.NET Core 3.0'ı** seçin ve **İleri'yi**seçin.
1. **Proje Adı**için *BookApi'yi* girin ve **Oluştur'u**seçin.
1. **Çözüm** defterinde, projenin **Bağımlılıklar** düğümüne sağ tıklayın ve **Paket Ekle'yi**seçin.
1. Arama *kutusuna MongoDB.Driver* girin, *MongoDB.Driver* paketini seçin ve **Paket Ekle'yi**seçin.
1. **Lisans Kabul** iletişim **kutusundakabul** et düğmesini seçin.

---

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

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. *Modeller* dizinine aşağıdaki kodla *bir BookstoreDatabaseSettings.cs* dosyası ekleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Önceki `BookstoreDatabaseSettings` sınıf *appsettings.json* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır. JSON ve C# özellik adları eşleme işlemini kolaylaştırmak için aynı şekilde adlandırılır.

1. Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   Yukarıdaki kodda:

   * *Appsettings.json* dosyasının `BookstoreDatabaseSettings` bölümünün bağladığı yapılandırma örneği Bağımlılık Enjeksiyonu (DI) kapsayıcısında kaydedilir. Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appsettings.json*özelliği ile doldurulur.
   * Arabirim, `IBookstoreDatabaseSettings` singleton [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)yle DI'de kayıtlıdır. Enjekte edildiğinde, arabirim örneği bir `BookstoreDatabaseSettings` nesneye giderilir.

1. Başvuruları ve `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` başvuruları çözmek için aşağıdaki kodu *Startup.cs* üst bölümüne ekleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>CRUD operasyon hizmeti ekleme

1. Proje köküne *bir Hizmet* dizini ekleyin.
1. Hizmetler `BookService` dizinine *Services* aşağıdaki kodla bir sınıf ekleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Önceki kodda, bir `IBookstoreDatabaseSettings` örnek yapı oluşturucu enjeksiyon yoluyla DI alınır. Bu teknik, [yapılandırma modeli ekle](#add-a-configuration-model) bölümüne eklenen *appsettings.json* yapılandırma değerlerine erişim sağlar.

1. Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Önceki kodda, `BookService` sınıf tüketen sınıflarda yapıcı enjeksiyonu desteklemek için DI'ye kaydedilir. Singleton hizmet ömrü en `BookService` uygundur, çünkü `MongoClient`doğrudan bir bağımlılık alır. Resmi [Mongo Müşteri yeniden](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)yönergeleri `MongoClient` başına, di bir singleton hizmet ömrü ile kayıtlı olmalıdır.

1. Başvuruyu çözmek için Startup.cs üst bölümüne aşağıdaki kodu ekleyin: *Startup.cs* `BookService`

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Sınıf, `BookService` veritabanına `MongoDB.Driver` karşı CRUD işlemleri gerçekleştirmek için aşağıdaki üyeleri kullanır:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; veritabanı işlemleri gerçekleştirmek için sunucu örneğini okur. Bu sınıfın oluşturucusu MongoDB bağlantı dizesi sağlanır:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

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

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

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

1. In `Startup.ConfigureServices`, `AddControllers` yöntem çağrısı üzerine aşağıdaki vurgulanan kodu zincir:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Önceki değişiklikle, web API'nin serileştirilmiş JSON yanıtındaki özellik adları CLR nesne türünde karşılık gelen özellik adlarıyla eşleşir. Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileşir.

1. *Modeller/Book.cs'* de, `BookName` özelliği aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) öznitelikle açıklama olarak belirtin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Özniteliğin `[JsonProperty]` `Name` değeri, web API'nin serileştirilmiş JSON yanıtındaki özellik adını temsil eder.

1. Öznitelik başvurularını çözmek `[JsonProperty]` için *Modeller/Book.cs'nin* üst bölümüne aşağıdaki kodu ekleyin:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. [Test web API](#test-the-web-api) bölümünde tanımlanan adımları yineleyin. JSON özellik adlarında farka dikkat edin.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğretici, [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini gerçekleştiren bir web API'si oluşturur.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * MongoDB'yi yapılandır
> * MongoDB veritabanı oluşturma
> * Bir MongoDB koleksiyonu ve şeması tanımlayın
> * Bir web API'den MongoDB CRUD işlemlerini gerçekleştirin
> * JSON serileştirmeyi özelleştir

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Çekirdek SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Çekirdek SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code için C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* [.NET Çekirdek SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Mac sürüm 7.7 veya sonrası için Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Dosya** > **Yeni** > **Proje**git.
1. ASP.NET **Çekirdek Web Uygulaması** proje türünü seçin ve **İleri'yi**seçin.
1. Proje *BooksApi*adını ve **Oluştur'u**seçin.
1. **.NET Core** hedef çerçevesini seçin ve **Core 2.2'ASP.NET.** **API** proje şablonu seçin ve **Oluştur'u**seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin. Paket **Yöneticisi Konsolu** penceresinde proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Komut kabuğunda aşağıdaki komutları çalıştırın:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   .NET Core'u hedefleyen yeni bir ASP.NET Core web API projesi Visual Studio Code'da oluşturulur ve açılır.

1. Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve hata ayıklama 'BooksApi'den eksiktir. Onları da eklemek mi?** **Evet'i**seçin.
1. MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin. **Tümleşik Terminali** açın ve proje köküne gidin. MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **DosyaYa** > Git **Yeni Çözüm** > **.NET Core** > **App**.
1. ASP.NET **Core Web API** C# proje şablonu seçin ve **İleri'yi**seçin.
1. **Hedef Çerçeve** açılır listesinden **.NET Core 2.2'yi** seçin ve **İleri'yi**seçin.
1. **Proje Adı**için *BookApi'yi* girin ve **Oluştur'u**seçin.
1. **Çözüm** defterinde, projenin **Bağımlılıklar** düğümüne sağ tıklayın ve **Paket Ekle'yi**seçin.
1. Arama *kutusuna MongoDB.Driver* girin, *MongoDB.Driver* paketini seçin ve **Paket Ekle'yi**seçin.
1. **Lisans Kabul** iletişim **kutusundakabul** et düğmesini seçin.

---

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

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. *Modeller* dizinine aşağıdaki kodla *bir BookstoreDatabaseSettings.cs* dosyası ekleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Önceki `BookstoreDatabaseSettings` sınıf *appsettings.json* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır. JSON ve C# özellik adları eşleme işlemini kolaylaştırmak için aynı şekilde adlandırılır.

1. Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Yukarıdaki kodda:

   * *Appsettings.json* dosyasının `BookstoreDatabaseSettings` bölümünün bağladığı yapılandırma örneği Bağımlılık Enjeksiyonu (DI) kapsayıcısında kaydedilir. Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appsettings.json*özelliği ile doldurulur.
   * Arabirim, `IBookstoreDatabaseSettings` singleton [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)yle DI'de kayıtlıdır. Enjekte edildiğinde, arabirim örneği bir `BookstoreDatabaseSettings` nesneye giderilir.

1. Başvuruları ve `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` başvuruları çözmek için aşağıdaki kodu *Startup.cs* üst bölümüne ekleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>CRUD operasyon hizmeti ekleme

1. Proje köküne *bir Hizmet* dizini ekleyin.
1. Hizmetler `BookService` dizinine *Services* aşağıdaki kodla bir sınıf ekleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Önceki kodda, bir `IBookstoreDatabaseSettings` örnek yapı oluşturucu enjeksiyon yoluyla DI alınır. Bu teknik, [yapılandırma modeli ekle](#add-a-configuration-model) bölümüne eklenen *appsettings.json* yapılandırma değerlerine erişim sağlar.

1. Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Önceki kodda, `BookService` sınıf tüketen sınıflarda yapıcı enjeksiyonu desteklemek için DI'ye kaydedilir. Singleton hizmet ömrü en `BookService` uygundur, çünkü `MongoClient`doğrudan bir bağımlılık alır. Resmi [Mongo Müşteri yeniden](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)yönergeleri `MongoClient` başına, di bir singleton hizmet ömrü ile kayıtlı olmalıdır.

1. Başvuruyu çözmek için Startup.cs üst bölümüne aşağıdaki kodu ekleyin: *Startup.cs* `BookService`

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Sınıf, `BookService` veritabanına `MongoDB.Driver` karşı CRUD işlemleri gerçekleştirmek için aşağıdaki üyeleri kullanır:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; veritabanı işlemleri gerçekleştirmek için sunucu örneğini okur. Bu sınıfın oluşturucusu MongoDB bağlantı dizesi sağlanır:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

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

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

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

1. In `Startup.ConfigureServices`, `AddMvc` yöntem çağrısı üzerine aşağıdaki vurgulanan kodu zincir:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Önceki değişiklikle, web API'nin serileştirilmiş JSON yanıtındaki özellik adları CLR nesne türünde karşılık gelen özellik adlarıyla eşleşir. Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileşir.

1. *Modeller/Book.cs'* de, `BookName` özelliği aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) öznitelikle açıklama olarak belirtin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Özniteliğin `[JsonProperty]` `Name` değeri, web API'nin serileştirilmiş JSON yanıtındaki özellik adını temsil eder.

1. Öznitelik başvurularını çözmek `[JsonProperty]` için *Modeller/Book.cs'nin* üst bölümüne aşağıdaki kodu ekleyin:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. [Test web API](#test-the-web-api) bölümünde tanımlanan adımları yineleyin. JSON özellik adlarında farka dikkat edin.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Web API'sine kimlik doğrulama desteği ekleme

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Sonraki adımlar

Core web API'ASP.NET leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Bu makalenin YouTube sürümü](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
