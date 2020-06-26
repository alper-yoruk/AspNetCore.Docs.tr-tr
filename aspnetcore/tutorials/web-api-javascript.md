---
title: "Öğretici: JavaScript ile ASP.NET Core Web API 'SI çağırma"
author: rick-anderson
description: JavaScript ile ASP.NET Core Web API 'sini çağırmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-javascript
ms.openlocfilehash: 4031289e43af75ef2026661dbecbbbce30593d43
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407986"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="4dd0c-103">Öğretici: JavaScript ile ASP.NET Core Web API 'SI çağırma</span><span class="sxs-lookup"><span data-stu-id="4dd0c-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="4dd0c-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4dd0c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4dd0c-105">Bu öğreticide, [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanılarak JavaScript ile ASP.NET Core Web API 'sinin nasıl çağrılacağını gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4dd0c-106">ASP.NET Core 2,2 için, [JavaScript ile Web API 'Sini çağırma](xref:tutorials/first-web-api#call-the-web-api-with-javascript)2,2 sürümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="4dd0c-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="4dd0c-107">Prerequisites</span></span>

* <span data-ttu-id="4dd0c-108">Tüm [öğreticiyi: Web API 'Si oluşturma](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="4dd0c-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="4dd0c-109">CSS, HTML ve JavaScript ile benzerlik</span><span class="sxs-lookup"><span data-stu-id="4dd0c-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4dd0c-110">JavaScript ile Web API 'sini çağırma</span><span class="sxs-lookup"><span data-stu-id="4dd0c-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="4dd0c-111">Bu bölümde, Yapılacaklar öğeleri oluşturmak ve yönetmek için form içeren bir HTML sayfası ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="4dd0c-112">Olay işleyicileri sayfadaki öğelere iliştirilir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="4dd0c-113">Olay işleyicileri, Web API 'sinin eylem yöntemlerine HTTP istekleri sonucu vermez.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="4dd0c-114">Fetch API 'nin `fetch` işlevi her http isteğini başlatır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="4dd0c-115">`fetch`İşlevi, nesne olarak temsil edilen BIR http yanıtı içeren bir [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) nesnesi döndürür `Response` .</span><span class="sxs-lookup"><span data-stu-id="4dd0c-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="4dd0c-116">Ortak bir model, nesnesinde işlevini çağırarak JSON yanıt gövdesini ayıklamaya yönelik olur `json` `Response` .</span><span class="sxs-lookup"><span data-stu-id="4dd0c-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="4dd0c-117">JavaScript, sayfayı Web API 'sinin yanıtından alınan ayrıntılarla güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4dd0c-118">En basit `fetch` çağrı, yolu temsil eden tek bir parametre kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="4dd0c-119">Nesne olarak bilinen ikinci bir parametre `init` isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="4dd0c-120">`init`HTTP isteğini yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="4dd0c-121">Uygulamayı [statik dosyaları sunacak](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ve [varsayılan dosya eşlemesini etkinleştirecek](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="4dd0c-122">Aşağıdaki vurgulanan kod, `Configure` *Startup.cs*yönteminde gereklidir:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="4dd0c-123">Proje kökünde bir *Wwwroot* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="4dd0c-124">*Wwwroot* klasörü içinde bir *js* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="4dd0c-125">*Wwwroot* klasörüne *index.html* adlı bir HTML dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="4dd0c-126">*index.html* öğesinin içeriğini aşağıdaki biçimlendirme ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="4dd0c-127">*Wwwroot/js* klasörüne *site.js* adlı bir JavaScript dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="4dd0c-128">*site.js* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4dd0c-129">HTML sayfasını yerel olarak test etmek için ASP.NET Core projesinin başlatma ayarlarındaki bir değişikliğin yapılması gerekebilir:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="4dd0c-130">*ÜzerindeProperties\launchSettings.js*açın.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="4dd0c-131">`launchUrl`Uygulamayı projenin varsayılan dosyasında *index.html*'de açmaya zorlamak için özelliği kaldırın &mdash; .</span><span class="sxs-lookup"><span data-stu-id="4dd0c-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4dd0c-132">Bu örnek, Web API 'sinin tüm CRUD yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4dd0c-133">Web API isteklerinin açıklamaları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4dd0c-134">Yapılacaklar öğelerinin bir listesini alın</span><span class="sxs-lookup"><span data-stu-id="4dd0c-134">Get a list of to-do items</span></span>

<span data-ttu-id="4dd0c-135">Aşağıdaki kodda, *API/todoıtems* yoluna BIR http get isteği gönderilir:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="4dd0c-136">Web API 'SI başarılı bir durum kodu döndürdüğünde, `_displayItems` işlev çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="4dd0c-137">Tarafından kabul edilen dizi parametresindeki her Yapılacaklar öğesi `_displayItems` , **Düzenle** ve **Sil** düğmeleri içeren bir tabloya eklenir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="4dd0c-138">Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="4dd0c-139">Yapılacaklar öğesi ekleme</span><span class="sxs-lookup"><span data-stu-id="4dd0c-139">Add a to-do item</span></span>

<span data-ttu-id="4dd0c-140">Aşağıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-140">In the following code:</span></span>

* <span data-ttu-id="4dd0c-141">Bir `item` değişken, yapılacaklar öğesinin nesne sabit gösterimini oluşturmak için bildirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="4dd0c-142">Aşağıdaki seçeneklerle bir getirme isteği yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="4dd0c-143">`method`&mdash;HTTP SONRASı eylem fiilini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="4dd0c-144">`body`&mdash;istek gövdesinin JSON temsilini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="4dd0c-145">JSON, içinde depolanan nesne sabit değeri `item` [JSON. stringbelirt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) işlevine geçirilerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="4dd0c-146">`headers`&mdash;`Accept`ve `Content-Type` http istek üst bilgilerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="4dd0c-147">Her iki üst bilgi `application/json` , sırasıyla alınan ve gönderilen medya türünü belirtmek için olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="4dd0c-148">*API/todoıtems* yoluna BIR http post isteği gönderilir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="4dd0c-149">Web API 'SI başarılı bir durum kodu döndürdüğünde, `getItems` Işlev HTML tablosunu güncelleştirmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="4dd0c-150">Web API isteği başarısız olursa, tarayıcının konsoluna bir hata kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="4dd0c-151">Yapılacaklar öğesini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="4dd0c-151">Update a to-do item</span></span>

<span data-ttu-id="4dd0c-152">Bir yapılacaklar öğesinin güncelleştirilmesi bir tane eklemeye benzer; Ancak, iki önemli fark vardır:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="4dd0c-153">Yol, güncelleştirilecek öğenin benzersiz tanımlayıcısı ile sone düzeltildi.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="4dd0c-154">Örneğin, *api/todoıtems/1*.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="4dd0c-155">HTTP eylemi fiili, seçeneğinde gösterildiği gibi konur `method` .</span><span class="sxs-lookup"><span data-stu-id="4dd0c-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4dd0c-156">Bir yapılacaklar öğesini silme</span><span class="sxs-lookup"><span data-stu-id="4dd0c-156">Delete a to-do item</span></span>

<span data-ttu-id="4dd0c-157">Bir yapılacaklar öğesini silmek için isteğin `method` seçeneğini olarak ayarlayın `DELETE` ve URL 'de öğenin benzersiz tanımlayıcısını belirtin.</span><span class="sxs-lookup"><span data-stu-id="4dd0c-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="4dd0c-158">Web API 'SI yardım sayfaları oluşturmayı öğrenmek için bir sonraki öğreticiye ilerleyin:</span><span class="sxs-lookup"><span data-stu-id="4dd0c-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
