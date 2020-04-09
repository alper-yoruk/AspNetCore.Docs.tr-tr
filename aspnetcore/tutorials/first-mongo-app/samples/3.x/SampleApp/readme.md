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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="9e1a4-102">ASP.NET Core ve MongoDB ile bir web API oluşturma</span><span class="sxs-lookup"><span data-stu-id="9e1a4-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="9e1a4-103">Bu öğretici, [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini gerçekleştiren bir web API'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="9e1a4-104">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="9e1a4-105">MongoDB'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="9e1a4-105">Configure MongoDB</span></span>
* <span data-ttu-id="9e1a4-106">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="9e1a4-106">Create a MongoDB database</span></span>
* <span data-ttu-id="9e1a4-107">Bir MongoDB koleksiyonu ve şeması tanımlayın</span><span class="sxs-lookup"><span data-stu-id="9e1a4-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="9e1a4-108">Bir web API'den MongoDB CRUD işlemlerini gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="9e1a4-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="9e1a4-109">JSON serileştirmeyi özelleştir</span><span class="sxs-lookup"><span data-stu-id="9e1a4-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e1a4-110">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="9e1a4-110">Prerequisites</span></span>

* [<span data-ttu-id="9e1a4-111">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="9e1a4-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="9e1a4-112">[Visual Studio 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) **ASP.NET ve web geliştirme** iş yükü ile önizleme</span><span class="sxs-lookup"><span data-stu-id="9e1a4-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="9e1a4-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9e1a4-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="9e1a4-114">MongoDB'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="9e1a4-114">Configure MongoDB</span></span>

<span data-ttu-id="9e1a4-115">Windows kullanıyorsanız, MongoDB varsayılan olarak *C:\\Program Files\\MongoDB'ye* yüklenir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="9e1a4-116">*C ekle:\\\\Program\\Dosyaları MongoDB Server\\\<version_number>\\bin* çevre değişkenine. `Path`</span><span class="sxs-lookup"><span data-stu-id="9e1a4-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="9e1a4-117">Bu değişiklik, geliştirme makinenizin herhangi bir yerinden MongoDB erişimine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="9e1a4-118">Bir veritabanı oluşturmak, koleksiyonlar oluşturmak ve belgeleri depolamak için aşağıdaki adımlardaki mongo Shell'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="9e1a4-119">Mongo Shell komutları hakkında daha fazla bilgi için [mongo Shell ile çalışma bilgisine](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)bakın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="9e1a4-120">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="9e1a4-121">Örneğin, *C:\\* Windows'da Kitaplar Verileri.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="9e1a4-122">Yoksa dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="9e1a4-123">Mongo Shell yeni dizinler yaratmaz.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="9e1a4-124">Komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-124">Open a command shell.</span></span> <span data-ttu-id="9e1a4-125">Varsayılan bağlantı noktası 27017'de MongoDB'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="9e1a4-126">Önceki adımda seçtiğiniz dizini değiştirmeyi `<data_directory_path>` unutmayın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="9e1a4-127">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-127">Open another command shell instance.</span></span> <span data-ttu-id="9e1a4-128">Aşağıdaki komutu çalıştırarak varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="9e1a4-129">Aşağıdakileri bir komut kabuğunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="9e1a4-130">Zaten yoksa, *BookstoreDb* adında bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="9e1a4-131">Veritabanı varsa, bağlantısı hareketler için açılır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="9e1a4-132">Aşağıdaki `Books` komutu kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="9e1a4-133">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="9e1a4-134">Koleksiyon için bir şema tanımlayın `Books` ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="9e1a4-135">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="9e1a4-136">Bu makalede gösterilen kimlik, bu örneği çalıştırdığınızda kimliklerle eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="9e1a4-137">Aşağıdaki komutu kullanarak veritabanındaki belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="9e1a4-138">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="9e1a4-139">Şema, her belge `_id` için otomatik `ObjectId` olarak oluşturulmuş bir tür özelliği ekler.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="9e1a4-140">Veritabanı hazır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-140">The database is ready.</span></span> <span data-ttu-id="9e1a4-141">ASP.NET Core web API'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="9e1a4-142">ASP.NET Core web API projesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="9e1a4-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="9e1a4-143">**Dosya** > **Yeni** > **Proje**git.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="9e1a4-144">ASP.NET **Çekirdek Web Uygulaması** proje türünü seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="9e1a4-145">Proje *BooksApi*adını ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="9e1a4-146">**.NET Core** hedef çerçevesini seçin ve **Core 3.0'ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="9e1a4-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="9e1a4-147">**API** proje şablonu seçin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="9e1a4-148">MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9e1a4-149">Paket **Yöneticisi Konsolu** penceresinde proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="9e1a4-150">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="9e1a4-151">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="9e1a4-151">Add an entity model</span></span>

1. <span data-ttu-id="9e1a4-152">Proje köküne bir *Modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="9e1a4-153">Aşağıdaki `Book` kodla *Modeller* dizinine bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="9e1a4-154">Önceki sınıfta, `Id` özellik:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="9e1a4-155">Ortak Dil Çalışma Zamanı (CLR) nesnesini MongoDB koleksiyonuyla eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="9e1a4-156">Bu özelliği belgenin [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) birincil anahtarı olarak belirlemek üzere açıklama yapılır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="9e1a4-157">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) parametrenin türü `string` olarak geçirilmesine izin vermek için açıklama yapılır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="9e1a4-158">Mongo dönüşüm `string` `ObjectId`yönetir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="9e1a4-159">Özellik `BookName` öznitelik ile [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) açıklamalı.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="9e1a4-160">Özniteliğin `Name` değeri MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="9e1a4-161">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="9e1a4-161">Add a configuration model</span></span>

1. <span data-ttu-id="9e1a4-162">*Appsettings.json'a*aşağıdaki veritabanı yapılandırma değerlerini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="9e1a4-163">*Modeller* dizinine aşağıdaki kodla *bir BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="9e1a4-164">Önceki `BookstoreDatabaseSettings` sınıf *appsettings.json* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="9e1a4-165">JSON ve C# özellik adları eşleme işlemini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="9e1a4-166">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="9e1a4-167">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-167">In the preceding code:</span></span>

    * <span data-ttu-id="9e1a4-168">*Appsettings.json* dosyasının `BookstoreDatabaseSettings` bölümünün bağladığı yapılandırma örneği Bağımlılık Enjeksiyonu (DI) kapsayıcısında kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="9e1a4-169">Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appsettings.json*özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="9e1a4-170">Arabirim, `IBookstoreDatabaseSettings` singleton [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)yle DI'de kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9e1a4-171">Enjekte edildiğinde, arabirim örneği bir `BookstoreDatabaseSettings` nesneye giderilir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="9e1a4-172">Başvuruları ve `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` başvuruları çözmek için aşağıdaki kodu *Startup.cs* üst bölümüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="9e1a4-173">CRUD operasyon hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="9e1a4-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="9e1a4-174">Proje köküne *bir Hizmet* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="9e1a4-175">Hizmetler `BookService` dizinine *Services* aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

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

    <span data-ttu-id="9e1a4-176">Önceki kodda, bir `IBookstoreDatabaseSettings` örnek yapı oluşturucu enjeksiyon yoluyla DI alınır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="9e1a4-177">Bu teknik, [yapılandırma modeli ekle](#add-a-configuration-model) bölümüne eklenen *appsettings.json* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="9e1a4-178">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="9e1a4-179">Önceki kodda, `BookService` sınıf tüketen sınıflarda yapıcı enjeksiyonu desteklemek için DI'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="9e1a4-180">Singleton hizmet ömrü en `BookService` uygundur, çünkü `MongoClient`doğrudan bir bağımlılık alır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="9e1a4-181">Resmi [Mongo Müşteri yeniden](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)yönergeleri `MongoClient` başına, di bir singleton hizmet ömrü ile kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="9e1a4-182">Başvuruyu çözmek için Startup.cs üst bölümüne aşağıdaki kodu ekleyin: *Startup.cs* `BookService`</span><span class="sxs-lookup"><span data-stu-id="9e1a4-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="9e1a4-183">Sınıf, `BookService` veritabanına `MongoDB.Driver` karşı CRUD işlemleri gerçekleştirmek için aşağıdaki üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="9e1a4-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; veritabanı işlemleri gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="9e1a4-185">Bu sınıfın oluşturucusu MongoDB bağlantı dizesi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="9e1a4-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Işlemleri gerçekleştirmek için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="9e1a4-187">Bu öğretici, belirli bir koleksiyondaki verilere erişmek için arabirimdeki genel [GetCollection\<TDocument>(toplama)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="9e1a4-188">Bu yöntem çağrıldıktan sonra koleksiyona karşı CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="9e1a4-189">Yöntem `GetCollection<TDocument>(collection)` aramasında:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="9e1a4-190">`collection`koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="9e1a4-191">`TDocument`koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="9e1a4-192">`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="9e1a4-193">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="9e1a4-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Sağlanan arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="9e1a4-195">[TBelgesi'ni\<bul>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Koleksiyondaki tüm belgeleri sağlanan arama ölçütleriyle eşleşen olarak döndürür.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="9e1a4-196">[InsertOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; sağlanan nesneyi koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="9e1a4-197">[ReplaceOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; sağlanan arama ölçütleriyle eşleşen tek belgeyi sağlanan nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9e1a4-198">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="9e1a4-198">Add a controller</span></span>

<span data-ttu-id="9e1a4-199">Denetleyiciler `BooksController` dizinine *Controllers* aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

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

<span data-ttu-id="9e1a4-200">Önceki web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-200">The preceding web API controller:</span></span>

* <span data-ttu-id="9e1a4-201">CRUD `BookService` işlemlerini gerçekleştirmek için sınıfı kullanır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="9e1a4-202">GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="9e1a4-203">Http <xref:System.Web.Http.ApiController.CreatedAtRoute*> [201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtını döndürmek için eylem yöntemini `Create` çağırır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="9e1a4-204">Durum kodu 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="9e1a4-205">`CreatedAtRoute`ayrıca yanıta bir `Location` üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="9e1a4-206">Üstbilgi, `Location` yeni oluşturulan kitabın URI'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="9e1a4-207">Web API'sini test edin</span><span class="sxs-lookup"><span data-stu-id="9e1a4-207">Test the web API</span></span>

1. <span data-ttu-id="9e1a4-208">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-208">Build and run the app.</span></span>

1. <span data-ttu-id="9e1a4-209">Denetleyicinin `http://localhost:<port>/api/books` parametresiz `Get` eylem yöntemini sınamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="9e1a4-210">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="9e1a4-211">Denetleyicinin `http://localhost:<port>/api/books/{id here}` aşırı yüklenmiş `Get` eylem yöntemini sınamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="9e1a4-212">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="9e1a4-213">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9e1a4-213">Configure JSON serialization options</span></span>

<span data-ttu-id="9e1a4-214">[Test web API](#test-the-web-api) bölümünde döndürülen JSON yanıtları hakkında değiştirmek için iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="9e1a4-215">Özellik adlarının varsayılan deve gövdesi, CLR nesnesinin özellik adlarının Pascal kasasına uyacak şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="9e1a4-216">Özellik `bookName` olarak `Name`döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="9e1a4-217">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="9e1a4-218">JSON.NET paylaşılan ASP.NET çerçeveden kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="9e1a4-219">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)bir paket başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-219">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="9e1a4-220">In `Startup.ConfigureServices`, `AddMvc` yöntem çağrısı üzerine aşağıdaki vurgulanan kodu zincir:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

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

    <span data-ttu-id="9e1a4-221">Önceki değişiklikle, web API'nin serileştirilmiş JSON yanıtındaki özellik adları CLR nesne türünde karşılık gelen özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="9e1a4-222">Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileşir.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="9e1a4-223">*Modeller/Book.cs'* de, `BookName` özelliği aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) öznitelikle açıklama olarak belirtin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="9e1a4-224">Özniteliğin `[JsonProperty]` `Name` değeri, web API'nin serileştirilmiş JSON yanıtındaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="9e1a4-225">Öznitelik başvurularını çözmek `[JsonProperty]` için *Modeller/Book.cs'nin* üst bölümüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="9e1a4-226">[Test web API](#test-the-web-api) bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="9e1a4-227">JSON özellik adlarında farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="9e1a4-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9e1a4-228">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9e1a4-228">Next steps</span></span>

<span data-ttu-id="9e1a4-229">Core web API'ASP.NET leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="9e1a4-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="9e1a4-230">Bu makalenin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="9e1a4-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="9e1a4-231">ASP.NET Core ile web API'leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="9e1a4-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
