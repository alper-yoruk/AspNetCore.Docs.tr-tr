---
title: 'Öğretici: ASP.NET Core ile bir web API oluşturma'
author: rick-anderson
description: ASP.NET Core ile nasıl web API oluşturarak nasıl oluşturabildiğini öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417653"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="8c03b-103">Öğretici: ASP.NET Core ile bir web API oluşturma</span><span class="sxs-lookup"><span data-stu-id="8c03b-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="8c03b-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), ve Mike [Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="8c03b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="8c03b-105">Bu öğretici ASP.NET Core ile bir web API oluşturma temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c03b-106">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8c03b-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8c03b-107">Bir web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-107">Create a web API project.</span></span>
> * <span data-ttu-id="8c03b-108">Model sınıfı ve veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="8c03b-109">CRUD yöntemleri ile bir denetleyici iskele.</span><span class="sxs-lookup"><span data-stu-id="8c03b-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="8c03b-110">Yönlendirmeyi, URL yollarını ve döndürme değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="8c03b-111">Postacı ile web API'yi arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-111">Call the web API with Postman.</span></span>

<span data-ttu-id="8c03b-112">Sonunda, veritabanında depolanan "yapılacaklar" öğelerini yönetebilen bir web API'nız vardır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="8c03b-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="8c03b-113">Overview</span></span>

<span data-ttu-id="8c03b-114">Bu öğretici aşağıdaki API'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="8c03b-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8c03b-115">API</span><span class="sxs-lookup"><span data-stu-id="8c03b-115">API</span></span> | <span data-ttu-id="8c03b-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8c03b-116">Description</span></span> | <span data-ttu-id="8c03b-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-117">Request body</span></span> | <span data-ttu-id="8c03b-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="8c03b-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8c03b-119">GET /api/TodoItems</span></span> | <span data-ttu-id="8c03b-120">Tüm yapılacak öğeleri alın</span><span class="sxs-lookup"><span data-stu-id="8c03b-120">Get all to-do items</span></span> | <span data-ttu-id="8c03b-121">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-121">None</span></span> | <span data-ttu-id="8c03b-122">Yapılacak öğeler dizisi</span><span class="sxs-lookup"><span data-stu-id="8c03b-122">Array of to-do items</span></span>|
|<span data-ttu-id="8c03b-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8c03b-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="8c03b-124">Bir öğeyi kimlikle alma</span><span class="sxs-lookup"><span data-stu-id="8c03b-124">Get an item by ID</span></span> | <span data-ttu-id="8c03b-125">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-125">None</span></span> | <span data-ttu-id="8c03b-126">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-126">To-do item</span></span>|
|<span data-ttu-id="8c03b-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8c03b-127">POST /api/TodoItems</span></span> | <span data-ttu-id="8c03b-128">Yeni bir öğe ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-128">Add a new item</span></span> | <span data-ttu-id="8c03b-129">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-129">To-do item</span></span> | <span data-ttu-id="8c03b-130">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-130">To-do item</span></span> |
|<span data-ttu-id="8c03b-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8c03b-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="8c03b-132">Varolan bir öğeyi güncelleştirme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8c03b-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8c03b-133">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-133">To-do item</span></span> | <span data-ttu-id="8c03b-134">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-134">None</span></span> |
|<span data-ttu-id="8c03b-135">DELETE /api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8c03b-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="8c03b-136">Öğeyi &nbsp; silme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8c03b-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8c03b-137">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-137">None</span></span> | <span data-ttu-id="8c03b-138">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-138">None</span></span>|

<span data-ttu-id="8c03b-139">Aşağıdaki diyagram, uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-139">The following diagram shows the design of the app.</span></span>

![İstemci soldaki bir kutu yla temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8c03b-145">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8c03b-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-148">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8c03b-149">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="8c03b-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-151">**Dosya** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8c03b-152">ASP.NET **Çekirdek Web Uygulaması** şablonunu seçin ve **İleri'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8c03b-153">Proje *todoApi* adı ve **Oluştur'u**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8c03b-154">Yeni **bir ASP.NET Core Web Uygulaması Oluştur** iletişim kutusunda **,.NET Core** ve ASP.NET Core **3.1'in** seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="8c03b-155">**API** şablonu seçin ve **Oluştur'u**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-155">Select the **API** template and click **Create**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8c03b-158">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8c03b-159">Dizinleri`cd`( ) proje klasörünü içeren klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8c03b-160">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="8c03b-161">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinisi sorduğunda **Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="8c03b-162">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="8c03b-162">The preceding commands:</span></span>

  * <span data-ttu-id="8c03b-163">Yeni bir web API projesi oluşturur ve Visual Studio Code'da açar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="8c03b-164">Bir sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-165">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8c03b-166">**Dosya** > **Yeni Çözüm**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-166">Select **File** > **New Solution**.</span></span>

  ![macOS Yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8c03b-168">**Sonraki** **.NET Çekirdek** > **Uygulama** > **API'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS Yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="8c03b-170">Yeni **ASP.NET Core Web API iletişim günlüğünüzü yapılandırın,** \**.NET Core 3.1'in* **Hedef Çerçevesi'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="8c03b-171">**Proje Adı** için *TodoApi* girin ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![config iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="8c03b-173">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="8c03b-174">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="8c03b-174">Test the API</span></span>

<span data-ttu-id="8c03b-175">Proje şablonu bir `WeatherForecast` API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="8c03b-176">Uygulamayı `Get` test etmek için yöntemi bir tarayıcıdan arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c03b-178">Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8c03b-179">Visual Studio bir tarayıcı başlatıyor `https://localhost:<port>/WeatherForecast`ve `<port>` rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol alar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8c03b-180">IIS Express sertifikasına güvenip güvenmemeniz gerektiğini soran bir iletişim kutusu **alırsanız, Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8c03b-181">Bir sonraki görünen **Güvenlik Uyarısı** iletişim kutusunda **Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8c03b-183">Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8c03b-184">Bir tarayıcıda, aşağıdaki URL'ye gidin: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="8c03b-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-185">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8c03b-186">Uygulamayı başlatmak için**Başlat Hata Ayıklama'yı** **çalıştır'ı** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="8c03b-187">Mac için Visual Studio bir tarayıcı `https://localhost:<port>`başlatıyor `<port>` ve rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol aldı.</span><span class="sxs-lookup"><span data-stu-id="8c03b-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8c03b-188">Bir HTTP 404 (Bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8c03b-189">URL'ye ek `/WeatherForecast` (URL'yi `https://localhost:<port>/WeatherForecast`değiştirin).</span><span class="sxs-lookup"><span data-stu-id="8c03b-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="8c03b-190">JSON benzer aşağıdaki döndürülür:</span><span class="sxs-lookup"><span data-stu-id="8c03b-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="8c03b-191">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-191">Add a model class</span></span>

<span data-ttu-id="8c03b-192">*Model,* uygulamanın yönettiği verileri temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8c03b-193">Bu uygulamanın modeli tek `TodoItem` bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-195">**Çözüm Gezgini'nde**projeyi sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8c03b-196">**Yeni Klasör** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8c03b-197">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="8c03b-198">*Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8c03b-199">Sınıf *TodoItem'i* adlandırın ve **Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8c03b-200">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8c03b-202">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8c03b-203">Aşağıdaki `TodoItem` kodu içeren *Modeller* klasörüne bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-204">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8c03b-205">Projeyi sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-205">Right-click the project.</span></span> <span data-ttu-id="8c03b-206">**Yeni Klasör** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8c03b-207">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-207">Name the folder *Models*.</span></span>

  ![yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8c03b-209">*Modeller* klasörüne sağ tıklayın ve **Yeni Dosya** > **Genel** > **Boş Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8c03b-210">Sınıf *TodoItem'i*adlandırın ve ardından **Yeni'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8c03b-211">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="8c03b-212">Özellik, `Id` ilişkisel bir veritabanında benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8c03b-213">Model sınıfları projede herhangi bir yere gidebilir, ancak *Modeller* klasörü kural kuralı tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8c03b-214">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-214">Add a database context</span></span>

<span data-ttu-id="8c03b-215">*Veritabanı bağlamı,* bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8c03b-216">Bu `Microsoft.EntityFrameworkCore.DbContext` sınıf, sınıftan türeerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="8c03b-218">Microsoft.EntityFrameworkCore.sqlserver ekle</span><span class="sxs-lookup"><span data-stu-id="8c03b-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="8c03b-219">**Araçlar** menüsünden **NuGet Paket Yöneticisi > Çözüm Için Paketleri Yönet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="8c03b-220">**Gözat** sekmesini seçin ve ardından arama kutusuna **Microsoft.EntityFrameworkCore.SqlServer** girin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="8c03b-221">Sol bölmede **Microsoft.EntityFrameworkCore.SqlServer'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="8c03b-222">Sağ bölmedeki **Proje** onay kutusunu seçin ve sonra **Yükle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="8c03b-223">NuGet paketini `Microsoft.EntityFrameworkCore.InMemory` eklemek için önceki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="8c03b-225">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="8c03b-226">*Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8c03b-227">Sınıf *TodoContext'ı* adlandırın ve **Ekle'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-228">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8c03b-229">Modeller `TodoContext` klasörüne *Models* bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8c03b-230">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8c03b-231">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="8c03b-231">Register the database context</span></span>

<span data-ttu-id="8c03b-232">ASP.NET Core'da, DB bağlamı gibi hizmetlerin [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8c03b-233">Kapsayıcı denetleyicilere hizmet sağlar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="8c03b-234">Aşağıdaki vurgulanan kod ile *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="8c03b-235">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="8c03b-235">The preceding code:</span></span>

* <span data-ttu-id="8c03b-236">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8c03b-237">Veritabanı bağlamını DI kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8c03b-238">Veritabanı bağlamında bellek içi bir veritabanı kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="8c03b-239">İskele bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="8c03b-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-241">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8c03b-242">**Yeni İskele**Öğesi **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="8c03b-243">**Eylemlerle API Denetleyicisi'ni seç, Entity Framework'ünü kullanarak**ve sonra **Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="8c03b-244">**Eylemlerle api denetleyicisi ekle, Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="8c03b-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="8c03b-245">**Model sınıfında** **TodoItem (TodoApi.Models)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="8c03b-246">**Veri bağlamı sınıfında** **TodoContext (TodoApi.Models)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="8c03b-247">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-248">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8c03b-249">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="8c03b-250">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="8c03b-250">The preceding commands:</span></span>

* <span data-ttu-id="8c03b-251">İskele için gerekli NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="8c03b-252">İskele motorini kurar`dotnet-aspnet-codegenerator`( ).</span><span class="sxs-lookup"><span data-stu-id="8c03b-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="8c03b-253">İskeleler `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="8c03b-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="8c03b-254">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="8c03b-254">The generated code:</span></span>

* <span data-ttu-id="8c03b-255">Sınıfı öznitelik [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) ile işaretler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="8c03b-256">Bu öznitelik denetleyiciweb API isteklerine yanıt gösterir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8c03b-257">Özniteliğin etkinleştirdığı belirli davranışlar hakkında bilgi <xref:web-api/index>için bkz.</span><span class="sxs-lookup"><span data-stu-id="8c03b-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8c03b-258">Veritabanı bağlamını denetleyiciye`TodoContext`enjekte etmek için DI kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8c03b-259">Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="8c03b-260">ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="8c03b-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="8c03b-261">Görünümleri olan `[action]` denetleyiciler rota şablonuna dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="8c03b-262">API denetleyicileri rota `[action]` şablonuna dahil etmez.</span><span class="sxs-lookup"><span data-stu-id="8c03b-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="8c03b-263">`[action]` Belirteç rota şablonunda yoksa, [eylem](xref:mvc/controllers/routing#action) adı rotanın dışında tutulur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="8c03b-264">Diğer bir deyişle, eylemin ilişkili yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="8c03b-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="8c03b-265">PostTodoItem oluşturma yöntemini inceleyin</span><span class="sxs-lookup"><span data-stu-id="8c03b-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="8c03b-266">Operatör adını kullanmak `PostTodoItem` için iade [deyimini değiştirin:](/dotnet/csharp/language-reference/operators/nameof)</span><span class="sxs-lookup"><span data-stu-id="8c03b-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="8c03b-267">Önceki kod, öznitelikte [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) belirtildiği gibi bir HTTP POST yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="8c03b-268">Yöntem, yapılacaklar öğesinin değerini HTTP isteğinin gövdesinden alır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8c03b-269">Yöntem: <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*></span><span class="sxs-lookup"><span data-stu-id="8c03b-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="8c03b-270">Başarılı olursa http 201 durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="8c03b-271">HTTP 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8c03b-272">Yanıta [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üstbilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="8c03b-273">Üstbilgi, `Location` yeni oluşturulan yapılacaklar öğesinin [URI'sini](https://developer.mozilla.org/docs/Glossary/URI) belirtir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="8c03b-274">Daha fazla bilgi için [bkz.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="8c03b-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8c03b-275">`Location` Üstbilginin `GetTodoItem` URI'sini oluşturmak için eyleme başvurur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8c03b-276">C# `nameof` anahtar `CreatedAtAction` kelimesi, çağrıdaki eylem adını sert kodlamaktan kaçınmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="8c03b-277">Postacı Yükle</span><span class="sxs-lookup"><span data-stu-id="8c03b-277">Install Postman</span></span>

<span data-ttu-id="8c03b-278">Bu öğretici, web API test etmek için Postacı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8c03b-279">[Postacı](https://www.getpostman.com/downloads/) Yükle</span><span class="sxs-lookup"><span data-stu-id="8c03b-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="8c03b-280">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-280">Start the web app.</span></span>
* <span data-ttu-id="8c03b-281">Postacı'yı başlat.</span><span class="sxs-lookup"><span data-stu-id="8c03b-281">Start Postman.</span></span>
* <span data-ttu-id="8c03b-282">**SSL sertifika doğrulamayı** devre dışı</span><span class="sxs-lookup"><span data-stu-id="8c03b-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="8c03b-283">**Dosya** > **Ayarları** 'ndan **(Genel** sekmesi), **SSL sertifika doğrulamasını**devre dışı.</span><span class="sxs-lookup"><span data-stu-id="8c03b-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="8c03b-284">Denetleyiciyi test ettikten sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="8c03b-285">Postman ile Test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="8c03b-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="8c03b-286">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-286">Create a new request.</span></span>
* <span data-ttu-id="8c03b-287">HTTP yöntemini `POST`' ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8c03b-288">**Vücut** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="8c03b-289">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8c03b-290">Türü **JSON (uygulama/json)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8c03b-291">İstek gövdesinde yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8c03b-292">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-292">Select **Send**.</span></span>

  ![Create request ile Postacı](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="8c03b-294">Konum üstbilgisini URI test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-294">Test the location header URI</span></span>

* <span data-ttu-id="8c03b-295">**Yanıt** bölmesinde **Üstbilgi** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8c03b-296">**Konum** üstbilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-296">Copy the **Location** header value:</span></span>

  ![Postacı konsolunun üstbilgi sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="8c03b-298">Yöntemi GET olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-298">Set the method to GET.</span></span>
* <span data-ttu-id="8c03b-299">URI yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="8c03b-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="8c03b-300">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="8c03b-301">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="8c03b-301">Examine the GET methods</span></span>

<span data-ttu-id="8c03b-302">Bu yöntemler iki GET uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="8c03b-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="8c03b-303">Uygulamayı bir tarayıcıdan veya Postacı'dan iki uç noktayı arayarak test edin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="8c03b-304">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="8c03b-305">Aşağıdakilere benzer bir yanıt çağrı tarafından `GetTodoItems`üretilir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="8c03b-306">Postacı ile Test Alın</span><span class="sxs-lookup"><span data-stu-id="8c03b-306">Test Get with Postman</span></span>

* <span data-ttu-id="8c03b-307">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-307">Create a new request.</span></span>
* <span data-ttu-id="8c03b-308">HTTP yöntemini **GET**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="8c03b-309">İstek URL'sini `https://localhost:<port>/api/TodoItems`' ' olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="8c03b-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="8c03b-310">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="8c03b-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="8c03b-311">**Postman'da iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8c03b-312">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-312">Select **Send**.</span></span>

<span data-ttu-id="8c03b-313">Bu uygulama bir bellek veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-313">This app uses an in-memory database.</span></span> <span data-ttu-id="8c03b-314">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veriyi döndürmez.</span><span class="sxs-lookup"><span data-stu-id="8c03b-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="8c03b-315">Veri döndürülmezse, verileri uygulamaya [gönderin.](#post)</span><span class="sxs-lookup"><span data-stu-id="8c03b-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="8c03b-316">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="8c03b-316">Routing and URL paths</span></span>

<span data-ttu-id="8c03b-317">Öznitelik, [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8c03b-318">Her yöntemin URL yolu aşağıdaki gibi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="8c03b-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8c03b-319">Denetleyicinin `Route` özniteliğindeki şablon dizesiyle başlayın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="8c03b-320">"Denetleyici" soneki eksi denetleyici sınıf adı olan denetleyicinin adı ile değiştirin. `[controller]`</span><span class="sxs-lookup"><span data-stu-id="8c03b-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8c03b-321">Bu örnek için denetleyici sınıf adı **TodoItems**Denetleyicisi olduğundan denetleyici adı "TodoItems"tır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="8c03b-322">ASP.NET Çekirdek [yönlendirme](xref:mvc/controllers/routing) durumda duyarsız olduğunu.</span><span class="sxs-lookup"><span data-stu-id="8c03b-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8c03b-323">`[HttpGet]` Öznitelik bir rota şablonu varsa `[HttpGet("products")]`(örneğin, ), bunu yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8c03b-324">Bu örnek şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="8c03b-324">This sample doesn't use a template.</span></span> <span data-ttu-id="8c03b-325">Daha fazla bilgi için [http[Fiil] öznitelikleri ile Öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)bakın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8c03b-326">Aşağıdaki `GetTodoItem` yöntemde, `"{id}"` yapılacak lar öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8c03b-327">Çağrıldığı zaman, `GetTodoItem` `"{id}"` URL'deki değeri `id` parametresinde yönteme sağlanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8c03b-328">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="8c03b-328">Return values</span></span>

<span data-ttu-id="8c03b-329">Ve `GetTodoItems` `GetTodoItem` yöntemlerin dönüş türü [ActionResult\<T> türüdür.](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="8c03b-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8c03b-330">ASP.NET Core nesneyi otomatik olarak [JSON'a](https://www.json.org/) serileştirir ve JSON'u yanıt iletisinin gövdesine yazar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8c03b-331">İşlenmemiş özel durumlar olmadığını varsayarak, bu iade türü için yanıt kodu 200'dür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8c03b-332">Işlenmemiş özel durumlar 5xx hatalarına çevrilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8c03b-333">`ActionResult`iade türleri çok çeşitli HTTP durum kodlarını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8c03b-334">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8c03b-335">Hiçbir öğe istenen kimlikle eşleşmiyorsa, yöntem 404 NoFound hata kodunu [döndürür.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)</span><span class="sxs-lookup"><span data-stu-id="8c03b-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="8c03b-336">Aksi takdirde, yöntem bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8c03b-337">Http `item` 200 yanıtıile sonuçları döndürün.</span><span class="sxs-lookup"><span data-stu-id="8c03b-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="8c03b-338">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="8c03b-338">The PutTodoItem method</span></span>

<span data-ttu-id="8c03b-339">Yöntemi `PutTodoItem` inceleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="8c03b-340">`PutTodoItem`benzer `PostTodoItem`, bu http PUT kullanır dışında.</span><span class="sxs-lookup"><span data-stu-id="8c03b-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8c03b-341">Yanıt [204 (İçerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8c03b-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8c03b-342">HTTP belirtimine göre, PUT isteği istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8c03b-343">Kısmi güncelleştirmeleri desteklemek [için HTTP PATCH'i](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8c03b-344">Arama hatası `PutTodoItem`alırsanız, `GET` veritabanında bir öğe olduğundan emin olmak için arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8c03b-345">PutTodoItem yöntemini test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="8c03b-346">Bu örnek, uygulama her başlatıldığında başlatılması gereken bir bellek içi veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="8c03b-347">PUT araması yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8c03b-348">PUT araması yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET'i arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8c03b-349">ID = 1 olan yapılacaklar öğesini güncelleştirin ve adını "balık beslemek" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8c03b-350">Aşağıdaki resim Postacı güncelleştirmesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-350">The following image shows the Postman update:</span></span>

![Postacı konsolu 204 (İçerik Yok) yanıtı gösteriyor](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="8c03b-352">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="8c03b-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="8c03b-353">Yöntemi `DeleteTodoItem` inceleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8c03b-354">DeleteTodoItem yöntemini test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8c03b-355">Yapılacaklar öğesini silmek için Postacı'yı kullanın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8c03b-356">Yöntemi `DELETE`' ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8c03b-357">Nesnenin URI'sini silmek için `https://localhost:5001/api/TodoItems/1`ayarlayın (örneğin).</span><span class="sxs-lookup"><span data-stu-id="8c03b-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="8c03b-358">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="8c03b-359">Aşırı deftere nakil önleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-359">Prevent over-posting</span></span>

<span data-ttu-id="8c03b-360">Şu anda örnek uygulama `TodoItem` tüm nesneyi ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="8c03b-361">Productions uygulamaları genellikle modelin bir alt kümesini kullanarak giriş ve döndürülen verileri sınırlar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="8c03b-362">Bunun arkasında birden fazla nedeni vardır ve güvenlik önemli bir tanesidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="8c03b-363">Bir modelin alt kümesi genellikle Veri Aktarım Nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="8c03b-364">**DTO** bu makalede kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="8c03b-365">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-365">A DTO may be used to:</span></span>

* <span data-ttu-id="8c03b-366">Aşırı deftere nakil yi önleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-366">Prevent over-posting.</span></span>
* <span data-ttu-id="8c03b-367">İstemcilerin görüntülememesi gereken özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="8c03b-368">Taşıma boyutunu azaltmak için bazı özellikleri atla.</span><span class="sxs-lookup"><span data-stu-id="8c03b-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="8c03b-369">İç içe nesneler içeren nesne grafiklerini düzleştirmek.</span><span class="sxs-lookup"><span data-stu-id="8c03b-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="8c03b-370">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="8c03b-371">DTO yaklaşımını göstermek için `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="8c03b-372">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması uygulamayı ifşa etmeyi seçebilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="8c03b-373">Gönderebileceğinizi ve gizli alanı alabileceğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="8c03b-374">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="8c03b-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="8c03b-375">Güncelleme `TodoItemsController` kullanmak `TodoItemDTO`için:</span><span class="sxs-lookup"><span data-stu-id="8c03b-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="8c03b-376">Gizli alanı gönderemezsiniz veya alınamadığınızı doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8c03b-377">JavaScript ile web API'sini arayın</span><span class="sxs-lookup"><span data-stu-id="8c03b-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="8c03b-378">[Bkz. Öğretici: JavaScript ile ASP.NET Core web API'yi arayın.](xref:tutorials/web-api-javascript)</span><span class="sxs-lookup"><span data-stu-id="8c03b-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c03b-379">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8c03b-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8c03b-380">Bir web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-380">Create a web API project.</span></span>
> * <span data-ttu-id="8c03b-381">Model sınıfı ve veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="8c03b-382">Bir denetleyici ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-382">Add a controller.</span></span>
> * <span data-ttu-id="8c03b-383">CRUD yöntemlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="8c03b-384">Yönlendirme ve URL yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="8c03b-385">İade değerlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-385">Specify return values.</span></span>
> * <span data-ttu-id="8c03b-386">Postacı ile web API'yi arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="8c03b-387">JavaScript ile web API'yi arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="8c03b-388">Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilen bir web API'nız vardır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="8c03b-389">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="8c03b-389">Overview</span></span>

<span data-ttu-id="8c03b-390">Bu öğretici aşağıdaki API'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="8c03b-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="8c03b-391">API</span><span class="sxs-lookup"><span data-stu-id="8c03b-391">API</span></span> | <span data-ttu-id="8c03b-392">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8c03b-392">Description</span></span> | <span data-ttu-id="8c03b-393">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-393">Request body</span></span> | <span data-ttu-id="8c03b-394">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="8c03b-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8c03b-395">GET /api/TodoItems</span></span> | <span data-ttu-id="8c03b-396">Tüm yapılacak öğeleri alın</span><span class="sxs-lookup"><span data-stu-id="8c03b-396">Get all to-do items</span></span> | <span data-ttu-id="8c03b-397">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-397">None</span></span> | <span data-ttu-id="8c03b-398">Yapılacak öğeler dizisi</span><span class="sxs-lookup"><span data-stu-id="8c03b-398">Array of to-do items</span></span>|
|<span data-ttu-id="8c03b-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8c03b-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="8c03b-400">Bir öğeyi kimlikle alma</span><span class="sxs-lookup"><span data-stu-id="8c03b-400">Get an item by ID</span></span> | <span data-ttu-id="8c03b-401">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-401">None</span></span> | <span data-ttu-id="8c03b-402">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-402">To-do item</span></span>|
|<span data-ttu-id="8c03b-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="8c03b-403">POST /api/TodoItems</span></span> | <span data-ttu-id="8c03b-404">Yeni bir öğe ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-404">Add a new item</span></span> | <span data-ttu-id="8c03b-405">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-405">To-do item</span></span> | <span data-ttu-id="8c03b-406">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-406">To-do item</span></span> |
|<span data-ttu-id="8c03b-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="8c03b-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="8c03b-408">Varolan bir öğeyi güncelleştirme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8c03b-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="8c03b-409">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="8c03b-409">To-do item</span></span> | <span data-ttu-id="8c03b-410">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-410">None</span></span> |
|<span data-ttu-id="8c03b-411">DELETE /api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8c03b-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="8c03b-412">Öğeyi &nbsp; silme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="8c03b-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="8c03b-413">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-413">None</span></span> | <span data-ttu-id="8c03b-414">None</span><span class="sxs-lookup"><span data-stu-id="8c03b-414">None</span></span>|

<span data-ttu-id="8c03b-415">Aşağıdaki diyagram, uygulamanın tasarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-415">The following diagram shows the design of the app.</span></span>

![İstemci soldaki bir kutu yla temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="8c03b-421">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8c03b-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-424">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="8c03b-425">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="8c03b-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-427">**Dosya** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8c03b-428">ASP.NET **Çekirdek Web Uygulaması** şablonunu seçin ve **İleri'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="8c03b-429">Proje *todoApi* adı ve **Oluştur'u**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="8c03b-430">Yeni **bir ASP.NET Core Web Uygulaması Oluştur** iletişim kutusunda **,.NET Core** ve ASP.NET Core **2.2'nin** seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="8c03b-431">**API** şablonu seçin ve **Oluştur'u**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="8c03b-432">Docker Desteğini **Enable Docker Support** **Etkinleştir'i seçmeyin.**</span><span class="sxs-lookup"><span data-stu-id="8c03b-432">**Don't** select **Enable Docker Support**.</span></span>

![VS yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8c03b-435">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8c03b-436">Dizinleri`cd`( ) proje klasörünü içeren klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="8c03b-437">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="8c03b-438">Bu komutlar yeni bir web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code'un yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="8c03b-439">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinisi sorduğunda **Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-440">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8c03b-441">**Dosya** > **Yeni Çözüm**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-441">Select **File** > **New Solution**.</span></span>

  ![macOS Yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="8c03b-443">**Sonraki** **.NET Çekirdek** > **Uygulama** > **API'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS Yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="8c03b-445">Yeni **ASP.NET Core Web API iletişim günlüğünüzde** \**.NET Core 2.2'nin*varsayılan **Hedef Çerçevesini** kabul edin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="8c03b-446">**Proje Adı** için *TodoApi* girin ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![config iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="8c03b-448">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="8c03b-448">Test the API</span></span>

<span data-ttu-id="8c03b-449">Proje şablonu bir `values` API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-449">The project template creates a `values` API.</span></span> <span data-ttu-id="8c03b-450">Uygulamayı `Get` test etmek için yöntemi bir tarayıcıdan arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c03b-452">Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8c03b-453">Visual Studio bir tarayıcı başlatıyor `https://localhost:<port>/api/values`ve `<port>` rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol alar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="8c03b-454">IIS Express sertifikasına güvenip güvenmemeniz gerektiğini soran bir iletişim kutusu **alırsanız, Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="8c03b-455">Bir sonraki görünen **Güvenlik Uyarısı** iletişim kutusunda **Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8c03b-457">Uygulamayı çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="8c03b-458">Bir tarayıcıda, aşağıdaki URL'ye gidin: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="8c03b-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-459">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8c03b-460">Uygulamayı başlatmak için**Başlat Hata Ayıklama'yı** **çalıştır'ı** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="8c03b-461">Mac için Visual Studio bir tarayıcı `https://localhost:<port>`başlatıyor `<port>` ve rastgele seçilen bir bağlantı noktası numarası olan yere doğru yol aldı.</span><span class="sxs-lookup"><span data-stu-id="8c03b-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="8c03b-462">Bir HTTP 404 (Bulunamadı) hatası döndürülür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="8c03b-463">URL'ye ek `/api/values` (URL'yi `https://localhost:<port>/api/values`değiştirin).</span><span class="sxs-lookup"><span data-stu-id="8c03b-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="8c03b-464">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="8c03b-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="8c03b-465">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-465">Add a model class</span></span>

<span data-ttu-id="8c03b-466">*Model,* uygulamanın yönettiği verileri temsil eden sınıflar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="8c03b-467">Bu uygulamanın modeli tek `TodoItem` bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-469">**Çözüm Gezgini'nde**projeyi sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="8c03b-470">**Yeni Klasör** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8c03b-471">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="8c03b-472">*Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8c03b-473">Sınıf *TodoItem'i* adlandırın ve **Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="8c03b-474">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c03b-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c03b-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8c03b-476">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="8c03b-477">Aşağıdaki `TodoItem` kodu içeren *Modeller* klasörüne bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-478">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8c03b-479">Projeyi sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-479">Right-click the project.</span></span> <span data-ttu-id="8c03b-480">**Yeni Klasör** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="8c03b-481">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-481">Name the folder *Models*.</span></span>

  ![yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="8c03b-483">*Modeller* klasörüne sağ tıklayın ve **Yeni Dosya** > **Genel** > **Boş Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="8c03b-484">Sınıf *TodoItem'i*adlandırın ve ardından **Yeni'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="8c03b-485">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="8c03b-486">Özellik, `Id` ilişkisel bir veritabanında benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="8c03b-487">Model sınıfları projede herhangi bir yere gidebilir, ancak *Modeller* klasörü kural kuralı tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="8c03b-488">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-488">Add a database context</span></span>

<span data-ttu-id="8c03b-489">*Veritabanı bağlamı,* bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="8c03b-490">Bu `Microsoft.EntityFrameworkCore.DbContext` sınıf, sınıftan türeerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-492">*Modeller* klasörüne sağ tıklayın ve**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="8c03b-493">Sınıf *TodoContext'ı* adlandırın ve **Ekle'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-494">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8c03b-495">Modeller `TodoContext` klasörüne *Models* bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="8c03b-496">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="8c03b-497">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="8c03b-497">Register the database context</span></span>

<span data-ttu-id="8c03b-498">ASP.NET Core'da, DB bağlamı gibi hizmetlerin [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8c03b-499">Kapsayıcı denetleyicilere hizmet sağlar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="8c03b-500">Aşağıdaki vurgulanan kod ile *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="8c03b-501">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="8c03b-501">The preceding code:</span></span>

* <span data-ttu-id="8c03b-502">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="8c03b-503">Veritabanı bağlamını DI kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="8c03b-504">Veritabanı bağlamında bellek içi bir veritabanı kullanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8c03b-505">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-507">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="8c03b-508">**Yeni Öğe** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="8c03b-509">Yeni **Öğe Ekle** iletişim kutusunda **API Denetleyici Sınıfı** şablonu'nu seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="8c03b-510">Sınıf *TodoController'ı*adlandırın ve **Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Arama kutusunda denetleyici ve web api denetleyicisi seçili yeni Öğe iletişim kutusu ekleme](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-512">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8c03b-513">*Denetleyiciler* klasöründe, '. `TodoController`adlı bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="8c03b-514">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="8c03b-515">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="8c03b-515">The preceding code:</span></span>

* <span data-ttu-id="8c03b-516">Yöntemsiz bir API denetleyici sınıfı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="8c03b-517">Sınıfı öznitelik [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) ile işaretler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="8c03b-518">Bu öznitelik denetleyiciweb API isteklerine yanıt gösterir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="8c03b-519">Özniteliğin etkinleştirdığı belirli davranışlar hakkında bilgi <xref:web-api/index>için bkz.</span><span class="sxs-lookup"><span data-stu-id="8c03b-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="8c03b-520">Veritabanı bağlamını denetleyiciye`TodoContext`enjekte etmek için DI kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="8c03b-521">Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="8c03b-522">Veritabanı boşsa veritabanına adlandırılmış `Item1` bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="8c03b-523">Bu kod oluşturucudadır, bu nedenle yeni bir HTTP isteği olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="8c03b-524">Tüm öğeleri silerseniz, bir `Item1` sonraki API yöntemi çağrıldığında oluşturucu yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="8c03b-525">Yani silme işlemi işe yaramamış gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="8c03b-526">Get yöntemlerini ekle</span><span class="sxs-lookup"><span data-stu-id="8c03b-526">Add Get methods</span></span>

<span data-ttu-id="8c03b-527">Yapılacak lar öğelerini alan bir API sağlamak için `TodoController` sınıfa aşağıdaki yöntemleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="8c03b-528">Bu yöntemler iki GET uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="8c03b-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="8c03b-529">Çalışmaya devam ediyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-529">Stop the app if it's still running.</span></span> <span data-ttu-id="8c03b-530">Ardından, en son değişiklikleri içerecek şekilde yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="8c03b-531">Uygulamayı tarayıcıdan iki uç noktayı arayarak test edin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="8c03b-532">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="8c03b-533">Aşağıdaki HTTP yanıtı çağrı tarafından `GetTodoItems`üretilir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="8c03b-534">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="8c03b-534">Routing and URL paths</span></span>

<span data-ttu-id="8c03b-535">Öznitelik, [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="8c03b-536">Her yöntemin URL yolu aşağıdaki gibi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="8c03b-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="8c03b-537">Denetleyicinin `Route` özniteliğindeki şablon dizesiyle başlayın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="8c03b-538">"Denetleyici" soneki eksi denetleyici sınıf adı olan denetleyicinin adı ile değiştirin. `[controller]`</span><span class="sxs-lookup"><span data-stu-id="8c03b-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="8c03b-539">Bu örnek için denetleyici sınıf adı **Todo**Denetleyicisi'dir, bu nedenle denetleyici adı "todo"dur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="8c03b-540">ASP.NET Çekirdek [yönlendirme](xref:mvc/controllers/routing) durumda duyarsız olduğunu.</span><span class="sxs-lookup"><span data-stu-id="8c03b-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="8c03b-541">`[HttpGet]` Öznitelik bir rota şablonu varsa `[HttpGet("products")]`(örneğin, ), bunu yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="8c03b-542">Bu örnek şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="8c03b-542">This sample doesn't use a template.</span></span> <span data-ttu-id="8c03b-543">Daha fazla bilgi için [http[Fiil] öznitelikleri ile Öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)bakın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="8c03b-544">Aşağıdaki `GetTodoItem` yöntemde, `"{id}"` yapılacak lar öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="8c03b-545">Çağrıldığı zaman, `GetTodoItem` `"{id}"` URL'deki değeri`id` parametresinde yönteme sağlanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="8c03b-546">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="8c03b-546">Return values</span></span>

<span data-ttu-id="8c03b-547">Ve `GetTodoItems` `GetTodoItem` yöntemlerin dönüş türü [ActionResult\<T> türüdür.](xref:web-api/action-return-types#actionresultt-type)</span><span class="sxs-lookup"><span data-stu-id="8c03b-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="8c03b-548">ASP.NET Core nesneyi otomatik olarak [JSON'a](https://www.json.org/) serileştirir ve JSON'u yanıt iletisinin gövdesine yazar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="8c03b-549">İşlenmemiş özel durumlar olmadığını varsayarak, bu iade türü için yanıt kodu 200'dür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="8c03b-550">Işlenmemiş özel durumlar 5xx hatalarına çevrilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="8c03b-551">`ActionResult`iade türleri çok çeşitli HTTP durum kodlarını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="8c03b-552">Örneğin, `GetTodoItem` iki farklı durum değerleri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="8c03b-553">Hiçbir öğe istenen kimlikle eşleşmiyorsa, yöntem 404 NoFound hata kodunu [döndürür.](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound)</span><span class="sxs-lookup"><span data-stu-id="8c03b-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="8c03b-554">Aksi takdirde, yöntem bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="8c03b-555">Http `item` 200 yanıtıile sonuçları döndürün.</span><span class="sxs-lookup"><span data-stu-id="8c03b-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="8c03b-556">GetTodoItems yöntemini test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="8c03b-557">Bu öğretici, web API test etmek için Postacı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="8c03b-558">[Postacı'yı](https://www.getpostman.com/downloads/)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="8c03b-559">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-559">Start the web app.</span></span>
* <span data-ttu-id="8c03b-560">Postacı'yı başlat.</span><span class="sxs-lookup"><span data-stu-id="8c03b-560">Start Postman.</span></span>
* <span data-ttu-id="8c03b-561">**SSL sertifika doğrulamayı**devre dışı kılmış.</span><span class="sxs-lookup"><span data-stu-id="8c03b-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c03b-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8c03b-563">**Dosya** > **Ayarları** 'ndan **(Genel** sekmesi), **SSL sertifika doğrulamasını**devre dışı.</span><span class="sxs-lookup"><span data-stu-id="8c03b-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c03b-564">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c03b-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8c03b-565">**Postacı** > **Tercihleri** 'nden **(Genel** sekme), **SSL sertifika doğrulamasını**devre dışı.</span><span class="sxs-lookup"><span data-stu-id="8c03b-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="8c03b-566">Alternatif olarak, anahtarı seçin ve **Ayarlar'ı**seçin, ardından SSL sertifika doğrulamasını devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="8c03b-567">Denetleyiciyi test ettikten sonra SSL sertifika doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="8c03b-568">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-568">Create a new request.</span></span>
  * <span data-ttu-id="8c03b-569">HTTP yöntemini **GET**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="8c03b-570">İstek URL'sini `https://localhost:<port>/api/todo`' ' olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="8c03b-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="8c03b-571">Örneğin, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="8c03b-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="8c03b-572">**Postman'da iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="8c03b-573">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-573">Select **Send**.</span></span>

![Postacı ile İstek alın](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="8c03b-575">Oluşturma yöntemi ekle</span><span class="sxs-lookup"><span data-stu-id="8c03b-575">Add a Create method</span></span>

<span data-ttu-id="8c03b-576">`PostTodoItem` *Denetleyiciler/TodoController.cs*içine aşağıdaki yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="8c03b-577">Önceki kod, öznitelikte [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) belirtildiği gibi bir HTTP POST yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="8c03b-578">Yöntem, yapılacaklar öğesinin değerini HTTP isteğinin gövdesinden alır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="8c03b-579">Yöntem: `CreatedAtAction`</span><span class="sxs-lookup"><span data-stu-id="8c03b-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="8c03b-580">Başarılı olursa http 201 durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="8c03b-581">HTTP 201, sunucuda yeni bir kaynak oluşturan bir HTTP POST yönteminin standart yanıtıdır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="8c03b-582">Yanıta `Location` bir üstbilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="8c03b-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="8c03b-583">Üstbilgi, `Location` yeni oluşturulan yapılacaklar öğesinin URI'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="8c03b-584">Daha fazla bilgi için [bkz.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="8c03b-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="8c03b-585">`Location` Üstbilginin `GetTodoItem` URI'sini oluşturmak için eyleme başvurur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="8c03b-586">C# `nameof` anahtar `CreatedAtAction` kelimesi, çağrıdaki eylem adını sert kodlamaktan kaçınmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="8c03b-587">PostTodoItem yöntemini test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="8c03b-588">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-588">Build the project.</span></span>
* <span data-ttu-id="8c03b-589">Postacı'da HTTP yöntemini `POST`.</span><span class="sxs-lookup"><span data-stu-id="8c03b-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="8c03b-590">**Vücut** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="8c03b-591">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="8c03b-592">Türü **JSON (uygulama/json)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="8c03b-593">İstek gövdesinde yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="8c03b-594">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-594">Select **Send**.</span></span>

  ![Create request ile Postacı](first-web-api/_static/create.png)

  <span data-ttu-id="8c03b-596">405 Yöntemi İzin Verilmez hatası alırsanız, bu büyük olasılıkla `PostTodoItem` yöntemi ekledikten sonra projederleme değil sonucu.</span><span class="sxs-lookup"><span data-stu-id="8c03b-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="8c03b-597">Konum üstbilgisini URI test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-597">Test the location header URI</span></span>

* <span data-ttu-id="8c03b-598">**Yanıt** bölmesinde **Üstbilgi** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="8c03b-599">**Konum** üstbilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-599">Copy the **Location** header value:</span></span>

  ![Postacı konsolunun üstbilgi sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="8c03b-601">Yöntemi GET olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-601">Set the method to GET.</span></span>
* <span data-ttu-id="8c03b-602">URI yapıştırın (örneğin, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="8c03b-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="8c03b-603">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="8c03b-604">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="8c03b-605">Aşağıdaki `PutTodoItem` yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="8c03b-606">`PutTodoItem`benzer `PostTodoItem`, bu http PUT kullanır dışında.</span><span class="sxs-lookup"><span data-stu-id="8c03b-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="8c03b-607">Yanıt [204 (İçerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8c03b-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="8c03b-608">HTTP belirtimine göre, PUT isteği istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="8c03b-609">Kısmi güncelleştirmeleri desteklemek [için HTTP PATCH'i](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="8c03b-610">Arama hatası `PutTodoItem`alırsanız, `GET` veritabanında bir öğe olduğundan emin olmak için arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="8c03b-611">PutTodoItem yöntemini test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="8c03b-612">Bu örnek, uygulama her başlatıldığında başlatılması gereken bir bellek içi veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="8c03b-613">PUT araması yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="8c03b-614">PUT araması yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET'i arayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="8c03b-615">id = 1 olan yapılacaklar öğesini güncelleştirin ve adını "balık beslemek" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="8c03b-616">Aşağıdaki resim Postacı güncelleştirmesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-616">The following image shows the Postman update:</span></span>

![Postacı konsolu 204 (İçerik Yok) yanıtı gösteriyor](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="8c03b-618">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="8c03b-619">Aşağıdaki `DeleteTodoItem` yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="8c03b-620">Yanıt `DeleteTodoItem` [204 (İçerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="8c03b-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="8c03b-621">DeleteTodoItem yöntemini test edin</span><span class="sxs-lookup"><span data-stu-id="8c03b-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="8c03b-622">Yapılacaklar öğesini silmek için Postacı'yı kullanın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="8c03b-623">Yöntemi `DELETE`' ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="8c03b-624">Nesnenin URI'sini silmek için `https://localhost:5001/api/todo/1`ayarlayın (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="8c03b-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="8c03b-625">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-625">Select **Send**.</span></span>

<span data-ttu-id="8c03b-626">Örnek uygulama tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="8c03b-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="8c03b-627">Ancak, son öğe silindiğinde, API bir sonraki çağrıldığında model sınıfı oluşturucu tarafından yeni bir öğe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8c03b-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8c03b-628">JavaScript ile web API'sini arayın</span><span class="sxs-lookup"><span data-stu-id="8c03b-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="8c03b-629">Bu bölümde, web API'sını aramak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="8c03b-630">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-630">jQuery initiates the request.</span></span> <span data-ttu-id="8c03b-631">JavaScript, sayfayı web API'sının yanıtındaki ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="8c03b-632">Uygulamayı [statik dosyalara hizmet](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sağlayacak şekilde yapılandırın ve aşağıdaki vurgulanan kodla *Startup.cs* güncelleştirerek varsayılan [dosya eşlemesini etkinleştirin:](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)</span><span class="sxs-lookup"><span data-stu-id="8c03b-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="8c03b-633">Proje dizininde bir *wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8c03b-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="8c03b-634">*wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="8c03b-635">İçeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="8c03b-636">*wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8c03b-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="8c03b-637">İçeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8c03b-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="8c03b-638">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarında bir değişiklik gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="8c03b-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="8c03b-639">*Özellikleri Aç\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8c03b-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="8c03b-640">Uygulamayı `launchUrl` projenin varsayılan *dosyasıindex.html'de*&mdash;açmaya zorlamak için özelliği kaldırın.</span><span class="sxs-lookup"><span data-stu-id="8c03b-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="8c03b-641">Bu örnek, web API'sının tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="8c03b-642">Aşağıda API'ye yapılan çağrıların açıklamaları yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="8c03b-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="8c03b-643">Yapılacak lar listesi alın</span><span class="sxs-lookup"><span data-stu-id="8c03b-643">Get a list of to-do items</span></span>

<span data-ttu-id="8c03b-644">jQuery, yapılacaklar öğelerini temsil eden JSON'u döndüren web API'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="8c03b-645">İstek `success` başarılı olursa geri arama işlevi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="8c03b-646">Geri aramada, DOM yapılacaklar bilgileriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="8c03b-647">Yapılacaklar öğesi ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-647">Add a to-do item</span></span>

<span data-ttu-id="8c03b-648">jQuery, istek gövdesindeki yapılacaklar öğesiyle birlikte bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="8c03b-649">Ve `accepts` `contentType` seçenekler, alınan `application/json` ve gönderilen ortam türünü belirtmek için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="8c03b-650">Yapılacaklar öğesi [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON'a dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="8c03b-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="8c03b-651">API başarılı bir durum kodu `getData` döndürdüğünde, işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="8c03b-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="8c03b-652">Yapılacaklar öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8c03b-652">Update a to-do item</span></span>

<span data-ttu-id="8c03b-653">Yapılacaklar öğesini güncelleştirmek, bir öğe eklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="8c03b-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="8c03b-654">Maddenin `url` benzersiz tanımlayıcıeklemek için değişiklikler ve `type` . `PUT`</span><span class="sxs-lookup"><span data-stu-id="8c03b-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="8c03b-655">Yapılacaklar öğesini silme</span><span class="sxs-lookup"><span data-stu-id="8c03b-655">Delete a to-do item</span></span>

<span data-ttu-id="8c03b-656">Yapılacaklar öğesi nin silmesi, AJAX `type` çağrısındaki öğeyi `DELETE` ayarlayarak ve URL'de öğenin benzersiz tanımlayıcısını belirterek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8c03b-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="8c03b-657">Web API'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="8c03b-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="8c03b-658">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8c03b-658">Additional resources</span></span>

<span data-ttu-id="8c03b-659">[Bu öğretici için örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="8c03b-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="8c03b-660">[Nasıl indirilir bakın.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="8c03b-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="8c03b-661">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="8c03b-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="8c03b-662">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="8c03b-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
