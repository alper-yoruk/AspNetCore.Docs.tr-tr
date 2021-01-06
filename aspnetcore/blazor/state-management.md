---
title: ASP.NET Core Blazor durum yönetimi
author: guardrex
description: Uygulamalarda durumu kalıcı hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 5b7a6fefd13d7255fbe3ae7010df48c6211fa755
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854357"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="e0f03-103">ASP.NET Core Blazor durum yönetimi</span><span class="sxs-lookup"><span data-stu-id="e0f03-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="e0f03-104">Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0f03-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="e0f03-105">Bir uygulamada oluşturulan kullanıcı durumu Blazor WebAssembly tarayıcının belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="e0f03-106">Tarayıcı belleğinde tutulan Kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="e0f03-107">Oluşturulan kullanıcı arabirimindeki bileşen örneklerinin ve en son işleme çıktılarının hiyerarşisi.</span><span class="sxs-lookup"><span data-stu-id="e0f03-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="e0f03-108">Bileşen örneklerinde alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="e0f03-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="e0f03-109">[Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="e0f03-110">[JavaScript birlikte çalışma](xref:blazor/call-javascript-from-dotnet) çağrıları aracılığıyla ayarlanan değerler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="e0f03-111">Kullanıcı tarayıcısını kapatıp yeniden açtığında veya sayfayı yeniden yüklediğinde, tarayıcının belleğinde tutulan Kullanıcı durumu kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="e0f03-112">[Korumalı tarayıcı depolaması](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) ( <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> ad alanı) ASP.NET Core veri korumasına dayanır ve yalnızca uygulamalar için desteklenir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e0f03-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="e0f03-113">Tarayıcı oturumlarında durumu kalıcı yap</span><span class="sxs-lookup"><span data-stu-id="e0f03-113">Persist state across browser sessions</span></span>

<span data-ttu-id="e0f03-114">Genellikle, kullanıcıların, zaten mevcut olan verileri okurken değil, etkin bir şekilde veri oluşturmakta olduğu tarayıcı oturumlarında durumu koruyun.</span><span class="sxs-lookup"><span data-stu-id="e0f03-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="e0f03-115">Tarayıcı oturumlarında durumu korumak için, uygulamanın verileri tarayıcı belleğinden başka bir depolama konumuna kalıcı hale vermelidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="e0f03-116">Durum kalıcılığı otomatik değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-116">State persistence isn't automatic.</span></span> <span data-ttu-id="e0f03-117">Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="e0f03-118">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="e0f03-119">Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="e0f03-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="e0f03-120">Çok adımlı Web formları: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir Web formunun birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="e0f03-121">Kullanıcı formdan ayrıldıklarında ve daha sonra geri dönüp Bu senaryodaki durumu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="e0f03-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="e0f03-122">Alışveriş sepetlerini: olası geliri temsil eden bir uygulamanın ticari olarak önemli bileşenleri korunabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="e0f03-123">Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="e0f03-124">Uygulama, *uygulama durumunu* yalnızca kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-124">An app can only persist *app state*.</span></span> <span data-ttu-id="e0f03-125">Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="e0f03-126">Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="e0f03-127">Ağaç görünümü denetiminin genişletilmiş düğümleri gibi UI durumunu kalıcı hale getirmek için, uygulamanın kullanıcı arabirimi durumunun davranışını seri hale getirilebilir uygulama durumu olarak modellemek üzere özel kod kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="e0f03-128">Durumun nerede kalıcı olduğu</span><span class="sxs-lookup"><span data-stu-id="e0f03-128">Where to persist state</span></span>

<span data-ttu-id="e0f03-129">Kalıcı durum için genel konumlar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="e0f03-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="e0f03-130">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="e0f03-131">URL</span><span class="sxs-lookup"><span data-stu-id="e0f03-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="e0f03-132">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="e0f03-133">Bellek içi durum kapsayıcısı hizmeti</span><span class="sxs-lookup"><span data-stu-id="e0f03-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="e0f03-134">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-134">Server-side storage</span></span></h2>

<span data-ttu-id="e0f03-135">Birden çok kullanıcı ve cihaza yayılan kalıcı veri kalıcılığı için, uygulama bir Web API 'SI aracılığıyla erişilen bağımsız sunucu tarafı depolama kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="e0f03-136">Seçenekler arasında şunlar bulunur:</span><span class="sxs-lookup"><span data-stu-id="e0f03-136">Options include:</span></span>

* <span data-ttu-id="e0f03-137">Blob depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-137">Blob storage</span></span>
* <span data-ttu-id="e0f03-138">Anahtar-değer depolaması</span><span class="sxs-lookup"><span data-stu-id="e0f03-138">Key-value storage</span></span>
* <span data-ttu-id="e0f03-139">İlişkisel veritabanı</span><span class="sxs-lookup"><span data-stu-id="e0f03-139">Relational database</span></span>
* <span data-ttu-id="e0f03-140">Table Storage</span><span class="sxs-lookup"><span data-stu-id="e0f03-140">Table storage</span></span>

<span data-ttu-id="e0f03-141">Veriler kaydedildikten sonra, kullanıcının durumu korunur ve herhangi bir yeni tarayıcı oturumunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="e0f03-142">Blazor WebAssemblyUygulamalar tamamen kullanıcının tarayıcısında çalıştığı için, depolama hizmetleri ve veritabanları gibi güvenli dış sistemlere erişmek için ek ölçüler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="e0f03-143">Blazor WebAssembly uygulamalar, tek sayfalı uygulamalarla (maça 'Lar) aynı şekilde güvenli hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="e0f03-144">Genellikle, bir uygulama [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) aracılığıyla bir kullanıcının kimliğini doğrular ve ardından sunucu tarafı bir uygulamaya Web API çağrıları aracılığıyla depolama hizmetleri ve veritabanlarıyla etkileşime girer.</span><span class="sxs-lookup"><span data-stu-id="e0f03-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="e0f03-145">Sunucu tarafı uygulama, Blazor WebAssembly uygulama ve depolama hizmeti ya da veritabanı arasında veri aktarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="e0f03-146">Sunucu tarafı uygulamasının Blazor WebAssembly depolamaya kalıcı bir bağlantısı olduğunda uygulama, sunucu tarafı uygulamasına kısa ömürlü bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="e0f03-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="e0f03-147">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="e0f03-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="e0f03-148">Blazor*Güvenlik ve Identity* makaleleri</span><span class="sxs-lookup"><span data-stu-id="e0f03-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="e0f03-149">Azure veri depolama seçenekleri hakkında daha fazla bilgi için aşağıdakilere bakın:</span><span class="sxs-lookup"><span data-stu-id="e0f03-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="e0f03-150">Azure veritabanları</span><span class="sxs-lookup"><span data-stu-id="e0f03-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="e0f03-151">Azure depolama belgeleri</span><span class="sxs-lookup"><span data-stu-id="e0f03-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="e0f03-152">URL</span><span class="sxs-lookup"><span data-stu-id="e0f03-152">URL</span></span></h2>

<span data-ttu-id="e0f03-153">Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="e0f03-154">URL 'de modellenen Kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="e0f03-155">Görüntülenen varlığın KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="e0f03-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="e0f03-156">Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="e0f03-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="e0f03-157">Kullanıcı sayfayı el ile yeniden yüklediğinde tarayıcının adres çubuğunun içeriği korunur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="e0f03-158">Yönergeyle URL desenleri tanımlama hakkında bilgi için [`@page`](xref:mvc/views/razor#page) bkz <xref:blazor/fundamentals/routing> ..</span><span class="sxs-lookup"><span data-stu-id="e0f03-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="e0f03-159">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-159">Browser storage</span></span></h2>

<span data-ttu-id="e0f03-160">Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın olarak kullanılan bir depolama konumu tarayıcının [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) ve [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) koleksiyonlarıdır:</span><span class="sxs-lookup"><span data-stu-id="e0f03-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="e0f03-161">`localStorage` tarayıcı penceresinin kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="e0f03-162">Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="e0f03-163">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="e0f03-164">Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="e0f03-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="e0f03-165">`sessionStorage` , tarayıcı sekmesinin kapsamına alınır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="e0f03-166">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="e0f03-167">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="e0f03-168">`localStorage` ve `sessionStorage` Blazor WebAssembly yalnızca özel kod yazarak veya üçüncü taraf bir paket kullanılarak uygulamalarda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="e0f03-169">Genellikle, `sessionStorage` kullanmak daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="e0f03-170">`sessionStorage` bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:</span><span class="sxs-lookup"><span data-stu-id="e0f03-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="e0f03-171">Sekmelerde durum depolamadaki hatalar.</span><span class="sxs-lookup"><span data-stu-id="e0f03-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="e0f03-172">Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.</span><span class="sxs-lookup"><span data-stu-id="e0f03-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="e0f03-173">`localStorage` uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f03-174">Kullanıcılar ve içinde depolanan verileri görüntüleyebilir veya bunlarla karşılaşabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="e0f03-175">Bellek içi durum kapsayıcısı hizmeti</span><span class="sxs-lookup"><span data-stu-id="e0f03-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="e0f03-176">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e0f03-176">Additional resources</span></span>

* [<span data-ttu-id="e0f03-177">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="e0f03-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="e0f03-178">Blazor Server durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="e0f03-179">Çoğu zaman, uygulama sunucusuyla bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="e0f03-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="e0f03-180">Kullanıcının durumu, sunucu belleğinde bir *devrende* tutulur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="e0f03-181">Bir devreye sahip kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="e0f03-182">Oluşturulan kullanıcı arabirimindeki bileşen örneklerinin ve en son işleme çıktılarının hiyerarşisi.</span><span class="sxs-lookup"><span data-stu-id="e0f03-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="e0f03-183">Bileşen örneklerinde alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="e0f03-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="e0f03-184">Devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="e0f03-185">Kullanıcı durumu, [JavaScript birlikte çalışma](xref:blazor/call-javascript-from-dotnet) çağrıları aracılığıyla tarayıcının bellek kümesindeki JavaScript değişkenlerinde de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="e0f03-186">Bir Kullanıcı geçici bir ağ bağlantısı kaybıyla karşılaşıyorsa, Blazor özgün durumlarındaki kullanıcıyı özgün devresine yeniden bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="e0f03-187">Ancak, bir kullanıcıyı sunucunun belleğindeki özgün devresine yeniden bağlamak her zaman mümkün değildir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="e0f03-188">Sunucu, bağlantısı kesilen bir devreni süresiz olarak sürdüremez.</span><span class="sxs-lookup"><span data-stu-id="e0f03-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="e0f03-189">Sunucu, bir zaman aşımından sonra veya sunucu bellek baskısı altında olduğunda, bağlantısı kesilen bir bağlantı hattını serbest bırakmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="e0f03-190">Çok sunuculu, yük dengeli dağıtım ortamlarında, tek tek sunucular başarısız olabilir veya artık tüm istek hacminin işlenmesi gerekmiyorsa otomatik olarak kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="e0f03-191">Kullanıcı yeniden bağlanmayı denediğinde, bir kullanıcı için özgün sunucu işleme istekleri kullanılamaz hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="e0f03-192">Kullanıcı tarayıcıyı kapatabilir ve yeniden açabilir veya sayfayı yeniden yükleyerek tarayıcı belleğinde tutulan tüm durumları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="e0f03-193">Örneğin, JavaScript birlikte çalışma çağrıları aracılığıyla ayarlanan JavaScript değişken değerleri kaybolur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="e0f03-194">Kullanıcı özgün devresine yeniden bağlanalınmayacaksa, Kullanıcı boş duruma sahip yeni bir devre alır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="e0f03-195">Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="e0f03-196">Devreler arasında durumu kalıcı yap</span><span class="sxs-lookup"><span data-stu-id="e0f03-196">Persist state across circuits</span></span>

<span data-ttu-id="e0f03-197">Genellikle, kullanıcıların, zaten mevcut olan verileri okumadan değil, etkin bir şekilde veri oluşturmakta olduğu devrelerde durumu koruyun.</span><span class="sxs-lookup"><span data-stu-id="e0f03-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="e0f03-198">Uygulama, durumu devrelerde korumak için, verilerin sunucu belleğinden başka bir depolama konumuna kalıcı hale gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="e0f03-199">Durum kalıcılığı otomatik değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-199">State persistence isn't automatic.</span></span> <span data-ttu-id="e0f03-200">Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="e0f03-201">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="e0f03-202">Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="e0f03-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="e0f03-203">Çok adımlı Web formları: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir Web formunun birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="e0f03-204">Kullanıcı formdan ayrıldıklarında ve daha sonra geri dönüp Bu senaryodaki durumu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="e0f03-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="e0f03-205">Alışveriş sepetlerini: olası geliri temsil eden bir uygulamanın ticari olarak önemli bileşenleri korunabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="e0f03-206">Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="e0f03-207">Uygulama, *uygulama durumunu* yalnızca kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-207">An app can only persist *app state*.</span></span> <span data-ttu-id="e0f03-208">Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="e0f03-209">Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="e0f03-210">Ağaç görünümü denetiminin genişletilmiş düğümleri gibi UI durumunu kalıcı hale getirmek için, uygulamanın kullanıcı arabirimi durumunun davranışını seri hale getirilebilir uygulama durumu olarak modellemek üzere özel kod kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="e0f03-211">Durumun nerede kalıcı olduğu</span><span class="sxs-lookup"><span data-stu-id="e0f03-211">Where to persist state</span></span>

<span data-ttu-id="e0f03-212">Kalıcı durum için genel konumlar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="e0f03-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="e0f03-213">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="e0f03-214">URL</span><span class="sxs-lookup"><span data-stu-id="e0f03-214">URL</span></span>](#url-server)
* [<span data-ttu-id="e0f03-215">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="e0f03-216">Bellek içi durum kapsayıcısı hizmeti</span><span class="sxs-lookup"><span data-stu-id="e0f03-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="e0f03-217">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-217">Server-side storage</span></span></h2>

<span data-ttu-id="e0f03-218">Birden çok kullanıcı ve cihaza yayılan kalıcı veri kalıcılığı için, uygulama sunucu tarafı depolama kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="e0f03-219">Seçenekler arasında şunlar bulunur:</span><span class="sxs-lookup"><span data-stu-id="e0f03-219">Options include:</span></span>

* <span data-ttu-id="e0f03-220">Blob depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-220">Blob storage</span></span>
* <span data-ttu-id="e0f03-221">Anahtar-değer depolaması</span><span class="sxs-lookup"><span data-stu-id="e0f03-221">Key-value storage</span></span>
* <span data-ttu-id="e0f03-222">İlişkisel veritabanı</span><span class="sxs-lookup"><span data-stu-id="e0f03-222">Relational database</span></span>
* <span data-ttu-id="e0f03-223">Table Storage</span><span class="sxs-lookup"><span data-stu-id="e0f03-223">Table storage</span></span>

<span data-ttu-id="e0f03-224">Veriler kaydedildikten sonra, kullanıcının durumu korunur ve yeni bir devrede kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="e0f03-225">Azure veri depolama seçenekleri hakkında daha fazla bilgi için aşağıdakilere bakın:</span><span class="sxs-lookup"><span data-stu-id="e0f03-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="e0f03-226">Azure veritabanları</span><span class="sxs-lookup"><span data-stu-id="e0f03-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="e0f03-227">Azure depolama belgeleri</span><span class="sxs-lookup"><span data-stu-id="e0f03-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="e0f03-228">URL</span><span class="sxs-lookup"><span data-stu-id="e0f03-228">URL</span></span></h2>

<span data-ttu-id="e0f03-229">Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="e0f03-230">URL 'de modellenen Kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="e0f03-231">Görüntülenen varlığın KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="e0f03-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="e0f03-232">Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="e0f03-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="e0f03-233">Tarayıcının adres çubuğunun içeriği korunur:</span><span class="sxs-lookup"><span data-stu-id="e0f03-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="e0f03-234">Kullanıcı sayfayı el ile yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="e0f03-235">Web sunucusu kullanılamaz hale gelirse ve Kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="e0f03-236">Yönergeyle URL desenleri tanımlama hakkında bilgi için [`@page`](xref:mvc/views/razor#page) bkz <xref:blazor/fundamentals/routing> ..</span><span class="sxs-lookup"><span data-stu-id="e0f03-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="e0f03-237">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="e0f03-237">Browser storage</span></span></h2>

<span data-ttu-id="e0f03-238">Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın olarak kullanılan bir depolama konumu tarayıcının [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) ve [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) koleksiyonlarıdır:</span><span class="sxs-lookup"><span data-stu-id="e0f03-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="e0f03-239">`localStorage` tarayıcı penceresinin kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="e0f03-240">Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="e0f03-241">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="e0f03-242">Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="e0f03-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="e0f03-243">`sessionStorage` , tarayıcı sekmesinin kapsamına alınır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="e0f03-244">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="e0f03-245">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="e0f03-246">Genellikle, `sessionStorage` kullanmak daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="e0f03-247">`sessionStorage` bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:</span><span class="sxs-lookup"><span data-stu-id="e0f03-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="e0f03-248">Sekmelerde durum depolamadaki hatalar.</span><span class="sxs-lookup"><span data-stu-id="e0f03-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="e0f03-249">Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.</span><span class="sxs-lookup"><span data-stu-id="e0f03-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="e0f03-250">`localStorage` uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="e0f03-251">Tarayıcı depolamayı kullanmaya yönelik uyarılar:</span><span class="sxs-lookup"><span data-stu-id="e0f03-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="e0f03-252">Sunucu tarafı veritabanının kullanımına benzer şekilde veri yükleme ve kaydetme zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="e0f03-253">Sunucu tarafı veritabanının aksine, istenen sayfa prerendering aşamasında tarayıcıda bulunmadığından, depolama alanı prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="e0f03-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="e0f03-254">Birkaç kilobayt veri depolaması, uygulamalar için kalıcı hale getirilmesi mantıklıdır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e0f03-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="e0f03-255">Birkaç kilobayt dışında, veriler ağ üzerinden yüklenip kaydedildiğinden performans etkilerini göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="e0f03-256">Kullanıcılar verileri görüntüleyebilir veya bunlarla karşılaşabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="e0f03-257">[ASP.NET Core veri koruma](xref:security/data-protection/introduction) riski azaltabilirler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="e0f03-258">Örneğin, [ASP.NET Core korumalı tarayıcı depolama](#aspnet-core-protected-browser-storage) ASP.NET Core veri koruma kullanır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="e0f03-259">Üçüncü taraf NuGet paketleri ve ile çalışmaya yönelik API 'Ler `localStorage` sağlar `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="e0f03-260">[ASP.NET Core veri korumasını](xref:security/data-protection/introduction)saydam olarak kullanan bir paket seçmeyi düşünülüyor.</span><span class="sxs-lookup"><span data-stu-id="e0f03-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="e0f03-261">Veri koruma, depolanan verileri şifreler ve depolanan verilerle yapılan değişikliklere karşı olası riski azaltır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="e0f03-262">JSON seri hale getirilmiş veriler düz metin halinde depolanıyorsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri de değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="e0f03-263">Verilerin güvenliğini sağlamak her zaman bir sorun değildir çünkü veriler önemsiz olarak olabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="e0f03-264">Örneğin, bir kullanıcı ARABIRIMI öğesinin saklı rengini okumak veya değiştirmek, Kullanıcı veya kuruluş için önemli bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="e0f03-265">Kullanıcıların *hassas verileri* incelemesine veya değiştirmesine izin vermeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="e0f03-266">ASP.NET Core korumalı tarayıcı depolaması</span><span class="sxs-lookup"><span data-stu-id="e0f03-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="e0f03-267">ASP.NET Core korumalı tarayıcı depolaması, ve için [veri koruma ASP.NET Core](xref:security/data-protection/introduction) yararlanır [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="e0f03-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="e0f03-268">Korumalı tarayıcı depolaması ASP.NET Core veri korumasına dayanır ve yalnızca uygulamalar için desteklenir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e0f03-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="e0f03-269">Bir bileşen içindeki verileri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="e0f03-269">Save and load data within a component</span></span>

<span data-ttu-id="e0f03-270">Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birini bir örnek eklemek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e0f03-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="e0f03-271">Seçim, hangi tarayıcı depolama konumuna kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="e0f03-272">Aşağıdaki örnekte, `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e0f03-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="e0f03-273">`@using`Yönergesi `_Imports.razor` bileşen yerine uygulamanın dosyasına yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="e0f03-274">`_Imports.razor`Dosya kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="e0f03-275">`currentCount` `Counter` Proje şablonunu temel alan bir uygulamanın bileşenindeki değeri kalıcı hale getirmek için Blazor Server , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="e0f03-276">Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="e0f03-277">Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="e0f03-278">`ProtectedSessionStore` karmaşık durum nesnelerini depolamak için JSON verilerini otomatik olarak serileştirir ve seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="e0f03-279">Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="e0f03-280">Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core veri koruma kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="e0f03-281">Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiyse incelenebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="e0f03-282">`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için, `Counter` kullanıcının yeni bir devrede olması dahil, şunu kullanın `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="e0f03-283">Bileşenin parametreleri gezinti durumu içeriyorsa, çağrısı `ProtectedSessionStore.GetAsync` `null` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> yapın ve not edin <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="e0f03-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="e0f03-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> yalnızca bileşen ilk kez oluşturulduğunda çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="e0f03-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="e0f03-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="e0f03-286">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="e0f03-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f03-287">Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="e0f03-288">Prerendering etkinken, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamadığı belirten bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="e0f03-289">Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="e0f03-290">Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="e0f03-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="e0f03-291">Yükleme durumunu işle</span><span class="sxs-lookup"><span data-stu-id="e0f03-291">Handle the loading state</span></span>

<span data-ttu-id="e0f03-292">Tarayıcı depolamaya bir ağ bağlantısı üzerinden zaman uyumsuz olarak erişildiği için, veriler yüklenmeden ve bir bileşen için kullanılabilir olmadan önce her zaman bir zaman dilimi vardır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="e0f03-293">En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="e0f03-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="e0f03-294">Bir yaklaşım, verilerin ne olduğunu `null` , yani verilerin hala yüklenmekte olduğunu izlemedir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="e0f03-295">Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="e0f03-296">Türe () bir soru işareti () ekleyerek [ `currentCount` null yapılabilir yapın](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="e0f03-297">Count ve Button öğesinin Koşullu olarak görüntülenmesi yerine **`Increment`** , bu öğeleri yalnızca veriler denetlenerek yüklenirse görüntüleyin <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="e0f03-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="e0f03-298">Tanıtıcı prerendering</span><span class="sxs-lookup"><span data-stu-id="e0f03-298">Handle prerendering</span></span>

<span data-ttu-id="e0f03-299">Prerendering sırasında:</span><span class="sxs-lookup"><span data-stu-id="e0f03-299">During prerendering:</span></span>

* <span data-ttu-id="e0f03-300">Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="e0f03-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="e0f03-301">Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="e0f03-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="e0f03-302">`localStorage` veya `sessionStorage` prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="e0f03-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="e0f03-303">Bileşen depolama ile etkileşim kurmayı denerse, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamayacağını açıklayan bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="e0f03-304">Hatayı çözmek için bir yol prerendering devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="e0f03-305">Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="e0f03-306">Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="e0f03-307">Prerendering 'yi devre dışı bırakmak için, `Pages/_Host.cshtml` dosyayı açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) özniteliğini şu şekilde değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="e0f03-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="e0f03-308">Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="e0f03-309">Prerendering devam etmek için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="e0f03-310">Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="e0f03-311">Durum korumasını ortak bir konuma ayırın</span><span class="sxs-lookup"><span data-stu-id="e0f03-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="e0f03-312">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="e0f03-313">Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="e0f03-314">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="e0f03-315">Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri şunlar için kalıcıdır `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="e0f03-316">`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="e0f03-317">Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın.</span><span class="sxs-lookup"><span data-stu-id="e0f03-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="e0f03-318">Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşenin () etrafında sarmalayın `App.razor` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="e0f03-319">Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="e0f03-320">Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:</span><span class="sxs-lookup"><span data-stu-id="e0f03-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="e0f03-321">Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="e0f03-322">Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="e0f03-323">Daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="e0f03-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="e0f03-324">Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="e0f03-325">Birçok bileşen genelinde durumu kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="e0f03-325">To consume state across many components.</span></span>
* <span data-ttu-id="e0f03-326">Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="e0f03-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="e0f03-327">Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, kalıcı durumu küresel olarak önlemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="e0f03-328">Korumalı tarayıcı depolaması deneysel NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="e0f03-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="e0f03-329">ASP.NET Core korumalı tarayıcı depolaması, ve için [veri koruma ASP.NET Core](xref:security/data-protection/introduction) yararlanır [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="e0f03-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="e0f03-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` , desteklenmeyen, deneysel bir paket, üretim kullanımı için uygun değil.</span><span class="sxs-lookup"><span data-stu-id="e0f03-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="e0f03-331">Paket yalnızca ASP.NET Core 3,1 Blazor Server uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="e0f03-332">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e0f03-332">Configuration</span></span>

1. <span data-ttu-id="e0f03-333">Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="e0f03-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="e0f03-334">`Pages/_Host.cshtml`Dosyasında, kapanış etiketinin içine aşağıdaki betiği ekleyin `</body>` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="e0f03-335">`Startup.ConfigureServices`' De, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="e0f03-336">Bir bileşen içindeki verileri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="e0f03-336">Save and load data within a component</span></span>

<span data-ttu-id="e0f03-337">Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birini bir örnek eklemek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e0f03-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="e0f03-338">Seçim, hangi tarayıcı depolama konumuna kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="e0f03-339">Aşağıdaki örnekte, `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e0f03-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="e0f03-340">`@using`İfade, `_Imports.razor` bileşen yerine bir dosyaya yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="e0f03-341">`_Imports.razor`Dosya kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="e0f03-342">`currentCount` `Counter` Proje şablonunu temel alan bir uygulamanın bileşenindeki değeri kalıcı hale getirmek için Blazor Server , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="e0f03-343">Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="e0f03-344">Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="e0f03-345">`ProtectedSessionStore` JSON verilerini otomatik olarak serileştirir ve seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="e0f03-346">Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="e0f03-347">Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core veri koruma kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="e0f03-348">Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiyse incelenebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="e0f03-349">`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için `Counter` , tamamen yeni bir devrede olanlar da dahil olmak üzere şunu kullanın `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="e0f03-350">Bileşenin parametreleri gezinti durumu içeriyorsa, `ProtectedSessionStore.GetAsync` sonucunu çağırın ve ' de atayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="e0f03-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="e0f03-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> yalnızca bileşen ilk kez oluşturulduğunda çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="e0f03-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="e0f03-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="e0f03-353">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="e0f03-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="e0f03-354">Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="e0f03-355">Prerendering etkinken, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamadığı belirten bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="e0f03-356">Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="e0f03-357">Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="e0f03-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="e0f03-358">Yükleme durumunu işle</span><span class="sxs-lookup"><span data-stu-id="e0f03-358">Handle the loading state</span></span>

<span data-ttu-id="e0f03-359">Tarayıcı depolamaya bir ağ bağlantısı üzerinden zaman uyumsuz olarak erişildiği için, veriler yüklenmeden ve bir bileşen için kullanılabilir olmadan önce her zaman bir zaman dilimi vardır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="e0f03-360">En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="e0f03-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="e0f03-361">Bir yaklaşım, verilerin ne olduğunu `null` , yani verilerin hala yüklenmekte olduğunu izlemedir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="e0f03-362">Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="e0f03-363">Türe () bir soru işareti () ekleyerek [ `currentCount` null yapılabilir yapın](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="e0f03-364">Count ve Button öğesinin Koşullu olarak görüntülenmesi yerine **`Increment`** , bu öğeleri yalnızca veriler yüklenmişse görüntülemeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="e0f03-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="e0f03-365">Tanıtıcı prerendering</span><span class="sxs-lookup"><span data-stu-id="e0f03-365">Handle prerendering</span></span>

<span data-ttu-id="e0f03-366">Prerendering sırasında:</span><span class="sxs-lookup"><span data-stu-id="e0f03-366">During prerendering:</span></span>

* <span data-ttu-id="e0f03-367">Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="e0f03-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="e0f03-368">Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="e0f03-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="e0f03-369">`localStorage` veya `sessionStorage` prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="e0f03-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="e0f03-370">Bileşen depolama ile etkileşim kurmayı denerse, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamayacağını açıklayan bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="e0f03-371">Hatayı çözmek için bir yol prerendering devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="e0f03-372">Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="e0f03-373">Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="e0f03-374">Prerendering 'yi devre dışı bırakmak için, `Pages/_Host.cshtml` dosyayı açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) özniteliğini şu şekilde değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="e0f03-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="e0f03-375">Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="e0f03-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="e0f03-376">Prerendering devam etmek için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin.</span><span class="sxs-lookup"><span data-stu-id="e0f03-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="e0f03-377">Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="e0f03-378">Durum korumasını ortak bir konuma ayırın</span><span class="sxs-lookup"><span data-stu-id="e0f03-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="e0f03-379">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e0f03-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="e0f03-380">Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="e0f03-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="e0f03-381">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="e0f03-382">Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri şunlar için kalıcıdır `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="e0f03-383">`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="e0f03-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="e0f03-384">Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın.</span><span class="sxs-lookup"><span data-stu-id="e0f03-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="e0f03-385">Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşenin () etrafında sarmalayın `App.razor` :</span><span class="sxs-lookup"><span data-stu-id="e0f03-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="e0f03-386">Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="e0f03-387">Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:</span><span class="sxs-lookup"><span data-stu-id="e0f03-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="e0f03-388">Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="e0f03-389">Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="e0f03-390">Daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="e0f03-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="e0f03-391">Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:</span><span class="sxs-lookup"><span data-stu-id="e0f03-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="e0f03-392">Birçok bileşen genelinde durumu kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="e0f03-392">To consume state across many components.</span></span>
* <span data-ttu-id="e0f03-393">Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="e0f03-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="e0f03-394">Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, kalıcı durumu küresel olarak önlemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="e0f03-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="e0f03-395">Bellek içi durum kapsayıcısı hizmeti</span><span class="sxs-lookup"><span data-stu-id="e0f03-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
