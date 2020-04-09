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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="1409d-103">ASP.NET Core ve MongoDB ile bir web API oluşturma</span><span class="sxs-lookup"><span data-stu-id="1409d-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="1409d-104">Yazar: [Pratik Khandelwal](https://twitter.com/K2Prk) ve [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1409d-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1409d-105">Bu öğretici, [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini gerçekleştiren bir web API'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1409d-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="1409d-106">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="1409d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1409d-107">MongoDB'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="1409d-107">Configure MongoDB</span></span>
> * <span data-ttu-id="1409d-108">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="1409d-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="1409d-109">Bir MongoDB koleksiyonu ve şeması tanımlayın</span><span class="sxs-lookup"><span data-stu-id="1409d-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="1409d-110">Bir web API'den MongoDB CRUD işlemlerini gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="1409d-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="1409d-111">JSON serileştirmeyi özelleştir</span><span class="sxs-lookup"><span data-stu-id="1409d-111">Customize JSON serialization</span></span>

<span data-ttu-id="1409d-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1409d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1409d-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1409d-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1409d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="1409d-115">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="1409d-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="1409d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile</span><span class="sxs-lookup"><span data-stu-id="1409d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="1409d-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1409d-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1409d-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1409d-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="1409d-119">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="1409d-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1409d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1409d-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="1409d-121">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="1409d-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="1409d-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1409d-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1409d-123">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1409d-124">.NET Core SDK 3.0 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="1409d-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1409d-125">Mac sürüm 7.7 veya sonrası için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="1409d-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1409d-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="1409d-127">MongoDB'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="1409d-127">Configure MongoDB</span></span>

<span data-ttu-id="1409d-128">Windows kullanıyorsanız, MongoDB varsayılan olarak *C:\\Program Files\\MongoDB'ye* yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1409d-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="1409d-129">*C ekle:\\\\Program\\Dosyaları MongoDB Server\\\<version_number>\\bin* çevre değişkenine. `Path`</span><span class="sxs-lookup"><span data-stu-id="1409d-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="1409d-130">Bu değişiklik, geliştirme makinenizin herhangi bir yerinden MongoDB erişimine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1409d-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="1409d-131">Bir veritabanı oluşturmak, koleksiyonlar oluşturmak ve belgeleri depolamak için aşağıdaki adımlardaki mongo Shell'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="1409d-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="1409d-132">Mongo Shell komutları hakkında daha fazla bilgi için [mongo Shell ile çalışma bilgisine](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)bakın.</span><span class="sxs-lookup"><span data-stu-id="1409d-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="1409d-133">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="1409d-134">Örneğin, *C:\\* Windows'da Kitaplar Verileri.</span><span class="sxs-lookup"><span data-stu-id="1409d-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="1409d-135">Yoksa dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1409d-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="1409d-136">Mongo Shell yeni dizinler yaratmaz.</span><span class="sxs-lookup"><span data-stu-id="1409d-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="1409d-137">Komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="1409d-137">Open a command shell.</span></span> <span data-ttu-id="1409d-138">Varsayılan bağlantı noktası 27017'de MongoDB'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1409d-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="1409d-139">Önceki adımda seçtiğiniz dizini değiştirmeyi `<data_directory_path>` unutmayın.</span><span class="sxs-lookup"><span data-stu-id="1409d-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="1409d-140">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="1409d-140">Open another command shell instance.</span></span> <span data-ttu-id="1409d-141">Aşağıdaki komutu çalıştırarak varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="1409d-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="1409d-142">Aşağıdakileri bir komut kabuğunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="1409d-143">Zaten yoksa, *BookstoreDb* adında bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1409d-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="1409d-144">Veritabanı varsa, bağlantısı hareketler için açılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="1409d-145">Aşağıdaki `Books` komutu kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1409d-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="1409d-146">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="1409d-147">Koleksiyon için bir şema tanımlayın `Books` ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="1409d-148">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="1409d-149">Bu makalede gösterilen kimlik, bu örneği çalıştırdığınızda kimliklerle eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="1409d-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="1409d-150">Aşağıdaki komutu kullanarak veritabanındaki belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="1409d-151">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="1409d-152">Şema, her belge `_id` için otomatik `ObjectId` olarak oluşturulmuş bir tür özelliği ekler.</span><span class="sxs-lookup"><span data-stu-id="1409d-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="1409d-153">Veritabanı hazır.</span><span class="sxs-lookup"><span data-stu-id="1409d-153">The database is ready.</span></span> <span data-ttu-id="1409d-154">ASP.NET Core web API'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1409d-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="1409d-155">ASP.NET Core web API projesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="1409d-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1409d-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1409d-157">**Dosya** > **Yeni** > **Proje**git.</span><span class="sxs-lookup"><span data-stu-id="1409d-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="1409d-158">ASP.NET **Çekirdek Web Uygulaması** proje türünü seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="1409d-159">Proje *BooksApi*adını ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="1409d-160">**.NET Core** hedef çerçevesini seçin ve **Core 3.0'ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="1409d-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="1409d-161">**API** proje şablonu seçin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="1409d-162">MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="1409d-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1409d-163">Paket **Yöneticisi Konsolu** penceresinde proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="1409d-164">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1409d-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1409d-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1409d-166">Komut kabuğunda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="1409d-167">.NET Core'u hedefleyen yeni bir ASP.NET Core web API projesi Visual Studio Code'da oluşturulur ve açılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="1409d-168">Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve hata ayıklama 'BooksApi'den eksiktir. Onları da eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="1409d-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="1409d-169">**Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-169">Select **Yes**.</span></span>
1. <span data-ttu-id="1409d-170">MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="1409d-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1409d-171">**Tümleşik Terminali** açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="1409d-172">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1409d-173">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1409d-174">**DosyaYa** > Git **Yeni Çözüm** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="1409d-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="1409d-175">ASP.NET **Core Web API** C# proje şablonu seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="1409d-176">**Hedef Çerçeve** açılır listesinden **.NET Core 3.0'ı** seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="1409d-177">**Proje Adı**için *BookApi'yi* girin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="1409d-178">**Çözüm** defterinde, projenin **Bağımlılıklar** düğümüne sağ tıklayın ve **Paket Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="1409d-179">Arama *kutusuna MongoDB.Driver* girin, *MongoDB.Driver* paketini seçin ve **Paket Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="1409d-180">**Lisans Kabul** iletişim **kutusundakabul** et düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="1409d-181">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-181">Add an entity model</span></span>

1. <span data-ttu-id="1409d-182">Proje köküne bir *Modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="1409d-183">Aşağıdaki `Book` kodla *Modeller* dizinine bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="1409d-184">Önceki sınıfta, `Id` özellik:</span><span class="sxs-lookup"><span data-stu-id="1409d-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="1409d-185">Ortak Dil Çalışma Zamanı (CLR) nesnesini MongoDB koleksiyonuyla eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1409d-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="1409d-186">Bu özelliği belgenin [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) birincil anahtarı olarak belirlemek üzere açıklama yapılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="1409d-187">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) parametrenin türü `string` olarak geçirilmesine izin vermek için açıklama yapılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="1409d-188">Mongo dönüşüm `string` `ObjectId`yönetir.</span><span class="sxs-lookup"><span data-stu-id="1409d-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="1409d-189">Özellik `BookName` öznitelik ile [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) açıklamalı.</span><span class="sxs-lookup"><span data-stu-id="1409d-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="1409d-190">Özniteliğin `Name` değeri MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="1409d-191">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-191">Add a configuration model</span></span>

1. <span data-ttu-id="1409d-192">*Appsettings.json'a*aşağıdaki veritabanı yapılandırma değerlerini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="1409d-193">*Modeller* dizinine aşağıdaki kodla *bir BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="1409d-194">Önceki `BookstoreDatabaseSettings` sınıf *appsettings.json* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="1409d-195">JSON ve C# özellik adları eşleme işlemini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="1409d-196">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="1409d-197">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="1409d-197">In the preceding code:</span></span>

   * <span data-ttu-id="1409d-198">*Appsettings.json* dosyasının `BookstoreDatabaseSettings` bölümünün bağladığı yapılandırma örneği Bağımlılık Enjeksiyonu (DI) kapsayıcısında kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1409d-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="1409d-199">Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appsettings.json*özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="1409d-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="1409d-200">Arabirim, `IBookstoreDatabaseSettings` singleton [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)yle DI'de kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="1409d-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1409d-201">Enjekte edildiğinde, arabirim örneği bir `BookstoreDatabaseSettings` nesneye giderilir.</span><span class="sxs-lookup"><span data-stu-id="1409d-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="1409d-202">Başvuruları ve `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` başvuruları çözmek için aşağıdaki kodu *Startup.cs* üst bölümüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="1409d-203">CRUD operasyon hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="1409d-204">Proje köküne *bir Hizmet* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="1409d-205">Hizmetler `BookService` dizinine *Services* aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="1409d-206">Önceki kodda, bir `IBookstoreDatabaseSettings` örnek yapı oluşturucu enjeksiyon yoluyla DI alınır.</span><span class="sxs-lookup"><span data-stu-id="1409d-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="1409d-207">Bu teknik, [yapılandırma modeli ekle](#add-a-configuration-model) bölümüne eklenen *appsettings.json* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="1409d-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="1409d-208">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="1409d-209">Önceki kodda, `BookService` sınıf tüketen sınıflarda yapıcı enjeksiyonu desteklemek için DI'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1409d-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="1409d-210">Singleton hizmet ömrü en `BookService` uygundur, çünkü `MongoClient`doğrudan bir bağımlılık alır.</span><span class="sxs-lookup"><span data-stu-id="1409d-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="1409d-211">Resmi [Mongo Müşteri yeniden](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)yönergeleri `MongoClient` başına, di bir singleton hizmet ömrü ile kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1409d-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="1409d-212">Başvuruyu çözmek için Startup.cs üst bölümüne aşağıdaki kodu ekleyin: *Startup.cs* `BookService`</span><span class="sxs-lookup"><span data-stu-id="1409d-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="1409d-213">Sınıf, `BookService` veritabanına `MongoDB.Driver` karşı CRUD işlemleri gerçekleştirmek için aşağıdaki üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="1409d-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="1409d-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; veritabanı işlemleri gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="1409d-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="1409d-215">Bu sınıfın oluşturucusu MongoDB bağlantı dizesi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="1409d-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="1409d-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Işlemleri gerçekleştirmek için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="1409d-217">Bu öğretici, belirli bir koleksiyondaki verilere erişmek için arabirimdeki genel [GetCollection\<TDocument>(toplama)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="1409d-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="1409d-218">Bu yöntem çağrıldıktan sonra koleksiyona karşı CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="1409d-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="1409d-219">Yöntem `GetCollection<TDocument>(collection)` aramasında:</span><span class="sxs-lookup"><span data-stu-id="1409d-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="1409d-220">`collection`koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="1409d-221">`TDocument`koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="1409d-222">`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="1409d-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="1409d-223">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="1409d-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="1409d-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Sağlanan arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="1409d-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="1409d-225">[TBelgesi'ni\<bul>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Koleksiyondaki tüm belgeleri sağlanan arama ölçütleriyle eşleşen olarak döndürür.</span><span class="sxs-lookup"><span data-stu-id="1409d-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="1409d-226">[InsertOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; sağlanan nesneyi koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="1409d-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="1409d-227">[ReplaceOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; sağlanan arama ölçütleriyle eşleşen tek belgeyi sağlanan nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="1409d-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1409d-228">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-228">Add a controller</span></span>

<span data-ttu-id="1409d-229">Denetleyiciler `BooksController` dizinine *Controllers* aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="1409d-230">Önceki web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="1409d-230">The preceding web API controller:</span></span>

* <span data-ttu-id="1409d-231">CRUD `BookService` işlemlerini gerçekleştirmek için sınıfı kullanır.</span><span class="sxs-lookup"><span data-stu-id="1409d-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="1409d-232">GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1409d-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="1409d-233">Http <xref:System.Web.Http.ApiController.CreatedAtRoute*> [201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtını döndürmek için eylem yöntemini `Create` çağırır.</span><span class="sxs-lookup"><span data-stu-id="1409d-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="1409d-234">Durum kodu 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.</span><span class="sxs-lookup"><span data-stu-id="1409d-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="1409d-235">`CreatedAtRoute`ayrıca yanıta bir `Location` üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="1409d-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="1409d-236">Üstbilgi, `Location` yeni oluşturulan kitabın URI'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1409d-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="1409d-237">Web API'sini test edin</span><span class="sxs-lookup"><span data-stu-id="1409d-237">Test the web API</span></span>

1. <span data-ttu-id="1409d-238">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1409d-238">Build and run the app.</span></span>

1. <span data-ttu-id="1409d-239">Denetleyicinin `http://localhost:<port>/api/books` parametresiz `Get` eylem yöntemini sınamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="1409d-240">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="1409d-241">Denetleyicinin `http://localhost:<port>/api/books/{id here}` aşırı yüklenmiş `Get` eylem yöntemini sınamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="1409d-242">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="1409d-243">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1409d-243">Configure JSON serialization options</span></span>

<span data-ttu-id="1409d-244">[Test web API](#test-the-web-api) bölümünde döndürülen JSON yanıtları hakkında değiştirmek için iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="1409d-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="1409d-245">Özellik adlarının varsayılan deve gövdesi, CLR nesnesinin özellik adlarının Pascal kasasına uyacak şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1409d-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="1409d-246">Özellik `bookName` olarak `Name`döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="1409d-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="1409d-247">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="1409d-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="1409d-248">JSON.NET paylaşılan ASP.NET çerçeveden kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="1409d-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="1409d-249">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)bir paket başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="1409d-250">In `Startup.ConfigureServices`, `AddControllers` yöntem çağrısı üzerine aşağıdaki vurgulanan kodu zincir:</span><span class="sxs-lookup"><span data-stu-id="1409d-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="1409d-251">Önceki değişiklikle, web API'nin serileştirilmiş JSON yanıtındaki özellik adları CLR nesne türünde karşılık gelen özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1409d-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="1409d-252">Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileşir.</span><span class="sxs-lookup"><span data-stu-id="1409d-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="1409d-253">*Modeller/Book.cs'* de, `BookName` özelliği aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) öznitelikle açıklama olarak belirtin:</span><span class="sxs-lookup"><span data-stu-id="1409d-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="1409d-254">Özniteliğin `[JsonProperty]` `Name` değeri, web API'nin serileştirilmiş JSON yanıtındaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="1409d-255">Öznitelik başvurularını çözmek `[JsonProperty]` için *Modeller/Book.cs'nin* üst bölümüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="1409d-256">[Test web API](#test-the-web-api) bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="1409d-257">JSON özellik adlarında farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="1409d-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1409d-258">Bu öğretici, [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL veritabanında Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini gerçekleştiren bir web API'si oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1409d-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="1409d-259">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="1409d-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1409d-260">MongoDB'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="1409d-260">Configure MongoDB</span></span>
> * <span data-ttu-id="1409d-261">MongoDB veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="1409d-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="1409d-262">Bir MongoDB koleksiyonu ve şeması tanımlayın</span><span class="sxs-lookup"><span data-stu-id="1409d-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="1409d-263">Bir web API'den MongoDB CRUD işlemlerini gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="1409d-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="1409d-264">JSON serileştirmeyi özelleştir</span><span class="sxs-lookup"><span data-stu-id="1409d-264">Customize JSON serialization</span></span>

<span data-ttu-id="1409d-265">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1409d-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1409d-266">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1409d-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1409d-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="1409d-268">.NET Çekirdek SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="1409d-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="1409d-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile</span><span class="sxs-lookup"><span data-stu-id="1409d-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="1409d-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1409d-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1409d-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1409d-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="1409d-272">.NET Çekirdek SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="1409d-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1409d-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1409d-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="1409d-274">Visual Studio Code için C#</span><span class="sxs-lookup"><span data-stu-id="1409d-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="1409d-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1409d-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1409d-276">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1409d-277">.NET Çekirdek SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="1409d-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="1409d-278">Mac sürüm 7.7 veya sonrası için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="1409d-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1409d-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="1409d-280">MongoDB'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="1409d-280">Configure MongoDB</span></span>

<span data-ttu-id="1409d-281">Windows kullanıyorsanız, MongoDB varsayılan olarak *C:\\Program Files\\MongoDB'ye* yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1409d-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="1409d-282">*C ekle:\\\\Program\\Dosyaları MongoDB Server\\\<version_number>\\bin* çevre değişkenine. `Path`</span><span class="sxs-lookup"><span data-stu-id="1409d-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="1409d-283">Bu değişiklik, geliştirme makinenizin herhangi bir yerinden MongoDB erişimine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1409d-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="1409d-284">Bir veritabanı oluşturmak, koleksiyonlar oluşturmak ve belgeleri depolamak için aşağıdaki adımlardaki mongo Shell'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="1409d-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="1409d-285">Mongo Shell komutları hakkında daha fazla bilgi için [mongo Shell ile çalışma bilgisine](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)bakın.</span><span class="sxs-lookup"><span data-stu-id="1409d-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="1409d-286">Verileri depolamak için geliştirme makinenizde bir dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="1409d-287">Örneğin, *C:\\* Windows'da Kitaplar Verileri.</span><span class="sxs-lookup"><span data-stu-id="1409d-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="1409d-288">Yoksa dizini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1409d-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="1409d-289">Mongo Shell yeni dizinler yaratmaz.</span><span class="sxs-lookup"><span data-stu-id="1409d-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="1409d-290">Komut kabuğunu açın.</span><span class="sxs-lookup"><span data-stu-id="1409d-290">Open a command shell.</span></span> <span data-ttu-id="1409d-291">Varsayılan bağlantı noktası 27017'de MongoDB'ye bağlanmak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1409d-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="1409d-292">Önceki adımda seçtiğiniz dizini değiştirmeyi `<data_directory_path>` unutmayın.</span><span class="sxs-lookup"><span data-stu-id="1409d-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="1409d-293">Başka bir komut kabuğu örneği açın.</span><span class="sxs-lookup"><span data-stu-id="1409d-293">Open another command shell instance.</span></span> <span data-ttu-id="1409d-294">Aşağıdaki komutu çalıştırarak varsayılan test veritabanına bağlanın:</span><span class="sxs-lookup"><span data-stu-id="1409d-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="1409d-295">Aşağıdakileri bir komut kabuğunda çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="1409d-296">Zaten yoksa, *BookstoreDb* adında bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1409d-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="1409d-297">Veritabanı varsa, bağlantısı hareketler için açılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="1409d-298">Aşağıdaki `Books` komutu kullanarak bir koleksiyon oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1409d-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="1409d-299">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="1409d-300">Koleksiyon için bir şema tanımlayın `Books` ve aşağıdaki komutu kullanarak iki belge ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="1409d-301">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="1409d-302">Bu makalede gösterilen kimlik, bu örneği çalıştırdığınızda kimliklerle eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="1409d-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="1409d-303">Aşağıdaki komutu kullanarak veritabanındaki belgeleri görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="1409d-304">Aşağıdaki sonuç görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="1409d-305">Şema, her belge `_id` için otomatik `ObjectId` olarak oluşturulmuş bir tür özelliği ekler.</span><span class="sxs-lookup"><span data-stu-id="1409d-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="1409d-306">Veritabanı hazır.</span><span class="sxs-lookup"><span data-stu-id="1409d-306">The database is ready.</span></span> <span data-ttu-id="1409d-307">ASP.NET Core web API'sini oluşturmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1409d-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="1409d-308">ASP.NET Core web API projesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="1409d-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1409d-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1409d-310">**Dosya** > **Yeni** > **Proje**git.</span><span class="sxs-lookup"><span data-stu-id="1409d-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="1409d-311">ASP.NET **Çekirdek Web Uygulaması** proje türünü seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="1409d-312">Proje *BooksApi*adını ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="1409d-313">**.NET Core** hedef çerçevesini seçin ve **Core 2.2'ASP.NET.**</span><span class="sxs-lookup"><span data-stu-id="1409d-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="1409d-314">**API** proje şablonu seçin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="1409d-315">MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="1409d-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1409d-316">Paket **Yöneticisi Konsolu** penceresinde proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="1409d-317">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1409d-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1409d-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1409d-319">Komut kabuğunda aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="1409d-320">.NET Core'u hedefleyen yeni bir ASP.NET Core web API projesi Visual Studio Code'da oluşturulur ve açılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="1409d-321">Durum çubuğunun OmniSharp alev simgesi yeşile döndükten sonra, bir iletişim kutusu gerekli varlıkların oluşturulmasını ister **ve hata ayıklama 'BooksApi'den eksiktir. Onları da eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="1409d-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="1409d-322">**Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-322">Select **Yes**.</span></span>
1. <span data-ttu-id="1409d-323">MongoDB için .NET sürücüsünün en son kararlı sürümünü belirlemek için [NuGet Galerisi: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) adresini ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="1409d-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="1409d-324">**Tümleşik Terminali** açın ve proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="1409d-325">MongoDB için .NET sürücüsünü yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1409d-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1409d-326">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1409d-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1409d-327">**DosyaYa** > Git **Yeni Çözüm** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="1409d-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="1409d-328">ASP.NET **Core Web API** C# proje şablonu seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="1409d-329">**Hedef Çerçeve** açılır listesinden **.NET Core 2.2'yi** seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="1409d-330">**Proje Adı**için *BookApi'yi* girin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="1409d-331">**Çözüm** defterinde, projenin **Bağımlılıklar** düğümüne sağ tıklayın ve **Paket Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="1409d-332">Arama *kutusuna MongoDB.Driver* girin, *MongoDB.Driver* paketini seçin ve **Paket Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="1409d-333">**Lisans Kabul** iletişim **kutusundakabul** et düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1409d-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="1409d-334">Varlık modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-334">Add an entity model</span></span>

1. <span data-ttu-id="1409d-335">Proje köküne bir *Modeller* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="1409d-336">Aşağıdaki `Book` kodla *Modeller* dizinine bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="1409d-337">Önceki sınıfta, `Id` özellik:</span><span class="sxs-lookup"><span data-stu-id="1409d-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="1409d-338">Ortak Dil Çalışma Zamanı (CLR) nesnesini MongoDB koleksiyonuyla eşlemek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1409d-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="1409d-339">Bu özelliği belgenin [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) birincil anahtarı olarak belirlemek üzere açıklama yapılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="1409d-340">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) yapısı yerine [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) parametrenin türü `string` olarak geçirilmesine izin vermek için açıklama yapılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="1409d-341">Mongo dönüşüm `string` `ObjectId`yönetir.</span><span class="sxs-lookup"><span data-stu-id="1409d-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="1409d-342">Özellik `BookName` öznitelik ile [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) açıklamalı.</span><span class="sxs-lookup"><span data-stu-id="1409d-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="1409d-343">Özniteliğin `Name` değeri MongoDB koleksiyonundaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="1409d-344">Yapılandırma modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-344">Add a configuration model</span></span>

1. <span data-ttu-id="1409d-345">*Appsettings.json'a*aşağıdaki veritabanı yapılandırma değerlerini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="1409d-346">*Modeller* dizinine aşağıdaki kodla *bir BookstoreDatabaseSettings.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="1409d-347">Önceki `BookstoreDatabaseSettings` sınıf *appsettings.json* dosyasının `BookstoreDatabaseSettings` özellik değerlerini depolamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="1409d-348">JSON ve C# özellik adları eşleme işlemini kolaylaştırmak için aynı şekilde adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1409d-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="1409d-349">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="1409d-350">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="1409d-350">In the preceding code:</span></span>

   * <span data-ttu-id="1409d-351">*Appsettings.json* dosyasının `BookstoreDatabaseSettings` bölümünün bağladığı yapılandırma örneği Bağımlılık Enjeksiyonu (DI) kapsayıcısında kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1409d-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="1409d-352">Örneğin, bir `BookstoreDatabaseSettings` nesnenin `ConnectionString` özelliği `BookstoreDatabaseSettings:ConnectionString` *appsettings.json*özelliği ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="1409d-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="1409d-353">Arabirim, `IBookstoreDatabaseSettings` singleton [hizmet ömrü](xref:fundamentals/dependency-injection#service-lifetimes)yle DI'de kayıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="1409d-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1409d-354">Enjekte edildiğinde, arabirim örneği bir `BookstoreDatabaseSettings` nesneye giderilir.</span><span class="sxs-lookup"><span data-stu-id="1409d-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="1409d-355">Başvuruları ve `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` başvuruları çözmek için aşağıdaki kodu *Startup.cs* üst bölümüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="1409d-356">CRUD operasyon hizmeti ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="1409d-357">Proje köküne *bir Hizmet* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="1409d-358">Hizmetler `BookService` dizinine *Services* aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="1409d-359">Önceki kodda, bir `IBookstoreDatabaseSettings` örnek yapı oluşturucu enjeksiyon yoluyla DI alınır.</span><span class="sxs-lookup"><span data-stu-id="1409d-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="1409d-360">Bu teknik, [yapılandırma modeli ekle](#add-a-configuration-model) bölümüne eklenen *appsettings.json* yapılandırma değerlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="1409d-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="1409d-361">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="1409d-362">Önceki kodda, `BookService` sınıf tüketen sınıflarda yapıcı enjeksiyonu desteklemek için DI'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1409d-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="1409d-363">Singleton hizmet ömrü en `BookService` uygundur, çünkü `MongoClient`doğrudan bir bağımlılık alır.</span><span class="sxs-lookup"><span data-stu-id="1409d-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="1409d-364">Resmi [Mongo Müşteri yeniden](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)yönergeleri `MongoClient` başına, di bir singleton hizmet ömrü ile kayıtlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1409d-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="1409d-365">Başvuruyu çözmek için Startup.cs üst bölümüne aşağıdaki kodu ekleyin: *Startup.cs* `BookService`</span><span class="sxs-lookup"><span data-stu-id="1409d-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="1409d-366">Sınıf, `BookService` veritabanına `MongoDB.Driver` karşı CRUD işlemleri gerçekleştirmek için aşağıdaki üyeleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="1409d-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="1409d-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; veritabanı işlemleri gerçekleştirmek için sunucu örneğini okur.</span><span class="sxs-lookup"><span data-stu-id="1409d-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="1409d-368">Bu sınıfın oluşturucusu MongoDB bağlantı dizesi sağlanır:</span><span class="sxs-lookup"><span data-stu-id="1409d-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="1409d-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Işlemleri gerçekleştirmek için Mongo veritabanını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="1409d-370">Bu öğretici, belirli bir koleksiyondaki verilere erişmek için arabirimdeki genel [GetCollection\<TDocument>(toplama)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="1409d-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="1409d-371">Bu yöntem çağrıldıktan sonra koleksiyona karşı CRUD işlemleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="1409d-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="1409d-372">Yöntem `GetCollection<TDocument>(collection)` aramasında:</span><span class="sxs-lookup"><span data-stu-id="1409d-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="1409d-373">`collection`koleksiyon adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="1409d-374">`TDocument`koleksiyonda depolanan CLR nesne türünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="1409d-375">`GetCollection<TDocument>(collection)`koleksiyonu temsil eden bir [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) nesnesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="1409d-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="1409d-376">Bu öğreticide, koleksiyonda aşağıdaki yöntemler çağrılır:</span><span class="sxs-lookup"><span data-stu-id="1409d-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="1409d-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Sağlanan arama ölçütleriyle eşleşen tek bir belgeyi siler.</span><span class="sxs-lookup"><span data-stu-id="1409d-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="1409d-378">[TBelgesi'ni\<bul>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Koleksiyondaki tüm belgeleri sağlanan arama ölçütleriyle eşleşen olarak döndürür.</span><span class="sxs-lookup"><span data-stu-id="1409d-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="1409d-379">[InsertOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; sağlanan nesneyi koleksiyonda yeni bir belge olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="1409d-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="1409d-380">[ReplaceOne,](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; sağlanan arama ölçütleriyle eşleşen tek belgeyi sağlanan nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="1409d-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1409d-381">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-381">Add a controller</span></span>

<span data-ttu-id="1409d-382">Denetleyiciler `BooksController` dizinine *Controllers* aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="1409d-383">Önceki web API denetleyicisi:</span><span class="sxs-lookup"><span data-stu-id="1409d-383">The preceding web API controller:</span></span>

* <span data-ttu-id="1409d-384">CRUD `BookService` işlemlerini gerçekleştirmek için sınıfı kullanır.</span><span class="sxs-lookup"><span data-stu-id="1409d-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="1409d-385">GET, POST, PUT ve DELETE HTTP isteklerini desteklemek için eylem yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1409d-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="1409d-386">Http <xref:System.Web.Http.ApiController.CreatedAtRoute*> [201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) yanıtını döndürmek için eylem yöntemini `Create` çağırır.</span><span class="sxs-lookup"><span data-stu-id="1409d-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="1409d-387">Durum kodu 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.</span><span class="sxs-lookup"><span data-stu-id="1409d-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="1409d-388">`CreatedAtRoute`ayrıca yanıta bir `Location` üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="1409d-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="1409d-389">Üstbilgi, `Location` yeni oluşturulan kitabın URI'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1409d-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="1409d-390">Web API'sini test edin</span><span class="sxs-lookup"><span data-stu-id="1409d-390">Test the web API</span></span>

1. <span data-ttu-id="1409d-391">Uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1409d-391">Build and run the app.</span></span>

1. <span data-ttu-id="1409d-392">Denetleyicinin `http://localhost:<port>/api/books` parametresiz `Get` eylem yöntemini sınamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="1409d-393">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="1409d-394">Denetleyicinin `http://localhost:<port>/api/books/{id here}` aşırı yüklenmiş `Get` eylem yöntemini sınamak için gidin.</span><span class="sxs-lookup"><span data-stu-id="1409d-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="1409d-395">Aşağıdaki JSON yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1409d-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="1409d-396">JSON serileştirme seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1409d-396">Configure JSON serialization options</span></span>

<span data-ttu-id="1409d-397">[Test web API](#test-the-web-api) bölümünde döndürülen JSON yanıtları hakkında değiştirmek için iki ayrıntı vardır:</span><span class="sxs-lookup"><span data-stu-id="1409d-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="1409d-398">Özellik adlarının varsayılan deve gövdesi, CLR nesnesinin özellik adlarının Pascal kasasına uyacak şekilde değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1409d-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="1409d-399">Özellik `bookName` olarak `Name`döndürülmelidir.</span><span class="sxs-lookup"><span data-stu-id="1409d-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="1409d-400">Önceki gereksinimleri karşılamak için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="1409d-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="1409d-401">In `Startup.ConfigureServices`, `AddMvc` yöntem çağrısı üzerine aşağıdaki vurgulanan kodu zincir:</span><span class="sxs-lookup"><span data-stu-id="1409d-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="1409d-402">Önceki değişiklikle, web API'nin serileştirilmiş JSON yanıtındaki özellik adları CLR nesne türünde karşılık gelen özellik adlarıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1409d-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="1409d-403">Örneğin, `Book` sınıfın `Author` özelliği olarak `Author`serileşir.</span><span class="sxs-lookup"><span data-stu-id="1409d-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="1409d-404">*Modeller/Book.cs'* de, `BookName` özelliği aşağıdaki [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) öznitelikle açıklama olarak belirtin:</span><span class="sxs-lookup"><span data-stu-id="1409d-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="1409d-405">Özniteliğin `[JsonProperty]` `Name` değeri, web API'nin serileştirilmiş JSON yanıtındaki özellik adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1409d-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="1409d-406">Öznitelik başvurularını çözmek `[JsonProperty]` için *Modeller/Book.cs'nin* üst bölümüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1409d-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="1409d-407">[Test web API](#test-the-web-api) bölümünde tanımlanan adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="1409d-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="1409d-408">JSON özellik adlarında farka dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="1409d-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="1409d-409">Web API'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="1409d-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="1409d-410">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1409d-410">Next steps</span></span>

<span data-ttu-id="1409d-411">Core web API'ASP.NET leri oluşturma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="1409d-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="1409d-412">Bu makalenin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="1409d-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
