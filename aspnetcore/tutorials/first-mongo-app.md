---
title: ASP.NET Core ve MongoDB ile Web API 'SI oluşturma
author: prkhandelwal
description: Bu öğreticide, MongoDB NoSQL veritabanı kullanarak ASP.NET Core Web API 'sinin nasıl oluşturulacağı gösterilmektedir.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060748"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="6affb-103">ASP.NET Core ve MongoDB ile Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="6affb-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="6affb-104">By [pratik Khandelwal](https://twitter.com/K2Prk) ve [Scott Ade](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6affb-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6affb-105">Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6affb-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6affb-106">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="6affb-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6affb-107">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6affb-107">Configure MongoDB</span></span>
> * <span data-ttu-id="6affb-108">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="6affb-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="6affb-109">MongoDB koleksiyonu ve şeması tanımlama</span><span class="sxs-lookup"><span data-stu-id="6affb-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="6affb-110">Web API 'sinden MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="6affb-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="6affb-111">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="6affb-111">Customize JSON serialization</span></span>

<span data-ttu-id="6affb-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6affb-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6affb-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="6affb-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6affb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="6affb-115">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6affb-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="6affb-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="6affb-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6affb-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6affb-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6affb-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6affb-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6affb-119">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6affb-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6affb-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6affb-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6affb-121">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="6affb-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="6affb-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6affb-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6affb-123">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6affb-124">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6affb-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6affb-125">Mac için Visual Studio sürüm 7,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6affb-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="6affb-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6affb-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="6affb-127">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6affb-127">Configure MongoDB</span></span>

<span data-ttu-id="6affb-128">Windows kullanıyorsanız MongoDB varsayılan olarak *C: \\ Program Files \\ MongoDB* konumunda yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="6affb-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6affb-129">Ortam değişkenine *C: \\ Program Files \\ MongoDB \\ sunucu \\ \<version_number> \\ kutusu* ekleyin `Path` .</span><span class="sxs-lookup"><span data-stu-id="6affb-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6affb-130">Bu değişiklik geliştirme makinenizde her yerden MongoDB erişimine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="6affb-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6affb-131">Bir veritabanı oluşturmak, koleksiyonları yapmak ve belgeleri depolamak için aşağıdaki adımlarda Mongo kabuğunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="6affb-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6affb-132">Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="6affb-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6affb-133">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6affb-134">Örneğin, *C: Windows üzerinde \\ booksdata* .</span><span class="sxs-lookup"><span data-stu-id="6affb-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6affb-135">Mevcut değilse dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6affb-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6affb-136">Mongo kabuğu yeni dizinler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="6affb-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6affb-137">Bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="6affb-137">Open a command shell.</span></span> <span data-ttu-id="6affb-138">Varsayılan bağlantı noktası 27017 ' de MongoDB 'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6affb-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6affb-139">`<data_directory_path>`Önceki adımda seçtiğiniz dizinle değiştirmeyi unutmayın.</span><span class="sxs-lookup"><span data-stu-id="6affb-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="6affb-140">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="6affb-140">Open another command shell instance.</span></span> <span data-ttu-id="6affb-141">Aşağıdaki komutu çalıştırarak Varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="6affb-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="6affb-142">Komut kabuğu 'nda aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="6affb-143">Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6affb-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6affb-144">Veritabanı mevcutsa, bağlantısı işlemler için açılır.</span><span class="sxs-lookup"><span data-stu-id="6affb-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6affb-145">`Books`Aşağıdaki komutu kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6affb-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="6affb-146">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="6affb-147">Koleksiyon için bir şema tanımlayın `Books` ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="6affb-148">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="6affb-149">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="6affb-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6affb-150">Aşağıdaki komutu kullanarak veritabanında bulunan belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="6affb-151">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="6affb-152">Şema, `_id` her belge için türünde bir otomatik olarak oluşturulan özellik ekler `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="6affb-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6affb-153">Veritabanı hazırlanıyor.</span><span class="sxs-lookup"><span data-stu-id="6affb-153">The database is ready.</span></span> <span data-ttu-id="6affb-154">ASP.NET Core Web API 'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6affb-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6affb-155">ASP.NET Core Web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6affb-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6affb-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6affb-157">**Dosya** > **Yeni** > **Proje**' ye gidin.</span><span class="sxs-lookup"><span data-stu-id="6affb-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6affb-158">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6affb-159">Projeyi *Booksapı* olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6affb-160">**.NET Core** hedef çerçevesini ve **3,0 ASP.NET Core** seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="6affb-161">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6affb-162">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6affb-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6affb-163">**Paket Yöneticisi konsol** penceresinde, proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6affb-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6affb-164">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6affb-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6affb-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6affb-166">Komut kabuğu 'nda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="6affb-167">Yeni bir ASP.NET Core Web API projesi hedefleme .NET Core Visual Studio Code oluşturulur ve açılır.</span><span class="sxs-lookup"><span data-stu-id="6affb-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="6affb-168">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?**.</span><span class="sxs-lookup"><span data-stu-id="6affb-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="6affb-169">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-169">Select **Yes**.</span></span>
1. <span data-ttu-id="6affb-170">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6affb-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6affb-171">**Tümleşik Terminal** ' i açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6affb-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="6affb-172">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6affb-173">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6affb-174">Sürüm 8,6 ' den önceki Mac için Visual Studio,   >  kenar çubuğundan Dosya **yeni çözüm**  >  **.NET Core**  >  **App** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="6affb-175">Sürüm 8,6 veya sonraki sürümlerde   >  kenar çubuğundan Dosya **yeni çözüm**  >  **Web ve konsol**  >  **uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="6affb-176">**ASP.NET Core** > **API** C# proje şablonunu seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="6affb-177">**Hedef çerçeve** açılır listesinden **.NET Core 3,1** ' i seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="6affb-178">**Proje adı** Için *booksapı* girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="6affb-179">**Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="6affb-180">Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="6affb-181">**Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="6affb-182">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-182">Add an entity model</span></span>

1. <span data-ttu-id="6affb-183">Proje köküne *modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6affb-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6affb-184">`Book` *Modeller* dizinine aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="6affb-185">Önceki sınıfta, `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="6affb-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="6affb-186">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6affb-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="6affb-187">, [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Bu özelliği belgenin birincil anahtarı olarak belirlemek için ile birlikte açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="6affb-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="6affb-188">, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) Parametresinin `string` [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür olarak geçirilmesine izin vermek için ile birlikte açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="6affb-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6affb-189">Mongo, ' den ' `string` ye dönüştürmeyi işler `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="6affb-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="6affb-190">`BookName`Özelliği özniteliğiyle açıklama eklenir [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="6affb-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6affb-191">Özniteliğinin değeri, `Name` MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6affb-192">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-192">Add a configuration model</span></span>

1. <span data-ttu-id="6affb-193">Aşağıdaki veritabanı yapılandırma değerlerini şu şekilde ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="6affb-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="6affb-194">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="6affb-195">Önceki `BookstoreDatabaseSettings` sınıf, *appsettings.json* dosyanın özellik değerlerini depolamak için kullanılır `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="6affb-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6affb-196">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6affb-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6affb-197">Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6affb-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="6affb-198">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="6affb-198">In the preceding code:</span></span>

   * <span data-ttu-id="6affb-199">Dosyanın bölüm bağlandığı yapılandırma örneği, *appsettings.json* `BookstoreDatabaseSettings` bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6affb-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6affb-200">Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` içindeki özelliği ile doldurulur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6affb-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="6affb-201">`IBookstoreDatabaseSettings`Arabirim, tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6affb-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6affb-202">Eklenen arabirim örneği bir nesne olarak çözümlenir `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="6affb-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6affb-203">Ve başvurularını çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="6affb-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6affb-204">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6affb-205">Proje köküne bir *Hizmetler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6affb-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6affb-206">`BookService` *Hizmetler* dizinine aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="6affb-207">Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır.</span><span class="sxs-lookup"><span data-stu-id="6affb-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6affb-208">Bu teknik, *appsettings.json* [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="6affb-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6affb-209">Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6affb-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="6affb-210">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek IÇIN dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6affb-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6affb-211">Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir bağımlılık alır `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="6affb-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6affb-212">Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir HIZMET ömrü ile dı 'ye kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6affb-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6affb-213">Başvuruyu çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookService` :</span><span class="sxs-lookup"><span data-stu-id="6affb-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="6affb-214">`BookService`Sınıfı, `MongoDB.Driver` veritabanına karşı CRUD işlemlerini gerçekleştirmek için aşağıdaki üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="6affb-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6affb-215">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="6affb-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6affb-216">Bu sınıfın oluşturucusuna MongoDB bağlantı dizesi verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="6affb-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="6affb-217">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): işlemleri gerçekleştirmek Için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6affb-218">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6affb-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6affb-219">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="6affb-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6affb-220">`GetCollection<TDocument>(collection)`Yöntem çağrısında:</span><span class="sxs-lookup"><span data-stu-id="6affb-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="6affb-221">`collection` koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6affb-222">`TDocument` Koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6affb-223">`GetCollection<TDocument>(collection)` koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="6affb-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6affb-224">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="6affb-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6affb-225">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="6affb-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6affb-226">[Bul \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): koleksiyonda, belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="6affb-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6affb-227">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="6affb-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6affb-228">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6affb-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6affb-229">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-229">Add a controller</span></span>

<span data-ttu-id="6affb-230">`BooksController` *Controllers* dizinine aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="6affb-231">Önceki Web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="6affb-231">The preceding web API controller:</span></span>

* <span data-ttu-id="6affb-232">`BookService`CRUD işlemleri gerçekleştirmek için sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6affb-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6affb-233">HTTP isteklerini al, gönder, koy ve SIL desteği için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="6affb-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6affb-234"><xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` [Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için eylem yöntemindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="6affb-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6affb-235">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="6affb-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6affb-236">`CreatedAtRoute` Ayrıca yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="6affb-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6affb-237">`Location`Üst bilgi, yeni oluşturulan KITABıN URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6affb-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6affb-238">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="6affb-238">Test the web API</span></span>

1. <span data-ttu-id="6affb-239">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6affb-239">Build and run the app.</span></span>

1. <span data-ttu-id="6affb-240">`http://localhost:<port>/api/books`Denetleyicinin parametresiz eylem yöntemini sınamak için öğesine gidin `Get` .</span><span class="sxs-lookup"><span data-stu-id="6affb-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6affb-241">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-241">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="6affb-242">`http://localhost:<port>/api/books/{id here}`Denetleyicinin aşırı yüklenmiş eylem yöntemini sınamak için öğesine gidin `Get` .</span><span class="sxs-lookup"><span data-stu-id="6affb-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6affb-243">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6affb-244">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6affb-244">Configure JSON serialization options</span></span>

<span data-ttu-id="6affb-245">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="6affb-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6affb-246">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6affb-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6affb-247">`bookName`Özelliği olarak döndürülmelidir `Name` .</span><span class="sxs-lookup"><span data-stu-id="6affb-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6affb-248">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="6affb-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6affb-249">JSON.NET, ASP.NET paylaşılan çerçevesinden kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="6affb-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="6affb-250">Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .</span><span class="sxs-lookup"><span data-stu-id="6affb-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="6affb-251">' De `Startup.ConfigureServices` , aşağıdaki vurgulanmış kodu yöntem çağrısına zincirle `AddControllers` :</span><span class="sxs-lookup"><span data-stu-id="6affb-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="6affb-252">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="6affb-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6affb-253">Örneğin, `Book` sınıfın `Author` özelliği olarak serileştirir `Author` .</span><span class="sxs-lookup"><span data-stu-id="6affb-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6affb-254">*Modeller/Book. cs*' de, `BookName` aşağıdaki özniteliğiyle özelliğe not ekleyin [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="6affb-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="6affb-255">`[JsonProperty]`Özniteliğinin değeri, `Name` Web API 'SININ serileştirilmiş JSON yanıtında özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6affb-256">Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="6affb-257">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="6affb-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6affb-258">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="6affb-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6affb-259">Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6affb-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="6affb-260">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="6affb-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6affb-261">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6affb-261">Configure MongoDB</span></span>
> * <span data-ttu-id="6affb-262">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="6affb-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="6affb-263">MongoDB koleksiyonu ve şeması tanımlama</span><span class="sxs-lookup"><span data-stu-id="6affb-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="6affb-264">Web API 'sinden MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="6affb-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="6affb-265">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="6affb-265">Customize JSON serialization</span></span>

<span data-ttu-id="6affb-266">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6affb-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6affb-267">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="6affb-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6affb-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="6affb-269">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6affb-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="6affb-270">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="6affb-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6affb-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6affb-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6affb-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6affb-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6affb-273">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6affb-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6affb-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6affb-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6affb-275">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="6affb-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="6affb-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6affb-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6affb-277">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6affb-278">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="6affb-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="6affb-279">Mac için Visual Studio sürüm 7,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6affb-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="6affb-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="6affb-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="6affb-281">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6affb-281">Configure MongoDB</span></span>

<span data-ttu-id="6affb-282">Windows kullanıyorsanız MongoDB varsayılan olarak *C: \\ Program Files \\ MongoDB* konumunda yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="6affb-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="6affb-283">Ortam değişkenine *C: \\ Program Files \\ MongoDB \\ sunucu \\ \<version_number> \\ kutusu* ekleyin `Path` .</span><span class="sxs-lookup"><span data-stu-id="6affb-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="6affb-284">Bu değişiklik geliştirme makinenizde her yerden MongoDB erişimine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="6affb-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="6affb-285">Bir veritabanı oluşturmak, koleksiyonları yapmak ve belgeleri depolamak için aşağıdaki adımlarda Mongo kabuğunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="6affb-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="6affb-286">Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="6affb-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="6affb-287">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="6affb-288">Örneğin, *C: Windows üzerinde \\ booksdata* .</span><span class="sxs-lookup"><span data-stu-id="6affb-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="6affb-289">Mevcut değilse dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6affb-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="6affb-290">Mongo kabuğu yeni dizinler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="6affb-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="6affb-291">Bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="6affb-291">Open a command shell.</span></span> <span data-ttu-id="6affb-292">Varsayılan bağlantı noktası 27017 ' de MongoDB 'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6affb-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="6affb-293">`<data_directory_path>`Önceki adımda seçtiğiniz dizinle değiştirmeyi unutmayın.</span><span class="sxs-lookup"><span data-stu-id="6affb-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="6affb-294">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="6affb-294">Open another command shell instance.</span></span> <span data-ttu-id="6affb-295">Aşağıdaki komutu çalıştırarak Varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="6affb-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="6affb-296">Komut kabuğu 'nda aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="6affb-297">Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6affb-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="6affb-298">Veritabanı mevcutsa, bağlantısı işlemler için açılır.</span><span class="sxs-lookup"><span data-stu-id="6affb-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="6affb-299">`Books`Aşağıdaki komutu kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6affb-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="6affb-300">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="6affb-301">Koleksiyon için bir şema tanımlayın `Books` ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="6affb-302">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-302">The following result is displayed:</span></span>

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
   > <span data-ttu-id="6affb-303">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="6affb-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="6affb-304">Aşağıdaki komutu kullanarak veritabanında bulunan belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="6affb-305">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-305">The following result is displayed:</span></span>

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

   <span data-ttu-id="6affb-306">Şema, `_id` her belge için türünde bir otomatik olarak oluşturulan özellik ekler `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="6affb-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="6affb-307">Veritabanı hazırlanıyor.</span><span class="sxs-lookup"><span data-stu-id="6affb-307">The database is ready.</span></span> <span data-ttu-id="6affb-308">ASP.NET Core Web API 'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6affb-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="6affb-309">ASP.NET Core Web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6affb-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6affb-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6affb-311">**Dosya** > **Yeni** > **Proje**' ye gidin.</span><span class="sxs-lookup"><span data-stu-id="6affb-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="6affb-312">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="6affb-313">Projeyi *Booksapı* olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="6affb-314">**.NET Core** hedef çerçevesini ve **2,2 ASP.NET Core** seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="6affb-315">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="6affb-316">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6affb-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6affb-317">**Paket Yöneticisi konsol** penceresinde, proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6affb-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="6affb-318">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6affb-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6affb-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6affb-320">Komut kabuğu 'nda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="6affb-321">Yeni bir ASP.NET Core Web API projesi hedefleme .NET Core Visual Studio Code oluşturulur ve açılır.</span><span class="sxs-lookup"><span data-stu-id="6affb-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="6affb-322">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?**.</span><span class="sxs-lookup"><span data-stu-id="6affb-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="6affb-323">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-323">Select **Yes**.</span></span>
1. <span data-ttu-id="6affb-324">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="6affb-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="6affb-325">**Tümleşik Terminal** ' i açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="6affb-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="6affb-326">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6affb-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6affb-327">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6affb-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6affb-328">Sürüm 8,6 ' den önceki Mac için Visual Studio,   >  kenar çubuğundan Dosya **yeni çözüm**  >  **.NET Core**  >  **App** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="6affb-329">Sürüm 8,6 veya sonraki sürümlerde   >  kenar çubuğundan Dosya **yeni çözüm**  >  **Web ve konsol**  >  **uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="6affb-330">**ASP.NET Core Web API** C# proje şablonunu seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="6affb-331">**Hedef çerçeve** açılır listesinden **.NET Core 2,2** ' i seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="6affb-332">**Proje adı** Için *booksapı* girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="6affb-333">**Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="6affb-334">Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="6affb-335">**Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6affb-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="6affb-336">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-336">Add an entity model</span></span>

1. <span data-ttu-id="6affb-337">Proje köküne *modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6affb-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="6affb-338">`Book` *Modeller* dizinine aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="6affb-339">Önceki sınıfta, `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="6affb-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="6affb-340">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6affb-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="6affb-341">, [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) Bu özelliği belgenin birincil anahtarı olarak belirlemek için ile birlikte açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="6affb-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="6affb-342">, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) Parametresinin `string` [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine tür olarak geçirilmesine izin vermek için ile birlikte açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="6affb-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="6affb-343">Mongo, ' den ' `string` ye dönüştürmeyi işler `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="6affb-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="6affb-344">`BookName`Özelliği özniteliğiyle açıklama eklenir [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="6affb-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="6affb-345">Özniteliğinin değeri, `Name` MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="6affb-346">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-346">Add a configuration model</span></span>

1. <span data-ttu-id="6affb-347">Aşağıdaki veritabanı yapılandırma değerlerini şu şekilde ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="6affb-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="6affb-348">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="6affb-349">Önceki `BookstoreDatabaseSettings` sınıf, *appsettings.json* dosyanın özellik değerlerini depolamak için kullanılır `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="6affb-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="6affb-350">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6affb-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="6affb-351">Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6affb-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="6affb-352">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="6affb-352">In the preceding code:</span></span>

   * <span data-ttu-id="6affb-353">Dosyanın bölüm bağlandığı yapılandırma örneği, *appsettings.json* `BookstoreDatabaseSettings` bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6affb-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="6affb-354">Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` içindeki özelliği ile doldurulur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6affb-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="6affb-355">`IBookstoreDatabaseSettings`Arabirim, tek bir [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6affb-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6affb-356">Eklenen arabirim örneği bir nesne olarak çözümlenir `BookstoreDatabaseSettings` .</span><span class="sxs-lookup"><span data-stu-id="6affb-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="6affb-357">Ve başvurularını çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :</span><span class="sxs-lookup"><span data-stu-id="6affb-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="6affb-358">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="6affb-359">Proje köküne bir *Hizmetler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6affb-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="6affb-360">`BookService` *Hizmetler* dizinine aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="6affb-361">Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır.</span><span class="sxs-lookup"><span data-stu-id="6affb-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="6affb-362">Bu teknik, *appsettings.json* [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="6affb-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="6affb-363">Aşağıdaki Vurgulanan kodu öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6affb-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="6affb-364">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek IÇIN dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6affb-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="6affb-365">Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir bağımlılık alır `MongoClient` .</span><span class="sxs-lookup"><span data-stu-id="6affb-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="6affb-366">Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir HIZMET ömrü ile dı 'ye kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6affb-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="6affb-367">Başvuruyu çözümlemek için aşağıdaki kodu *Startup.cs* 'in en üstüne ekleyin `BookService` :</span><span class="sxs-lookup"><span data-stu-id="6affb-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="6affb-368">`BookService`Sınıfı, `MongoDB.Driver` veritabanına karşı CRUD işlemlerini gerçekleştirmek için aşağıdaki üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="6affb-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="6affb-369">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="6affb-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="6affb-370">Bu sınıfın oluşturucusuna MongoDB bağlantı dizesi verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="6affb-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="6affb-371">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): işlemleri gerçekleştirmek Için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="6affb-372">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="6affb-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="6affb-373">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="6affb-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="6affb-374">`GetCollection<TDocument>(collection)`Yöntem çağrısında:</span><span class="sxs-lookup"><span data-stu-id="6affb-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="6affb-375">`collection` koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="6affb-376">`TDocument` Koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="6affb-377">`GetCollection<TDocument>(collection)` koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="6affb-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="6affb-378">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="6affb-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="6affb-379">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="6affb-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="6affb-380">[Bul \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): koleksiyonda, belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="6affb-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="6affb-381">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="6affb-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="6affb-382">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6affb-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6affb-383">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-383">Add a controller</span></span>

<span data-ttu-id="6affb-384">`BooksController` *Controllers* dizinine aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="6affb-385">Önceki Web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="6affb-385">The preceding web API controller:</span></span>

* <span data-ttu-id="6affb-386">`BookService`CRUD işlemleri gerçekleştirmek için sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="6affb-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="6affb-387">HTTP isteklerini al, gönder, koy ve SIL desteği için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="6affb-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="6affb-388"><xref:System.Web.Http.ApiController.CreatedAtRoute*> `Create` [Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı döndürmek için eylem yöntemindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="6affb-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="6affb-389">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="6affb-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="6affb-390">`CreatedAtRoute` Ayrıca yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="6affb-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="6affb-391">`Location`Üst bilgi, yeni oluşturulan KITABıN URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6affb-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="6affb-392">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="6affb-392">Test the web API</span></span>

1. <span data-ttu-id="6affb-393">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6affb-393">Build and run the app.</span></span>

1. <span data-ttu-id="6affb-394">`http://localhost:<port>/api/books`Denetleyicinin parametresiz eylem yöntemini sınamak için öğesine gidin `Get` .</span><span class="sxs-lookup"><span data-stu-id="6affb-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="6affb-395">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-395">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="6affb-396">`http://localhost:<port>/api/books/{id here}`Denetleyicinin aşırı yüklenmiş eylem yöntemini sınamak için öğesine gidin `Get` .</span><span class="sxs-lookup"><span data-stu-id="6affb-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="6affb-397">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6affb-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="6affb-398">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6affb-398">Configure JSON serialization options</span></span>

<span data-ttu-id="6affb-399">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="6affb-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="6affb-400">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6affb-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="6affb-401">`bookName`Özelliği olarak döndürülmelidir `Name` .</span><span class="sxs-lookup"><span data-stu-id="6affb-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="6affb-402">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="6affb-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="6affb-403">' De `Startup.ConfigureServices` , aşağıdaki vurgulanmış kodu yöntem çağrısına zincirle `AddMvc` :</span><span class="sxs-lookup"><span data-stu-id="6affb-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="6affb-404">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="6affb-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="6affb-405">Örneğin, `Book` sınıfın `Author` özelliği olarak serileştirir `Author` .</span><span class="sxs-lookup"><span data-stu-id="6affb-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="6affb-406">*Modeller/Book. cs*' de, `BookName` aşağıdaki özniteliğiyle özelliğe not ekleyin [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="6affb-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="6affb-407">`[JsonProperty]`Özniteliğinin değeri, `Name` Web API 'SININ serileştirilmiş JSON yanıtında özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="6affb-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="6affb-408">Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:</span><span class="sxs-lookup"><span data-stu-id="6affb-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="6affb-409">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="6affb-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="6affb-410">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="6affb-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="6affb-411">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="6affb-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="6affb-412">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="6affb-412">Next steps</span></span>

<span data-ttu-id="6affb-413">ASP.NET Core Web API 'Leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="6affb-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="6affb-414">Bu makalenin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="6affb-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
