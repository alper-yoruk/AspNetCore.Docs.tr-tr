---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 2383934070a65b8131e890a170186b736d3fcec0
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/28/2020
ms.locfileid: "86869997"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="0a5ab-103">Öğretici: ASP.NET Core bir Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="0a5ab-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="0a5ab-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Mike te son](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="0a5ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="0a5ab-105">Bu öğreticide, ASP.NET Core ile Web API 'SI oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a5ab-106">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a5ab-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-107">Create a web API project.</span></span>
> * <span data-ttu-id="0a5ab-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a5ab-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0a5ab-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0a5ab-111">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-111">Call the web API with Postman.</span></span>

<span data-ttu-id="0a5ab-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0a5ab-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="0a5ab-113">Overview</span></span>

<span data-ttu-id="0a5ab-114">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a5ab-115">API</span><span class="sxs-lookup"><span data-stu-id="0a5ab-115">API</span></span> | <span data-ttu-id="0a5ab-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="0a5ab-116">Description</span></span> | <span data-ttu-id="0a5ab-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-117">Request body</span></span> | <span data-ttu-id="0a5ab-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="0a5ab-119">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-119">Get all to-do items</span></span> | <span data-ttu-id="0a5ab-120">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-120">None</span></span> | <span data-ttu-id="0a5ab-121">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="0a5ab-122">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-122">Get an item by ID</span></span> | <span data-ttu-id="0a5ab-123">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-123">None</span></span> | <span data-ttu-id="0a5ab-124">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="0a5ab-125">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="0a5ab-125">Add a new item</span></span> | <span data-ttu-id="0a5ab-126">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-126">To-do item</span></span> | <span data-ttu-id="0a5ab-127">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="0a5ab-128">Mevcut bir öğeyi güncelleştir&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a5ab-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a5ab-129">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-129">To-do item</span></span> | <span data-ttu-id="0a5ab-130">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-130">None</span></span> |
|<span data-ttu-id="0a5ab-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a5ab-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a5ab-132">Öğe &nbsp; silme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a5ab-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a5ab-133">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-133">None</span></span> | <span data-ttu-id="0a5ab-134">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-134">None</span></span>|

<span data-ttu-id="0a5ab-135">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-135">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0a5ab-141">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0a5ab-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-144">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0a5ab-145">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0a5ab-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-147">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a5ab-148">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a5ab-149">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a5ab-150">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0a5ab-151">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-151">Select the **API** template and click **Create**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a5ab-154">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a5ab-155">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a5ab-156">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0a5ab-157">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0a5ab-158">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-158">The preceding commands:</span></span>

  * <span data-ttu-id="0a5ab-159">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0a5ab-160">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-161">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a5ab-162">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-162">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a5ab-164">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a5ab-165">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="0a5ab-167">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda en son .NET Core 3. x **hedef çerçevesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="0a5ab-168">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-168">Select **Next**.</span></span>

* <span data-ttu-id="0a5ab-169">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0a5ab-171">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="0a5ab-172">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-172">Test the API</span></span>

<span data-ttu-id="0a5ab-173">Proje şablonu bir API oluşturur `WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="0a5ab-174">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a5ab-176">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a5ab-177">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/WeatherForecast` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0a5ab-178">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0a5ab-179">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a5ab-181">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a5ab-182">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-183">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a5ab-184">Uygulamayı **başlatmak**  >  için**hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a5ab-185">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a5ab-186">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a5ab-187">`/WeatherForecast`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="0a5ab-188">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="0a5ab-189">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-189">Add a model class</span></span>

<span data-ttu-id="0a5ab-190">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a5ab-191">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-193">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0a5ab-194">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a5ab-195">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a5ab-196">*Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a5ab-197">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a5ab-198">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a5ab-200">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a5ab-201">`TodoItem` *Modeller* klasörüne aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-202">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a5ab-203">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-203">Right-click the project.</span></span> <span data-ttu-id="0a5ab-204">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a5ab-205">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-205">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a5ab-207">*Modeller* klasörüne sağ tıklayın ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a5ab-208">Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0a5ab-209">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="0a5ab-210">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a5ab-211">Model sınıfları projede herhangi bir yere gidebilir, ancak *modeller* klasörü kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0a5ab-212">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-212">Add a database context</span></span>

<span data-ttu-id="0a5ab-213">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a5ab-214">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="0a5ab-216">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-216">Add NuGet packages</span></span>

* <span data-ttu-id="0a5ab-217">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0a5ab-218">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="0a5ab-219">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0a5ab-220">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="0a5ab-221">Önceki yönergeleri kullanarak `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0a5ab-223">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="0a5ab-224">*Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a5ab-225">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-226">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a5ab-227">`TodoContext` *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a5ab-228">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0a5ab-229">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-229">Register the database context</span></span>

<span data-ttu-id="0a5ab-230">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a5ab-231">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a5ab-232">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0a5ab-233">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-233">The preceding code:</span></span>

* <span data-ttu-id="0a5ab-234">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a5ab-235">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a5ab-236">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0a5ab-237">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="0a5ab-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-239">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a5ab-240">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0a5ab-241">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="0a5ab-242">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0a5ab-243">**Model sınıfında** **TodoItem (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0a5ab-244">**Veri bağlamı sınıfında** **TodoContext (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0a5ab-245">**Ekle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-246">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a5ab-247">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0a5ab-248">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-248">The preceding commands:</span></span>

* <span data-ttu-id="0a5ab-249">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0a5ab-250">Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0a5ab-251">Yapı iskelesi `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0a5ab-252">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-252">The generated code:</span></span>

* <span data-ttu-id="0a5ab-253">Sınıfını [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="0a5ab-254">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a5ab-255">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="0a5ab-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a5ab-256">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a5ab-257">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="0a5ab-258">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="0a5ab-259">Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="0a5ab-260">API denetleyicileri `[action]` yol şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="0a5ab-261">`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="0a5ab-262">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="0a5ab-263">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="0a5ab-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="0a5ab-264">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0a5ab-265">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="0a5ab-266">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a5ab-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0a5ab-268">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="0a5ab-269">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a5ab-270">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0a5ab-271">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0a5ab-272">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a5ab-273">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a5ab-274">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0a5ab-275">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-275">Install Postman</span></span>

<span data-ttu-id="0a5ab-276">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a5ab-277">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0a5ab-278">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-278">Start the web app.</span></span>
* <span data-ttu-id="0a5ab-279">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-279">Start Postman.</span></span>
* <span data-ttu-id="0a5ab-280">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0a5ab-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="0a5ab-281">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0a5ab-282">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0a5ab-283">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="0a5ab-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0a5ab-284">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-284">Create a new request.</span></span>
* <span data-ttu-id="0a5ab-285">HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a5ab-286">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a5ab-287">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a5ab-288">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a5ab-289">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a5ab-290">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-290">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0a5ab-292">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-292">Test the location header URI</span></span>

* <span data-ttu-id="0a5ab-293">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a5ab-294">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-294">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="0a5ab-296">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-296">Set the method to GET.</span></span>
* <span data-ttu-id="0a5ab-297">URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0a5ab-298">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0a5ab-299">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="0a5ab-299">Examine the GET methods</span></span>

<span data-ttu-id="0a5ab-300">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0a5ab-301">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0a5ab-302">Örnek:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="0a5ab-303">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0a5ab-304">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-304">Test Get with Postman</span></span>

* <span data-ttu-id="0a5ab-305">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-305">Create a new request.</span></span>
* <span data-ttu-id="0a5ab-306">**Almak**için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0a5ab-307">İstek URL 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0a5ab-308">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0a5ab-309">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a5ab-310">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-310">Select **Send**.</span></span>

<span data-ttu-id="0a5ab-311">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-311">This app uses an in-memory database.</span></span> <span data-ttu-id="0a5ab-312">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0a5ab-313">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0a5ab-314">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="0a5ab-314">Routing and URL paths</span></span>

<span data-ttu-id="0a5ab-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a5ab-316">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a5ab-317">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0a5ab-318">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a5ab-319">Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0a5ab-320">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a5ab-321">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a5ab-322">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-322">This sample doesn't use a template.</span></span> <span data-ttu-id="0a5ab-323">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a5ab-324">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a5ab-325">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0a5ab-326">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-326">Return values</span></span>

<span data-ttu-id="0a5ab-327">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a5ab-328">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a5ab-329">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a5ab-330">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a5ab-331">`ActionResult`dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a5ab-332">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a5ab-333">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="0a5ab-334">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a5ab-335">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0a5ab-336">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-336">The PutTodoItem method</span></span>

<span data-ttu-id="0a5ab-337">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0a5ab-338">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a5ab-339">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a5ab-340">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a5ab-341">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a5ab-342">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0a5ab-343">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="0a5ab-344">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a5ab-345">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a5ab-346">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a5ab-347">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a5ab-348">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-348">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0a5ab-350">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="0a5ab-351">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0a5ab-352">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0a5ab-353">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a5ab-354">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a5ab-355">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0a5ab-356">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="0a5ab-357">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="0a5ab-357">Prevent over-posting</span></span>

<span data-ttu-id="0a5ab-358">Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="0a5ab-359">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="0a5ab-360">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="0a5ab-361">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="0a5ab-362">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="0a5ab-363">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-363">A DTO may be used to:</span></span>

* <span data-ttu-id="0a5ab-364">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-364">Prevent over-posting.</span></span>
* <span data-ttu-id="0a5ab-365">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="0a5ab-366">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="0a5ab-367">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="0a5ab-368">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="0a5ab-369">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="0a5ab-370">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="0a5ab-371">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="0a5ab-372">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="0a5ab-373">`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="0a5ab-374">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0a5ab-375">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="0a5ab-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="0a5ab-376">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a5ab-377">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a5ab-378">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-378">Create a web API project.</span></span>
> * <span data-ttu-id="0a5ab-379">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0a5ab-380">Denetleyici ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-380">Add a controller.</span></span>
> * <span data-ttu-id="0a5ab-381">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="0a5ab-382">Yönlendirme ve URL yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="0a5ab-383">Dönüş değerlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-383">Specify return values.</span></span>
> * <span data-ttu-id="0a5ab-384">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="0a5ab-385">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="0a5ab-386">Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="0a5ab-387">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="0a5ab-387">Overview</span></span>

<span data-ttu-id="0a5ab-388">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0a5ab-389">API</span><span class="sxs-lookup"><span data-stu-id="0a5ab-389">API</span></span> | <span data-ttu-id="0a5ab-390">Açıklama</span><span class="sxs-lookup"><span data-stu-id="0a5ab-390">Description</span></span> | <span data-ttu-id="0a5ab-391">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-391">Request body</span></span> | <span data-ttu-id="0a5ab-392">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0a5ab-393">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-393">GET /api/TodoItems</span></span> | <span data-ttu-id="0a5ab-394">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-394">Get all to-do items</span></span> | <span data-ttu-id="0a5ab-395">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-395">None</span></span> | <span data-ttu-id="0a5ab-396">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-396">Array of to-do items</span></span>|
|<span data-ttu-id="0a5ab-397">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0a5ab-398">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="0a5ab-398">Get an item by ID</span></span> | <span data-ttu-id="0a5ab-399">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-399">None</span></span> | <span data-ttu-id="0a5ab-400">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-400">To-do item</span></span>|
|<span data-ttu-id="0a5ab-401">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0a5ab-401">POST /api/TodoItems</span></span> | <span data-ttu-id="0a5ab-402">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="0a5ab-402">Add a new item</span></span> | <span data-ttu-id="0a5ab-403">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-403">To-do item</span></span> | <span data-ttu-id="0a5ab-404">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-404">To-do item</span></span> |
|<span data-ttu-id="0a5ab-405">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="0a5ab-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0a5ab-406">Mevcut bir öğeyi güncelleştir&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a5ab-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0a5ab-407">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="0a5ab-407">To-do item</span></span> | <span data-ttu-id="0a5ab-408">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-408">None</span></span> |
|<span data-ttu-id="0a5ab-409">/Api/TodoItems/{id} &nbsp; Sil&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a5ab-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a5ab-410">Öğe &nbsp; silme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="0a5ab-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0a5ab-411">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-411">None</span></span> | <span data-ttu-id="0a5ab-412">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-412">None</span></span>|

<span data-ttu-id="0a5ab-413">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-413">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0a5ab-419">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0a5ab-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-422">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0a5ab-423">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0a5ab-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-425">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0a5ab-426">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0a5ab-427">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0a5ab-428">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="0a5ab-429">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="0a5ab-430">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-430">**Don't** select **Enable Docker Support**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a5ab-433">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a5ab-434">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0a5ab-435">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="0a5ab-436">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="0a5ab-437">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-438">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a5ab-439">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-439">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0a5ab-441">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="0a5ab-442">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="0a5ab-443">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda en son .NET Core 2. x **hedef çerçevesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="0a5ab-444">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-444">Select **Next**.</span></span>

* <span data-ttu-id="0a5ab-445">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="0a5ab-447">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-447">Test the API</span></span>

<span data-ttu-id="0a5ab-448">Proje şablonu bir API oluşturur `values` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-448">The project template creates a `values` API.</span></span> <span data-ttu-id="0a5ab-449">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a5ab-451">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a5ab-452">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/api/values` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0a5ab-453">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0a5ab-454">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a5ab-456">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0a5ab-457">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-458">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a5ab-459">Uygulamayı **başlatmak**  >  için**hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0a5ab-460">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0a5ab-461">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0a5ab-462">`/api/values`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="0a5ab-463">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="0a5ab-464">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-464">Add a model class</span></span>

<span data-ttu-id="0a5ab-465">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0a5ab-466">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-468">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0a5ab-469">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a5ab-470">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="0a5ab-471">*Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a5ab-472">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0a5ab-473">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a5ab-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a5ab-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a5ab-475">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0a5ab-476">`TodoItem` *Modeller* klasörüne aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-477">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a5ab-478">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-478">Right-click the project.</span></span> <span data-ttu-id="0a5ab-479">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a5ab-480">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-480">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0a5ab-482">*Modeller* klasörüne sağ tıklayın ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0a5ab-483">Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0a5ab-484">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="0a5ab-485">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0a5ab-486">Model sınıfları projede herhangi bir yere gidebilir, ancak *modeller* klasörü kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0a5ab-487">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-487">Add a database context</span></span>

<span data-ttu-id="0a5ab-488">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0a5ab-489">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-491">*Modeller* klasörüne sağ tıklayın ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0a5ab-492">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-493">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a5ab-494">`TodoContext` *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0a5ab-495">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0a5ab-496">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-496">Register the database context</span></span>

<span data-ttu-id="0a5ab-497">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0a5ab-498">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="0a5ab-499">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="0a5ab-500">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-500">The preceding code:</span></span>

* <span data-ttu-id="0a5ab-501">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0a5ab-502">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0a5ab-503">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0a5ab-504">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-506">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0a5ab-507">**Add** > **Yeni öğe**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="0a5ab-508">**Yeni öğe Ekle** iletişim kutusunda, **API denetleyici sınıfı** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="0a5ab-509">Sınıfı *TodoController*olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Arama kutusu ve Web API denetleyicisi seçiliyken denetleyiciyi içeren yeni öğe iletişim kutusu Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-511">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a5ab-512">*Denetleyiciler* klasöründe adlı bir sınıf oluşturun `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="0a5ab-513">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="0a5ab-514">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-514">The preceding code:</span></span>

* <span data-ttu-id="0a5ab-515">Yöntemler olmadan bir API denetleyici sınıfı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0a5ab-516">Sınıfını [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="0a5ab-517">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0a5ab-518">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="0a5ab-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0a5ab-519">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0a5ab-520">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="0a5ab-521">Veritabanı boşsa veritabanına adlı bir öğe ekler `Item1` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="0a5ab-522">Bu kod oluşturucuda yer aldığı için, her yeni HTTP isteği olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="0a5ab-523">Tüm öğeleri silerseniz, `Item1` BIR API yönteminin bir sonraki çağrılışında Oluşturucu yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="0a5ab-524">Bu nedenle, aslında çalışırken silme işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="0a5ab-525">Get yöntemleri ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-525">Add Get methods</span></span>

<span data-ttu-id="0a5ab-526">Yapılacaklar öğelerini alan bir API sağlamak için aşağıdaki yöntemleri `TodoController` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="0a5ab-527">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="0a5ab-528">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-528">Stop the app if it's still running.</span></span> <span data-ttu-id="0a5ab-529">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="0a5ab-530">Bir tarayıcıdan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="0a5ab-531">Örnek:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="0a5ab-532">Aşağıdaki HTTP yanıtı, çağrısı tarafından oluşturulur `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="0a5ab-533">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="0a5ab-533">Routing and URL paths</span></span>

<span data-ttu-id="0a5ab-534">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0a5ab-535">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0a5ab-536">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="0a5ab-537">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0a5ab-538">Bu örnek için denetleyici sınıf adı **Todo**Controller olduğundan, denetleyici adı "Todo" olur.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="0a5ab-539">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0a5ab-540">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0a5ab-541">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-541">This sample doesn't use a template.</span></span> <span data-ttu-id="0a5ab-542">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0a5ab-543">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0a5ab-544">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0a5ab-545">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="0a5ab-545">Return values</span></span>

<span data-ttu-id="0a5ab-546">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0a5ab-547">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0a5ab-548">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0a5ab-549">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0a5ab-550">`ActionResult`dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0a5ab-551">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0a5ab-552">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="0a5ab-553">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0a5ab-554">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="0a5ab-555">GetTodoItems yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="0a5ab-556">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0a5ab-557">[Postman](https://www.getpostman.com/downloads/)'yi yükleme.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="0a5ab-558">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-558">Start the web app.</span></span>
* <span data-ttu-id="0a5ab-559">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-559">Start Postman.</span></span>
* <span data-ttu-id="0a5ab-560">**SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a5ab-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a5ab-562">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a5ab-563">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a5ab-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a5ab-564">**Postman**  >  **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="0a5ab-565">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="0a5ab-566">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="0a5ab-567">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-567">Create a new request.</span></span>
  * <span data-ttu-id="0a5ab-568">**Almak**için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="0a5ab-569">İstek URL 'sini olarak ayarlayın `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="0a5ab-570">Örneğin, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="0a5ab-571">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0a5ab-572">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-572">Select **Send**.</span></span>

![Get isteği ile Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="0a5ab-574">Create yöntemi ekle</span><span class="sxs-lookup"><span data-stu-id="0a5ab-574">Add a Create method</span></span>

<span data-ttu-id="0a5ab-575">`PostTodoItem` *Controllers/TodoController. cs*içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0a5ab-576">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="0a5ab-577">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0a5ab-578">`CreatedAtAction`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="0a5ab-579">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="0a5ab-580">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0a5ab-581">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="0a5ab-582">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar ÖĞESININ URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0a5ab-583">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0a5ab-584">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0a5ab-585">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="0a5ab-586">PostTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="0a5ab-587">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-587">Build the project.</span></span>
* <span data-ttu-id="0a5ab-588">Postman 'da HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0a5ab-589">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="0a5ab-590">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0a5ab-591">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0a5ab-592">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0a5ab-593">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-593">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/create.png)

  <span data-ttu-id="0a5ab-595">Bir 405 yöntemine Izin verilmiyor hatası alırsanız, yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0a5ab-596">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-596">Test the location header URI</span></span>

* <span data-ttu-id="0a5ab-597">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0a5ab-598">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-598">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="0a5ab-600">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-600">Set the method to GET.</span></span>
* <span data-ttu-id="0a5ab-601">URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/Todo/2` ).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="0a5ab-602">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="0a5ab-603">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="0a5ab-604">Aşağıdaki yöntemi ekleyin `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0a5ab-605">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0a5ab-606">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0a5ab-607">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0a5ab-608">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0a5ab-609">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0a5ab-610">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="0a5ab-611">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="0a5ab-612">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0a5ab-613">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0a5ab-614">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0a5ab-615">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-615">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="0a5ab-617">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="0a5ab-618">Aşağıdaki yöntemi ekleyin `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0a5ab-619">`DeleteTodoItem`Yanıt 204 ' dir [(içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0a5ab-620">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0a5ab-621">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0a5ab-622">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0a5ab-623">Silinecek nesnenin URI 'sini ayarlayın (örneğin, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="0a5ab-624">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-624">Select **Send**.</span></span>

<span data-ttu-id="0a5ab-625">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="0a5ab-626">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0a5ab-627">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="0a5ab-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="0a5ab-628">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="0a5ab-629">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-629">jQuery initiates the request.</span></span> <span data-ttu-id="0a5ab-630">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="0a5ab-631">Uygulamayı [statik dosyalara sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="0a5ab-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="0a5ab-632">Proje dizininde bir *Wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="0a5ab-633">*Wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="0a5ab-634">İçeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="0a5ab-635">*Wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="0a5ab-636">Dosyanın içeriğini aşağıdaki kod ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="0a5ab-637">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="0a5ab-638">*ÜzerindeProperties\launchSettings.js*açın.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="0a5ab-639">`launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html*'de açmaya zorlamak için özelliği kaldırın &mdash; .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="0a5ab-640">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="0a5ab-641">API çağrılarının açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="0a5ab-642">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="0a5ab-642">Get a list of to-do items</span></span>

<span data-ttu-id="0a5ab-643">jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="0a5ab-644">`success`Geri çağırma işlevi, istek başarılı olursa çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="0a5ab-645">Geri aramada, DOM, yapılacaklar bilgileriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="0a5ab-646">Yapılacaklar öğesi ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-646">Add a to-do item</span></span>

<span data-ttu-id="0a5ab-647">jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="0a5ab-648">`accepts`Ve `contentType` seçenekleri, `application/json` alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="0a5ab-649">Yapılacaklar öğesi [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON 'a dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="0a5ab-650">API başarılı bir durum kodu döndürdüğünde, `getData` Işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="0a5ab-651">Yapılacaklar öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-651">Update a to-do item</span></span>

<span data-ttu-id="0a5ab-652">Bir yapılacaklar öğesinin güncelleştirilmesi, bir tane eklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="0a5ab-653">`url`Öğenin benzersiz tanımlayıcısını ekleme değişiklikleri ve öğesi `type` `PUT` .</span><span class="sxs-lookup"><span data-stu-id="0a5ab-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="0a5ab-654">Bir yapılacaklar öğesini silme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-654">Delete a to-do item</span></span>

<span data-ttu-id="0a5ab-655">Bir yapılacaklar öğesinin silinmesi, `type` Ajax çağrısının üzerine ayarlanarak `DELETE` ve öğenin URL 'sindeki benzersiz tanımlayıcısını belirterek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0a5ab-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="0a5ab-656">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="0a5ab-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="0a5ab-657">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0a5ab-657">Additional resources</span></span>

<span data-ttu-id="0a5ab-658">[Bu öğretici için örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="0a5ab-659">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0a5ab-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="0a5ab-660">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="0a5ab-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="0a5ab-661">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="0a5ab-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
