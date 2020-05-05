---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767245"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="19306-103">Öğretici: ASP.NET Core bir Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="19306-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="19306-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Mike te son](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="19306-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="19306-105">Bu öğreticide, ASP.NET Core ile Web API 'SI oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="19306-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="19306-106">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="19306-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19306-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-107">Create a web API project.</span></span>
> * <span data-ttu-id="19306-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="19306-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="19306-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="19306-111">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-111">Call the web API with Postman.</span></span>

<span data-ttu-id="19306-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="19306-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="19306-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="19306-113">Overview</span></span>

<span data-ttu-id="19306-114">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="19306-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="19306-115">API</span><span class="sxs-lookup"><span data-stu-id="19306-115">API</span></span> | <span data-ttu-id="19306-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="19306-116">Description</span></span> | <span data-ttu-id="19306-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="19306-117">Request body</span></span> | <span data-ttu-id="19306-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="19306-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="19306-119">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="19306-119">Get all to-do items</span></span> | <span data-ttu-id="19306-120">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-120">None</span></span> | <span data-ttu-id="19306-121">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="19306-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="19306-122">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="19306-122">Get an item by ID</span></span> | <span data-ttu-id="19306-123">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-123">None</span></span> | <span data-ttu-id="19306-124">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="19306-125">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="19306-125">Add a new item</span></span> | <span data-ttu-id="19306-126">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-126">To-do item</span></span> | <span data-ttu-id="19306-127">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="19306-128">Mevcut bir öğeyi güncelleştir&nbsp;</span><span class="sxs-lookup"><span data-stu-id="19306-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="19306-129">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-129">To-do item</span></span> | <span data-ttu-id="19306-130">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-130">None</span></span> |
|<span data-ttu-id="19306-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="19306-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="19306-132">Öğe &nbsp; silme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="19306-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="19306-133">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-133">None</span></span> | <span data-ttu-id="19306-134">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-134">None</span></span>|

<span data-ttu-id="19306-135">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="19306-135">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="19306-141">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="19306-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-144">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="19306-145">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="19306-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-147">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="19306-148">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="19306-149">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="19306-150">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="19306-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="19306-151">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-151">Select the **API** template and click **Create**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="19306-154">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="19306-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="19306-155">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="19306-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="19306-156">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="19306-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="19306-157">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="19306-158">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="19306-158">The preceding commands:</span></span>

  * <span data-ttu-id="19306-159">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="19306-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="19306-160">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="19306-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-161">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19306-162">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-162">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="19306-164">**.NET Core** > **uygulama** > **API 'si** > **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="19306-166">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **hedef Framework** \**.NET Core 3,1*' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="19306-167">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="19306-169">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="19306-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="19306-170">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="19306-170">Test the API</span></span>

<span data-ttu-id="19306-171">Proje şablonu bir `WeatherForecast` API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="19306-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="19306-172">Uygulamayı test `Get` etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19306-174">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="19306-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="19306-175">Visual Studio bir tarayıcı başlatır ve `https://localhost:<port>/WeatherForecast`' a gider, `<port>` burada rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="19306-176">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="19306-177">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="19306-179">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="19306-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="19306-180">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="19306-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-181">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="19306-182">Uygulamayı başlatmak için**hata ayıklamayı Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="19306-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="19306-183">Mac için Visual Studio bir tarayıcı başlatır ve `https://localhost:<port>`' a gider, `<port>` burada rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="19306-184">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="19306-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="19306-185">URL `/WeatherForecast` 'ye ekleyın (URL 'yi olarak `https://localhost:<port>/WeatherForecast`değiştirin).</span><span class="sxs-lookup"><span data-stu-id="19306-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="19306-186">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="19306-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="19306-187">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-187">Add a model class</span></span>

<span data-ttu-id="19306-188">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="19306-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="19306-189">Bu uygulamanın modeli tek `TodoItem` bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="19306-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-191">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="19306-192">**Yeni klasör** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="19306-193">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="19306-194">*Modeller* klasörüne sağ tıklayın ve**sınıf** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="19306-195">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="19306-196">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="19306-198">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="19306-199">Modeller klasörüne `TodoItem` aşağıdaki kodla bir *Models* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-200">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19306-201">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-201">Right-click the project.</span></span> <span data-ttu-id="19306-202">**Yeni klasör** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="19306-203">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-203">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="19306-205">*Modeller* klasörüne sağ tıklayın ve **yeni dosya** > **Ekle** > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="19306-206">Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="19306-207">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="19306-208">Özelliği `Id` , ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="19306-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="19306-209">Model sınıfları projede herhangi bir yere gidebilir, ancak *modeller* klasörü kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="19306-210">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-210">Add a database context</span></span>

<span data-ttu-id="19306-211">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="19306-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="19306-212">Bu sınıf `Microsoft.EntityFrameworkCore.DbContext` sınıfından türeterek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="19306-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="19306-214">Microsoft. EntityFrameworkCore. SqlServer ekleyin</span><span class="sxs-lookup"><span data-stu-id="19306-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="19306-215">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="19306-216">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="19306-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="19306-217">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="19306-218">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="19306-219">Önceki yönergeleri kullanarak `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="19306-221">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="19306-222">*Modeller* klasörüne sağ tıklayın ve**sınıf** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="19306-223">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19306-224">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="19306-225">Modeller klasörüne `TodoContext` bir sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="19306-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="19306-226">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="19306-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="19306-227">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="19306-227">Register the database context</span></span>

<span data-ttu-id="19306-228">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="19306-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="19306-229">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="19306-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="19306-230">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="19306-231">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="19306-231">The preceding code:</span></span>

* <span data-ttu-id="19306-232">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="19306-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="19306-233">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="19306-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="19306-234">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="19306-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="19306-235">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="19306-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-237">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="19306-238">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="19306-239">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="19306-240">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="19306-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="19306-241">**Model sınıfında** **TodoItem (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="19306-242">**Veri bağlamı sınıfında** **TodoContext (TodoApi. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="19306-243">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19306-244">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="19306-245">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="19306-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="19306-246">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="19306-246">The preceding commands:</span></span>

* <span data-ttu-id="19306-247">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="19306-248">Scafkatlama altyapısını (`dotnet-aspnet-codegenerator`) kurar.</span><span class="sxs-lookup"><span data-stu-id="19306-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="19306-249">Yapı iskelesi `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="19306-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="19306-250">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="19306-250">The generated code:</span></span>

* <span data-ttu-id="19306-251">Sınıfını [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="19306-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="19306-252">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="19306-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="19306-253">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index>..</span><span class="sxs-lookup"><span data-stu-id="19306-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="19306-254">Veritabanı bağlamını (`TodoContext`) denetleyiciye eklemek için dı kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="19306-255">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="19306-256">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="19306-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="19306-257">Görünümleri olan denetleyiciler yol `[action]` şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="19306-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="19306-258">API denetleyicileri yol şablonuna `[action]` dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="19306-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="19306-259">`[action]` Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="19306-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="19306-260">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="19306-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="19306-261">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="19306-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="19306-262">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="19306-263">Yukarıdaki kod, [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliğiyle gösterildiği gıbı bır http post yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="19306-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="19306-264">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="19306-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="19306-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="19306-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="19306-266">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="19306-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="19306-267">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="19306-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="19306-268">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="19306-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="19306-269">`Location` Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="19306-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="19306-270">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="19306-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="19306-271">`Location` Üstbilginin URI 'sini oluşturma `GetTodoItem` eylemine başvurur.</span><span class="sxs-lookup"><span data-stu-id="19306-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="19306-272">C# `nameof` anahtar sözcüğü, `CreatedAtAction` çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="19306-273">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="19306-273">Install Postman</span></span>

<span data-ttu-id="19306-274">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="19306-275">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="19306-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="19306-276">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="19306-276">Start the web app.</span></span>
* <span data-ttu-id="19306-277">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="19306-277">Start Postman.</span></span>
* <span data-ttu-id="19306-278">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="19306-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="19306-279">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="19306-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="19306-280">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="19306-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="19306-281">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="19306-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="19306-282">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-282">Create a new request.</span></span>
* <span data-ttu-id="19306-283">HTTP yöntemini olarak `POST`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="19306-284">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="19306-285">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="19306-286">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="19306-287">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="19306-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="19306-288">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-288">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="19306-290">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-290">Test the location header URI</span></span>

* <span data-ttu-id="19306-291">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="19306-292">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="19306-292">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="19306-294">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-294">Set the method to GET.</span></span>
* <span data-ttu-id="19306-295">URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="19306-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="19306-296">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="19306-297">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="19306-297">Examine the GET methods</span></span>

<span data-ttu-id="19306-298">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="19306-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="19306-299">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="19306-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="19306-300">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="19306-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="19306-301">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="19306-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="19306-302">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="19306-302">Test Get with Postman</span></span>

* <span data-ttu-id="19306-303">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-303">Create a new request.</span></span>
* <span data-ttu-id="19306-304">**Almak**için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="19306-305">İstek URL 'sini olarak `https://localhost:<port>/api/TodoItems`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="19306-306">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="19306-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="19306-307">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="19306-308">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-308">Select **Send**.</span></span>

<span data-ttu-id="19306-309">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-309">This app uses an in-memory database.</span></span> <span data-ttu-id="19306-310">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="19306-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="19306-311">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="19306-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="19306-312">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="19306-312">Routing and URL paths</span></span>

<span data-ttu-id="19306-313">Öznitelik [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) , BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="19306-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="19306-314">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="19306-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="19306-315">Denetleyicinin `Route` özniteliğinde şablon dizesiyle başlayın:</span><span class="sxs-lookup"><span data-stu-id="19306-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="19306-316">Denetleyicinin `[controller]` adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="19306-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="19306-317">Bu örnek için denetleyici sınıfı adı **todoıtems**denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="19306-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="19306-318">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="19306-319">`[HttpGet]` Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="19306-320">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="19306-320">This sample doesn't use a template.</span></span> <span data-ttu-id="19306-321">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="19306-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="19306-322">Aşağıdaki `GetTodoItem` yöntemde, `"{id}"` Yapılacaklar öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="19306-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="19306-323">`GetTodoItem` ÇAĞRıLDıĞıNDA, URL `"{id}"` 'deki değeri, yönteminin `id` parametresindeki yöntemine sağlanır.</span><span class="sxs-lookup"><span data-stu-id="19306-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="19306-324">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="19306-324">Return values</span></span>

<span data-ttu-id="19306-325">Ve yöntemlerinin dönüş türü [ActionResult\<T> türüdür.](xref:web-api/action-return-types#actionresultt-type) `GetTodoItems` `GetTodoItem`</span><span class="sxs-lookup"><span data-stu-id="19306-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="19306-326">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="19306-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="19306-327">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="19306-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="19306-328">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="19306-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="19306-329">`ActionResult`dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="19306-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="19306-330">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="19306-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="19306-331">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="19306-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="19306-332">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="19306-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="19306-333">Sonuçları `item` bir http 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="19306-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="19306-334">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="19306-334">The PutTodoItem method</span></span>

<span data-ttu-id="19306-335">`PutTodoItem` Yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="19306-336">`PutTodoItem`, HTTP PUT `PostTodoItem`kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="19306-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="19306-337">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="19306-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="19306-338">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="19306-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="19306-339">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="19306-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="19306-340">Çağrılırken `PutTodoItem`bir hata alırsanız, veritabanında bir öğe `GET` olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="19306-341">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="19306-342">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="19306-343">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="19306-344">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="19306-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="19306-345">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="19306-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="19306-346">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="19306-346">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="19306-348">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="19306-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="19306-349">`DeleteTodoItem` Yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="19306-350">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="19306-351">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="19306-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="19306-352">Yöntemini olarak `DELETE`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="19306-353">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="19306-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="19306-354">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="19306-355">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="19306-355">Prevent over-posting</span></span>

<span data-ttu-id="19306-356">Şu anda örnek uygulama tüm `TodoItem` nesneyi kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="19306-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="19306-357">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="19306-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="19306-358">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="19306-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="19306-359">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="19306-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="19306-360">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="19306-361">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="19306-361">A DTO may be used to:</span></span>

* <span data-ttu-id="19306-362">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-362">Prevent over-posting.</span></span>
* <span data-ttu-id="19306-363">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="19306-364">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="19306-365">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="19306-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="19306-366">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="19306-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="19306-367">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="19306-368">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="19306-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="19306-369">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="19306-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="19306-370">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="19306-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="19306-371">Kullanmak `TodoItemsController` `TodoItemDTO`için öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="19306-372">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="19306-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="19306-373">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="19306-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="19306-374">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="19306-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="19306-375">Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="19306-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19306-376">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-376">Create a web API project.</span></span>
> * <span data-ttu-id="19306-377">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="19306-378">Denetleyici ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-378">Add a controller.</span></span>
> * <span data-ttu-id="19306-379">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="19306-380">Yönlendirme ve URL yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="19306-381">Dönüş değerlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="19306-381">Specify return values.</span></span>
> * <span data-ttu-id="19306-382">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="19306-383">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="19306-384">Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="19306-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="19306-385">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="19306-385">Overview</span></span>

<span data-ttu-id="19306-386">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="19306-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="19306-387">API</span><span class="sxs-lookup"><span data-stu-id="19306-387">API</span></span> | <span data-ttu-id="19306-388">Açıklama</span><span class="sxs-lookup"><span data-stu-id="19306-388">Description</span></span> | <span data-ttu-id="19306-389">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="19306-389">Request body</span></span> | <span data-ttu-id="19306-390">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="19306-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="19306-391">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="19306-391">GET /api/TodoItems</span></span> | <span data-ttu-id="19306-392">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="19306-392">Get all to-do items</span></span> | <span data-ttu-id="19306-393">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-393">None</span></span> | <span data-ttu-id="19306-394">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="19306-394">Array of to-do items</span></span>|
|<span data-ttu-id="19306-395">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="19306-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="19306-396">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="19306-396">Get an item by ID</span></span> | <span data-ttu-id="19306-397">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-397">None</span></span> | <span data-ttu-id="19306-398">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-398">To-do item</span></span>|
|<span data-ttu-id="19306-399">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="19306-399">POST /api/TodoItems</span></span> | <span data-ttu-id="19306-400">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="19306-400">Add a new item</span></span> | <span data-ttu-id="19306-401">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-401">To-do item</span></span> | <span data-ttu-id="19306-402">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-402">To-do item</span></span> |
|<span data-ttu-id="19306-403">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="19306-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="19306-404">Mevcut bir öğeyi güncelleştir&nbsp;</span><span class="sxs-lookup"><span data-stu-id="19306-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="19306-405">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="19306-405">To-do item</span></span> | <span data-ttu-id="19306-406">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-406">None</span></span> |
|<span data-ttu-id="19306-407">/Api/TodoItems/{id} &nbsp; Sil&nbsp;</span><span class="sxs-lookup"><span data-stu-id="19306-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="19306-408">Öğe &nbsp; silme&nbsp;</span><span class="sxs-lookup"><span data-stu-id="19306-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="19306-409">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-409">None</span></span> | <span data-ttu-id="19306-410">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="19306-410">None</span></span>|

<span data-ttu-id="19306-411">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="19306-411">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="19306-417">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="19306-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-420">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="19306-421">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="19306-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-423">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="19306-424">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="19306-425">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="19306-426">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="19306-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="19306-427">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="19306-428">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="19306-428">**Don't** select **Enable Docker Support**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="19306-431">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="19306-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="19306-432">Dizinleri (`cd`) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="19306-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="19306-433">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="19306-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="19306-434">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="19306-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="19306-435">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-436">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19306-437">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-437">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="19306-439">**.NET Core** > **uygulama** > **API 'si** > **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="19306-441">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, \**.NET Core 2,2*' un varsayılan **hedef çerçevesini** kabul edin.</span><span class="sxs-lookup"><span data-stu-id="19306-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="19306-442">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="19306-444">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="19306-444">Test the API</span></span>

<span data-ttu-id="19306-445">Proje şablonu bir `values` API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="19306-445">The project template creates a `values` API.</span></span> <span data-ttu-id="19306-446">Uygulamayı test `Get` etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19306-448">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="19306-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="19306-449">Visual Studio bir tarayıcı başlatır ve `https://localhost:<port>/api/values`' a gider, `<port>` burada rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="19306-450">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="19306-451">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="19306-453">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="19306-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="19306-454">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="19306-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-455">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="19306-456">Uygulamayı başlatmak için**hata ayıklamayı Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="19306-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="19306-457">Mac için Visual Studio bir tarayıcı başlatır ve `https://localhost:<port>`' a gider, `<port>` burada rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="19306-458">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="19306-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="19306-459">URL `/api/values` 'ye ekleyın (URL 'yi olarak `https://localhost:<port>/api/values`değiştirin).</span><span class="sxs-lookup"><span data-stu-id="19306-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="19306-460">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="19306-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="19306-461">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-461">Add a model class</span></span>

<span data-ttu-id="19306-462">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="19306-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="19306-463">Bu uygulamanın modeli tek `TodoItem` bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="19306-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-465">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="19306-466">**Yeni klasör** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="19306-467">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="19306-468">*Modeller* klasörüne sağ tıklayın ve**sınıf** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="19306-469">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="19306-470">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="19306-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19306-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="19306-472">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="19306-473">Modeller klasörüne `TodoItem` aşağıdaki kodla bir *Models* sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="19306-474">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19306-475">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-475">Right-click the project.</span></span> <span data-ttu-id="19306-476">**Yeni klasör** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="19306-477">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="19306-477">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="19306-479">*Modeller* klasörüne sağ tıklayın ve **yeni dosya** > **Ekle** > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="19306-480">Sınıfı *TodoItem*olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="19306-481">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="19306-482">Özelliği `Id` , ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="19306-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="19306-483">Model sınıfları projede herhangi bir yere gidebilir, ancak *modeller* klasörü kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="19306-484">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-484">Add a database context</span></span>

<span data-ttu-id="19306-485">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="19306-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="19306-486">Bu sınıf `Microsoft.EntityFrameworkCore.DbContext` sınıfından türeterek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="19306-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-488">*Modeller* klasörüne sağ tıklayın ve**sınıf** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="19306-489">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19306-490">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="19306-491">Modeller klasörüne `TodoContext` bir sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="19306-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="19306-492">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="19306-493">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="19306-493">Register the database context</span></span>

<span data-ttu-id="19306-494">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="19306-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="19306-495">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="19306-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="19306-496">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="19306-497">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="19306-497">The preceding code:</span></span>

* <span data-ttu-id="19306-498">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="19306-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="19306-499">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="19306-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="19306-500">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="19306-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="19306-501">Denetleyici ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-503">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="19306-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="19306-504">**Yeni öğe** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="19306-505">**Yeni öğe Ekle** iletişim kutusunda, **API denetleyici sınıfı** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="19306-506">Sınıfı *TodoController*olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Arama kutusu ve Web API denetleyicisi seçiliyken denetleyiciyi içeren yeni öğe iletişim kutusu Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19306-508">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="19306-509">*Denetleyiciler* klasöründe adlı `TodoController`bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="19306-510">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="19306-511">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="19306-511">The preceding code:</span></span>

* <span data-ttu-id="19306-512">Yöntemler olmadan bir API denetleyici sınıfı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="19306-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="19306-513">Sınıfını [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="19306-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="19306-514">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="19306-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="19306-515">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index>..</span><span class="sxs-lookup"><span data-stu-id="19306-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="19306-516">Veritabanı bağlamını (`TodoContext`) denetleyiciye eklemek için dı kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="19306-517">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="19306-518">Veritabanı boşsa veritabanına adlı `Item1` bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="19306-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="19306-519">Bu kod oluşturucuda yer aldığı için, her yeni HTTP isteği olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="19306-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="19306-520">Tüm öğeleri silerseniz, bir API yönteminin bir sonraki `Item1` çağrılışında Oluşturucu yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="19306-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="19306-521">Bu nedenle, aslında çalışırken silme işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="19306-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="19306-522">Get yöntemleri ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-522">Add Get methods</span></span>

<span data-ttu-id="19306-523">Yapılacaklar öğelerini alan bir API sağlamak için aşağıdaki yöntemleri `TodoController` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="19306-524">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="19306-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="19306-525">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="19306-525">Stop the app if it's still running.</span></span> <span data-ttu-id="19306-526">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="19306-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="19306-527">Bir tarayıcıdan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="19306-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="19306-528">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="19306-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="19306-529">Aşağıdaki HTTP yanıtı, çağrısı tarafından oluşturulur `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="19306-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="19306-530">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="19306-530">Routing and URL paths</span></span>

<span data-ttu-id="19306-531">Öznitelik [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) , BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="19306-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="19306-532">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="19306-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="19306-533">Denetleyicinin `Route` özniteliğinde şablon dizesiyle başlayın:</span><span class="sxs-lookup"><span data-stu-id="19306-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="19306-534">Denetleyicinin `[controller]` adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="19306-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="19306-535">Bu örnek için denetleyici sınıf adı **Todo**Controller olduğundan, denetleyici adı "Todo" olur.</span><span class="sxs-lookup"><span data-stu-id="19306-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="19306-536">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="19306-537">`[HttpGet]` Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]`), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="19306-538">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="19306-538">This sample doesn't use a template.</span></span> <span data-ttu-id="19306-539">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="19306-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="19306-540">Aşağıdaki `GetTodoItem` yöntemde, `"{id}"` Yapılacaklar öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="19306-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="19306-541">`GetTodoItem` ÇAĞRıLDıĞıNDA, URL `"{id}"` 'deki değeri, yönteminin`id` parametresindeki yöntemine sağlanır.</span><span class="sxs-lookup"><span data-stu-id="19306-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="19306-542">Döndürülen değerler</span><span class="sxs-lookup"><span data-stu-id="19306-542">Return values</span></span>

<span data-ttu-id="19306-543">Ve yöntemlerinin dönüş türü [ActionResult\<T> türüdür.](xref:web-api/action-return-types#actionresultt-type) `GetTodoItems` `GetTodoItem`</span><span class="sxs-lookup"><span data-stu-id="19306-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="19306-544">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="19306-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="19306-545">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="19306-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="19306-546">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="19306-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="19306-547">`ActionResult`dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="19306-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="19306-548">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="19306-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="19306-549">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="19306-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="19306-550">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="19306-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="19306-551">Sonuçları `item` bir http 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="19306-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="19306-552">GetTodoItems yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="19306-553">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="19306-554">[Postman](https://www.getpostman.com/downloads/)'yi yükleme.</span><span class="sxs-lookup"><span data-stu-id="19306-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="19306-555">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="19306-555">Start the web app.</span></span>
* <span data-ttu-id="19306-556">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="19306-556">Start Postman.</span></span>
* <span data-ttu-id="19306-557">**SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="19306-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19306-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19306-559">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="19306-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="19306-560">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19306-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="19306-561">**Postman** > **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını**devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="19306-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="19306-562">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="19306-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="19306-563">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="19306-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="19306-564">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-564">Create a new request.</span></span>
  * <span data-ttu-id="19306-565">**Almak**için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="19306-566">İstek URL 'sini olarak `https://localhost:<port>/api/todo`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="19306-567">Örneğin, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="19306-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="19306-568">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="19306-569">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-569">Select **Send**.</span></span>

![Get isteği ile Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="19306-571">Create yöntemi ekle</span><span class="sxs-lookup"><span data-stu-id="19306-571">Add a Create method</span></span>

<span data-ttu-id="19306-572">`PostTodoItem` *Controllers/TodoController. cs*içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="19306-573">Yukarıdaki kod, [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) özniteliğiyle gösterildiği gıbı bır http post yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="19306-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="19306-574">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="19306-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="19306-575">`CreatedAtAction` Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="19306-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="19306-576">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="19306-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="19306-577">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="19306-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="19306-578">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="19306-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="19306-579">`Location` Üst bilgi, yeni oluşturulan YAPıLACAKLAR öğesinin URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="19306-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="19306-580">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="19306-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="19306-581">`Location` Üstbilginin URI 'sini oluşturma `GetTodoItem` eylemine başvurur.</span><span class="sxs-lookup"><span data-stu-id="19306-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="19306-582">C# `nameof` anahtar sözcüğü, `CreatedAtAction` çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="19306-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="19306-583">PostTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="19306-584">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-584">Build the project.</span></span>
* <span data-ttu-id="19306-585">Postman 'da HTTP yöntemini olarak `POST`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="19306-586">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="19306-587">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="19306-588">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="19306-589">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="19306-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="19306-590">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-590">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/create.png)

  <span data-ttu-id="19306-592">Bir 405 yöntemine Izin verilmiyor hatası alırsanız, `PostTodoItem` Yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur.</span><span class="sxs-lookup"><span data-stu-id="19306-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="19306-593">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-593">Test the location header URI</span></span>

* <span data-ttu-id="19306-594">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="19306-595">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="19306-595">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="19306-597">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-597">Set the method to GET.</span></span>
* <span data-ttu-id="19306-598">URI 'yi yapıştırın (örneğin, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="19306-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="19306-599">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="19306-600">PutTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="19306-601">Aşağıdaki `PutTodoItem` yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="19306-602">`PutTodoItem`, HTTP PUT `PostTodoItem`kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="19306-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="19306-603">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="19306-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="19306-604">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="19306-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="19306-605">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="19306-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="19306-606">Çağrılırken `PutTodoItem`bir hata alırsanız, veritabanında bir öğe `GET` olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="19306-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="19306-607">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="19306-608">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="19306-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="19306-609">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="19306-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="19306-610">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="19306-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="19306-611">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="19306-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="19306-612">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="19306-612">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="19306-614">DeleteTodoItem yöntemi ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="19306-615">Aşağıdaki `DeleteTodoItem` yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="19306-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="19306-616">`DeleteTodoItem` Yanıt 204 ' dir [(içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="19306-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="19306-617">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="19306-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="19306-618">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="19306-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="19306-619">Yöntemini olarak `DELETE`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="19306-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="19306-620">Silinecek nesnenin URI 'sini ayarlayın (örneğin, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="19306-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="19306-621">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="19306-621">Select **Send**.</span></span>

<span data-ttu-id="19306-622">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="19306-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="19306-623">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="19306-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="19306-624">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="19306-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="19306-625">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="19306-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="19306-626">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="19306-626">jQuery initiates the request.</span></span> <span data-ttu-id="19306-627">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="19306-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="19306-628">Uygulamayı [statik dosyalara sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="19306-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="19306-629">Proje dizininde bir *Wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="19306-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="19306-630">*Wwwroot* dizinine *index. HTML* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="19306-631">İçeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="19306-632">*Wwwroot* dizinine *site. js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="19306-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="19306-633">İçeriğini şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="19306-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="19306-634">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="19306-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="19306-635">*Properties\launchSettings.JSON*'i açın.</span><span class="sxs-lookup"><span data-stu-id="19306-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="19306-636">Uygulamanın varsayılan `launchUrl` dosyasını *index. html*&mdash;dizininde açılmasını zorlamak için özelliği kaldırın.</span><span class="sxs-lookup"><span data-stu-id="19306-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="19306-637">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="19306-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="19306-638">API çağrılarının açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="19306-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="19306-639">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="19306-639">Get a list of to-do items</span></span>

<span data-ttu-id="19306-640">jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="19306-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="19306-641">`success` Geri çağırma işlevi, istek başarılı olursa çağrılır.</span><span class="sxs-lookup"><span data-stu-id="19306-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="19306-642">Geri aramada, DOM, yapılacaklar bilgileriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="19306-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="19306-643">Yapılacaklar öğesi ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-643">Add a to-do item</span></span>

<span data-ttu-id="19306-644">jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="19306-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="19306-645">Ve `accepts` `contentType` seçenekleri, alınan ve gönderilen `application/json` medya türünü belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="19306-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="19306-646">Yapılacaklar öğesi [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON 'a dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="19306-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="19306-647">API başarılı bir durum kodu döndürdüğünde, `getData` işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="19306-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="19306-648">Yapılacaklar öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="19306-648">Update a to-do item</span></span>

<span data-ttu-id="19306-649">Bir yapılacaklar öğesinin güncelleştirilmesi, bir tane eklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="19306-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="19306-650">Öğenin `url` benzersiz tanımlayıcısını ekleme değişiklikleri ve `type` öğesi `PUT`.</span><span class="sxs-lookup"><span data-stu-id="19306-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="19306-651">Bir yapılacaklar öğesini silme</span><span class="sxs-lookup"><span data-stu-id="19306-651">Delete a to-do item</span></span>

<span data-ttu-id="19306-652">Bir yapılacaklar öğesinin silinmesi, AJAX çağrısının `type` üzerine ayarlanarak `DELETE` ve öğenin URL 'sindeki benzersiz tanımlayıcısını belirterek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="19306-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="19306-653">Web API 'sine kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="19306-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="19306-654">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="19306-654">Additional resources</span></span>

<span data-ttu-id="19306-655">[Bu öğretici için örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="19306-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="19306-656">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="19306-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="19306-657">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="19306-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="19306-658">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="19306-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
