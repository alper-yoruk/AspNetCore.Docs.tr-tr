---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ebce9f2f4992d83c6b28edb5c771cdfc8a7a0b6a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080387"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="2a46f-103">Öğretici: ASP.NET Core bir Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="2a46f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="2a46f-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Mike te son](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="2a46f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="2a46f-105">Bu öğreticide, ASP.NET Core ile Web API 'SI oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a46f-106">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="2a46f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a46f-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-107">Create a web API project.</span></span>
> * <span data-ttu-id="2a46f-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2a46f-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="2a46f-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="2a46f-111">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="2a46f-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="2a46f-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="2a46f-113">Overview</span></span>

<span data-ttu-id="2a46f-114">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2a46f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2a46f-115">API</span><span class="sxs-lookup"><span data-stu-id="2a46f-115">API</span></span> | <span data-ttu-id="2a46f-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a46f-116">Description</span></span> | <span data-ttu-id="2a46f-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-117">Request body</span></span> | <span data-ttu-id="2a46f-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="2a46f-119">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="2a46f-119">Get all to-do items</span></span> | <span data-ttu-id="2a46f-120">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-120">None</span></span> | <span data-ttu-id="2a46f-121">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="2a46f-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="2a46f-122">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="2a46f-122">Get an item by ID</span></span> | <span data-ttu-id="2a46f-123">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-123">None</span></span> | <span data-ttu-id="2a46f-124">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="2a46f-125">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="2a46f-125">Add a new item</span></span> | <span data-ttu-id="2a46f-126">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-126">To-do item</span></span> | <span data-ttu-id="2a46f-127">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="2a46f-128">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a46f-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2a46f-129">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-129">To-do item</span></span> | <span data-ttu-id="2a46f-130">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-130">None</span></span> |
|<span data-ttu-id="2a46f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a46f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a46f-132">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a46f-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a46f-133">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-133">None</span></span> | <span data-ttu-id="2a46f-134">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-134">None</span></span>|

<span data-ttu-id="2a46f-135">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-135">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="2a46f-141">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="2a46f-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-144">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2a46f-145">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="2a46f-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-147">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2a46f-148">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="2a46f-149">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="2a46f-150">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="2a46f-151">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-151">Select the **API** template and click **Create**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a46f-154">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2a46f-155">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2a46f-156">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="2a46f-157">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="2a46f-158">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="2a46f-158">The preceding commands:</span></span>

  * <span data-ttu-id="2a46f-159">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="2a46f-160">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-161">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a46f-162">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-162">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2a46f-164">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="2a46f-165">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="2a46f-167">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 3. x **hedef çerçevesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="2a46f-168">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-168">Select **Next**.</span></span>

* <span data-ttu-id="2a46f-169">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="2a46f-171">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="2a46f-172">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-172">Test the API</span></span>

<span data-ttu-id="2a46f-173">Proje şablonu bir API oluşturur `WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="2a46f-174">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a46f-176">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a46f-177">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/WeatherForecast` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="2a46f-178">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="2a46f-179">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a46f-181">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a46f-182">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-183">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a46f-184">Uygulamayı **başlatmak**  >  için**hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2a46f-185">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2a46f-186">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="2a46f-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2a46f-187">`/WeatherForecast`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="2a46f-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="2a46f-188">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="2a46f-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="2a46f-189">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-189">Add a model class</span></span>

<span data-ttu-id="2a46f-190">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2a46f-191">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-193">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="2a46f-194">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2a46f-195">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="2a46f-196">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="2a46f-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2a46f-197">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="2a46f-198">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a46f-200">Adlı bir klasör ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="2a46f-201">`TodoItem` *Models* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-202">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a46f-203">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-203">Right-click the project.</span></span> <span data-ttu-id="2a46f-204">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2a46f-205">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-205">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2a46f-207">Klasöre sağ tıklayın *Models* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="2a46f-208">Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="2a46f-209">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="2a46f-210">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2a46f-211">Model sınıfları projede herhangi bir yere gidebilir, ancak *Models* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="2a46f-212">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-212">Add a database context</span></span>

<span data-ttu-id="2a46f-213">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2a46f-214">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="2a46f-216">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-216">Add NuGet packages</span></span>

* <span data-ttu-id="2a46f-217">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="2a46f-218">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="2a46f-219">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="2a46f-220">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="2a46f-221">Yukarıdaki yönergeleri kullanarak **Microsoft. EntityFrameworkCore. InMemory** NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="2a46f-223">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="2a46f-224">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="2a46f-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2a46f-225">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-226">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a46f-227">Klasöre bir `TodoContext` sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="2a46f-228">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="2a46f-229">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="2a46f-229">Register the database context</span></span>

<span data-ttu-id="2a46f-230">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a46f-231">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="2a46f-232">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="2a46f-233">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2a46f-233">The preceding code:</span></span>

* <span data-ttu-id="2a46f-234">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2a46f-235">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2a46f-236">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="2a46f-237">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="2a46f-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-239">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2a46f-240">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2a46f-241">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="2a46f-242">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="2a46f-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="2a46f-243">**Model sınıfında** **TodoItem (TodoApi. Models )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="2a46f-244">**Veri bağlamı sınıfında** **TodoContext (TodoApi. Models )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="2a46f-245">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-246">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a46f-247">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="2a46f-248">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="2a46f-248">The preceding commands:</span></span>

* <span data-ttu-id="2a46f-249">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="2a46f-250">Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="2a46f-251">Yapı iskelesi `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="2a46f-252">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="2a46f-252">The generated code:</span></span>

* <span data-ttu-id="2a46f-253">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2a46f-254">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2a46f-255">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="2a46f-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2a46f-256">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2a46f-257">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="2a46f-258">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="2a46f-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="2a46f-259">Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="2a46f-260">API denetleyicileri `[action]` yol şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="2a46f-261">`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="2a46f-262">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2a46f-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="2a46f-263">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="2a46f-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="2a46f-264">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="2a46f-265">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="2a46f-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2a46f-266">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2a46f-267">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="2a46f-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a46f-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2a46f-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="2a46f-269">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="2a46f-270">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2a46f-271">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="2a46f-272">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="2a46f-273">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="2a46f-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2a46f-274">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2a46f-275">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="2a46f-276">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-276">Install Postman</span></span>

<span data-ttu-id="2a46f-277">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2a46f-278">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="2a46f-279">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-279">Start the web app.</span></span>
* <span data-ttu-id="2a46f-280">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-280">Start Postman.</span></span>
* <span data-ttu-id="2a46f-281">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="2a46f-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="2a46f-282">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="2a46f-283">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="2a46f-284">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="2a46f-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="2a46f-285">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-285">Create a new request.</span></span>
* <span data-ttu-id="2a46f-286">HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2a46f-287">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2a46f-288">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="2a46f-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2a46f-289">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="2a46f-290">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2a46f-291">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="2a46f-292">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2a46f-293">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-293">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="2a46f-295">Konum üstbilgisi URI 'sini Postman ile test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="2a46f-296">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2a46f-297">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-297">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="2a46f-299">HTTP yöntemini olarak ayarlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="2a46f-300">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="2a46f-301">Örneğin, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="2a46f-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="2a46f-302">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="2a46f-303">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="2a46f-303">Examine the GET methods</span></span>

<span data-ttu-id="2a46f-304">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="2a46f-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="2a46f-305">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="2a46f-306">Örnek:</span><span class="sxs-lookup"><span data-stu-id="2a46f-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="2a46f-307">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="2a46f-308">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="2a46f-308">Test Get with Postman</span></span>

* <span data-ttu-id="2a46f-309">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-309">Create a new request.</span></span>
* <span data-ttu-id="2a46f-310">**Almak**için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="2a46f-311">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2a46f-312">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="2a46f-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2a46f-313">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2a46f-314">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-314">Select **Send**.</span></span>

<span data-ttu-id="2a46f-315">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-315">This app uses an in-memory database.</span></span> <span data-ttu-id="2a46f-316">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="2a46f-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="2a46f-317">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="2a46f-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="2a46f-318">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="2a46f-318">Routing and URL paths</span></span>

<span data-ttu-id="2a46f-319">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2a46f-320">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2a46f-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2a46f-321">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="2a46f-322">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2a46f-323">Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="2a46f-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="2a46f-324">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2a46f-325">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2a46f-326">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="2a46f-326">This sample doesn't use a template.</span></span> <span data-ttu-id="2a46f-327">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="2a46f-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a46f-328">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2a46f-329">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="2a46f-330">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="2a46f-330">Return values</span></span>

<span data-ttu-id="2a46f-331">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="2a46f-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2a46f-332">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2a46f-333">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2a46f-334">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2a46f-335">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2a46f-336">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="2a46f-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2a46f-337">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2a46f-338">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2a46f-339">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="2a46f-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="2a46f-340">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="2a46f-340">The PutTodoItem method</span></span>

<span data-ttu-id="2a46f-341">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="2a46f-342">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2a46f-343">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="2a46f-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2a46f-344">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2a46f-345">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2a46f-346">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="2a46f-347">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="2a46f-348">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2a46f-349">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2a46f-350">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2a46f-351">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2a46f-352">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2a46f-352">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="2a46f-354">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="2a46f-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="2a46f-355">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="2a46f-356">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="2a46f-357">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2a46f-358">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2a46f-359">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="2a46f-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="2a46f-360">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="2a46f-361">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="2a46f-361">Prevent over-posting</span></span>

<span data-ttu-id="2a46f-362">Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="2a46f-363">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="2a46f-364">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="2a46f-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="2a46f-365">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="2a46f-366">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="2a46f-367">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2a46f-367">A DTO may be used to:</span></span>

* <span data-ttu-id="2a46f-368">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-368">Prevent over-posting.</span></span>
* <span data-ttu-id="2a46f-369">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="2a46f-370">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="2a46f-371">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="2a46f-372">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="2a46f-373">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="2a46f-374">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="2a46f-375">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="2a46f-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="2a46f-376">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2a46f-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="2a46f-377">`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="2a46f-378">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="2a46f-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2a46f-379">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="2a46f-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="2a46f-380">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="2a46f-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a46f-381">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="2a46f-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a46f-382">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-382">Create a web API project.</span></span>
> * <span data-ttu-id="2a46f-383">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2a46f-384">Denetleyici ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-384">Add a controller.</span></span>
> * <span data-ttu-id="2a46f-385">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="2a46f-386">Yönlendirme ve URL yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="2a46f-387">Dönüş değerlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-387">Specify return values.</span></span>
> * <span data-ttu-id="2a46f-388">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="2a46f-389">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="2a46f-390">Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="2a46f-391">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="2a46f-391">Overview</span></span>

<span data-ttu-id="2a46f-392">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2a46f-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2a46f-393">API</span><span class="sxs-lookup"><span data-stu-id="2a46f-393">API</span></span> | <span data-ttu-id="2a46f-394">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a46f-394">Description</span></span> | <span data-ttu-id="2a46f-395">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-395">Request body</span></span> | <span data-ttu-id="2a46f-396">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="2a46f-397">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="2a46f-397">GET /api/TodoItems</span></span> | <span data-ttu-id="2a46f-398">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="2a46f-398">Get all to-do items</span></span> | <span data-ttu-id="2a46f-399">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-399">None</span></span> | <span data-ttu-id="2a46f-400">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="2a46f-400">Array of to-do items</span></span>|
|<span data-ttu-id="2a46f-401">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="2a46f-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="2a46f-402">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="2a46f-402">Get an item by ID</span></span> | <span data-ttu-id="2a46f-403">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-403">None</span></span> | <span data-ttu-id="2a46f-404">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-404">To-do item</span></span>|
|<span data-ttu-id="2a46f-405">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="2a46f-405">POST /api/TodoItems</span></span> | <span data-ttu-id="2a46f-406">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="2a46f-406">Add a new item</span></span> | <span data-ttu-id="2a46f-407">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-407">To-do item</span></span> | <span data-ttu-id="2a46f-408">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-408">To-do item</span></span> |
|<span data-ttu-id="2a46f-409">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="2a46f-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="2a46f-410">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a46f-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2a46f-411">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="2a46f-411">To-do item</span></span> | <span data-ttu-id="2a46f-412">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-412">None</span></span> |
|<span data-ttu-id="2a46f-413">/Api/TodoItems/{id} &nbsp; Sil &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a46f-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a46f-414">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a46f-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a46f-415">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-415">None</span></span> | <span data-ttu-id="2a46f-416">Yok</span><span class="sxs-lookup"><span data-stu-id="2a46f-416">None</span></span>|

<span data-ttu-id="2a46f-417">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-417">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="2a46f-423">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="2a46f-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-426">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2a46f-427">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="2a46f-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-429">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2a46f-430">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="2a46f-431">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="2a46f-432">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="2a46f-433">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="2a46f-434">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="2a46f-434">**Don't** select **Enable Docker Support**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a46f-437">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2a46f-438">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2a46f-439">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="2a46f-440">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="2a46f-441">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-442">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a46f-443">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-443">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2a46f-445">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="2a46f-446">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="2a46f-447">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 2. x **hedef çerçevesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="2a46f-448">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-448">Select **Next**.</span></span>

* <span data-ttu-id="2a46f-449">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="2a46f-451">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-451">Test the API</span></span>

<span data-ttu-id="2a46f-452">Proje şablonu bir API oluşturur `values` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-452">The project template creates a `values` API.</span></span> <span data-ttu-id="2a46f-453">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a46f-455">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a46f-456">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/api/values` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="2a46f-457">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="2a46f-458">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a46f-460">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a46f-461">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-462">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a46f-463">Uygulamayı **başlatmak**  >  için**hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2a46f-464">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2a46f-465">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="2a46f-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2a46f-466">`/api/values`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="2a46f-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="2a46f-467">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="2a46f-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="2a46f-468">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-468">Add a model class</span></span>

<span data-ttu-id="2a46f-469">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2a46f-470">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-472">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="2a46f-473">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2a46f-474">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="2a46f-475">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="2a46f-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2a46f-476">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="2a46f-477">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a46f-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a46f-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a46f-479">Adlı bir klasör ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="2a46f-480">`TodoItem` *Models* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-481">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a46f-482">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-482">Right-click the project.</span></span> <span data-ttu-id="2a46f-483">**Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2a46f-484">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-484">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2a46f-486">Klasöre sağ tıklayın *Models* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="2a46f-487">Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="2a46f-488">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="2a46f-489">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2a46f-490">Model sınıfları projede herhangi bir yere gidebilir, ancak *Models* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="2a46f-491">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-491">Add a database context</span></span>

<span data-ttu-id="2a46f-492">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2a46f-493">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-495">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="2a46f-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2a46f-496">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-497">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a46f-498">Klasöre bir `TodoContext` sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="2a46f-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="2a46f-499">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="2a46f-500">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="2a46f-500">Register the database context</span></span>

<span data-ttu-id="2a46f-501">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a46f-502">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="2a46f-503">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="2a46f-504">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2a46f-504">The preceding code:</span></span>

* <span data-ttu-id="2a46f-505">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2a46f-506">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2a46f-507">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="2a46f-508">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-510">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2a46f-511">**Add** > **Yeni öğe**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="2a46f-512">**Yeni öğe Ekle** iletişim kutusunda, **API denetleyici sınıfı** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="2a46f-513">Sınıfı *TodoController*olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![Arama kutusu ve Web API denetleyicisi seçiliyken denetleyiciyi içeren yeni öğe iletişim kutusu Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-515">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a46f-516">*Denetleyiciler* klasöründe adlı bir sınıf oluşturun `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="2a46f-517">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="2a46f-518">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2a46f-518">The preceding code:</span></span>

* <span data-ttu-id="2a46f-519">Yöntemler olmadan bir API denetleyici sınıfı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="2a46f-520">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2a46f-521">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2a46f-522">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="2a46f-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2a46f-523">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2a46f-524">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="2a46f-525">Veritabanı boşsa veritabanına adlı bir öğe ekler `Item1` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="2a46f-526">Bu kod oluşturucuda yer aldığı için, her yeni HTTP isteği olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="2a46f-527">Tüm öğeleri silerseniz, `Item1` BIR API yönteminin bir sonraki çağrılışında Oluşturucu yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2a46f-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="2a46f-528">Bu nedenle, aslında çalışırken silme işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="2a46f-529">Get yöntemleri ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-529">Add Get methods</span></span>

<span data-ttu-id="2a46f-530">Yapılacaklar öğelerini alan bir API sağlamak için aşağıdaki yöntemleri `TodoController` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="2a46f-531">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="2a46f-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="2a46f-532">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-532">Stop the app if it's still running.</span></span> <span data-ttu-id="2a46f-533">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="2a46f-534">Bir tarayıcıdan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="2a46f-535">Örnek:</span><span class="sxs-lookup"><span data-stu-id="2a46f-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="2a46f-536">Aşağıdaki HTTP yanıtı, çağrısı tarafından oluşturulur `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="2a46f-537">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="2a46f-537">Routing and URL paths</span></span>

<span data-ttu-id="2a46f-538">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2a46f-539">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2a46f-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2a46f-540">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="2a46f-541">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2a46f-542">Bu örnek için denetleyici sınıf adı **Todo**Controller olduğundan, denetleyici adı "Todo" olur.</span><span class="sxs-lookup"><span data-stu-id="2a46f-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="2a46f-543">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2a46f-544">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2a46f-545">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="2a46f-545">This sample doesn't use a template.</span></span> <span data-ttu-id="2a46f-546">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="2a46f-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a46f-547">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2a46f-548">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="2a46f-549">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="2a46f-549">Return values</span></span>

<span data-ttu-id="2a46f-550">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="2a46f-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2a46f-551">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2a46f-552">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2a46f-553">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2a46f-554">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2a46f-555">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="2a46f-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2a46f-556">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2a46f-557">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2a46f-558">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="2a46f-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="2a46f-559">GetTodoItems yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="2a46f-560">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2a46f-561">[Postman](https://www.getpostman.com/downloads/)'yi yükleme.</span><span class="sxs-lookup"><span data-stu-id="2a46f-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="2a46f-562">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-562">Start the web app.</span></span>
* <span data-ttu-id="2a46f-563">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-563">Start Postman.</span></span>
* <span data-ttu-id="2a46f-564">**SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a46f-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a46f-566">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a46f-567">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a46f-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a46f-568">**Postman**  >  **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="2a46f-569">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="2a46f-570">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="2a46f-571">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-571">Create a new request.</span></span>
  * <span data-ttu-id="2a46f-572">**Almak**için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="2a46f-573">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="2a46f-574">Örneğin, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="2a46f-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="2a46f-575">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2a46f-576">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-576">Select **Send**.</span></span>

![Get isteği ile Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="2a46f-578">Create yöntemi ekle</span><span class="sxs-lookup"><span data-stu-id="2a46f-578">Add a Create method</span></span>

<span data-ttu-id="2a46f-579">`PostTodoItem` *Controllers/TodoController. cs*içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="2a46f-580">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="2a46f-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2a46f-581">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2a46f-582">`CreatedAtAction`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2a46f-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="2a46f-583">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="2a46f-584">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2a46f-585">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="2a46f-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="2a46f-586">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar ÖĞESININ URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="2a46f-587">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="2a46f-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2a46f-588">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2a46f-589">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="2a46f-590">PostTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="2a46f-591">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-591">Build the project.</span></span>
* <span data-ttu-id="2a46f-592">Postman 'da HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2a46f-593">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="2a46f-594">Örneğin, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="2a46f-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="2a46f-595">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="2a46f-596">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2a46f-597">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="2a46f-598">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2a46f-599">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-599">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/create.png)

  <span data-ttu-id="2a46f-601">Bir 405 yöntemine Izin verilmiyor hatası alırsanız, yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="2a46f-602">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-602">Test the location header URI</span></span>

* <span data-ttu-id="2a46f-603">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2a46f-604">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-604">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="2a46f-606">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-606">Set the method to GET.</span></span>
<span data-ttu-id="2a46f-607">\* URI değerini olarak ayarlayın  `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="2a46f-608">Örneğin,  `https://localhost:5001/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="2a46f-609">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="2a46f-610">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="2a46f-611">Aşağıdaki yöntemi ekleyin `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="2a46f-612">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2a46f-613">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="2a46f-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2a46f-614">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2a46f-615">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2a46f-616">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="2a46f-617">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="2a46f-618">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2a46f-619">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2a46f-620">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2a46f-621">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2a46f-622">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2a46f-622">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="2a46f-624">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="2a46f-625">Aşağıdaki yöntemi ekleyin `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="2a46f-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="2a46f-626">`DeleteTodoItem`Yanıt 204 ' dir [(içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="2a46f-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="2a46f-627">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="2a46f-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="2a46f-628">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2a46f-629">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2a46f-630">Silinecek nesnenin URI 'sini ayarlayın (örneğin, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="2a46f-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="2a46f-631">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-631">Select **Send**.</span></span>

<span data-ttu-id="2a46f-632">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a46f-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="2a46f-633">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2a46f-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2a46f-634">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="2a46f-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="2a46f-635">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="2a46f-636">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-636">jQuery initiates the request.</span></span> <span data-ttu-id="2a46f-637">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="2a46f-638">Uygulamayı [statik dosyalara sunacak](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) :</span><span class="sxs-lookup"><span data-stu-id="2a46f-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="2a46f-639">Proje dizininde bir *Wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a46f-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="2a46f-640">*Wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="2a46f-641">İçeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="2a46f-642">*Wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2a46f-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="2a46f-643">Dosyanın içeriğini aşağıdaki kod ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2a46f-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="2a46f-644">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="2a46f-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="2a46f-645">*ÜzerindeProperties\launchSettings.js*açın.</span><span class="sxs-lookup"><span data-stu-id="2a46f-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="2a46f-646">`launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html*'de açmaya zorlamak için özelliği kaldırın &mdash; .</span><span class="sxs-lookup"><span data-stu-id="2a46f-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="2a46f-647">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="2a46f-648">API çağrılarının açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="2a46f-649">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="2a46f-649">Get a list of to-do items</span></span>

<span data-ttu-id="2a46f-650">jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="2a46f-651">`success`Geri çağırma işlevi, istek başarılı olursa çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="2a46f-652">Geri aramada, DOM, yapılacaklar bilgileriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="2a46f-653">Yapılacaklar öğesi ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-653">Add a to-do item</span></span>

<span data-ttu-id="2a46f-654">jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="2a46f-655">`accepts`Ve `contentType` seçenekleri, `application/json` alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="2a46f-656">Yapılacaklar öğesi [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON 'a dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="2a46f-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="2a46f-657">API başarılı bir durum kodu döndürdüğünde, `getData` Işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2a46f-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="2a46f-658">Yapılacaklar öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="2a46f-658">Update a to-do item</span></span>

<span data-ttu-id="2a46f-659">Bir yapılacaklar öğesinin güncelleştirilmesi, bir tane eklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="2a46f-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="2a46f-660">`url`Öğenin benzersiz tanımlayıcısını ekleme değişiklikleri ve öğesi `type` `PUT` .</span><span class="sxs-lookup"><span data-stu-id="2a46f-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="2a46f-661">Bir yapılacaklar öğesini silme</span><span class="sxs-lookup"><span data-stu-id="2a46f-661">Delete a to-do item</span></span>

<span data-ttu-id="2a46f-662">Bir yapılacaklar öğesinin silinmesi, `type` Ajax çağrısının üzerine ayarlanarak `DELETE` ve öğenin URL 'sindeki benzersiz tanımlayıcısını belirterek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2a46f-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="2a46f-663">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="2a46f-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="2a46f-664">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2a46f-664">Additional resources</span></span>

<span data-ttu-id="2a46f-665">[Bu öğretici için örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="2a46f-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="2a46f-666">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2a46f-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="2a46f-667">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="2a46f-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="2a46f-668">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="2a46f-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
