---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175058"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="f6e38-103">Öğretici: ASP.NET Core bir Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="f6e38-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="f6e38-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Mike te son](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="f6e38-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="f6e38-105">Bu öğreticide, ASP.NET Core ile Web API 'SI oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f6e38-106">Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="f6e38-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6e38-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-107">Create a web API project.</span></span>
> * <span data-ttu-id="f6e38-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f6e38-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="f6e38-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="f6e38-111">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-111">Call the web API with Postman.</span></span>

<span data-ttu-id="f6e38-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="f6e38-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="f6e38-113">Overview</span></span>

<span data-ttu-id="f6e38-114">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f6e38-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f6e38-115">API</span><span class="sxs-lookup"><span data-stu-id="f6e38-115">API</span></span> | <span data-ttu-id="f6e38-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f6e38-116">Description</span></span> | <span data-ttu-id="f6e38-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-117">Request body</span></span> | <span data-ttu-id="f6e38-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="f6e38-119">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="f6e38-119">Get all to-do items</span></span> | <span data-ttu-id="f6e38-120">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-120">None</span></span> | <span data-ttu-id="f6e38-121">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="f6e38-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="f6e38-122">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="f6e38-122">Get an item by ID</span></span> | <span data-ttu-id="f6e38-123">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-123">None</span></span> | <span data-ttu-id="f6e38-124">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="f6e38-125">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="f6e38-125">Add a new item</span></span> | <span data-ttu-id="f6e38-126">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-126">To-do item</span></span> | <span data-ttu-id="f6e38-127">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="f6e38-128">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f6e38-129">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-129">To-do item</span></span> | <span data-ttu-id="f6e38-130">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-130">None</span></span> |
|<span data-ttu-id="f6e38-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6e38-132">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6e38-133">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-133">None</span></span> | <span data-ttu-id="f6e38-134">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-134">None</span></span>|

<span data-ttu-id="f6e38-135">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-135">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f6e38-141">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f6e38-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-144">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f6e38-145">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f6e38-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-147">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6e38-148">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f6e38-149">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f6e38-150">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 5,0** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="f6e38-151">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-151">Select the **API** template and click **Create**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e38-154">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f6e38-155">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f6e38-156">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="f6e38-157">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="f6e38-158">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="f6e38-158">The preceding commands:</span></span>

  * <span data-ttu-id="f6e38-159">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="f6e38-160">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-161">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6e38-162">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-162">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f6e38-164">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f6e38-165">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="f6e38-167">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 5. x **hedef çerçevesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="f6e38-168">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-168">Select **Next**.</span></span>

* <span data-ttu-id="f6e38-169">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="f6e38-171">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="f6e38-172">Projeyi test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-172">Test the project</span></span>

<span data-ttu-id="f6e38-173">Proje şablonu, `WeatherForecast` [Swagger](xref:tutorials/web-api-help-pages-using-swagger)desteğiyle bir API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e38-175">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="f6e38-176">Visual Studio başlatılır:</span><span class="sxs-lookup"><span data-stu-id="f6e38-176">Visual Studio launches:</span></span>

* <span data-ttu-id="f6e38-177">IIS Express Web sunucusu.</span><span class="sxs-lookup"><span data-stu-id="f6e38-177">The IIS Express web server.</span></span>
* <span data-ttu-id="f6e38-178">Varsayılan tarayıcı ve `https://localhost:<port>/swagger/index.html` ' a gider, burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="f6e38-180">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6e38-181">Bir tarayıcıda aşağıdaki URL 'ye gidin: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="f6e38-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-182">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6e38-183">Uygulamayı **başlatmak**  >  için **hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f6e38-184">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f6e38-185">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="f6e38-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f6e38-186">`/swagger`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/swagger` ).</span><span class="sxs-lookup"><span data-stu-id="f6e38-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="f6e38-187">Swagger sayfası `/swagger/index.html` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="f6e38-188">**Al**  >  **deneyin**  >  **Çalıştır**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="f6e38-189">Sayfa şunu görüntüler:</span><span class="sxs-lookup"><span data-stu-id="f6e38-189">The page displays:</span></span>

* <span data-ttu-id="f6e38-190">Dalgalı tahmin API 'sini test etmek için [kıvrımlı](https://curl.haxx.se/) komutu.</span><span class="sxs-lookup"><span data-stu-id="f6e38-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="f6e38-191">Dalgalı tahmin API 'sini test etmek için URL.</span><span class="sxs-lookup"><span data-stu-id="f6e38-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="f6e38-192">Yanıt kodu, gövde ve üstbilgiler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="f6e38-193">Medya türleri ve örnek değer ve şema içeren bir açılır liste kutusu.</span><span class="sxs-lookup"><span data-stu-id="f6e38-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="f6e38-194">Swagger, Web API 'Leri için yararlı belgeler ve yardım sayfaları oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="f6e38-195">Bu öğretici, bir Web API 'SI oluşturmaya odaklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="f6e38-196">Swagger hakkında daha fazla bilgi için bkz <xref:tutorials/web-api-help-pages-using-swagger> ..</span><span class="sxs-lookup"><span data-stu-id="f6e38-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="f6e38-197">**Istek URL** 'sini kopyalayıp tarayıcıya yapıştırın:`https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="f6e38-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="f6e38-198">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="f6e38-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="f6e38-199">LaunchUrl 'Yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="f6e38-199">Update the launchUrl</span></span>

<span data-ttu-id="f6e38-200">*Properties\launchSettings.jstarihinde*, ' `launchUrl` den ' `"swagger"` e Güncelleştir `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="f6e38-201">Swagger kaldırıldığından, yukarıdaki biçimlendirme, başlatılan URL 'YI aşağıdaki bölümlerde eklenen denetleyicinin GET yöntemine değiştirir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="f6e38-202">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-202">Add a model class</span></span>

<span data-ttu-id="f6e38-203">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f6e38-204">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-206">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f6e38-207">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6e38-208">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="f6e38-209">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="f6e38-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6e38-210">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f6e38-211">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e38-213">Adlı bir klasör ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f6e38-214">`TodoItem` *Models* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-215">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6e38-216">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-216">Right-click the project.</span></span> <span data-ttu-id="f6e38-217">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6e38-218">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-218">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f6e38-220">Klasöre sağ tıklayın *Models* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f6e38-221">Sınıfı *TodoItem* olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f6e38-222">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="f6e38-223">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f6e38-224">Model sınıfları projede herhangi bir yere gidebilir, ancak *Models* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f6e38-225">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-225">Add a database context</span></span>

<span data-ttu-id="f6e38-226">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f6e38-227">Bu sınıf sınıfından türeterek oluşturulur <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="f6e38-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="f6e38-229">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-229">Add NuGet packages</span></span>

* <span data-ttu-id="f6e38-230">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="f6e38-231">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="f6e38-232">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="f6e38-233">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="f6e38-234">Yukarıdaki yönergeleri kullanarak **Microsoft. EntityFrameworkCore. InMemory** NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="f6e38-235">![NuGet Paket Yöneticisi](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="f6e38-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="f6e38-236">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="f6e38-237">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="f6e38-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6e38-238">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-239">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6e38-240">Klasöre bir `TodoContext` sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f6e38-241">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f6e38-242">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="f6e38-242">Register the database context</span></span>

<span data-ttu-id="f6e38-243">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f6e38-244">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="f6e38-245">Aşağıdaki kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="f6e38-246">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="f6e38-246">The preceding code:</span></span>

* <span data-ttu-id="f6e38-247">Swagger çağrılarını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="f6e38-248">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f6e38-249">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f6e38-250">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="f6e38-251">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="f6e38-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-253">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f6e38-254">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f6e38-255">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="f6e38-256">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="f6e38-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="f6e38-257">**Model sınıfında** **TodoItem (TodoApi. Models )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="f6e38-258">**Veri bağlamı sınıfında** **TodoContext (TodoApi. Models )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="f6e38-259">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-260">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f6e38-261">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="f6e38-262">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="f6e38-262">The preceding commands:</span></span>

* <span data-ttu-id="f6e38-263">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="f6e38-264">Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="f6e38-265">Yapı iskelesi `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="f6e38-266">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="f6e38-266">The generated code:</span></span>

* <span data-ttu-id="f6e38-267">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="f6e38-268">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f6e38-269">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="f6e38-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f6e38-270">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f6e38-271">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="f6e38-272">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="f6e38-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="f6e38-273">Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="f6e38-274">API denetleyicileri `[action]` yol şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="f6e38-275">`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="f6e38-276">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="f6e38-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="f6e38-277">PostTodoItem Create metodunu güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="f6e38-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="f6e38-278">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="f6e38-279">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f6e38-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="f6e38-280">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f6e38-281">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f6e38-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f6e38-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="f6e38-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="f6e38-283">Başarılı olursa bir [HTTP 201 durum kodu](https://developer.mozilla.org/docs/Web/HTTP/Status/201) döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="f6e38-284">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f6e38-285">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="f6e38-286">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="f6e38-287">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f6e38-288">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f6e38-289">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="f6e38-290">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-290">Install Postman</span></span>

<span data-ttu-id="f6e38-291">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f6e38-292">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f6e38-293">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-293">Start the web app.</span></span>
* <span data-ttu-id="f6e38-294">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-294">Start Postman.</span></span>
* <span data-ttu-id="f6e38-295">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f6e38-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="f6e38-296">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f6e38-297">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="f6e38-298">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="f6e38-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="f6e38-299">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-299">Create a new request.</span></span>
* <span data-ttu-id="f6e38-300">HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f6e38-301">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f6e38-302">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f6e38-303">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="f6e38-304">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f6e38-305">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f6e38-306">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f6e38-307">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-307">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f6e38-309">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-309">Test the location header URI</span></span>

<span data-ttu-id="f6e38-310">Konum üst bilgisi URI 'SI tarayıcıda test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="f6e38-311">Konum üst bilgisi URI 'sini kopyalayıp tarayıcıya yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="f6e38-312">Postman 'da test etmek için:</span><span class="sxs-lookup"><span data-stu-id="f6e38-312">To test in Postman:</span></span>

* <span data-ttu-id="f6e38-313">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f6e38-314">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-314">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="f6e38-316">HTTP yöntemini olarak ayarlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="f6e38-317">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="f6e38-318">Örneğin, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="f6e38-319">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="f6e38-320">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="f6e38-320">Examine the GET methods</span></span>

<span data-ttu-id="f6e38-321">İki uç nokta al uygulandı:</span><span class="sxs-lookup"><span data-stu-id="f6e38-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="f6e38-322">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="f6e38-323">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="f6e38-324">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="f6e38-325">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="f6e38-325">Test Get with Postman</span></span>

* <span data-ttu-id="f6e38-326">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-326">Create a new request.</span></span>
* <span data-ttu-id="f6e38-327">**Almak** için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="f6e38-328">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f6e38-329">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f6e38-330">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f6e38-331">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-331">Select **Send**.</span></span>

<span data-ttu-id="f6e38-332">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-332">This app uses an in-memory database.</span></span> <span data-ttu-id="f6e38-333">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="f6e38-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="f6e38-334">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="f6e38-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="f6e38-335">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="f6e38-335">Routing and URL paths</span></span>

<span data-ttu-id="f6e38-336">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f6e38-337">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f6e38-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f6e38-338">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="f6e38-339">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f6e38-340">Bu örnek için denetleyici sınıfı adı **todoıtems** denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="f6e38-341">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f6e38-342">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f6e38-343">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="f6e38-343">This sample doesn't use a template.</span></span> <span data-ttu-id="f6e38-344">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f6e38-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f6e38-345">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f6e38-346">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f6e38-347">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="f6e38-347">Return values</span></span>

<span data-ttu-id="f6e38-348">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f6e38-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f6e38-349">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f6e38-350">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak [200 Tamam](https://developer.mozilla.org/docs/Web/HTTP/Status/200)olur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f6e38-351">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f6e38-352">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f6e38-353">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f6e38-354">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem [404 durum](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="f6e38-355">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f6e38-356">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="f6e38-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="f6e38-357">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="f6e38-357">The PutTodoItem method</span></span>

<span data-ttu-id="f6e38-358">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="f6e38-359">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f6e38-360">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f6e38-361">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f6e38-362">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f6e38-363">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f6e38-364">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="f6e38-365">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f6e38-366">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f6e38-367">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f6e38-368">Kimlik = 1 olan Yapılacaklar öğesini güncelleştirin ve adını şu şekilde ayarlayın `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f6e38-369">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-369">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="f6e38-371">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="f6e38-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="f6e38-372">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f6e38-373">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f6e38-374">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f6e38-375">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f6e38-376">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="f6e38-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f6e38-377">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="f6e38-378">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="f6e38-378">Prevent over-posting</span></span>

<span data-ttu-id="f6e38-379">Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="f6e38-380">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="f6e38-381">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="f6e38-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="f6e38-382">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="f6e38-383">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="f6e38-384">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-384">A DTO may be used to:</span></span>

* <span data-ttu-id="f6e38-385">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-385">Prevent over-posting.</span></span>
* <span data-ttu-id="f6e38-386">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="f6e38-387">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="f6e38-388">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="f6e38-389">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="f6e38-390">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="f6e38-391">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="f6e38-392">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="f6e38-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="f6e38-393">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f6e38-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="f6e38-394">`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="f6e38-395">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="f6e38-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f6e38-396">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="f6e38-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="f6e38-397">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="f6e38-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f6e38-398">Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="f6e38-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6e38-399">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-399">Create a web API project.</span></span>
> * <span data-ttu-id="f6e38-400">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f6e38-401">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="f6e38-402">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="f6e38-403">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-403">Call the web API with Postman.</span></span>

<span data-ttu-id="f6e38-404">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="f6e38-405">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="f6e38-405">Overview</span></span>

<span data-ttu-id="f6e38-406">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f6e38-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f6e38-407">API</span><span class="sxs-lookup"><span data-stu-id="f6e38-407">API</span></span> | <span data-ttu-id="f6e38-408">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f6e38-408">Description</span></span> | <span data-ttu-id="f6e38-409">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-409">Request body</span></span> | <span data-ttu-id="f6e38-410">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="f6e38-411">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="f6e38-411">Get all to-do items</span></span> | <span data-ttu-id="f6e38-412">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-412">None</span></span> | <span data-ttu-id="f6e38-413">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="f6e38-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="f6e38-414">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="f6e38-414">Get an item by ID</span></span> | <span data-ttu-id="f6e38-415">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-415">None</span></span> | <span data-ttu-id="f6e38-416">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="f6e38-417">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="f6e38-417">Add a new item</span></span> | <span data-ttu-id="f6e38-418">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-418">To-do item</span></span> | <span data-ttu-id="f6e38-419">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="f6e38-420">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f6e38-421">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-421">To-do item</span></span> | <span data-ttu-id="f6e38-422">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-422">None</span></span> |
|<span data-ttu-id="f6e38-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6e38-424">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6e38-425">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-425">None</span></span> | <span data-ttu-id="f6e38-426">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-426">None</span></span>|

<span data-ttu-id="f6e38-427">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-427">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f6e38-433">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="f6e38-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-436">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f6e38-437">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f6e38-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-439">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6e38-440">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f6e38-441">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f6e38-442">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="f6e38-443">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-443">Select the **API** template and click **Create**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e38-446">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f6e38-447">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f6e38-448">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="f6e38-449">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="f6e38-450">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="f6e38-450">The preceding commands:</span></span>

  * <span data-ttu-id="f6e38-451">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="f6e38-452">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-453">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6e38-454">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-454">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f6e38-456">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f6e38-457">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="f6e38-459">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 3. x **hedef çerçevesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="f6e38-460">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-460">Select **Next**.</span></span>

* <span data-ttu-id="f6e38-461">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="f6e38-463">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="f6e38-464">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-464">Test the API</span></span>

<span data-ttu-id="f6e38-465">Proje şablonu bir API oluşturur `WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="f6e38-466">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e38-468">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6e38-469">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/WeatherForecast` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f6e38-470">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f6e38-471">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6e38-473">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6e38-474">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-475">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6e38-476">Uygulamayı **başlatmak**  >  için **hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f6e38-477">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f6e38-478">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="f6e38-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f6e38-479">`/WeatherForecast`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="f6e38-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="f6e38-480">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="f6e38-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="f6e38-481">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-481">Add a model class</span></span>

<span data-ttu-id="f6e38-482">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f6e38-483">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-485">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f6e38-486">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6e38-487">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="f6e38-488">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="f6e38-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6e38-489">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f6e38-490">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e38-492">Adlı bir klasör ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f6e38-493">`TodoItem` *Models* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-494">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6e38-495">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-495">Right-click the project.</span></span> <span data-ttu-id="f6e38-496">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6e38-497">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-497">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f6e38-499">Klasöre sağ tıklayın *Models* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f6e38-500">Sınıfı *TodoItem* olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f6e38-501">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="f6e38-502">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f6e38-503">Model sınıfları projede herhangi bir yere gidebilir, ancak *Models* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f6e38-504">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-504">Add a database context</span></span>

<span data-ttu-id="f6e38-505">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f6e38-506">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="f6e38-508">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-508">Add NuGet packages</span></span>

* <span data-ttu-id="f6e38-509">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="f6e38-510">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="f6e38-511">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="f6e38-512">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="f6e38-513">Yukarıdaki yönergeleri kullanarak **Microsoft. EntityFrameworkCore. InMemory** NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="f6e38-515">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="f6e38-516">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="f6e38-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6e38-517">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-518">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6e38-519">Klasöre bir `TodoContext` sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f6e38-520">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f6e38-521">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="f6e38-521">Register the database context</span></span>

<span data-ttu-id="f6e38-522">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f6e38-523">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="f6e38-524">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="f6e38-525">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="f6e38-525">The preceding code:</span></span>

* <span data-ttu-id="f6e38-526">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f6e38-527">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f6e38-528">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="f6e38-529">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="f6e38-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-531">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f6e38-532">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f6e38-533">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="f6e38-534">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="f6e38-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="f6e38-535">**Model sınıfında** **TodoItem (TodoApi. Models )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="f6e38-536">**Veri bağlamı sınıfında** **TodoContext (TodoApi. Models )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="f6e38-537">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-538">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f6e38-539">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="f6e38-540">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="f6e38-540">The preceding commands:</span></span>

* <span data-ttu-id="f6e38-541">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="f6e38-542">Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="f6e38-543">Yapı iskelesi `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="f6e38-544">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="f6e38-544">The generated code:</span></span>

* <span data-ttu-id="f6e38-545">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="f6e38-546">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f6e38-547">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="f6e38-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f6e38-548">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f6e38-549">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="f6e38-550">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="f6e38-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="f6e38-551">Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="f6e38-552">API denetleyicileri `[action]` yol şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="f6e38-553">`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="f6e38-554">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="f6e38-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="f6e38-555">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="f6e38-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="f6e38-556">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="f6e38-557">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f6e38-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="f6e38-558">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f6e38-559">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f6e38-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f6e38-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="f6e38-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="f6e38-561">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="f6e38-562">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f6e38-563">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="f6e38-564">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="f6e38-565">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f6e38-566">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f6e38-567">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="f6e38-568">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-568">Install Postman</span></span>

<span data-ttu-id="f6e38-569">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f6e38-570">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f6e38-571">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-571">Start the web app.</span></span>
* <span data-ttu-id="f6e38-572">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-572">Start Postman.</span></span>
* <span data-ttu-id="f6e38-573">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f6e38-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="f6e38-574">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f6e38-575">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="f6e38-576">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="f6e38-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="f6e38-577">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-577">Create a new request.</span></span>
* <span data-ttu-id="f6e38-578">HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f6e38-579">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f6e38-580">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f6e38-581">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="f6e38-582">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f6e38-583">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f6e38-584">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f6e38-585">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-585">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="f6e38-587">Konum üstbilgisi URI 'sini Postman ile test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="f6e38-588">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f6e38-589">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-589">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="f6e38-591">HTTP yöntemini olarak ayarlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="f6e38-592">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="f6e38-593">Örneğin, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="f6e38-594">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="f6e38-595">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="f6e38-595">Examine the GET methods</span></span>

<span data-ttu-id="f6e38-596">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="f6e38-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="f6e38-597">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="f6e38-598">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="f6e38-599">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="f6e38-600">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="f6e38-600">Test Get with Postman</span></span>

* <span data-ttu-id="f6e38-601">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-601">Create a new request.</span></span>
* <span data-ttu-id="f6e38-602">**Almak** için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="f6e38-603">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f6e38-604">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f6e38-605">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f6e38-606">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-606">Select **Send**.</span></span>

<span data-ttu-id="f6e38-607">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-607">This app uses an in-memory database.</span></span> <span data-ttu-id="f6e38-608">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="f6e38-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="f6e38-609">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="f6e38-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="f6e38-610">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="f6e38-610">Routing and URL paths</span></span>

<span data-ttu-id="f6e38-611">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f6e38-612">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f6e38-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f6e38-613">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="f6e38-614">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f6e38-615">Bu örnek için denetleyici sınıfı adı **todoıtems** denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="f6e38-616">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f6e38-617">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f6e38-618">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="f6e38-618">This sample doesn't use a template.</span></span> <span data-ttu-id="f6e38-619">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f6e38-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f6e38-620">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f6e38-621">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f6e38-622">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="f6e38-622">Return values</span></span> 

<span data-ttu-id="f6e38-623">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f6e38-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f6e38-624">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f6e38-625">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f6e38-626">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f6e38-627">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f6e38-628">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f6e38-629">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="f6e38-630">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f6e38-631">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="f6e38-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="f6e38-632">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="f6e38-632">The PutTodoItem method</span></span>

<span data-ttu-id="f6e38-633">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="f6e38-634">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f6e38-635">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f6e38-636">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f6e38-637">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f6e38-638">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f6e38-639">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="f6e38-640">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f6e38-641">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f6e38-642">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f6e38-643">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f6e38-644">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-644">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="f6e38-646">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="f6e38-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="f6e38-647">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f6e38-648">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f6e38-649">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f6e38-650">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f6e38-651">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="f6e38-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f6e38-652">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="f6e38-653">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="f6e38-653">Prevent over-posting</span></span>

<span data-ttu-id="f6e38-654">Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="f6e38-655">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="f6e38-656">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="f6e38-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="f6e38-657">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="f6e38-658">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="f6e38-659">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-659">A DTO may be used to:</span></span>

* <span data-ttu-id="f6e38-660">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-660">Prevent over-posting.</span></span>
* <span data-ttu-id="f6e38-661">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="f6e38-662">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="f6e38-663">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="f6e38-664">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="f6e38-665">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="f6e38-666">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="f6e38-667">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="f6e38-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="f6e38-668">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f6e38-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="f6e38-669">`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="f6e38-670">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="f6e38-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f6e38-671">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="f6e38-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="f6e38-672">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="f6e38-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6e38-673">Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="f6e38-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6e38-674">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-674">Create a web API project.</span></span>
> * <span data-ttu-id="f6e38-675">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f6e38-676">Denetleyici ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-676">Add a controller.</span></span>
> * <span data-ttu-id="f6e38-677">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="f6e38-678">Yönlendirme ve URL yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f6e38-679">Dönüş değerlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-679">Specify return values.</span></span>
> * <span data-ttu-id="f6e38-680">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f6e38-681">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="f6e38-682">Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="f6e38-683">Genel Bakış 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-683">Overview 2.1</span></span>

<span data-ttu-id="f6e38-684">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f6e38-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f6e38-685">API</span><span class="sxs-lookup"><span data-stu-id="f6e38-685">API</span></span> | <span data-ttu-id="f6e38-686">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f6e38-686">Description</span></span> | <span data-ttu-id="f6e38-687">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-687">Request body</span></span> | <span data-ttu-id="f6e38-688">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="f6e38-689">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="f6e38-689">GET /api/TodoItems</span></span> | <span data-ttu-id="f6e38-690">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="f6e38-690">Get all to-do items</span></span> | <span data-ttu-id="f6e38-691">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-691">None</span></span> | <span data-ttu-id="f6e38-692">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="f6e38-692">Array of to-do items</span></span>|
|<span data-ttu-id="f6e38-693">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="f6e38-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="f6e38-694">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="f6e38-694">Get an item by ID</span></span> | <span data-ttu-id="f6e38-695">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-695">None</span></span> | <span data-ttu-id="f6e38-696">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-696">To-do item</span></span>|
|<span data-ttu-id="f6e38-697">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f6e38-697">POST /api/TodoItems</span></span> | <span data-ttu-id="f6e38-698">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="f6e38-698">Add a new item</span></span> | <span data-ttu-id="f6e38-699">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-699">To-do item</span></span> | <span data-ttu-id="f6e38-700">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-700">To-do item</span></span> |
|<span data-ttu-id="f6e38-701">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="f6e38-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="f6e38-702">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f6e38-703">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="f6e38-703">To-do item</span></span> | <span data-ttu-id="f6e38-704">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-704">None</span></span> |
|<span data-ttu-id="f6e38-705">/Api/TodoItems/{id} &nbsp; Sil &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6e38-706">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6e38-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6e38-707">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-707">None</span></span> | <span data-ttu-id="f6e38-708">Yok</span><span class="sxs-lookup"><span data-stu-id="f6e38-708">None</span></span>|

<span data-ttu-id="f6e38-709">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-709">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="f6e38-715">Önkoşullar 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-718">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="f6e38-719">Web projesi oluşturma 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-721">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6e38-722">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f6e38-723">Projeyi *TodoApi* olarak adlandırın ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f6e38-724">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="f6e38-725">**API** şablonunu seçin ve **Oluştur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="f6e38-726">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="f6e38-726">**Don't** select **Enable Docker Support**.</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e38-729">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f6e38-730">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f6e38-731">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="f6e38-732">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="f6e38-733">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-734">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6e38-735">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-735">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f6e38-737">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f6e38-738">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="f6e38-739">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 2. x **hedef çerçevesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="f6e38-740">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-740">Select **Next**.</span></span>

* <span data-ttu-id="f6e38-741">**Proje adı** için *TodoApi* girin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="f6e38-743">API 2,1 'YI test etme</span><span class="sxs-lookup"><span data-stu-id="f6e38-743">Test the API 2.1</span></span>

<span data-ttu-id="f6e38-744">Proje şablonu bir API oluşturur `values` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-744">The project template creates a `values` API.</span></span> <span data-ttu-id="f6e38-745">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e38-747">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6e38-748">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/api/values` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f6e38-749">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f6e38-750">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6e38-752">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6e38-753">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-754">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6e38-755">Uygulamayı **başlatmak**  >  için **hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f6e38-756">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f6e38-757">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="f6e38-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f6e38-758">`/api/values`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="f6e38-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="f6e38-759">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="f6e38-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="f6e38-760">Model sınıfı ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-760">Add a model class 2.1</span></span>

<span data-ttu-id="f6e38-761">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f6e38-762">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-764">**Çözüm Gezgini**, projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f6e38-765">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6e38-766">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="f6e38-767">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="f6e38-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6e38-768">Sınıfı *TodoItem* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f6e38-769">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e38-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e38-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e38-771">Adlı bir klasör ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f6e38-772">`TodoItem` *Models* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-773">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6e38-774">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-774">Right-click the project.</span></span> <span data-ttu-id="f6e38-775">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6e38-776">Klasörü adlandırın *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-776">Name the folder *Models*.</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f6e38-778">Klasöre sağ tıklayın *Models* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f6e38-779">Sınıfı *TodoItem* olarak adlandırın ve ardından **Yeni**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f6e38-780">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="f6e38-781">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f6e38-782">Model sınıfları projede herhangi bir yere gidebilir, ancak *Models* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="f6e38-783">Veritabanı bağlamı ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-783">Add a database context 2.1</span></span>

<span data-ttu-id="f6e38-784">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f6e38-785">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-787">Klasöre sağ tıklayın *Models* ve sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="f6e38-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6e38-788">Sınıfı *TodoContext* olarak adlandırın ve **Ekle**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-789">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6e38-790">Klasöre bir `TodoContext` sınıf ekleyin *Models* .</span><span class="sxs-lookup"><span data-stu-id="f6e38-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f6e38-791">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="f6e38-792">Veritabanı bağlamını kaydetme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-792">Register the database context 2.1</span></span>

<span data-ttu-id="f6e38-793">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f6e38-794">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="f6e38-795">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="f6e38-796">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="f6e38-796">The preceding code:</span></span>

* <span data-ttu-id="f6e38-797">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f6e38-798">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f6e38-799">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="f6e38-800">Denetleyici ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-802">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f6e38-803">**Add** > **Yeni öğe** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="f6e38-804">**Yeni öğe Ekle** iletişim kutusunda, **API denetleyici sınıfı** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="f6e38-805">Sınıfı *TodoController* olarak adlandırın ve **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![Arama kutusu ve Web API denetleyicisi seçiliyken denetleyiciyi içeren yeni öğe iletişim kutusu Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-807">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6e38-808">*Denetleyiciler* klasöründe adlı bir sınıf oluşturun `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="f6e38-809">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="f6e38-810">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="f6e38-810">The preceding code:</span></span>

* <span data-ttu-id="f6e38-811">Yöntemler olmadan bir API denetleyici sınıfı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="f6e38-812">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="f6e38-813">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f6e38-814">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="f6e38-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f6e38-815">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f6e38-816">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f6e38-817">Veritabanı boşsa veritabanına adlı bir öğe ekler `Item1` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="f6e38-818">Bu kod oluşturucuda yer aldığı için, her yeni HTTP isteği olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="f6e38-819">Tüm öğeleri silerseniz, `Item1` BIR API yönteminin bir sonraki çağrılışında Oluşturucu yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="f6e38-820">Bu nedenle, aslında çalışırken silme işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="f6e38-821">Get yöntemleri ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-821">Add Get methods 2.1</span></span>

<span data-ttu-id="f6e38-822">Yapılacaklar öğelerini alan bir API sağlamak için aşağıdaki yöntemleri `TodoController` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="f6e38-823">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="f6e38-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f6e38-824">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-824">Stop the app if it's still running.</span></span> <span data-ttu-id="f6e38-825">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="f6e38-826">Bir tarayıcıdan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="f6e38-827">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="f6e38-828">Aşağıdaki HTTP yanıtı, çağrısı tarafından oluşturulur `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="f6e38-829">Yönlendirme ve URL yolları 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="f6e38-830">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f6e38-831">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f6e38-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f6e38-832">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="f6e38-833">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f6e38-834">Bu örnek için denetleyici sınıf adı **Todo** Controller olduğundan, denetleyici adı "Todo" olur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="f6e38-835">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f6e38-836">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f6e38-837">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="f6e38-837">This sample doesn't use a template.</span></span> <span data-ttu-id="f6e38-838">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f6e38-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f6e38-839">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f6e38-840">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="f6e38-841">Dönüş değerleri 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-841">Return values 2.1</span></span>

<span data-ttu-id="f6e38-842">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f6e38-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f6e38-843">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f6e38-844">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f6e38-845">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f6e38-846">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f6e38-847">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f6e38-848">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="f6e38-849">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f6e38-850">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="f6e38-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="f6e38-851">GetTodoItems yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="f6e38-852">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f6e38-853">[Postman](https://www.getpostman.com/downloads/)'yi yükleme.</span><span class="sxs-lookup"><span data-stu-id="f6e38-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="f6e38-854">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-854">Start the web app.</span></span>
* <span data-ttu-id="f6e38-855">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-855">Start Postman.</span></span>
* <span data-ttu-id="f6e38-856">**SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e38-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e38-858">**Dosya** > **ayarlarından** (**genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f6e38-859">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e38-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6e38-860">**Postman**  >  **tercihleri** ' nden (**genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="f6e38-861">Alternatif olarak, wranı seçin ve **Ayarlar**' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="f6e38-862">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="f6e38-863">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-863">Create a new request.</span></span>
  * <span data-ttu-id="f6e38-864">**Almak** için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="f6e38-865">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="f6e38-866">Örneğin, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="f6e38-867">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f6e38-868">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-868">Select **Send**.</span></span>

![Get isteği ile Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="f6e38-870">Create yöntemi ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-870">Add a Create method 2.1</span></span>

<span data-ttu-id="f6e38-871">`PostTodoItem` *Controllers/TodoController. cs* içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="f6e38-872">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f6e38-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="f6e38-873">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f6e38-874">`CreatedAtAction`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="f6e38-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="f6e38-875">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="f6e38-876">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f6e38-877">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="f6e38-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="f6e38-878">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar ÖĞESININ URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="f6e38-879">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f6e38-880">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f6e38-881">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="f6e38-882">PostTodoItem yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="f6e38-883">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-883">Build the project.</span></span>
* <span data-ttu-id="f6e38-884">Postman 'da HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f6e38-885">URI değerini olarak ayarlayın `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="f6e38-886">Örneğin, `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="f6e38-887">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="f6e38-888">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f6e38-889">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f6e38-890">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f6e38-891">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-891">Select **Send**.</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/create.png)

  <span data-ttu-id="f6e38-893">Bir 405 yöntemine Izin verilmiyor hatası alırsanız, yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="f6e38-894">Konum üst bilgisi URI 2,1 ' i test edin</span><span class="sxs-lookup"><span data-stu-id="f6e38-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="f6e38-895">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f6e38-896">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-896">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="f6e38-898">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-898">Set the method to GET.</span></span>
* <span data-ttu-id="f6e38-899">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="f6e38-900">Örneğin, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="f6e38-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="f6e38-901">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="f6e38-902">PutTodoItem yöntemi ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="f6e38-903">Aşağıdaki yöntemi ekleyin `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="f6e38-904">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f6e38-905">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f6e38-906">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f6e38-907">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f6e38-908">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="f6e38-909">PutTodoItem yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="f6e38-910">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f6e38-911">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f6e38-912">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f6e38-913">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f6e38-914">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-914">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="f6e38-916">DeleteTodoItem yöntemi ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="f6e38-917">Aşağıdaki yöntemi ekleyin `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f6e38-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="f6e38-918">`DeleteTodoItem`Yanıt 204 ' dir [(içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f6e38-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="f6e38-919">DeleteTodoItem yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="f6e38-920">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f6e38-921">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f6e38-922">Silinecek nesnenin URI 'sini ayarlayın (örneğin, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="f6e38-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="f6e38-923">**Gönder**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-923">Select **Send**.</span></span>

<span data-ttu-id="f6e38-924">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f6e38-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="f6e38-925">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f6e38-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="f6e38-926">JavaScript 2,1 ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="f6e38-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="f6e38-927">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="f6e38-928">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-928">jQuery initiates the request.</span></span> <span data-ttu-id="f6e38-929">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="f6e38-930">Uygulamayı [statik dosyalara sunacak](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) :</span><span class="sxs-lookup"><span data-stu-id="f6e38-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="f6e38-931">Proje dizininde bir *Wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f6e38-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="f6e38-932">*Wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f6e38-933">İçeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="f6e38-934">*Wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f6e38-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f6e38-935">Dosyanın içeriğini aşağıdaki kod ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f6e38-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f6e38-936">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="f6e38-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="f6e38-937">*ÜzerindeProperties\launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="f6e38-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="f6e38-938">`launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html*'de açmaya zorlamak için özelliği kaldırın &mdash; .</span><span class="sxs-lookup"><span data-stu-id="f6e38-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f6e38-939">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="f6e38-940">API çağrılarının açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="f6e38-941">Yapılacaklar öğelerinin bir listesini alın 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="f6e38-942">jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="f6e38-943">`success`Geri çağırma işlevi, istek başarılı olursa çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="f6e38-944">Geri aramada, DOM, yapılacaklar bilgileriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="f6e38-945">Yapılacak bir öğe ekleyin 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="f6e38-946">jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="f6e38-947">`accepts`Ve `contentType` seçenekleri, `application/json` alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="f6e38-948">Yapılacaklar öğesi [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON 'a dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="f6e38-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="f6e38-949">API başarılı bir durum kodu döndürdüğünde, `getData` Işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f6e38-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="f6e38-950">Yapılacak bir öğeyi güncelleştirme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="f6e38-951">Bir yapılacaklar öğesinin güncelleştirilmesi, bir tane eklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="f6e38-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="f6e38-952">`url`Öğenin benzersiz tanımlayıcısını ekleme değişiklikleri ve öğesi `type` `PUT` .</span><span class="sxs-lookup"><span data-stu-id="f6e38-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="f6e38-953">Yapılacak bir öğeyi silme 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="f6e38-954">Bir yapılacaklar öğesinin silinmesi, `type` Ajax çağrısının üzerine ayarlanarak `DELETE` ve öğenin URL 'sindeki benzersiz tanımlayıcısını belirterek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f6e38-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="f6e38-955">Web API 2,1 kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="f6e38-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="f6e38-956">Ek kaynaklar 2,1</span><span class="sxs-lookup"><span data-stu-id="f6e38-956">Additional resources 2.1</span></span>

<span data-ttu-id="f6e38-957">[Bu öğretici için örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="f6e38-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="f6e38-958">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f6e38-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f6e38-959">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="f6e38-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="f6e38-960">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="f6e38-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
