---
title: "Öğretici: JavaScript ile ASP.NET Core web API'yi arayın"
author: rick-anderson
description: JavaScript ile ASP.NET Core web API'yi nasıl arayacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655255"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="8ba63-103">Öğretici: JavaScript ile ASP.NET Core web API'yi arayın</span><span class="sxs-lookup"><span data-stu-id="8ba63-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="8ba63-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ba63-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8ba63-105">Bu öğretici, [ApI Getir'i](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanarak JavaScript ile ASP.NET Core web API'yi nasıl çağıracağımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8ba63-106">Core 2.2ASP.NET [için, JavaScript ile web API'yi Arayın'ın](xref:tutorials/first-web-api#call-the-web-api-with-javascript)2.2 sürümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8ba63-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="8ba63-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8ba63-107">Prerequisites</span></span>

* <span data-ttu-id="8ba63-108">Komple [Öğretici: Bir web API oluşturma](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="8ba63-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="8ba63-109">CSS, HTML ve JavaScript ile aşinalık</span><span class="sxs-lookup"><span data-stu-id="8ba63-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8ba63-110">JavaScript ile web API'sini arayın</span><span class="sxs-lookup"><span data-stu-id="8ba63-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="8ba63-111">Bu bölümde, yapılacak lar öğeleri oluşturmak ve yönetmek için formlar içeren bir HTML sayfası eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="8ba63-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="8ba63-112">Olay işleyicileri sayfadaki öğelere eklenir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="8ba63-113">Olay işleyicileri, web API'nin eylem yöntemlerine http istekleriyle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="8ba63-114">Api Getir `fetch` işlevi her BIR HTTP isteğini başlatır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="8ba63-115">İşlev, `fetch` [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) `Response` nesne olarak temsil edilen bir HTTP yanıtı içeren bir Söz nesnesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="8ba63-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="8ba63-116">Yaygın bir desen `json` `Response` nesne üzerinde işlevi çağırarak JSON yanıt gövdesi ayıklamaktır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="8ba63-117">JavaScript, sayfayı web API'sının yanıtındaki ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="8ba63-118">En basit `fetch` arama, rotayı temsil eden tek bir parametreyi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="8ba63-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="8ba63-119">`init` Nesne olarak bilinen ikinci bir parametre isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="8ba63-120">`init`HTTP isteğini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="8ba63-121">Uygulamayı [statik dosyalara hizmet etmek](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve varsayılan dosya [eşlemesini etkinleştirecek](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8ba63-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="8ba63-122">Aşağıdaki vurgulanan kod *Startup.cs* `Configure` yöntemi gereklidir:</span><span class="sxs-lookup"><span data-stu-id="8ba63-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="8ba63-123">Proje kökünde bir *wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8ba63-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="8ba63-124">*wwwroot* klasörünün içinde bir *js* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8ba63-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="8ba63-125">*wwwroot* klasörüne *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8ba63-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="8ba63-126">*index.html* içeriğini aşağıdaki biçimlendirmeyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8ba63-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="8ba63-127">*wwwroot/js* klasörüne *site.js* adında bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8ba63-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="8ba63-128">*site.js* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8ba63-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="8ba63-129">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarında bir değişiklik gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="8ba63-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="8ba63-130">*Özellikleri Aç\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8ba63-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="8ba63-131">Uygulamayı `launchUrl` projenin varsayılan *dosyasıindex.html'de*&mdash;açmaya zorlamak için özelliği kaldırın.</span><span class="sxs-lookup"><span data-stu-id="8ba63-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="8ba63-132">Bu örnek, web API'sının tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="8ba63-133">Aşağıda web API isteklerinin açıklamaları veremeleri yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="8ba63-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="8ba63-134">Yapılacak lar listesi alın</span><span class="sxs-lookup"><span data-stu-id="8ba63-134">Get a list of to-do items</span></span>

<span data-ttu-id="8ba63-135">Aşağıdaki kodda, *api/TodoItems* rotasına bir HTTP GET isteği gönderilir:</span><span class="sxs-lookup"><span data-stu-id="8ba63-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="8ba63-136">Web API başarılı bir durum kodu `_displayItems` döndürdüğünde, işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="8ba63-137">Kabul `_displayItems` edilen dizi parametresindeki yapılacaklar öğesi **Düzenle** ve **Sil** düğmelerinin yer aldığı bir tabloya eklenir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="8ba63-138">Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="8ba63-139">Yapılacaklar öğesi ekleme</span><span class="sxs-lookup"><span data-stu-id="8ba63-139">Add a to-do item</span></span>

<span data-ttu-id="8ba63-140">Aşağıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="8ba63-140">In the following code:</span></span>

* <span data-ttu-id="8ba63-141">Bir `item` değişken, yapılacaklar öğesinin nesne gerçek gösterimini oluşturmak için bildirilir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="8ba63-142">Bir Getir isteği aşağıdaki seçeneklerle yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="8ba63-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="8ba63-143">`method`&mdash;POST HTTP eylem fiilini belirtir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="8ba63-144">`body`&mdash;talep organının JSON temsilini belirtir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="8ba63-145">JSON, depolanan nesnenin `item` [json.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) işlevine aktarılmasıyla üretilir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="8ba63-146">`headers`&mdash;ve `Content-Type` HTTP `Accept` istek üstbilgilerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="8ba63-147">Her iki üstbilgi `application/json` de sırasıyla alınan ve gönderilen ortam türünü belirtmek için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="8ba63-148">*API/TodoItems* rotasına bir HTTP POST isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="8ba63-149">Web API başarılı bir durum kodu `getItems` döndürdüğünde, işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="8ba63-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="8ba63-150">Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="8ba63-151">Yapılacaklar öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8ba63-151">Update a to-do item</span></span>

<span data-ttu-id="8ba63-152">Yapılacaklar öğesini güncelleştirmek, bir öğe eklemeye benzer; ancak, iki önemli fark vardır:</span><span class="sxs-lookup"><span data-stu-id="8ba63-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="8ba63-153">Rota, güncelleştirilen öğenin benzersiz tanımlayıcısıyla sabitlenir.</span><span class="sxs-lookup"><span data-stu-id="8ba63-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="8ba63-154">Örneğin, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="8ba63-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="8ba63-155">HTTP eylem fiili, `method` seçenekte belirtildiği gibi PUT'dur.</span><span class="sxs-lookup"><span data-stu-id="8ba63-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="8ba63-156">Yapılacaklar öğesini silme</span><span class="sxs-lookup"><span data-stu-id="8ba63-156">Delete a to-do item</span></span>

<span data-ttu-id="8ba63-157">Yapılacaklar öğesini silmek için, isteğin `method` `DELETE` seçeneğini belirleyin ve öğenin URL'deki benzersiz tanımlayıcısını belirtin.</span><span class="sxs-lookup"><span data-stu-id="8ba63-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="8ba63-158">Web API yardım sayfalarını nasıl oluşturacağınızı öğrenmek için bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="8ba63-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
