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
ms.openlocfilehash: 4d1c2d915c646dd1c8fcadd25bcd420a0a749dc9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774775"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="3f5dc-103">ASP.NET Core ve MongoDB ile Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="3f5dc-104">By [pratik Khandelwal](https://twitter.com/K2Prk) ve [Scott Ade](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3f5dc-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f5dc-105">Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3f5dc-106">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3f5dc-107">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-107">Configure MongoDB</span></span>
> * <span data-ttu-id="3f5dc-108">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="3f5dc-109">MongoDB koleksiyonu ve şeması tanımlama</span><span class="sxs-lookup"><span data-stu-id="3f5dc-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="3f5dc-110">Web API 'sinden MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="3f5dc-111">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-111">Customize JSON serialization</span></span>

<span data-ttu-id="3f5dc-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f5dc-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f5dc-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3f5dc-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f5dc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="3f5dc-115">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="3f5dc-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="3f5dc-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="3f5dc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3f5dc-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f5dc-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3f5dc-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f5dc-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="3f5dc-119">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="3f5dc-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3f5dc-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f5dc-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="3f5dc-121">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="3f5dc-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="3f5dc-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f5dc-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3f5dc-123">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3f5dc-124">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="3f5dc-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3f5dc-125">Mac için Visual Studio sürüm 7,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="3f5dc-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="3f5dc-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f5dc-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="3f5dc-127">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-127">Configure MongoDB</span></span>

<span data-ttu-id="3f5dc-128">Windows kullanıyorsanız MongoDB varsayılan olarak *C:\\Program Files\\MongoDB* konumunda yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3f5dc-129">Ortam değişkenine *C\\: program\\Files\\MongoDB\\\<Server Version_Number \\>bin* ekleyin. `Path`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3f5dc-130">Bu değişiklik geliştirme makinenizde her yerden MongoDB erişimine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3f5dc-131">Bir veritabanı oluşturmak, koleksiyonları yapmak ve belgeleri depolamak için aşağıdaki adımlarda Mongo kabuğunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3f5dc-132">Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="3f5dc-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3f5dc-133">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3f5dc-134">Örneğin, *C:\\Windows üzerinde booksdata* .</span><span class="sxs-lookup"><span data-stu-id="3f5dc-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3f5dc-135">Mevcut değilse dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3f5dc-136">Mongo kabuğu yeni dizinler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3f5dc-137">Bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-137">Open a command shell.</span></span> <span data-ttu-id="3f5dc-138">Varsayılan bağlantı noktası 27017 ' de MongoDB 'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3f5dc-139">Önceki adımda seçtiğiniz `<data_directory_path>` dizinle değiştirmeyi unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="3f5dc-140">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-140">Open another command shell instance.</span></span> <span data-ttu-id="3f5dc-141">Aşağıdaki komutu çalıştırarak Varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="3f5dc-142">Komut kabuğu 'nda aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="3f5dc-143">Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3f5dc-144">Veritabanı mevcutsa, bağlantısı işlemler için açılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3f5dc-145">Aşağıdaki komutu `Books` kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="3f5dc-146">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="3f5dc-147">`Books` Koleksiyon için bir şema tanımlayın ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="3f5dc-148">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="3f5dc-149">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3f5dc-150">Aşağıdaki komutu kullanarak veritabanında bulunan belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="3f5dc-151">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="3f5dc-152">Şema, her belge için `_id` türünde `ObjectId` bir otomatik olarak oluşturulan özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3f5dc-153">Veritabanı hazırlanıyor.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-153">The database is ready.</span></span> <span data-ttu-id="3f5dc-154">ASP.NET Core Web API 'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3f5dc-155">ASP.NET Core Web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f5dc-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3f5dc-157">**Dosya** > **New** yeni > **Proje**' ye gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3f5dc-158">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3f5dc-159">Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3f5dc-160">**.NET Core** hedef çerçevesini ve **3,0 ASP.NET Core**seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="3f5dc-161">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3f5dc-162">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="3f5dc-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3f5dc-163">**Paket Yöneticisi konsol** penceresinde, proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3f5dc-164">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3f5dc-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f5dc-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3f5dc-166">Komut kabuğu 'nda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="3f5dc-167">Yeni bir ASP.NET Core Web API projesi hedefleme .NET Core Visual Studio Code oluşturulur ve açılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="3f5dc-168">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?**.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="3f5dc-169">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-169">Select **Yes**.</span></span>
1. <span data-ttu-id="3f5dc-170">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="3f5dc-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3f5dc-171">**Tümleşik Terminal** ' i açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="3f5dc-172">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3f5dc-173">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3f5dc-174">**Dosya** > **yeni çözüm** > **.NET Core** > **uygulaması**sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="3f5dc-175">**ASP.NET Core Web API** C# proje şablonunu seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="3f5dc-176">**Hedef çerçeve** açılır listesinden **.NET Core 3,0** ' i seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="3f5dc-177">**Proje adı**Için *booksapı* girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="3f5dc-178">**Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="3f5dc-179">Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="3f5dc-180">**Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="3f5dc-181">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-181">Add an entity model</span></span>

1. <span data-ttu-id="3f5dc-182">Proje köküne *modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3f5dc-183">Modeller dizinine `Book` aşağıdaki kodla bir *Models* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="3f5dc-184">Önceki sınıfta, `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="3f5dc-185">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="3f5dc-186">, Bu özelliği [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) belgenin birincil anahtarı olarak belirlemek için ile birlikte açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="3f5dc-187">, Parametresinin ObjectID yapısı yerine tür [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) `string` olarak geçirilmesine izin vermek için ile birlikte [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3f5dc-188">Mongo, ' den `string` ' ye `ObjectId`dönüştürmeyi işler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="3f5dc-189">`BookName` Özelliği [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3f5dc-190">Özniteliğinin değeri, MongoDB koleksiyonundaki özellik adını `Name` temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3f5dc-191">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-191">Add a configuration model</span></span>

1. <span data-ttu-id="3f5dc-192">Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="3f5dc-193">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="3f5dc-194">Yukarıdaki `BookstoreDatabaseSettings` sınıf, *appSettings. JSON* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3f5dc-195">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3f5dc-196">Aşağıdaki Vurgulanan kodu öğesine `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="3f5dc-197">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-197">In the preceding code:</span></span>

   * <span data-ttu-id="3f5dc-198">*AppSettings. JSON* dosyasının `BookstoreDatabaseSettings` bölüm bağlandığı yapılandırma örneği, bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3f5dc-199">Örneğin `BookstoreDatabaseSettings` , bir `ConnectionString` nesnenin özelliği `BookstoreDatabaseSettings:ConnectionString` *appSettings. JSON*içindeki özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="3f5dc-200">Arabirim `IBookstoreDatabaseSettings` , tek bir [HIZMET ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3f5dc-201">Eklenen arabirim örneği bir `BookstoreDatabaseSettings` nesne olarak çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3f5dc-202">Ve başvurularını çözümlemek için aşağıdaki kodu Startup.cs 'in en üstüne ekleyin: *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3f5dc-203">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3f5dc-204">Proje köküne bir *Hizmetler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3f5dc-205">Hizmetler dizinine `BookService` aşağıdaki kodla bir *Services* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="3f5dc-206">Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3f5dc-207">Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3f5dc-208">Aşağıdaki Vurgulanan kodu öğesine `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="3f5dc-209">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek için dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3f5dc-210">Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir bağımlılık alır. `MongoClient`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3f5dc-211">Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir hizmet ömrü ile dı 'ye kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3f5dc-212">Başvuruyu çözümlemek için aşağıdaki kodu Startup.cs 'in en üstüne ekleyin: *Startup.cs* `BookService`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="3f5dc-213">`BookService` Sınıfı, veritabanına karşı CRUD işlemlerini gerçekleştirmek için aşağıdaki `MongoDB.Driver` üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3f5dc-214">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; , veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3f5dc-215">Bu sınıfın oluşturucusuna MongoDB bağlantı dizesi verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="3f5dc-216">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; , işlemleri gerçekleştirmek için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3f5dc-217">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection\<tdocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3f5dc-218">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3f5dc-219">`GetCollection<TDocument>(collection)` Yöntem çağrısında:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="3f5dc-220">`collection`koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3f5dc-221">`TDocument`Koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3f5dc-222">`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3f5dc-223">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3f5dc-224">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; , belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3f5dc-225">[Tdocument>\<](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; bul, koleksiyonda belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3f5dc-226">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; , belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3f5dc-227">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; , belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3f5dc-228">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-228">Add a controller</span></span>

<span data-ttu-id="3f5dc-229">Controllers dizinine `BooksController` aşağıdaki kodla bir *Controllers* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="3f5dc-230">Önceki Web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-230">The preceding web API controller:</span></span>

* <span data-ttu-id="3f5dc-231">CRUD `BookService` işlemleri gerçekleştirmek için sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3f5dc-232">HTTP isteklerini al, gönder, koy ve SIL desteği için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3f5dc-233"><xref:System.Web.Http.ApiController.CreatedAtRoute*> [Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı `Create` döndürmek için eylem yöntemindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3f5dc-234">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3f5dc-235">`CreatedAtRoute`Ayrıca yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3f5dc-236">`Location` Üst bilgi, yeni oluşturulan kitabın URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3f5dc-237">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-237">Test the web API</span></span>

1. <span data-ttu-id="3f5dc-238">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-238">Build and run the app.</span></span>

1. <span data-ttu-id="3f5dc-239">Denetleyicinin parametresiz `http://localhost:<port>/api/books` `Get` eylem yöntemini sınamak için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3f5dc-240">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="3f5dc-241">Denetleyicinin aşırı `http://localhost:<port>/api/books/{id here}` yüklenmiş `Get` eylem yöntemini sınamak için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3f5dc-242">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3f5dc-243">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-243">Configure JSON serialization options</span></span>

<span data-ttu-id="3f5dc-244">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3f5dc-245">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3f5dc-246">`bookName` Özelliği olarak `Name`döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3f5dc-247">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3f5dc-248">JSON.NET, ASP.NET paylaşılan çerçevesinden kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="3f5dc-249">[Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="3f5dc-250">' `Startup.ConfigureServices`De, aşağıdaki vurgulanmış kodu `AddControllers` yöntem çağrısına zincirle:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="3f5dc-251">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3f5dc-252">Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileştirir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3f5dc-253">*Modeller/Book. cs*' de, aşağıdaki `BookName` [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) özniteliğiyle özelliğe not ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="3f5dc-254">`[JsonProperty]` Özniteliğinin değeri, Web API `Name` 'sinin serileştirilmiş JSON yanıtında özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3f5dc-255">Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="3f5dc-256">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3f5dc-257">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f5dc-258">Bu öğretici, bir [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerini gerçekleştiren BIR Web API 'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3f5dc-259">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3f5dc-260">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-260">Configure MongoDB</span></span>
> * <span data-ttu-id="3f5dc-261">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="3f5dc-262">MongoDB koleksiyonu ve şeması tanımlama</span><span class="sxs-lookup"><span data-stu-id="3f5dc-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="3f5dc-263">Web API 'sinden MongoDB CRUD işlemleri gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="3f5dc-264">JSON serileştirmesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-264">Customize JSON serialization</span></span>

<span data-ttu-id="3f5dc-265">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f5dc-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f5dc-266">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3f5dc-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f5dc-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="3f5dc-268">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="3f5dc-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="3f5dc-269">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="3f5dc-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3f5dc-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f5dc-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3f5dc-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f5dc-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="3f5dc-272">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="3f5dc-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3f5dc-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f5dc-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="3f5dc-274">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="3f5dc-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="3f5dc-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f5dc-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3f5dc-276">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3f5dc-277">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="3f5dc-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3f5dc-278">Mac için Visual Studio sürüm 7,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="3f5dc-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="3f5dc-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3f5dc-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="3f5dc-280">MongoDB 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-280">Configure MongoDB</span></span>

<span data-ttu-id="3f5dc-281">Windows kullanıyorsanız MongoDB varsayılan olarak *C:\\Program Files\\MongoDB* konumunda yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3f5dc-282">Ortam değişkenine *C\\: program\\Files\\MongoDB\\\<Server Version_Number \\>bin* ekleyin. `Path`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3f5dc-283">Bu değişiklik geliştirme makinenizde her yerden MongoDB erişimine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3f5dc-284">Bir veritabanı oluşturmak, koleksiyonları yapmak ve belgeleri depolamak için aşağıdaki adımlarda Mongo kabuğunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3f5dc-285">Mongo kabuğu komutları hakkında daha fazla bilgi için bkz. [Mongo kabuğu Ile çalışma](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="3f5dc-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3f5dc-286">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3f5dc-287">Örneğin, *C:\\Windows üzerinde booksdata* .</span><span class="sxs-lookup"><span data-stu-id="3f5dc-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3f5dc-288">Mevcut değilse dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3f5dc-289">Mongo kabuğu yeni dizinler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3f5dc-290">Bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-290">Open a command shell.</span></span> <span data-ttu-id="3f5dc-291">Varsayılan bağlantı noktası 27017 ' de MongoDB 'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3f5dc-292">Önceki adımda seçtiğiniz `<data_directory_path>` dizinle değiştirmeyi unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="3f5dc-293">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-293">Open another command shell instance.</span></span> <span data-ttu-id="3f5dc-294">Aşağıdaki komutu çalıştırarak Varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="3f5dc-295">Komut kabuğu 'nda aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="3f5dc-296">Zaten mevcut değilse, *BookstoreDb* adlı bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3f5dc-297">Veritabanı mevcutsa, bağlantısı işlemler için açılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3f5dc-298">Aşağıdaki komutu `Books` kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="3f5dc-299">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="3f5dc-300">`Books` Koleksiyon için bir şema tanımlayın ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="3f5dc-301">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="3f5dc-302">Bu makalede gösterilen KIMLIK, bu örneği çalıştırdığınızda kimliklerle eşleşmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3f5dc-303">Aşağıdaki komutu kullanarak veritabanında bulunan belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="3f5dc-304">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="3f5dc-305">Şema, her belge için `_id` türünde `ObjectId` bir otomatik olarak oluşturulan özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3f5dc-306">Veritabanı hazırlanıyor.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-306">The database is ready.</span></span> <span data-ttu-id="3f5dc-307">ASP.NET Core Web API 'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3f5dc-308">ASP.NET Core Web API projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f5dc-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3f5dc-310">**Dosya** > **New** yeni > **Proje**' ye gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3f5dc-311">**ASP.NET Core Web uygulaması** proje türünü seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3f5dc-312">Projeyi *Booksapı*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3f5dc-313">**.NET Core** hedef çerçevesini ve **2,2 ASP.NET Core**seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="3f5dc-314">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3f5dc-315">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="3f5dc-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3f5dc-316">**Paket Yöneticisi konsol** penceresinde, proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3f5dc-317">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3f5dc-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3f5dc-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3f5dc-319">Komut kabuğu 'nda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="3f5dc-320">Yeni bir ASP.NET Core Web API projesi hedefleme .NET Core Visual Studio Code oluşturulur ve açılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="3f5dc-321">Durum çubuğunun omnisharp Yangın simgesi yeşil ' i etkinleştirdikten sonra, **gerekli varlıkların derleme ve hata ayıklama için ' booksapı ' içinde eksik bir iletişim kutusu yok. Bunları ekleyin mi?**.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="3f5dc-322">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-322">Select **Yes**.</span></span>
1. <span data-ttu-id="3f5dc-323">MongoDB için .NET sürücüsünün en son kararlı sürümünü öğrenmek üzere [NuGet galerisini ziyaret edin: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) .</span><span class="sxs-lookup"><span data-stu-id="3f5dc-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3f5dc-324">**Tümleşik Terminal** ' i açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="3f5dc-325">MongoDB için .NET sürücüsünü yüklemek üzere aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3f5dc-326">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f5dc-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3f5dc-327">**Dosya** > **yeni çözüm** > **.NET Core** > **uygulaması**sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="3f5dc-328">**ASP.NET Core Web API** C# proje şablonunu seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="3f5dc-329">**Hedef çerçeve** açılır listesinden **.NET Core 2,2** ' i seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="3f5dc-330">**Proje adı**Için *booksapı* girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="3f5dc-331">**Çözüm** panelinde, projenin **Bağımlılıklar** düğümünü sağ tıklatın ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="3f5dc-332">Arama kutusuna *MongoDB. Driver* girin, *MongoDB. Driver* paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="3f5dc-333">**Lisans kabulü** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="3f5dc-334">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-334">Add an entity model</span></span>

1. <span data-ttu-id="3f5dc-335">Proje köküne *modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3f5dc-336">Modeller dizinine `Book` aşağıdaki kodla bir *Models* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="3f5dc-337">Önceki sınıfta, `Id` özelliği:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="3f5dc-338">Ortak dil çalışma zamanı (CLR) nesnesini MongoDB koleksiyonuna eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="3f5dc-339">, Bu özelliği [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) belgenin birincil anahtarı olarak belirlemek için ile birlikte açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="3f5dc-340">, Parametresinin ObjectID yapısı yerine tür [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) `string` olarak geçirilmesine izin vermek için ile birlikte [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) açıklanmış.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3f5dc-341">Mongo, ' den `string` ' ye `ObjectId`dönüştürmeyi işler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="3f5dc-342">`BookName` Özelliği [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3f5dc-343">Özniteliğinin değeri, MongoDB koleksiyonundaki özellik adını `Name` temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3f5dc-344">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-344">Add a configuration model</span></span>

1. <span data-ttu-id="3f5dc-345">Aşağıdaki veritabanı yapılandırma değerlerini *appSettings. JSON*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="3f5dc-346">*Modeller* dizinine aşağıdaki kodla bir *BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="3f5dc-347">Yukarıdaki `BookstoreDatabaseSettings` sınıf, *appSettings. JSON* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3f5dc-348">JSON ve C# Özellik adları, eşleme sürecini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3f5dc-349">Aşağıdaki Vurgulanan kodu öğesine `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="3f5dc-350">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-350">In the preceding code:</span></span>

   * <span data-ttu-id="3f5dc-351">*AppSettings. JSON* dosyasının `BookstoreDatabaseSettings` bölüm bağlandığı yapılandırma örneği, bağımlılık ekleme (dı) kapsayıcısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3f5dc-352">Örneğin `BookstoreDatabaseSettings` , bir `ConnectionString` nesnenin özelliği `BookstoreDatabaseSettings:ConnectionString` *appSettings. JSON*içindeki özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="3f5dc-353">Arabirim `IBookstoreDatabaseSettings` , tek bir [HIZMET ömrü](xref:fundamentals/dependency-injection#service-lifetimes)ile dı 'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3f5dc-354">Eklenen arabirim örneği bir `BookstoreDatabaseSettings` nesne olarak çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3f5dc-355">Ve başvurularını çözümlemek için aşağıdaki kodu Startup.cs 'in en üstüne ekleyin: *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3f5dc-356">CRUD işlemleri hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3f5dc-357">Proje köküne bir *Hizmetler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3f5dc-358">Hizmetler dizinine `BookService` aşağıdaki kodla bir *Services* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="3f5dc-359">Yukarıdaki kodda, bir `IBookstoreDatabaseSettings` örnek oluşturucu ekleme yoluyla dı 'den alınır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3f5dc-360">Bu teknik, [yapılandırma modeli ekleme](#add-a-configuration-model) bölümüne eklenen *appSettings. JSON* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3f5dc-361">Aşağıdaki Vurgulanan kodu öğesine `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="3f5dc-362">Önceki kodda, `BookService` sınıfı, tüketen sınıflarda Oluşturucu ekleme işlemini desteklemek için dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3f5dc-363">Tek hizmet ömrü en uygundur çünkü `BookService` doğrudan bir bağımlılık alır. `MongoClient`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3f5dc-364">Resmi [Mongo istemci yeniden kullanım yönergelerine](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)göre, `MongoClient` tek bir hizmet ömrü ile dı 'ye kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3f5dc-365">Başvuruyu çözümlemek için aşağıdaki kodu Startup.cs 'in en üstüne ekleyin: *Startup.cs* `BookService`</span><span class="sxs-lookup"><span data-stu-id="3f5dc-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="3f5dc-366">`BookService` Sınıfı, veritabanına karşı CRUD işlemlerini gerçekleştirmek için aşağıdaki `MongoDB.Driver` üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3f5dc-367">[Mongoclient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; , veritabanı işlemlerini gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3f5dc-368">Bu sınıfın oluşturucusuna MongoDB bağlantı dizesi verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="3f5dc-369">[Imongodatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; , işlemleri gerçekleştirmek için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3f5dc-370">Bu öğretici, belirli bir koleksiyondaki verilere erişim kazanmak için arabirimdeki genel [GetCollection\<tdocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3f5dc-371">Bu yöntem çağrıldıktan sonra, koleksiyonda CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3f5dc-372">`GetCollection<TDocument>(collection)` Yöntem çağrısında:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="3f5dc-373">`collection`koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3f5dc-374">`TDocument`Koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3f5dc-375">`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [Mongocollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3f5dc-376">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3f5dc-377">[Deleteone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; , belirtilen arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3f5dc-378">[Tdocument>\<](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; bul, koleksiyonda belirtilen arama ölçütleriyle eşleşen tüm belgeleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3f5dc-379">[Insertone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; , belirtilen nesneyi, koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3f5dc-380">[Replaceone](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; , belirtilen arama ölçütleriyle eşleşen tek belgeyi, belirtilen nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3f5dc-381">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-381">Add a controller</span></span>

<span data-ttu-id="3f5dc-382">Controllers dizinine `BooksController` aşağıdaki kodla bir *Controllers* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="3f5dc-383">Önceki Web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-383">The preceding web API controller:</span></span>

* <span data-ttu-id="3f5dc-384">CRUD `BookService` işlemleri gerçekleştirmek için sınıfını kullanır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3f5dc-385">HTTP isteklerini al, gönder, koy ve SIL desteği için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3f5dc-386"><xref:System.Web.Http.ApiController.CreatedAtRoute*> [Http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtı `Create` döndürmek için eylem yöntemindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3f5dc-387">Durum kodu 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3f5dc-388">`CreatedAtRoute`Ayrıca yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3f5dc-389">`Location` Üst bilgi, yeni oluşturulan kitabın URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3f5dc-390">Web API 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-390">Test the web API</span></span>

1. <span data-ttu-id="3f5dc-391">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-391">Build and run the app.</span></span>

1. <span data-ttu-id="3f5dc-392">Denetleyicinin parametresiz `http://localhost:<port>/api/books` `Get` eylem yöntemini sınamak için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3f5dc-393">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="3f5dc-394">Denetleyicinin aşırı `http://localhost:<port>/api/books/{id here}` yüklenmiş `Get` eylem yöntemini sınamak için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3f5dc-395">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3f5dc-396">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3f5dc-396">Configure JSON serialization options</span></span>

<span data-ttu-id="3f5dc-397">[Web API 'Sini test](#test-the-web-api) etme bölümünde döndürülen JSON yanıtları hakkında iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3f5dc-398">' Varsayılan ortası büyük/küçük harf özelliği, CLR nesnesinin özellik adlarının Pascal büyük küçük harfleriyle eşleşecek şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3f5dc-399">`bookName` Özelliği olarak `Name`döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3f5dc-400">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3f5dc-401">' `Startup.ConfigureServices`De, aşağıdaki vurgulanmış kodu `AddMvc` yöntem çağrısına zincirle:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="3f5dc-402">Önceki değişiklik ile, Web API 'sinin seri hale getirilmiş JSON yanıtındaki Özellik adları CLR nesne türündeki ilgili özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3f5dc-403">Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileştirir.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3f5dc-404">*Modeller/Book. cs*' de, aşağıdaki `BookName` [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) özniteliğiyle özelliğe not ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="3f5dc-405">`[JsonProperty]` Özniteliğinin değeri, Web API `Name` 'sinin serileştirilmiş JSON yanıtında özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3f5dc-406">Aşağıdaki kodu *modeller/Book. cs* ' nin üst kısmına ekleyerek `[JsonProperty]` öznitelik başvurusunu çözümleyin:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="3f5dc-407">[Web API 'Sini test](#test-the-web-api) etme bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3f5dc-408">JSON Özellik adlarındaki farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="3f5dc-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="3f5dc-409">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="3f5dc-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="3f5dc-410">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="3f5dc-410">Next steps</span></span>

<span data-ttu-id="3f5dc-411">ASP.NET Core Web API 'Leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="3f5dc-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="3f5dc-412">Bu makalenin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="3f5dc-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
