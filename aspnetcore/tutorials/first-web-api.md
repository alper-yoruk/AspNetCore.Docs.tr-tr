---
title: "Öğretici: ASP.NET Core bir Web API 'SI oluşturma"
author: rick-anderson
description: ASP.NET Core ile Web API 'SI oluşturmayı öğrenin.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(Models):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 17f04dc9a0bdcf8ff016d83b915c017ff485cb36
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690697"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="c4357-103">Öğretici: ASP.NET Core bir Web API 'SI oluşturma</span><span class="sxs-lookup"><span data-stu-id="c4357-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="c4357-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Mike te son](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="c4357-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="c4357-105">Bu öğreticide, ASP.NET Core ile Web API 'SI oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c4357-106">Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="c4357-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4357-107">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-107">Create a web API project.</span></span>
> * <span data-ttu-id="c4357-108">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c4357-109">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c4357-110">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c4357-111">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-111">Call the web API with Postman.</span></span>

<span data-ttu-id="c4357-112">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="c4357-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c4357-113">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="c4357-113">Overview</span></span>

<span data-ttu-id="c4357-114">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c4357-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c4357-115">API</span><span class="sxs-lookup"><span data-stu-id="c4357-115">API</span></span> | <span data-ttu-id="c4357-116">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c4357-116">Description</span></span> | <span data-ttu-id="c4357-117">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="c4357-117">Request body</span></span> | <span data-ttu-id="c4357-118">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="c4357-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c4357-119">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="c4357-119">Get all to-do items</span></span> | <span data-ttu-id="c4357-120">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-120">None</span></span> | <span data-ttu-id="c4357-121">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="c4357-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c4357-122">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="c4357-122">Get an item by ID</span></span> | <span data-ttu-id="c4357-123">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-123">None</span></span> | <span data-ttu-id="c4357-124">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c4357-125">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="c4357-125">Add a new item</span></span> | <span data-ttu-id="c4357-126">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-126">To-do item</span></span> | <span data-ttu-id="c4357-127">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c4357-128">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c4357-129">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-129">To-do item</span></span> | <span data-ttu-id="c4357-130">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-130">None</span></span> |
|<span data-ttu-id="c4357-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c4357-132">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c4357-133">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-133">None</span></span> | <span data-ttu-id="c4357-134">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-134">None</span></span>|

<span data-ttu-id="c4357-135">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c4357-135">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c4357-141">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c4357-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-144">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c4357-145">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="c4357-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-147">**Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-147">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="c4357-148">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-148">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="c4357-149">Projeyi *TodoApi* olarak adlandırın ve **Oluştur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-149">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="c4357-150">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 5,0** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="c4357-151">**API** şablonunu seçin ve **Oluştur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-151">Select the **API** template and click **Create** .</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4357-154">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c4357-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c4357-155">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c4357-156">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c4357-157">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-157">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="c4357-158">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="c4357-158">The preceding commands:</span></span>

  * <span data-ttu-id="c4357-159">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="c4357-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c4357-160">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-161">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4357-162">**Dosya** > **yeni çözüm** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-162">Select **File** > **New Solution** .</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c4357-164">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="c4357-165">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c4357-167">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 3. x **hedef çerçevesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="c4357-168">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-168">Select **Next** .</span></span>

* <span data-ttu-id="c4357-169">**Proje adı** için *TodoApi* girin ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-169">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c4357-171">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="c4357-172">Projeyi test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-172">Test the project</span></span>

<span data-ttu-id="c4357-173">Proje şablonu, `WeatherForecast` [Swagger](xref:tutorials/web-api-help-pages-using-swagger)desteğiyle bir API oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c4357-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4357-175">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c4357-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c4357-176">Visual Studio başlatılır:</span><span class="sxs-lookup"><span data-stu-id="c4357-176">Visual Studio launches:</span></span>

* <span data-ttu-id="c4357-177">IIS Express Web sunucusu.</span><span class="sxs-lookup"><span data-stu-id="c4357-177">The IIS Express web server.</span></span>
* <span data-ttu-id="c4357-178">Varsayılan tarayıcı ve `https://localhost:<port>/https://localhost:5001/swagger/index.html` ' a gider, burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="c4357-180">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c4357-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c4357-181">Bir tarayıcıda aşağıdaki URL 'ye gidin: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="c4357-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-182">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c4357-183">Uygulamayı **başlatmak**  >  için **hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c4357-184">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c4357-185">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="c4357-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c4357-186">`/swagger`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/swagger` ).</span><span class="sxs-lookup"><span data-stu-id="c4357-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="c4357-187">Swagger sayfası `/swagger/index.html` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c4357-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="c4357-188">**Al**  >  **deneyin**  >  **Çalıştır** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-188">Select **GET** > **Try it out** > **Execute** .</span></span> <span data-ttu-id="c4357-189">Sayfa şunu görüntüler:</span><span class="sxs-lookup"><span data-stu-id="c4357-189">The page displays:</span></span>

* <span data-ttu-id="c4357-190">Dalgalı tahmin API 'sini test etmek için [kıvrımlı](https://curl.haxx.se/) komutu.</span><span class="sxs-lookup"><span data-stu-id="c4357-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c4357-191">Dalgalı tahmin API 'sini test etmek için URL.</span><span class="sxs-lookup"><span data-stu-id="c4357-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c4357-192">Yanıt kodu, gövde ve üstbilgiler.</span><span class="sxs-lookup"><span data-stu-id="c4357-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="c4357-193">Medya türleri ve örnek değer ve şema içeren bir açılır liste kutusu.</span><span class="sxs-lookup"><span data-stu-id="c4357-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="c4357-194">Swagger, Web API 'Leri için yararlı belgeler ve yardım sayfaları oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="c4357-195">Bu öğretici, bir Web API 'SI oluşturmaya odaklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c4357-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="c4357-196">Swagger hakkında daha fazla bilgi için bkz <xref:tutorials/web-api-help-pages-using-swagger> ..</span><span class="sxs-lookup"><span data-stu-id="c4357-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="c4357-197">**Istek URL** 'sini tarayıcıda kopyalayın ve geçmiş:`https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="c4357-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="c4357-198">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="c4357-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="c4357-199">LaunchUrl 'Yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="c4357-199">Update the launchUrl</span></span>

<span data-ttu-id="c4357-200">*Properties\launchSettings.jstarihinde* , ' `launchUrl` den ' `"swagger"` e Güncelleştir `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="c4357-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="c4357-201">Swagger kaldırıldığından, yukarıdaki biçimlendirme, başlatılan URL 'YI aşağıdaki bölümlerde eklenen denetleyicinin GET yöntemine değiştirir.</span><span class="sxs-lookup"><span data-stu-id="c4357-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="c4357-202">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-202">Add a model class</span></span>

<span data-ttu-id="c4357-203">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c4357-204">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c4357-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-206">**Çözüm Gezgini** , projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="c4357-207">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-207">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="c4357-208">Klasörü adlandırın *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-208">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="c4357-209">Klasöre sağ tıklayın *:::no-loc(Models):::* ve sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="c4357-209">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="c4357-210">Sınıfı *TodoItem* olarak adlandırın ve **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-210">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="c4357-211">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4357-213">Adlı bir klasör ekleyin *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-213">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="c4357-214">`TodoItem` *:::no-loc(Models):::* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-214">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-215">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4357-216">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-216">Right-click the project.</span></span> <span data-ttu-id="c4357-217">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-217">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="c4357-218">Klasörü adlandırın *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-218">Name the folder *:::no-loc(Models):::* .</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c4357-220">Klasöre sağ tıklayın *:::no-loc(Models):::* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-220">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="c4357-221">Sınıfı *TodoItem* olarak adlandırın ve ardından **Yeni** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-221">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="c4357-222">Şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="c4357-223">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="c4357-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c4357-224">Model sınıfları projede herhangi bir yere gidebilir, ancak *:::no-loc(Models):::* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-224">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c4357-225">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-225">Add a database context</span></span>

<span data-ttu-id="c4357-226">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c4357-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c4357-227">Bu sınıf sınıfından türeterek oluşturulur <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="c4357-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c4357-229">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-229">Add NuGet packages</span></span>

* <span data-ttu-id="c4357-230">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="c4357-231">**Gözatın** sekmesini seçin ve ardından \* \* Microsoft girin.</span><span class="sxs-lookup"><span data-stu-id="c4357-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="c4357-232">Arama kutusunda **Entityframeworkcore. SqlServer** .</span><span class="sxs-lookup"><span data-stu-id="c4357-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="c4357-233">5,0 RC sürümü kullanılabilir durumda olan **ön sürümü dahil et** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="c4357-234">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c4357-235">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-235">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="c4357-236">Yukarıdaki yönergeleri kullanarak **Microsoft. EntityFrameworkCore. InMemory** NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="c4357-237">![NuGet Paket Yöneticisi](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="c4357-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c4357-238">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="c4357-239">Klasöre sağ tıklayın *:::no-loc(Models):::* ve sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="c4357-239">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="c4357-240">Sınıfı *TodoContext* olarak adlandırın ve **Ekle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-240">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-241">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c4357-242">Klasöre bir `TodoContext` sınıf ekleyin *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-242">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="c4357-243">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="c4357-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c4357-244">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="c4357-244">Register the database context</span></span>

<span data-ttu-id="c4357-245">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4357-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c4357-246">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4357-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="c4357-247">Aşağıdaki kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c4357-248">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="c4357-248">The preceding code:</span></span>

* <span data-ttu-id="c4357-249">Swagger çağrılarını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="c4357-250">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c4357-251">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c4357-252">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c4357-253">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="c4357-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-255">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c4357-256">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-256">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="c4357-257">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi** ' ni seçin ve ardından **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-257">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="c4357-258">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="c4357-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c4357-259">**Model sınıfında** **TodoItem (TodoApi. :::no-loc(Models)::: )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-259">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="c4357-260">**Veri bağlamı sınıfında** **TodoContext (TodoApi. :::no-loc(Models)::: )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-260">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="c4357-261">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-261">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-262">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c4357-263">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c4357-264">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="c4357-264">The preceding commands:</span></span>

* <span data-ttu-id="c4357-265">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c4357-266">Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.</span><span class="sxs-lookup"><span data-stu-id="c4357-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c4357-267">Yapı iskelesi `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="c4357-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c4357-268">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="c4357-268">The generated code:</span></span>

* <span data-ttu-id="c4357-269">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="c4357-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c4357-270">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c4357-271">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="c4357-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c4357-272">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="c4357-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c4357-273">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c4357-274">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="c4357-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c4357-275">Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="c4357-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c4357-276">API denetleyicileri `[action]` yol şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c4357-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c4357-277">`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c4357-278">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="c4357-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="c4357-279">PostTodoItem Create metodunu güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="c4357-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="c4357-280">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c4357-281">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="c4357-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c4357-282">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="c4357-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c4357-283">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c4357-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c4357-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="c4357-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c4357-285">Başarılı olursa bir [HTTP 201 durum kodu](https://developer.mozilla.org/docs/Web/HTTP/Status/201) döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="c4357-286">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="c4357-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c4357-287">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c4357-288">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c4357-289">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c4357-290">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="c4357-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c4357-291">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="c4357-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c4357-292">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="c4357-292">Install Postman</span></span>

<span data-ttu-id="c4357-293">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c4357-294">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="c4357-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c4357-295">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4357-295">Start the web app.</span></span>
* <span data-ttu-id="c4357-296">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4357-296">Start Postman.</span></span>
* <span data-ttu-id="c4357-297">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="c4357-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c4357-298">**Dosya** > **ayarlarından** ( **genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="c4357-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="c4357-299">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c4357-300">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c4357-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c4357-301">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-301">Create a new request.</span></span>
* <span data-ttu-id="c4357-302">HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="c4357-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c4357-303">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="c4357-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c4357-304">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c4357-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c4357-305">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="c4357-306">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c4357-307">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-307">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="c4357-308">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="c4357-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c4357-309">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-309">Select **Send** .</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="c4357-311">Konum üst bilgisi URI 'sini test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-311">Test the location header URI</span></span>

<span data-ttu-id="c4357-312">Konum üst bilgisi URI 'SI tarayıcıda test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="c4357-313">Konum üst bilgisi URI 'sini kopyalayıp tarayıcıya yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="c4357-314">Postman 'da test etmek için:</span><span class="sxs-lookup"><span data-stu-id="c4357-314">To test in Postman:</span></span>

* <span data-ttu-id="c4357-315">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c4357-316">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="c4357-316">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="c4357-318">HTTP yöntemini olarak ayarlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="c4357-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c4357-319">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="c4357-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c4357-320">Örneğin, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c4357-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c4357-321">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-321">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c4357-322">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="c4357-322">Examine the GET methods</span></span>

<span data-ttu-id="c4357-323">İki uç nokta al uygulandı:</span><span class="sxs-lookup"><span data-stu-id="c4357-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c4357-324">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="c4357-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c4357-325">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c4357-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c4357-326">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="c4357-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c4357-327">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="c4357-327">Test Get with Postman</span></span>

* <span data-ttu-id="c4357-328">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-328">Create a new request.</span></span>
* <span data-ttu-id="c4357-329">**Almak** için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-329">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="c4357-330">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="c4357-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c4357-331">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c4357-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c4357-332">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c4357-333">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-333">Select **Send** .</span></span>

<span data-ttu-id="c4357-334">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-334">This app uses an in-memory database.</span></span> <span data-ttu-id="c4357-335">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="c4357-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c4357-336">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="c4357-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c4357-337">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="c4357-337">Routing and URL paths</span></span>

<span data-ttu-id="c4357-338">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4357-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c4357-339">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="c4357-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c4357-340">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="c4357-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c4357-341">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c4357-342">Bu örnek için denetleyici sınıfı adı **todoıtems** denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="c4357-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c4357-343">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c4357-344">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c4357-345">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c4357-345">This sample doesn't use a template.</span></span> <span data-ttu-id="c4357-346">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c4357-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c4357-347">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c4357-348">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="c4357-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c4357-349">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="c4357-349">Return values</span></span>

<span data-ttu-id="c4357-350">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c4357-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c4357-351">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="c4357-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c4357-352">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak [200 Tamam](https://developer.mozilla.org/docs/Web/HTTP/Status/200)olur.</span><span class="sxs-lookup"><span data-stu-id="c4357-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c4357-353">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c4357-354">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c4357-355">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="c4357-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c4357-356">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem [404 durum](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c4357-357">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c4357-358">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="c4357-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c4357-359">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="c4357-359">The PutTodoItem method</span></span>

<span data-ttu-id="c4357-360">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c4357-361">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="c4357-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c4357-362">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c4357-363">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c4357-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c4357-364">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4357-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c4357-365">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c4357-366">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="c4357-367">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c4357-368">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c4357-369">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="c4357-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c4357-370">Kimlik = 1 olan Yapılacaklar öğesini güncelleştirin ve adını şu şekilde ayarlayın `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="c4357-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c4357-371">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c4357-371">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c4357-373">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="c4357-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="c4357-374">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c4357-375">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c4357-376">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="c4357-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c4357-377">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="c4357-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c4357-378">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="c4357-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c4357-379">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-379">Select **Send** .</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c4357-380">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="c4357-380">Prevent over-posting</span></span>

<span data-ttu-id="c4357-381">Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="c4357-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c4357-382">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c4357-383">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="c4357-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c4357-384">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c4357-385">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="c4357-386">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c4357-386">A DTO may be used to:</span></span>

* <span data-ttu-id="c4357-387">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-387">Prevent over-posting.</span></span>
* <span data-ttu-id="c4357-388">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c4357-389">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c4357-390">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c4357-391">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c4357-392">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="c4357-393">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c4357-394">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="c4357-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c4357-395">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c4357-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c4357-396">`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="c4357-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c4357-397">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="c4357-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c4357-398">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="c4357-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="c4357-399">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c4357-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c4357-400">Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="c4357-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4357-401">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-401">Create a web API project.</span></span>
> * <span data-ttu-id="c4357-402">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c4357-403">CRUD yöntemleriyle bir denetleyiciyi dolandırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c4357-404">Yönlendirmeyi, URL yollarını ve dönüş değerlerini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c4357-405">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-405">Call the web API with Postman.</span></span>

<span data-ttu-id="c4357-406">Sonunda, bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="c4357-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c4357-407">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="c4357-407">Overview</span></span>

<span data-ttu-id="c4357-408">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c4357-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c4357-409">API</span><span class="sxs-lookup"><span data-stu-id="c4357-409">API</span></span> | <span data-ttu-id="c4357-410">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c4357-410">Description</span></span> | <span data-ttu-id="c4357-411">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="c4357-411">Request body</span></span> | <span data-ttu-id="c4357-412">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="c4357-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c4357-413">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="c4357-413">Get all to-do items</span></span> | <span data-ttu-id="c4357-414">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-414">None</span></span> | <span data-ttu-id="c4357-415">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="c4357-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c4357-416">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="c4357-416">Get an item by ID</span></span> | <span data-ttu-id="c4357-417">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-417">None</span></span> | <span data-ttu-id="c4357-418">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c4357-419">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="c4357-419">Add a new item</span></span> | <span data-ttu-id="c4357-420">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-420">To-do item</span></span> | <span data-ttu-id="c4357-421">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c4357-422">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c4357-423">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-423">To-do item</span></span> | <span data-ttu-id="c4357-424">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-424">None</span></span> |
|<span data-ttu-id="c4357-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c4357-426">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c4357-427">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-427">None</span></span> | <span data-ttu-id="c4357-428">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-428">None</span></span>|

<span data-ttu-id="c4357-429">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c4357-429">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c4357-435">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c4357-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-438">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c4357-439">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="c4357-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-441">**Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-441">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="c4357-442">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-442">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="c4357-443">Projeyi *TodoApi* olarak adlandırın ve **Oluştur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-443">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="c4357-444">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c4357-445">**API** şablonunu seçin ve **Oluştur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-445">Select the **API** template and click **Create** .</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4357-448">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c4357-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c4357-449">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c4357-450">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c4357-451">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-451">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="c4357-452">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="c4357-452">The preceding commands:</span></span>

  * <span data-ttu-id="c4357-453">Yeni bir Web API projesi oluşturur ve Visual Studio Code açar.</span><span class="sxs-lookup"><span data-stu-id="c4357-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c4357-454">Sonraki bölümde gerekli olan NuGet paketlerini ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-455">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4357-456">**Dosya** > **yeni çözüm** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-456">Select **File** > **New Solution** .</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c4357-458">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="c4357-459">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS API şablonu seçimi](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c4357-461">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 3. x **hedef çerçevesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="c4357-462">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-462">Select **Next** .</span></span>

* <span data-ttu-id="c4357-463">**Proje adı** için *TodoApi* girin ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-463">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c4357-465">Proje klasöründe bir komut terminali açın ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="c4357-466">API’yi test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-466">Test the API</span></span>

<span data-ttu-id="c4357-467">Proje şablonu bir API oluşturur `WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="c4357-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="c4357-468">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4357-470">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c4357-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c4357-471">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/WeatherForecast` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c4357-472">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="c4357-473">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-473">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4357-475">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c4357-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c4357-476">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="c4357-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-477">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c4357-478">Uygulamayı **başlatmak**  >  için **hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c4357-479">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c4357-480">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="c4357-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c4357-481">`/WeatherForecast`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="c4357-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="c4357-482">Aşağıdakine benzer bir JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="c4357-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="c4357-483">Model sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-483">Add a model class</span></span>

<span data-ttu-id="c4357-484">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c4357-485">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c4357-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-487">**Çözüm Gezgini** , projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="c4357-488">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-488">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="c4357-489">Klasörü adlandırın *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-489">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="c4357-490">Klasöre sağ tıklayın *:::no-loc(Models):::* ve sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="c4357-490">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="c4357-491">Sınıfı *TodoItem* olarak adlandırın ve **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-491">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="c4357-492">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4357-494">Adlı bir klasör ekleyin *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-494">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="c4357-495">`TodoItem` *:::no-loc(Models):::* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-495">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-496">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4357-497">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-497">Right-click the project.</span></span> <span data-ttu-id="c4357-498">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-498">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="c4357-499">Klasörü adlandırın *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-499">Name the folder *:::no-loc(Models):::* .</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c4357-501">Klasöre sağ tıklayın *:::no-loc(Models):::* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-501">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="c4357-502">Sınıfı *TodoItem* olarak adlandırın ve ardından **Yeni** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-502">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="c4357-503">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="c4357-504">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="c4357-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c4357-505">Model sınıfları projede herhangi bir yere gidebilir, ancak *:::no-loc(Models):::* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-505">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c4357-506">Veritabanı bağlamı ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-506">Add a database context</span></span>

<span data-ttu-id="c4357-507">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c4357-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c4357-508">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="c4357-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c4357-510">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-510">Add NuGet packages</span></span>

* <span data-ttu-id="c4357-511">**Araçlar** menüsünde **nuget Paket Yöneticisi > çözüm Için NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="c4357-512">**Araştır** sekmesini seçin ve arama kutusuna **Microsoft. Entityframeworkcore. SqlServer** yazın.</span><span class="sxs-lookup"><span data-stu-id="c4357-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="c4357-513">Sol bölmedeki **Microsoft. EntityFrameworkCore. SqlServer** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c4357-514">Sağ bölmedeki **Proje** onay kutusunu seçin ve ardından **Install** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-514">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="c4357-515">Yukarıdaki yönergeleri kullanarak **Microsoft. EntityFrameworkCore. InMemory** NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet Paket Yöneticisi](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c4357-517">TodoContext veritabanı bağlamını ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="c4357-518">Klasöre sağ tıklayın *:::no-loc(Models):::* ve sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="c4357-518">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="c4357-519">Sınıfı *TodoContext* olarak adlandırın ve **Ekle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-519">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-520">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c4357-521">Klasöre bir `TodoContext` sınıf ekleyin *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-521">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="c4357-522">Aşağıdaki kodu girin:</span><span class="sxs-lookup"><span data-stu-id="c4357-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c4357-523">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="c4357-523">Register the database context</span></span>

<span data-ttu-id="c4357-524">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4357-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c4357-525">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4357-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="c4357-526">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c4357-527">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="c4357-527">The preceding code:</span></span>

* <span data-ttu-id="c4357-528">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c4357-529">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c4357-530">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c4357-531">Denetleyiciyi bir denetleyiciye katlama</span><span class="sxs-lookup"><span data-stu-id="c4357-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-533">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c4357-534">**Add** > **Yeni yapı iskelesi öğesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-534">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="c4357-535">**Entity Framework kullanarak ve eylemler Içeren API denetleyicisi** ' ni seçin ve ardından **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-535">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="c4357-536">**API denetleyiciyi eylemler Ile Ekle ' de Entity Framework** iletişim kutusunu kullanarak:</span><span class="sxs-lookup"><span data-stu-id="c4357-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c4357-537">**Model sınıfında** **TodoItem (TodoApi. :::no-loc(Models)::: )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-537">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="c4357-538">**Veri bağlamı sınıfında** **TodoContext (TodoApi. :::no-loc(Models)::: )** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-538">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="c4357-539">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-539">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-540">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c4357-541">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c4357-542">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="c4357-542">The preceding commands:</span></span>

* <span data-ttu-id="c4357-543">Yapı iskelesi için gereken NuGet paketlerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c4357-544">Scafkatlama altyapısını ( `dotnet-aspnet-codegenerator` ) kurar.</span><span class="sxs-lookup"><span data-stu-id="c4357-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c4357-545">Yapı iskelesi `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="c4357-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c4357-546">Oluşturulan kod:</span><span class="sxs-lookup"><span data-stu-id="c4357-546">The generated code:</span></span>

* <span data-ttu-id="c4357-547">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="c4357-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c4357-548">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c4357-549">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="c4357-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c4357-550">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="c4357-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c4357-551">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c4357-552">İçin ASP.NET Core şablonları:</span><span class="sxs-lookup"><span data-stu-id="c4357-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c4357-553">Görünümleri olan denetleyiciler `[action]` yol şablonuna dahildir.</span><span class="sxs-lookup"><span data-stu-id="c4357-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c4357-554">API denetleyicileri `[action]` yol şablonuna dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="c4357-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c4357-555">`[action]`Belirteç yol şablonunda olmadığında, [eylem](xref:mvc/controllers/routing#action) adı rotadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c4357-556">Diğer bir deyişle, eylemin ilişkili Yöntem adı eşleşen rotada kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="c4357-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="c4357-557">PostTodoItem Create metodunu inceleyin</span><span class="sxs-lookup"><span data-stu-id="c4357-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="c4357-558">İçindeki return ifadesini, `PostTodoItem` [NameOf](/dotnet/csharp/language-reference/operators/nameof) işlecini kullanmak için değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c4357-559">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="c4357-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c4357-560">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="c4357-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c4357-561">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c4357-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c4357-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="c4357-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c4357-563">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="c4357-564">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="c4357-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c4357-565">Yanıta bir [konum](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) üst bilgisi ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c4357-566">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar öğesinin [URI](https://developer.mozilla.org/docs/Glossary/URI) 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c4357-567">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c4357-568">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="c4357-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c4357-569">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="c4357-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c4357-570">Postman yükleme</span><span class="sxs-lookup"><span data-stu-id="c4357-570">Install Postman</span></span>

<span data-ttu-id="c4357-571">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c4357-572">[Postman](https://www.getpostman.com/downloads/) yükleme</span><span class="sxs-lookup"><span data-stu-id="c4357-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c4357-573">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4357-573">Start the web app.</span></span>
* <span data-ttu-id="c4357-574">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4357-574">Start Postman.</span></span>
* <span data-ttu-id="c4357-575">**SSL sertifikası doğrulamasını** devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="c4357-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c4357-576">**Dosya** > **ayarlarından** ( **genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="c4357-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="c4357-577">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c4357-578">Postman ile test PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c4357-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c4357-579">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-579">Create a new request.</span></span>
* <span data-ttu-id="c4357-580">HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="c4357-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c4357-581">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="c4357-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c4357-582">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c4357-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c4357-583">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="c4357-584">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c4357-585">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-585">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="c4357-586">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="c4357-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c4357-587">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-587">Select **Send** .</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="c4357-589">Konum üstbilgisi URI 'sini Postman ile test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="c4357-590">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c4357-591">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="c4357-591">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/3/create.png)

* <span data-ttu-id="c4357-593">HTTP yöntemini olarak ayarlayın `GET` .</span><span class="sxs-lookup"><span data-stu-id="c4357-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c4357-594">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="c4357-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c4357-595">Örneğin, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="c4357-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c4357-596">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-596">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c4357-597">GET yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="c4357-597">Examine the GET methods</span></span>

<span data-ttu-id="c4357-598">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="c4357-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c4357-599">Tarayıcıdan veya Postman 'dan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="c4357-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c4357-600">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c4357-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c4357-601">Şuna benzer bir yanıt, şu çağrı tarafından üretilir `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="c4357-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c4357-602">Postman ile test al</span><span class="sxs-lookup"><span data-stu-id="c4357-602">Test Get with Postman</span></span>

* <span data-ttu-id="c4357-603">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-603">Create a new request.</span></span>
* <span data-ttu-id="c4357-604">**Almak** için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-604">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="c4357-605">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="c4357-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c4357-606">Örneğin, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="c4357-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c4357-607">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c4357-608">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-608">Select **Send** .</span></span>

<span data-ttu-id="c4357-609">Bu uygulama, bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-609">This app uses an in-memory database.</span></span> <span data-ttu-id="c4357-610">Uygulama durdurulup başlatılırsa, önceki GET isteği herhangi bir veri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="c4357-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c4357-611">Hiçbir veri döndürülmezse, verileri uygulamaya [gönderin](#post) .</span><span class="sxs-lookup"><span data-stu-id="c4357-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c4357-612">Yönlendirme ve URL yolları</span><span class="sxs-lookup"><span data-stu-id="c4357-612">Routing and URL paths</span></span>

<span data-ttu-id="c4357-613">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4357-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c4357-614">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="c4357-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c4357-615">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="c4357-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c4357-616">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c4357-617">Bu örnek için denetleyici sınıfı adı **todoıtems** denetleyicisidir, bu nedenle denetleyicinin adı "todoıtems" olur.</span><span class="sxs-lookup"><span data-stu-id="c4357-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c4357-618">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c4357-619">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c4357-620">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c4357-620">This sample doesn't use a template.</span></span> <span data-ttu-id="c4357-621">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c4357-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c4357-622">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c4357-623">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="c4357-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c4357-624">Dönüş değerleri</span><span class="sxs-lookup"><span data-stu-id="c4357-624">Return values</span></span> 

<span data-ttu-id="c4357-625">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c4357-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c4357-626">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="c4357-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c4357-627">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="c4357-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c4357-628">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c4357-629">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c4357-630">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="c4357-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c4357-631">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c4357-632">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c4357-633">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="c4357-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c4357-634">PutTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="c4357-634">The PutTodoItem method</span></span>

<span data-ttu-id="c4357-635">`PutTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c4357-636">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="c4357-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c4357-637">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c4357-638">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c4357-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c4357-639">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4357-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c4357-640">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c4357-641">PutTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="c4357-642">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c4357-643">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c4357-644">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="c4357-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c4357-645">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c4357-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c4357-646">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c4357-646">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c4357-648">DeleteTodoItem yöntemi</span><span class="sxs-lookup"><span data-stu-id="c4357-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="c4357-649">`DeleteTodoItem` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c4357-650">DeleteTodoItem yöntemini test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c4357-651">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="c4357-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c4357-652">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="c4357-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c4357-653">Silinecek nesnenin URI 'sini ayarlayın (örneğin `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="c4357-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c4357-654">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-654">Select **Send** .</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c4357-655">Fazla nakletmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="c4357-655">Prevent over-posting</span></span>

<span data-ttu-id="c4357-656">Şu anda örnek uygulama tüm nesneyi kullanıma sunar `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="c4357-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c4357-657">Üretim uygulamaları tipik olarak, bir modelin alt kümesini kullanarak girdi ve döndürülen verileri sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c4357-658">Bunun arkasında birden çok neden vardır ve güvenlik önemli bir değer.</span><span class="sxs-lookup"><span data-stu-id="c4357-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c4357-659">Bir modelin alt kümesi genellikle Veri Aktarımı nesnesi (DTO), giriş modeli veya görünüm modeli olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c4357-660">Bu makalede **DTO** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="c4357-661">Bir DTO için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c4357-661">A DTO may be used to:</span></span>

* <span data-ttu-id="c4357-662">Fazla nakletmeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-662">Prevent over-posting.</span></span>
* <span data-ttu-id="c4357-663">İstemcilerin görüntülemesi beklenen özellikleri gizleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c4357-664">Yük boyutunu azaltmak için bazı özellikleri atlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c4357-665">İç içe geçmiş nesneler içeren nesne grafiklerini düzleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c4357-666">Düzleştirilmiş nesne grafikleri istemciler için daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c4357-667">DTO yaklaşımını göstermek için, `TodoItem` sınıfı gizli bir alan içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="c4357-668">Gizli alanın bu uygulamadan gizlenmesi gerekir, ancak bir yönetim uygulaması onu kullanıma sunmayı seçebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c4357-669">Gizli dizi alanını nakledebildiğinizi ve alabilirim.</span><span class="sxs-lookup"><span data-stu-id="c4357-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c4357-670">Bir DTO modeli oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c4357-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c4357-671">`TodoItemsController`Kullanmak için öğesini güncelleştirin `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="c4357-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c4357-672">Gizli dizi alanını nakledemeyeceğinizi veya alamazsınız.</span><span class="sxs-lookup"><span data-stu-id="c4357-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c4357-673">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="c4357-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="c4357-674">Bkz. [öğretici: JavaScript ile ASP.NET Core Web API 'Si çağırma](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="c4357-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c4357-675">Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:</span><span class="sxs-lookup"><span data-stu-id="c4357-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c4357-676">Bir Web API projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-676">Create a web API project.</span></span>
> * <span data-ttu-id="c4357-677">Bir model sınıfı ve bir veritabanı bağlamı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c4357-678">Denetleyici ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-678">Add a controller.</span></span>
> * <span data-ttu-id="c4357-679">CRUD yöntemleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="c4357-680">Yönlendirme ve URL yollarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="c4357-681">Dönüş değerlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="c4357-681">Specify return values.</span></span>
> * <span data-ttu-id="c4357-682">Postman ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="c4357-683">JavaScript ile Web API 'sini çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="c4357-684">Sonunda, ilişkisel bir veritabanında depolanan "yapılacaklar" öğelerini yönetebilmek için bir Web API 'SI vardır.</span><span class="sxs-lookup"><span data-stu-id="c4357-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="c4357-685">Genel Bakış 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-685">Overview 2.1</span></span>

<span data-ttu-id="c4357-686">Bu öğretici aşağıdaki API 'YI oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c4357-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c4357-687">API</span><span class="sxs-lookup"><span data-stu-id="c4357-687">API</span></span> | <span data-ttu-id="c4357-688">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c4357-688">Description</span></span> | <span data-ttu-id="c4357-689">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="c4357-689">Request body</span></span> | <span data-ttu-id="c4357-690">Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="c4357-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="c4357-691">/Api/TodoItems al</span><span class="sxs-lookup"><span data-stu-id="c4357-691">GET /api/TodoItems</span></span> | <span data-ttu-id="c4357-692">Tüm yapılacaklar öğelerini Al</span><span class="sxs-lookup"><span data-stu-id="c4357-692">Get all to-do items</span></span> | <span data-ttu-id="c4357-693">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-693">None</span></span> | <span data-ttu-id="c4357-694">Yapılacaklar öğeleri dizisi</span><span class="sxs-lookup"><span data-stu-id="c4357-694">Array of to-do items</span></span>|
|<span data-ttu-id="c4357-695">/Api/TodoItems/{id} al</span><span class="sxs-lookup"><span data-stu-id="c4357-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="c4357-696">KIMLIĞE göre öğe al</span><span class="sxs-lookup"><span data-stu-id="c4357-696">Get an item by ID</span></span> | <span data-ttu-id="c4357-697">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-697">None</span></span> | <span data-ttu-id="c4357-698">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-698">To-do item</span></span>|
|<span data-ttu-id="c4357-699">POST/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c4357-699">POST /api/TodoItems</span></span> | <span data-ttu-id="c4357-700">Yeni öğe Ekle</span><span class="sxs-lookup"><span data-stu-id="c4357-700">Add a new item</span></span> | <span data-ttu-id="c4357-701">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-701">To-do item</span></span> | <span data-ttu-id="c4357-702">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-702">To-do item</span></span> |
|<span data-ttu-id="c4357-703">/Api/TodoItems/{id} koy</span><span class="sxs-lookup"><span data-stu-id="c4357-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="c4357-704">Mevcut bir öğeyi güncelleştir &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c4357-705">Yapılacaklar öğesi</span><span class="sxs-lookup"><span data-stu-id="c4357-705">To-do item</span></span> | <span data-ttu-id="c4357-706">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-706">None</span></span> |
|<span data-ttu-id="c4357-707">/Api/TodoItems/{id} &nbsp; Sil &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="c4357-708">Öğe &nbsp; silme &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c4357-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c4357-709">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-709">None</span></span> | <span data-ttu-id="c4357-710">Yok</span><span class="sxs-lookup"><span data-stu-id="c4357-710">None</span></span>|

<span data-ttu-id="c4357-711">Aşağıdaki diyagramda uygulamanın tasarımı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c4357-711">The following diagram shows the design of the app.</span></span>

![İstemci, sol taraftaki bir kutu ile temsil edilir.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="c4357-717">Önkoşullar 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-720">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="c4357-721">Web projesi oluşturma 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-723">**Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-723">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="c4357-724">**ASP.NET Core Web uygulaması** şablonunu seçin ve **İleri** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-724">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="c4357-725">Projeyi *TodoApi* olarak adlandırın ve **Oluştur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-725">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="c4357-726">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 2,2** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="c4357-727">**API** şablonunu seçin ve **Oluştur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-727">Select the **API** template and click **Create** .</span></span> <span data-ttu-id="c4357-728">**Docker desteğini etkinleştir** **' i seçmeyin** .</span><span class="sxs-lookup"><span data-stu-id="c4357-728">**Don't** select **Enable Docker Support** .</span></span>

![VS Yeni proje iletişim kutusu](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4357-731">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c4357-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c4357-732">Dizinleri ( `cd` ) proje klasörünü içerecek olan klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c4357-733">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c4357-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="c4357-734">Bu komutlar yeni bir Web API projesi oluşturur ve yeni proje klasöründe Visual Studio Code yeni bir örneğini açar.</span><span class="sxs-lookup"><span data-stu-id="c4357-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="c4357-735">Bir iletişim kutusu projeye gerekli varlıkları eklemek isteyip istemediğinizi sorduğunda **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-735">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-736">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4357-737">**Dosya** > **yeni çözüm** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-737">Select **File** > **New Solution** .</span></span>

  ![macOS yeni çözüm](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c4357-739">Sürüm 8,6 ' den önceki Mac için Visual Studio, **.NET Core**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="c4357-740">Sürüm 8,6 veya üzeri sürümlerde **Web ve konsol**  >  **uygulama**  >  **API 'si**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>
  
* <span data-ttu-id="c4357-741">**Yeni ASP.NET Core Web API 'Sini Yapılandır** iletişim kutusunda en son .NET Core 2. x **hedef çerçevesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework** .</span></span> <span data-ttu-id="c4357-742">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-742">Select **Next** .</span></span>

* <span data-ttu-id="c4357-743">**Proje adı** için *TodoApi* girin ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-743">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![yapılandırma iletişim kutusu](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="c4357-745">API 2,1 'YI test etme</span><span class="sxs-lookup"><span data-stu-id="c4357-745">Test the API 2.1</span></span>

<span data-ttu-id="c4357-746">Proje şablonu bir API oluşturur `values` .</span><span class="sxs-lookup"><span data-stu-id="c4357-746">The project template creates a `values` API.</span></span> <span data-ttu-id="c4357-747">`Get`Uygulamayı test etmek için bir tarayıcıdan yöntemi çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4357-749">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c4357-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c4357-750">Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>/api/values` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c4357-751">IIS Express sertifikaya güvenip güvenmemeyi soran bir iletişim kutusu alırsanız **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="c4357-752">Sonraki görüntülenen **güvenlik uyarısı** Iletişim kutusunda **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-752">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4357-754">Uygulamayı çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c4357-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c4357-755">Bir tarayıcıda aşağıdaki URL 'ye gidin: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="c4357-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-756">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c4357-757">Uygulamayı **başlatmak**  >  için **hata ayıklamayı Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c4357-758">Mac için Visual Studio bir tarayıcı başlatır ve ' a gider `https://localhost:<port>` , burada `<port>` rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c4357-759">HTTP 404 (bulunamadı) hatası döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="c4357-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c4357-760">`/api/values`URL 'ye ekleyin (URL 'yi olarak değiştirin `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="c4357-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="c4357-761">Aşağıdaki JSON döndürülür:</span><span class="sxs-lookup"><span data-stu-id="c4357-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="c4357-762">Model sınıfı ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-762">Add a model class 2.1</span></span>

<span data-ttu-id="c4357-763">*Model* , uygulamanın yönettiği verileri temsil eden bir sınıf kümesidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c4357-764">Bu uygulamanın modeli tek bir `TodoItem` sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c4357-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-766">**Çözüm Gezgini** , projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="c4357-767">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-767">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="c4357-768">Klasörü adlandırın *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-768">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="c4357-769">Klasöre sağ tıklayın *:::no-loc(Models):::* ve sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="c4357-769">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="c4357-770">Sınıfı *TodoItem* olarak adlandırın ve **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-770">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="c4357-771">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4357-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4357-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4357-773">Adlı bir klasör ekleyin *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-773">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="c4357-774">`TodoItem` *:::no-loc(Models):::* Klasöre aşağıdaki kodla bir sınıf ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-774">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c4357-775">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c4357-776">Projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-776">Right-click the project.</span></span> <span data-ttu-id="c4357-777">**Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-777">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="c4357-778">Klasörü adlandırın *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-778">Name the folder *:::no-loc(Models):::* .</span></span>

  ![Yeni klasör](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c4357-780">Klasöre sağ tıklayın *:::no-loc(Models):::* ve **Add** > **yeni dosya** Ekle > **genel** > **boş sınıfı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-780">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="c4357-781">Sınıfı *TodoItem* olarak adlandırın ve ardından **Yeni** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-781">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="c4357-782">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoItem.cs)]

<span data-ttu-id="c4357-783">`Id`Özelliği, ilişkisel bir veritabanındaki benzersiz anahtar olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="c4357-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c4357-784">Model sınıfları projede herhangi bir yere gidebilir, ancak *:::no-loc(Models):::* klasör kural tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-784">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="c4357-785">Veritabanı bağlamı ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-785">Add a database context 2.1</span></span>

<span data-ttu-id="c4357-786">*Veritabanı bağlamı* , bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="c4357-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c4357-787">Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` .</span><span class="sxs-lookup"><span data-stu-id="c4357-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-789">Klasöre sağ tıklayın *:::no-loc(Models):::* ve sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="c4357-789">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="c4357-790">Sınıfı *TodoContext* olarak adlandırın ve **Ekle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-790">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-791">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c4357-792">Klasöre bir `TodoContext` sınıf ekleyin *:::no-loc(Models):::* .</span><span class="sxs-lookup"><span data-stu-id="c4357-792">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="c4357-793">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="c4357-794">Veritabanı bağlamını kaydetme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-794">Register the database context 2.1</span></span>

<span data-ttu-id="c4357-795">ASP.NET Core, VERITABANı bağlamı gibi hizmetlerin [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısına kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4357-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c4357-796">Kapsayıcı hizmeti denetleyicilere sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4357-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="c4357-797">Aşağıdaki Vurgulanan kodla *Startup.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="c4357-798">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="c4357-798">The preceding code:</span></span>

* <span data-ttu-id="c4357-799">Kullanılmayan `using` bildirimleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c4357-800">Veritabanı bağlamını dı kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c4357-801">Veritabanı bağlamının bellek içi bir veritabanını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="c4357-802">Denetleyici ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-804">*Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c4357-805">**Add** > **Yeni öğe** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-805">Select **Add** > **New Item** .</span></span>
* <span data-ttu-id="c4357-806">**Yeni öğe Ekle** iletişim kutusunda, **API denetleyici sınıfı** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="c4357-807">Sınıfı *TodoController* olarak adlandırın ve **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-807">Name the class *TodoController* , and select **Add** .</span></span>

  ![Arama kutusu ve Web API denetleyicisi seçiliyken denetleyiciyi içeren yeni öğe iletişim kutusu Ekle](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-809">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c4357-810">*Denetleyiciler* klasöründe adlı bir sınıf oluşturun `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="c4357-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="c4357-811">Şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="c4357-812">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="c4357-812">The preceding code:</span></span>

* <span data-ttu-id="c4357-813">Yöntemler olmadan bir API denetleyici sınıfı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c4357-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="c4357-814">Sınıfını [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) özniteliğiyle işaretler.</span><span class="sxs-lookup"><span data-stu-id="c4357-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c4357-815">Bu öznitelik, denetleyicinin Web API isteklerine yanıt verdiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c4357-816">Özniteliğin izin aldığı belirli davranışlar hakkında daha fazla bilgi için bkz <xref:web-api/index> ..</span><span class="sxs-lookup"><span data-stu-id="c4357-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c4357-817">Veritabanı bağlamını () denetleyiciye eklemek için DI kullanır `TodoContext` .</span><span class="sxs-lookup"><span data-stu-id="c4357-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c4357-818">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="c4357-819">Veritabanı boşsa veritabanına adlı bir öğe ekler `Item1` .</span><span class="sxs-lookup"><span data-stu-id="c4357-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="c4357-820">Bu kod oluşturucuda yer aldığı için, her yeni HTTP isteği olduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="c4357-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="c4357-821">Tüm öğeleri silerseniz, `Item1` BIR API yönteminin bir sonraki çağrılışında Oluşturucu yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c4357-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="c4357-822">Bu nedenle, aslında çalışırken silme işe yaramadı gibi görünebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="c4357-823">Get yöntemleri ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-823">Add Get methods 2.1</span></span>

<span data-ttu-id="c4357-824">Yapılacaklar öğelerini alan bir API sağlamak için aşağıdaki yöntemleri `TodoController` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="c4357-825">Bu yöntemler iki al uç noktası uygular:</span><span class="sxs-lookup"><span data-stu-id="c4357-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="c4357-826">Hala çalışıyorsa uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="c4357-826">Stop the app if it's still running.</span></span> <span data-ttu-id="c4357-827">Ardından, en son değişiklikleri dahil etmek için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="c4357-828">Bir tarayıcıdan iki uç noktayı çağırarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="c4357-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="c4357-829">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c4357-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="c4357-830">Aşağıdaki HTTP yanıtı, çağrısı tarafından oluşturulur `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="c4357-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="c4357-831">Yönlendirme ve URL yolları 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="c4357-832">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Öznitelik, BIR HTTP GET isteğine yanıt veren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4357-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c4357-833">Her yöntemin URL yolu şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="c4357-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c4357-834">Denetleyicinin özniteliğinde şablon dizesiyle başlayın `Route` :</span><span class="sxs-lookup"><span data-stu-id="c4357-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="c4357-835">`[controller]`Denetleyicinin adıyla değiştirin; bu kural, denetleyici sınıf adı "denetleyici" sonekidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c4357-836">Bu örnek için denetleyici sınıf adı **Todo** Controller olduğundan, denetleyici adı "Todo" olur.</span><span class="sxs-lookup"><span data-stu-id="c4357-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="c4357-837">ASP.NET Core [yönlendirme](xref:mvc/controllers/routing) büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c4357-838">`[HttpGet]`Özniteliğin bir yol şablonu varsa (örneğin, `[HttpGet("products")]` ), yola ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c4357-839">Bu örnek, bir şablon kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c4357-839">This sample doesn't use a template.</span></span> <span data-ttu-id="c4357-840">Daha fazla bilgi için bkz. [http [fiil] öznitelikleriyle öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="c4357-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c4357-841">Aşağıdaki `GetTodoItem` yöntemde, yapılacaklar `"{id}"` öğesinin benzersiz tanımlayıcısı için bir yer tutucu değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="c4357-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c4357-842">`GetTodoItem`Çağrıldığında, `"{id}"` URL 'deki değeri, yönteminin parametresindeki yöntemine sağlanır `id` .</span><span class="sxs-lookup"><span data-stu-id="c4357-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="c4357-843">Dönüş değerleri 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-843">Return values 2.1</span></span>

<span data-ttu-id="c4357-844">`GetTodoItems`Ve yöntemlerinin dönüş türü `GetTodoItem` [ActionResult \<T> türüdür](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="c4357-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c4357-845">ASP.NET Core nesneyi [JSON](https://www.json.org/) 'a otomatik olarak serileştirir ve yanıt ILETISININ gövdesine JSON yazar.</span><span class="sxs-lookup"><span data-stu-id="c4357-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c4357-846">Bu dönüş türü için yanıt kodu, işlenmemiş özel durum olmadığı varsayılarak 200 ' dir.</span><span class="sxs-lookup"><span data-stu-id="c4357-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c4357-847">İşlenmemiş özel durumlar 5 xx hataya çevrilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c4357-848">`ActionResult` dönüş türleri, geniş bir HTTP durum kodu aralığını temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c4357-849">Örneğin, `GetTodoItem` iki farklı durum değeri döndürebilir:</span><span class="sxs-lookup"><span data-stu-id="c4357-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c4357-850">İstenen KIMLIKLE eşleşen hiçbir öğe yoksa, yöntem bir 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> hata kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c4357-851">Aksi takdirde, yöntemi bir JSON yanıt gövdesi ile 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c4357-852">`item`Sonuçları BIR HTTP 200 yanıtına döndürme.</span><span class="sxs-lookup"><span data-stu-id="c4357-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="c4357-853">GetTodoItems yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="c4357-854">Bu öğretici, Web API 'sini test etmek için Postman kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c4357-855">[Postman](https://www.getpostman.com/downloads/)'yi yükleme.</span><span class="sxs-lookup"><span data-stu-id="c4357-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="c4357-856">Web uygulamasını başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4357-856">Start the web app.</span></span>
* <span data-ttu-id="c4357-857">Postman 'ı başlatın.</span><span class="sxs-lookup"><span data-stu-id="c4357-857">Start Postman.</span></span>
* <span data-ttu-id="c4357-858">**SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="c4357-858">Disable **SSL certificate verification** .</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4357-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4357-860">**Dosya** > **ayarlarından** ( **genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="c4357-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c4357-861">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4357-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c4357-862">**Postman**  >  **tercihleri** ' nden ( **genel** sekmesinden) **SSL sertifikası doğrulamasını** devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="c4357-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification** .</span></span> <span data-ttu-id="c4357-863">Alternatif olarak, wranı seçin ve **Ayarlar** ' ı seçip SSL sertifikası doğrulamasını devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="c4357-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="c4357-864">Denetleyiciyi test ettikten sonra SSL sertifikası doğrulamasını yeniden etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4357-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="c4357-865">Yeni bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-865">Create a new request.</span></span>
  * <span data-ttu-id="c4357-866">**Almak** için http yöntemini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-866">Set the HTTP method to **GET** .</span></span>
  * <span data-ttu-id="c4357-867">İstek URI 'sini olarak ayarlayın `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="c4357-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="c4357-868">Örneğin, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="c4357-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="c4357-869">Postman 'da **iki bölme görünümü** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c4357-870">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-870">Select **Send** .</span></span>

![Get isteği ile Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="c4357-872">Create yöntemi ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-872">Add a Create method 2.1</span></span>

<span data-ttu-id="c4357-873">`PostTodoItem` *Controllers/TodoController. cs* içindeki şu yöntemi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4357-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="c4357-874">Yukarıdaki kod, özniteliğiyle gösterildiği gibi bir HTTP POST yöntemidir [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) .</span><span class="sxs-lookup"><span data-stu-id="c4357-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c4357-875">Yöntemi, HTTP isteğinin gövdesinden Yapılacaklar öğesinin değerini alır.</span><span class="sxs-lookup"><span data-stu-id="c4357-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c4357-876">`CreatedAtAction`Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="c4357-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="c4357-877">Başarılı olursa bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="c4357-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="c4357-878">HTTP 201, sunucuda yeni bir kaynak oluşturan HTTP POST yöntemi için standart yanıttır.</span><span class="sxs-lookup"><span data-stu-id="c4357-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c4357-879">Yanıta bir `Location` üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="c4357-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="c4357-880">`Location`Üst bilgi, yeni oluşturulan Yapılacaklar ÖĞESININ URI 'sini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c4357-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c4357-881">Daha fazla bilgi için bkz. [10.2.2 201 oluşturma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c4357-882">`GetTodoItem`ÜSTBILGININ URI 'sini oluşturma eylemine başvurur `Location` .</span><span class="sxs-lookup"><span data-stu-id="c4357-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c4357-883">C# `nameof` anahtar sözcüğü, çağrıda eylem adının sabit kodlanmasını önlemek için kullanılır `CreatedAtAction` .</span><span class="sxs-lookup"><span data-stu-id="c4357-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="c4357-884">PostTodoItem yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="c4357-885">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-885">Build the project.</span></span>
* <span data-ttu-id="c4357-886">Postman 'da HTTP yöntemini olarak ayarlayın `POST` .</span><span class="sxs-lookup"><span data-stu-id="c4357-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c4357-887">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="c4357-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="c4357-888">Örneğin, `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="c4357-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="c4357-889">**Gövde** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="c4357-890">**Ham** radyo düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c4357-891">Türü **JSON (Application/JSON)** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-891">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="c4357-892">İstek gövdesinde, bir yapılacaklar öğesi için JSON girin:</span><span class="sxs-lookup"><span data-stu-id="c4357-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c4357-893">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-893">Select **Send** .</span></span>

  ![Oluşturma isteğiyle Postman](first-web-api/_static/create.png)

  <span data-ttu-id="c4357-895">Bir 405 yöntemine Izin verilmiyor hatası alırsanız, yöntem eklendikten sonra projeyi derlememe sonucu büyük olasılıkla oluşur `PostTodoItem` .</span><span class="sxs-lookup"><span data-stu-id="c4357-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="c4357-896">Konum üst bilgisi URI 2,1 ' i test edin</span><span class="sxs-lookup"><span data-stu-id="c4357-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="c4357-897">**Yanıt** bölmesinde **üstbilgiler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c4357-898">**Konum** üst bilgisi değerini kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="c4357-898">Copy the **Location** header value:</span></span>

  ![Postman konsolunun üstbilgiler sekmesi](first-web-api/_static/pmc2.png)

* <span data-ttu-id="c4357-900">ALıNACAK yöntemi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4357-900">Set the method to GET.</span></span>
* <span data-ttu-id="c4357-901">URI değerini olarak ayarlayın `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="c4357-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="c4357-902">Örneğin, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="c4357-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="c4357-903">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-903">Select **Send** .</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="c4357-904">PutTodoItem yöntemi ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="c4357-905">Aşağıdaki yöntemi ekleyin `PutTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="c4357-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="c4357-906">`PutTodoItem``PostTodoItem`, http put kullanması dışında öğesine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="c4357-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c4357-907">Yanıt 204 ' dir [(Içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c4357-908">HTTP belirtimine göre bir PUT isteği, istemcinin yalnızca değişiklikleri değil, tüm güncelleştirilmiş varlığı göndermesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c4357-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c4357-909">Kısmi güncelleştirmeleri desteklemek için [http Patch](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4357-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c4357-910">Çağrılırken bir hata alırsanız `PutTodoItem` , `GET` veritabanında bir öğe olduğundan emin olmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="c4357-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="c4357-911">PutTodoItem yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="c4357-912">Bu örnek, uygulama her başlatıldığında başlatılmış olması gereken bellek içi bir veritabanını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c4357-913">Bir PUT çağrısı yapmadan önce veritabanında bir öğe olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4357-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c4357-914">PUT çağrısı yapmadan önce veritabanında bir öğe olduğundan emin olmak için GET çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="c4357-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c4357-915">ID = 1 olan Yapılacaklar öğesini güncelleştirin ve adını "Feed balık" olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c4357-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c4357-916">Aşağıdaki görüntüde Postman güncelleştirmesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c4357-916">The following image shows the Postman update:</span></span>

![204 (Içerik yok) yanıtı gösteren Postman konsolu](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="c4357-918">DeleteTodoItem yöntemi ekleme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c4357-919">Aşağıdaki yöntemi ekleyin `DeleteTodoItem` :</span><span class="sxs-lookup"><span data-stu-id="c4357-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="c4357-920">`DeleteTodoItem`Yanıt 204 ' dir [(içerik yok)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="c4357-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="c4357-921">DeleteTodoItem yöntemini test etme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c4357-922">Bir yapılacaklar öğesini silmek için Postman kullanın:</span><span class="sxs-lookup"><span data-stu-id="c4357-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c4357-923">Yöntemini olarak ayarlayın `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="c4357-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c4357-924">Silinecek nesnenin URI 'sini ayarlayın (örneğin, `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="c4357-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="c4357-925">**Gönder** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="c4357-925">Select **Send** .</span></span>

<span data-ttu-id="c4357-926">Örnek uygulama, tüm öğeleri silmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4357-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="c4357-927">Ancak, son öğe silindiğinde, API 'nin bir sonraki çağrılışında model sınıfı Oluşturucu tarafından yeni bir tane oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c4357-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="c4357-928">JavaScript 2,1 ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="c4357-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="c4357-929">Bu bölümde, Web API 'sini çağırmak için JavaScript kullanan bir HTML sayfası eklenir.</span><span class="sxs-lookup"><span data-stu-id="c4357-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="c4357-930">jQuery isteği başlatır.</span><span class="sxs-lookup"><span data-stu-id="c4357-930">jQuery initiates the request.</span></span> <span data-ttu-id="c4357-931">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="c4357-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="c4357-932">Uygulamayı [statik dosyalara sunacak](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) şekilde yapılandırın ve aşağıdaki vurgulanmış kodla *Startup.cs* güncelleştirerek [varsayılan dosya eşlemesini etkinleştirin](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) :</span><span class="sxs-lookup"><span data-stu-id="c4357-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="c4357-933">Proje dizininde bir *Wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c4357-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="c4357-934">*Wwwroot* dizinine *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="c4357-935">İçeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="c4357-936">*Wwwroot* dizinine *site.js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4357-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="c4357-937">Dosyanın içeriğini aşağıdaki kod ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c4357-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="c4357-938">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="c4357-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="c4357-939">*ÜzerindeProperties\launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="c4357-939">Open *Properties\launchSettings.json* .</span></span>
* <span data-ttu-id="c4357-940">`launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html* 'de açmaya zorlamak için özelliği kaldırın &mdash; .</span><span class="sxs-lookup"><span data-stu-id="c4357-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="c4357-941">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="c4357-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="c4357-942">API çağrılarının açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c4357-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="c4357-943">Yapılacaklar öğelerinin bir listesini alın 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="c4357-944">jQuery, bir to-do öğesi dizisini temsil eden JSON döndüren Web API 'sine bir HTTP GET isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="c4357-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="c4357-945">`success`Geri çağırma işlevi, istek başarılı olursa çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="c4357-946">Geri aramada, DOM, yapılacaklar bilgileriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="c4357-947">Yapılacak bir öğe ekleyin 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="c4357-948">jQuery, istek gövdesinde Yapılacaklar öğesiyle bir HTTP POST isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="c4357-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="c4357-949">`accepts`Ve `contentType` seçenekleri, `application/json` alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c4357-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="c4357-950">Yapılacaklar öğesi [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)kullanılarak JSON 'a dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="c4357-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="c4357-951">API başarılı bir durum kodu döndürdüğünde, `getData` Işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c4357-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="c4357-952">Yapılacak bir öğeyi güncelleştirme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="c4357-953">Bir yapılacaklar öğesinin güncelleştirilmesi, bir tane eklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="c4357-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="c4357-954">`url`Öğenin benzersiz tanımlayıcısını ekleme değişiklikleri ve öğesi `type` `PUT` .</span><span class="sxs-lookup"><span data-stu-id="c4357-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="c4357-955">Yapılacak bir öğeyi silme 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="c4357-956">Bir yapılacaklar öğesinin silinmesi, `type` Ajax çağrısının üzerine ayarlanarak `DELETE` ve öğenin URL 'sindeki benzersiz tanımlayıcısını belirterek gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c4357-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="c4357-957">Web API 2,1 kimlik doğrulama desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="c4357-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="c4357-958">Ek kaynaklar 2,1</span><span class="sxs-lookup"><span data-stu-id="c4357-958">Additional resources 2.1</span></span>

<span data-ttu-id="c4357-959">[Bu öğretici için örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="c4357-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="c4357-960">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c4357-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="c4357-961">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="c4357-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="c4357-962">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="c4357-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
