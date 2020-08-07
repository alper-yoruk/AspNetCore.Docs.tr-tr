---
title: ASP.NET Core Blazor durum yönetimi
author: guardrex
description: Uygulamalarda durumu kalıcı hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e4ec360e1f9fb0bc5784b3120d7842faf24cfa5b
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818774"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="3202a-103">ASP.NET Core Blazor durum yönetimi</span><span class="sxs-lookup"><span data-stu-id="3202a-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="3202a-104">Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3202a-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="3202a-105">Bir uygulamada oluşturulan kullanıcı durumu Blazor WebAssembly tarayıcının belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="3202a-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="3202a-106">Tarayıcı belleğinde tutulan Kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="3202a-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="3202a-107">Oluşturulan kullanıcı arabirimindeki bileşen örneklerinin ve en son işleme çıktılarının hiyerarşisi.</span><span class="sxs-lookup"><span data-stu-id="3202a-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="3202a-108">Bileşen örneklerinde alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="3202a-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="3202a-109">[Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.</span><span class="sxs-lookup"><span data-stu-id="3202a-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="3202a-110">[JavaScript birlikte çalışma](xref:blazor/call-javascript-from-dotnet) çağrıları aracılığıyla ayarlanan değerler.</span><span class="sxs-lookup"><span data-stu-id="3202a-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="3202a-111">Kullanıcı tarayıcısını kapatıp yeniden açtığında veya sayfayı yeniden yüklediğinde, tarayıcının belleğinde tutulan Kullanıcı durumu kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="3202a-112">Tarayıcı oturumlarında durumu kalıcı yap</span><span class="sxs-lookup"><span data-stu-id="3202a-112">Persist state across browser sessions</span></span>

<span data-ttu-id="3202a-113">Genellikle, kullanıcıların, zaten mevcut olan verileri okurken değil, etkin bir şekilde veri oluşturmakta olduğu tarayıcı oturumlarında durumu koruyun.</span><span class="sxs-lookup"><span data-stu-id="3202a-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="3202a-114">Tarayıcı oturumlarında durumu korumak için, uygulamanın verileri tarayıcı belleğinden başka bir depolama konumuna kalıcı hale vermelidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="3202a-115">Durum kalıcılığı otomatik değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-115">State persistence isn't automatic.</span></span> <span data-ttu-id="3202a-116">Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3202a-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="3202a-117">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="3202a-118">Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="3202a-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="3202a-119">Çok adımlı Web formları: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir Web formunun birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır.</span><span class="sxs-lookup"><span data-stu-id="3202a-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="3202a-120">Kullanıcı formdan ayrıldıklarında ve daha sonra geri dönüp Bu senaryodaki durumu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="3202a-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="3202a-121">Alışveriş sepetlerini: olası geliri temsil eden bir uygulamanın ticari olarak önemli bileşenleri korunabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="3202a-122">Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="3202a-123">Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="3202a-123">An app can only persist *app state*.</span></span> <span data-ttu-id="3202a-124">Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="3202a-125">Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="3202a-126">Ağaç görünümü denetiminin genişletilmiş düğümleri gibi UI durumunu kalıcı hale getirmek için, uygulamanın kullanıcı arabirimi durumunun davranışını seri hale getirilebilir uygulama durumu olarak modellemek üzere özel kod kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3202a-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="3202a-127">Durumun nerede kalıcı olduğu</span><span class="sxs-lookup"><span data-stu-id="3202a-127">Where to persist state</span></span>

<span data-ttu-id="3202a-128">Kalıcı durum için üç ortak konum mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="3202a-128">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="3202a-129">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="3202a-130">URL</span><span class="sxs-lookup"><span data-stu-id="3202a-130">URL</span></span>](#url)
* [<span data-ttu-id="3202a-131">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-131">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="3202a-132">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-132">Server-side storage</span></span>

<span data-ttu-id="3202a-133">Birden çok kullanıcı ve cihaza yayılan kalıcı veri kalıcılığı için, uygulama bir Web API 'SI aracılığıyla erişilen bağımsız sunucu tarafı depolama kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-133">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="3202a-134">Seçeneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="3202a-134">Options include:</span></span>

* <span data-ttu-id="3202a-135">Blob depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-135">Blob storage</span></span>
* <span data-ttu-id="3202a-136">Anahtar-değer depolaması</span><span class="sxs-lookup"><span data-stu-id="3202a-136">Key-value storage</span></span>
* <span data-ttu-id="3202a-137">İlişkisel veritabanı</span><span class="sxs-lookup"><span data-stu-id="3202a-137">Relational database</span></span>
* <span data-ttu-id="3202a-138">Table Storage</span><span class="sxs-lookup"><span data-stu-id="3202a-138">Table storage</span></span>

<span data-ttu-id="3202a-139">Veriler kaydedildikten sonra, kullanıcının durumu korunur ve herhangi bir yeni tarayıcı oturumunda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-139">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="3202a-140">Blazor WebAssemblyUygulamalar tamamen kullanıcının tarayıcısında çalıştığı için, depolama hizmetleri ve veritabanları gibi güvenli dış sistemlere erişmek için ek ölçüler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-140">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="3202a-141">Blazor WebAssemblyuygulamalar, tek sayfalı uygulamalarla (maça 'Lar) aynı şekilde güvenli hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-141">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="3202a-142">Genellikle, bir uygulama [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) aracılığıyla bir kullanıcının kimliğini doğrular ve ardından sunucu tarafı bir uygulamaya Web API çağrıları aracılığıyla depolama hizmetleri ve veritabanlarıyla etkileşime girer.</span><span class="sxs-lookup"><span data-stu-id="3202a-142">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="3202a-143">Sunucu tarafı uygulama, Blazor WebAssembly uygulama ve depolama hizmeti ya da veritabanı arasında veri aktarımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3202a-143">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="3202a-144">Sunucu tarafı uygulamasının Blazor WebAssembly depolamaya kalıcı bir bağlantısı olduğunda uygulama, sunucu tarafı uygulamasına kısa ömürlü bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="3202a-144">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="3202a-145">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="3202a-145">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="3202a-146">Blazor\*Güvenlik ve Identity \* makaleleri</span><span class="sxs-lookup"><span data-stu-id="3202a-146">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="3202a-147">Azure veri depolama seçenekleri hakkında daha fazla bilgi için aşağıdakilere bakın:</span><span class="sxs-lookup"><span data-stu-id="3202a-147">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="3202a-148">Azure veritabanları</span><span class="sxs-lookup"><span data-stu-id="3202a-148">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="3202a-149">Azure depolama belgeleri</span><span class="sxs-lookup"><span data-stu-id="3202a-149">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="3202a-150">URL</span><span class="sxs-lookup"><span data-stu-id="3202a-150">URL</span></span>

<span data-ttu-id="3202a-151">Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-151">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="3202a-152">URL 'de modellenen Kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="3202a-152">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="3202a-153">Görüntülenen varlığın KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="3202a-153">The ID of a viewed entity.</span></span>
* <span data-ttu-id="3202a-154">Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="3202a-154">The current page number in a paged grid.</span></span>

<span data-ttu-id="3202a-155">Kullanıcı sayfayı el ile yeniden yüklediğinde tarayıcının adres çubuğunun içeriği korunur.</span><span class="sxs-lookup"><span data-stu-id="3202a-155">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="3202a-156">Yönergeyle URL desenleri tanımlama hakkında bilgi için [`@page`](xref:mvc/views/razor#page) bkz <xref:blazor/fundamentals/routing> ..</span><span class="sxs-lookup"><span data-stu-id="3202a-156">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="3202a-157">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-157">Browser storage</span></span>

<span data-ttu-id="3202a-158">Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın olarak kullanılan bir depolama konumu tarayıcının [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) ve [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) koleksiyonlarıdır:</span><span class="sxs-lookup"><span data-stu-id="3202a-158">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="3202a-159">`localStorage`tarayıcı penceresinin kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="3202a-159">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="3202a-160">Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-160">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="3202a-161">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="3202a-161">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="3202a-162">Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="3202a-162">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="3202a-163">`sessionStorage`, tarayıcı sekmesinin kapsamına alınır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-163">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="3202a-164">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-164">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="3202a-165">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.</span><span class="sxs-lookup"><span data-stu-id="3202a-165">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="3202a-166">`localStorage`ve `sessionStorage` Blazor WebAssembly yalnızca özel kod yazarak veya üçüncü taraf bir paket kullanılarak uygulamalarda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-166">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="3202a-167">Genellikle, `sessionStorage` kullanmak daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-167">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="3202a-168">`sessionStorage`bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:</span><span class="sxs-lookup"><span data-stu-id="3202a-168">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="3202a-169">Sekmelerde durum depolamadaki hatalar.</span><span class="sxs-lookup"><span data-stu-id="3202a-169">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="3202a-170">Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.</span><span class="sxs-lookup"><span data-stu-id="3202a-170">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="3202a-171">`localStorage`uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="3202a-171">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="3202a-172">Kullanıcılar ve içinde depolanan verileri görüntüleyebilir veya bunlarla karşılaşabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="3202a-172">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3202a-173">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3202a-173">Additional resources</span></span>

* [<span data-ttu-id="3202a-174">Bir kimlik doğrulama işleminden önce uygulama durumunu Kaydet</span><span class="sxs-lookup"><span data-stu-id="3202a-174">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="3202a-175">Blazor Serverdurum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-175">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="3202a-176">Çoğu zaman, uygulama sunucusuyla bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="3202a-176">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="3202a-177">Kullanıcının durumu, sunucu belleğinde bir *devrende*tutulur.</span><span class="sxs-lookup"><span data-stu-id="3202a-177">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="3202a-178">Bir devreye sahip kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="3202a-178">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="3202a-179">Oluşturulan kullanıcı arabirimindeki bileşen örneklerinin ve en son işleme çıktılarının hiyerarşisi.</span><span class="sxs-lookup"><span data-stu-id="3202a-179">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="3202a-180">Bileşen örneklerinde alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="3202a-180">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="3202a-181">Devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.</span><span class="sxs-lookup"><span data-stu-id="3202a-181">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="3202a-182">Kullanıcı durumu, [JavaScript birlikte çalışma](xref:blazor/call-javascript-from-dotnet) çağrıları aracılığıyla tarayıcının bellek kümesindeki JavaScript değişkenlerinde de bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-182">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="3202a-183">Bir Kullanıcı geçici bir ağ bağlantısı kaybıyla karşılaşıyorsa, Blazor özgün durumlarındaki kullanıcıyı özgün devresine yeniden bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="3202a-183">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="3202a-184">Ancak, bir kullanıcıyı sunucunun belleğindeki özgün devresine yeniden bağlamak her zaman mümkün değildir:</span><span class="sxs-lookup"><span data-stu-id="3202a-184">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="3202a-185">Sunucu, bağlantısı kesilen bir devreni süresiz olarak sürdüremez.</span><span class="sxs-lookup"><span data-stu-id="3202a-185">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="3202a-186">Sunucu, bir zaman aşımından sonra veya sunucu bellek baskısı altında olduğunda, bağlantısı kesilen bir bağlantı hattını serbest bırakmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3202a-186">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="3202a-187">Çok sunuculu, yük dengeli dağıtım ortamlarında, tek tek sunucular başarısız olabilir veya artık tüm istek hacminin işlenmesi gerekmiyorsa otomatik olarak kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-187">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="3202a-188">Kullanıcı yeniden bağlanmayı denediğinde, bir kullanıcı için özgün sunucu işleme istekleri kullanılamaz hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-188">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="3202a-189">Kullanıcı tarayıcıyı kapatabilir ve yeniden açabilir veya sayfayı yeniden yükleyerek tarayıcı belleğinde tutulan tüm durumları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="3202a-189">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="3202a-190">Örneğin, JavaScript birlikte çalışma çağrıları aracılığıyla ayarlanan JavaScript değişken değerleri kaybolur.</span><span class="sxs-lookup"><span data-stu-id="3202a-190">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="3202a-191">Kullanıcı özgün devresine yeniden bağlanalınmayacaksa, Kullanıcı boş duruma sahip yeni bir devre alır.</span><span class="sxs-lookup"><span data-stu-id="3202a-191">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="3202a-192">Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="3202a-192">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="3202a-193">Devreler arasında durumu kalıcı yap</span><span class="sxs-lookup"><span data-stu-id="3202a-193">Persist state across circuits</span></span>

<span data-ttu-id="3202a-194">Genellikle, kullanıcıların, zaten mevcut olan verileri okumadan değil, etkin bir şekilde veri oluşturmakta olduğu devrelerde durumu koruyun.</span><span class="sxs-lookup"><span data-stu-id="3202a-194">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="3202a-195">Uygulama, durumu devrelerde korumak için, verilerin sunucu belleğinden başka bir depolama konumuna kalıcı hale gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-195">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="3202a-196">Durum kalıcılığı otomatik değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-196">State persistence isn't automatic.</span></span> <span data-ttu-id="3202a-197">Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3202a-197">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="3202a-198">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-198">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="3202a-199">Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="3202a-199">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="3202a-200">Çok adımlı Web formları: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir Web formunun birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır.</span><span class="sxs-lookup"><span data-stu-id="3202a-200">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="3202a-201">Kullanıcı formdan ayrıldıklarında ve daha sonra geri dönüp Bu senaryodaki durumu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="3202a-201">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="3202a-202">Alışveriş sepetlerini: olası geliri temsil eden bir uygulamanın ticari olarak önemli bileşenleri korunabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-202">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="3202a-203">Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-203">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="3202a-204">Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="3202a-204">An app can only persist *app state*.</span></span> <span data-ttu-id="3202a-205">Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-205">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="3202a-206">Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-206">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="3202a-207">Ağaç görünümü denetiminin genişletilmiş düğümleri gibi UI durumunu kalıcı hale getirmek için, uygulamanın kullanıcı arabirimi durumunun davranışını seri hale getirilebilir uygulama durumu olarak modellemek üzere özel kod kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3202a-207">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="3202a-208">Durumun nerede kalıcı olduğu</span><span class="sxs-lookup"><span data-stu-id="3202a-208">Where to persist state</span></span>

<span data-ttu-id="3202a-209">Kalıcı durum için üç ortak konum mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="3202a-209">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="3202a-210">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-210">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="3202a-211">URL</span><span class="sxs-lookup"><span data-stu-id="3202a-211">URL</span></span>](#url)
* [<span data-ttu-id="3202a-212">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-212">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="3202a-213">Sunucu tarafı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-213">Server-side storage</span></span>

<span data-ttu-id="3202a-214">Birden çok kullanıcı ve cihaza yayılan kalıcı veri kalıcılığı için, uygulama sunucu tarafı depolama kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-214">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="3202a-215">Seçeneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="3202a-215">Options include:</span></span>

* <span data-ttu-id="3202a-216">Blob depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-216">Blob storage</span></span>
* <span data-ttu-id="3202a-217">Anahtar-değer depolaması</span><span class="sxs-lookup"><span data-stu-id="3202a-217">Key-value storage</span></span>
* <span data-ttu-id="3202a-218">İlişkisel veritabanı</span><span class="sxs-lookup"><span data-stu-id="3202a-218">Relational database</span></span>
* <span data-ttu-id="3202a-219">Table Storage</span><span class="sxs-lookup"><span data-stu-id="3202a-219">Table storage</span></span>

<span data-ttu-id="3202a-220">Veriler kaydedildikten sonra, kullanıcının durumu korunur ve yeni bir devrede kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-220">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="3202a-221">Azure veri depolama seçenekleri hakkında daha fazla bilgi için aşağıdakilere bakın:</span><span class="sxs-lookup"><span data-stu-id="3202a-221">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="3202a-222">Azure veritabanları</span><span class="sxs-lookup"><span data-stu-id="3202a-222">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="3202a-223">Azure depolama belgeleri</span><span class="sxs-lookup"><span data-stu-id="3202a-223">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="3202a-224">URL</span><span class="sxs-lookup"><span data-stu-id="3202a-224">URL</span></span>

<span data-ttu-id="3202a-225">Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-225">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="3202a-226">URL 'de modellenen Kullanıcı durumu örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="3202a-226">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="3202a-227">Görüntülenen varlığın KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="3202a-227">The ID of a viewed entity.</span></span>
* <span data-ttu-id="3202a-228">Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="3202a-228">The current page number in a paged grid.</span></span>

<span data-ttu-id="3202a-229">Tarayıcının adres çubuğunun içeriği korunur:</span><span class="sxs-lookup"><span data-stu-id="3202a-229">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="3202a-230">Kullanıcı sayfayı el ile yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="3202a-230">If the user manually reloads the page.</span></span>
* <span data-ttu-id="3202a-231">Web sunucusu kullanılamaz hale gelirse ve Kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="3202a-231">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="3202a-232">Yönergeyle URL desenleri tanımlama hakkında bilgi için [`@page`](xref:mvc/views/razor#page) bkz <xref:blazor/fundamentals/routing> ..</span><span class="sxs-lookup"><span data-stu-id="3202a-232">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="3202a-233">Tarayıcı depolama</span><span class="sxs-lookup"><span data-stu-id="3202a-233">Browser storage</span></span>

<span data-ttu-id="3202a-234">Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın olarak kullanılan bir depolama konumu tarayıcının [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) ve [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) koleksiyonlarıdır:</span><span class="sxs-lookup"><span data-stu-id="3202a-234">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="3202a-235">`localStorage`tarayıcı penceresinin kapsamına alınır.</span><span class="sxs-lookup"><span data-stu-id="3202a-235">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="3202a-236">Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-236">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="3202a-237">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="3202a-237">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="3202a-238">Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="3202a-238">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="3202a-239">`sessionStorage`, tarayıcı sekmesinin kapsamına alınır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-239">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="3202a-240">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-240">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="3202a-241">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.</span><span class="sxs-lookup"><span data-stu-id="3202a-241">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="3202a-242">Genellikle, `sessionStorage` kullanmak daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-242">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="3202a-243">`sessionStorage`bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:</span><span class="sxs-lookup"><span data-stu-id="3202a-243">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="3202a-244">Sekmelerde durum depolamadaki hatalar.</span><span class="sxs-lookup"><span data-stu-id="3202a-244">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="3202a-245">Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.</span><span class="sxs-lookup"><span data-stu-id="3202a-245">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="3202a-246">`localStorage`uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="3202a-246">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="3202a-247">Tarayıcı depolamayı kullanmaya yönelik uyarılar:</span><span class="sxs-lookup"><span data-stu-id="3202a-247">Caveats for using browser storage:</span></span>

* <span data-ttu-id="3202a-248">Sunucu tarafı veritabanının kullanımına benzer şekilde veri yükleme ve kaydetme zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="3202a-248">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="3202a-249">Sunucu tarafı veritabanının aksine, istenen sayfa prerendering aşamasında tarayıcıda bulunmadığından, depolama alanı prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="3202a-249">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="3202a-250">Birkaç kilobayt veri depolaması, uygulamalar için kalıcı hale getirilmesi mantıklıdır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="3202a-250">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="3202a-251">Birkaç kilobayt dışında, veriler ağ üzerinden yüklenip kaydedildiğinden performans etkilerini göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3202a-251">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="3202a-252">Kullanıcılar verileri görüntüleyebilir veya bunlarla karşılaşabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-252">Users may view or tamper with the data.</span></span> <span data-ttu-id="3202a-253">[ASP.NET Core veri koruma](xref:security/data-protection/introduction) riski azaltabilirler.</span><span class="sxs-lookup"><span data-stu-id="3202a-253">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="3202a-254">Örneğin, [ASP.NET Core korumalı tarayıcı depolama](#aspnet-core-protected-browser-storage) ASP.NET Core veri koruma kullanır.</span><span class="sxs-lookup"><span data-stu-id="3202a-254">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="3202a-255">Üçüncü taraf NuGet paketleri ve ile çalışmaya yönelik API 'Ler `localStorage` sağlar `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="3202a-255">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="3202a-256">[ASP.NET Core veri korumasını](xref:security/data-protection/introduction)saydam olarak kullanan bir paket seçmeyi düşünülüyor.</span><span class="sxs-lookup"><span data-stu-id="3202a-256">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="3202a-257">Veri koruma, depolanan verileri şifreler ve depolanan verilerle yapılan değişikliklere karşı olası riski azaltır.</span><span class="sxs-lookup"><span data-stu-id="3202a-257">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="3202a-258">JSON seri hale getirilmiş veriler düz metin halinde depolanıyorsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri de değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-258">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="3202a-259">Verilerin güvenliğini sağlamak her zaman bir sorun değildir çünkü veriler önemsiz olarak olabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-259">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="3202a-260">Örneğin, bir kullanıcı ARABIRIMI öğesinin saklı rengini okumak veya değiştirmek, Kullanıcı veya kuruluş için önemli bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-260">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="3202a-261">Kullanıcıların *hassas verileri*incelemesine veya değiştirmesine izin vermeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-261">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="3202a-262">ASP.NET Core korumalı tarayıcı depolaması</span><span class="sxs-lookup"><span data-stu-id="3202a-262">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="3202a-263">ASP.NET Core korumalı tarayıcı depolaması, ve için [veri koruma ASP.NET Core](xref:security/data-protection/introduction) yararlanır [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="3202a-263">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="3202a-264">Korumalı tarayıcı depolaması ASP.NET Core veri korumasına dayanır ve yalnızca uygulamalar için desteklenir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="3202a-264">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="3202a-265">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3202a-265">Configuration</span></span>

1. <span data-ttu-id="3202a-266">Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="3202a-266">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="3202a-267">`Startup.ConfigureServices`' De, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="3202a-267">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="3202a-268">Bir bileşen içindeki verileri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="3202a-268">Save and load data within a component</span></span>

<span data-ttu-id="3202a-269">Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birini bir örnek eklemek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3202a-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="3202a-270">Seçim, hangi tarayıcı depolama konumuna kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3202a-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="3202a-271">Aşağıdaki örnekte, `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="3202a-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="3202a-272">`@using`Yönergesi `_Imports.razor` bileşen yerine uygulamanın dosyasına yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="3202a-273">`_Imports.razor`Dosya kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="3202a-274">`currentCount` `Counter` Proje şablonunu temel alan bir uygulamanın bileşenindeki değeri kalıcı hale getirmek için Blazor Server , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="3202a-274">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="3202a-275">Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="3202a-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="3202a-276">Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="3202a-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="3202a-277">`ProtectedSessionStore`karmaşık durum nesnelerini depolamak için JSON verilerini otomatik olarak serileştirir ve seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="3202a-278">Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="3202a-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="3202a-279">Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core veri koruma kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="3202a-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="3202a-280">Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiyse incelenebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="3202a-281">`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için, `Counter` kullanıcının yeni bir devrede olması dahil, şunu kullanın `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="3202a-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="3202a-282">Bileşenin parametreleri gezinti durumu içeriyorsa, çağrısı `ProtectedSessionStore.GetAsync` `null` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> yapın ve not edin <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="3202a-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="3202a-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>yalnızca bileşen ilk kez oluşturulduğunda çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3202a-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="3202a-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="3202a-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="3202a-285">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="3202a-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="3202a-286">Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır.</span><span class="sxs-lookup"><span data-stu-id="3202a-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="3202a-287">Prerendering etkinken, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamadığı belirten bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3202a-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="3202a-288">Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="3202a-289">Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="3202a-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="3202a-290">Yükleme durumunu işle</span><span class="sxs-lookup"><span data-stu-id="3202a-290">Handle the loading state</span></span>

<span data-ttu-id="3202a-291">Tarayıcı depolamaya bir ağ bağlantısı üzerinden zaman uyumsuz olarak erişildiği için, veriler yüklenmeden ve bir bileşen için kullanılabilir olmadan önce her zaman bir zaman dilimi vardır.</span><span class="sxs-lookup"><span data-stu-id="3202a-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="3202a-292">En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="3202a-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="3202a-293">Bir yaklaşım, verilerin ne olduğunu `null` , yani verilerin hala yüklenmekte olduğunu izlemedir.</span><span class="sxs-lookup"><span data-stu-id="3202a-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="3202a-294">Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` .</span><span class="sxs-lookup"><span data-stu-id="3202a-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="3202a-295">Türe () bir soru işareti () ekleyerek [ `currentCount` null yapılabilir yapın](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` :</span><span class="sxs-lookup"><span data-stu-id="3202a-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="3202a-296">Count ve Button öğesinin Koşullu olarak görüntülenmesi yerine **`Increment`** , bu öğeleri yalnızca veriler denetlenerek yüklenirse görüntüleyin <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="3202a-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="3202a-297">Tanıtıcı prerendering</span><span class="sxs-lookup"><span data-stu-id="3202a-297">Handle prerendering</span></span>

<span data-ttu-id="3202a-298">Prerendering sırasında:</span><span class="sxs-lookup"><span data-stu-id="3202a-298">During prerendering:</span></span>

* <span data-ttu-id="3202a-299">Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="3202a-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="3202a-300">Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="3202a-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="3202a-301">`localStorage`veya `sessionStorage` prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="3202a-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="3202a-302">Bileşen depolama ile etkileşim kurmayı denerse, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamayacağını açıklayan bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3202a-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="3202a-303">Hatayı çözmek için bir yol prerendering devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="3202a-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="3202a-304">Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="3202a-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="3202a-305">Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="3202a-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="3202a-306">Prerendering 'yi devre dışı bırakmak için, `Pages/_Host.cshtml` dosyayı açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) özniteliğini şu şekilde değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="3202a-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="3202a-307">Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="3202a-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="3202a-308">Prerendering devam etmek için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="3202a-309">Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3202a-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="3202a-310">Durum korumasını ortak bir konuma ayırın</span><span class="sxs-lookup"><span data-stu-id="3202a-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="3202a-311">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3202a-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="3202a-312">Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="3202a-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="3202a-313">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="3202a-314">Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri şunlar için kalıcıdır `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="3202a-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
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

<span data-ttu-id="3202a-315">`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="3202a-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="3202a-316">Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın.</span><span class="sxs-lookup"><span data-stu-id="3202a-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="3202a-317">Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşenin () etrafında sarmalayın `App.razor` :</span><span class="sxs-lookup"><span data-stu-id="3202a-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="3202a-318">Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="3202a-319">Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:</span><span class="sxs-lookup"><span data-stu-id="3202a-319">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="3202a-320">Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="3202a-321">Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="3202a-322">Daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="3202a-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="3202a-323">Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:</span><span class="sxs-lookup"><span data-stu-id="3202a-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="3202a-324">Birçok bileşen genelinde durumu kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="3202a-324">To consume state across many components.</span></span>
* <span data-ttu-id="3202a-325">Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="3202a-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="3202a-326">Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, kalıcı durumu küresel olarak önlemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="3202a-327">Korumalı tarayıcı depolaması deneysel NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="3202a-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="3202a-328">ASP.NET Core korumalı tarayıcı depolaması, ve için [veri koruma ASP.NET Core](xref:security/data-protection/introduction) yararlanır [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="3202a-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="3202a-329">`Microsoft.AspNetCore.ProtectedBrowserStorage`, desteklenmeyen, deneysel bir paket, üretim kullanımı için uygun değil.</span><span class="sxs-lookup"><span data-stu-id="3202a-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="3202a-330">Paket yalnızca ASP.NET Core 3,1 Blazor Server uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-330">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="3202a-331">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3202a-331">Configuration</span></span>

1. <span data-ttu-id="3202a-332">Öğesine bir paket başvurusu ekleyin [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="3202a-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="3202a-333">`Pages/_Host.cshtml`Dosyasında, kapanış etiketinin içine aşağıdaki betiği ekleyin `</body>` :</span><span class="sxs-lookup"><span data-stu-id="3202a-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="3202a-334">`Startup.ConfigureServices`' De, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="3202a-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="3202a-335">Bir bileşen içindeki verileri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="3202a-335">Save and load data within a component</span></span>

<span data-ttu-id="3202a-336">Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birini bir örnek eklemek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3202a-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="3202a-337">Seçim, hangi tarayıcı depolama konumuna kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3202a-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="3202a-338">Aşağıdaki örnekte, `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="3202a-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="3202a-339">`@using`İfade, `_Imports.razor` bileşen yerine bir dosyaya yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="3202a-340">`_Imports.razor`Dosya kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="3202a-341">`currentCount` `Counter` Proje şablonunu temel alan bir uygulamanın bileşenindeki değeri kalıcı hale getirmek için Blazor Server , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="3202a-341">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="3202a-342">Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="3202a-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="3202a-343">Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="3202a-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="3202a-344">`ProtectedSessionStore`JSON verilerini otomatik olarak serileştirir ve seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="3202a-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="3202a-345">Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="3202a-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="3202a-346">Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core veri koruma kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="3202a-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="3202a-347">Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiyse incelenebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="3202a-348">`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için `Counter` , tamamen yeni bir devrede olanlar da dahil olmak üzere şunu kullanın `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="3202a-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="3202a-349">Bileşenin parametreleri gezinti durumu içeriyorsa, `ProtectedSessionStore.GetAsync` sonucunu çağırın ve ' de atayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="3202a-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="3202a-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>yalnızca bileşen ilk kez oluşturulduğunda çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3202a-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="3202a-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="3202a-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="3202a-352">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="3202a-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="3202a-353">Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır.</span><span class="sxs-lookup"><span data-stu-id="3202a-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="3202a-354">Prerendering etkinken, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamadığı belirten bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3202a-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="3202a-355">Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="3202a-356">Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="3202a-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="3202a-357">Yükleme durumunu işle</span><span class="sxs-lookup"><span data-stu-id="3202a-357">Handle the loading state</span></span>

<span data-ttu-id="3202a-358">Tarayıcı depolamaya bir ağ bağlantısı üzerinden zaman uyumsuz olarak erişildiği için, veriler yüklenmeden ve bir bileşen için kullanılabilir olmadan önce her zaman bir zaman dilimi vardır.</span><span class="sxs-lookup"><span data-stu-id="3202a-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="3202a-359">En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="3202a-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="3202a-360">Bir yaklaşım, verilerin ne olduğunu `null` , yani verilerin hala yüklenmekte olduğunu izlemedir.</span><span class="sxs-lookup"><span data-stu-id="3202a-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="3202a-361">Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` .</span><span class="sxs-lookup"><span data-stu-id="3202a-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="3202a-362">Türe () bir soru işareti () ekleyerek [ `currentCount` null yapılabilir yapın](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) `?` `int` :</span><span class="sxs-lookup"><span data-stu-id="3202a-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="3202a-363">Count ve Button öğesinin Koşullu olarak görüntülenmesi yerine **`Increment`** , bu öğeleri yalnızca veriler yüklenmişse görüntülemeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="3202a-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="3202a-364">Tanıtıcı prerendering</span><span class="sxs-lookup"><span data-stu-id="3202a-364">Handle prerendering</span></span>

<span data-ttu-id="3202a-365">Prerendering sırasında:</span><span class="sxs-lookup"><span data-stu-id="3202a-365">During prerendering:</span></span>

* <span data-ttu-id="3202a-366">Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="3202a-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="3202a-367">Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="3202a-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="3202a-368">`localStorage`veya `sessionStorage` prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="3202a-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="3202a-369">Bileşen depolama ile etkileşim kurmayı denerse, bileşen önceden çıkarılmakta olduğundan JavaScript birlikte çalışma çağrılarının yayımlanamayacağını açıklayan bir hata oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3202a-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="3202a-370">Hatayı çözmek için bir yol prerendering devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="3202a-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="3202a-371">Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="3202a-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="3202a-372">Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="3202a-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="3202a-373">Prerendering 'yi devre dışı bırakmak için, `Pages/_Host.cshtml` dosyayı açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) özniteliğini şu şekilde değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="3202a-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="3202a-374">Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="3202a-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="3202a-375">Prerendering devam etmek için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin.</span><span class="sxs-lookup"><span data-stu-id="3202a-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="3202a-376">Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3202a-376">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="3202a-377">Durum korumasını ortak bir konuma ayırın</span><span class="sxs-lookup"><span data-stu-id="3202a-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="3202a-378">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3202a-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="3202a-379">Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="3202a-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="3202a-380">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="3202a-381">Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri şunlar için kalıcıdır `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="3202a-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

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

<span data-ttu-id="3202a-382">`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="3202a-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="3202a-383">Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın.</span><span class="sxs-lookup"><span data-stu-id="3202a-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="3202a-384">Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşenin () etrafında sarmalayın `App.razor` :</span><span class="sxs-lookup"><span data-stu-id="3202a-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="3202a-385">Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="3202a-386">Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:</span><span class="sxs-lookup"><span data-stu-id="3202a-386">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="3202a-387">Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3202a-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="3202a-388">Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="3202a-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="3202a-389">Daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="3202a-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="3202a-390">Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:</span><span class="sxs-lookup"><span data-stu-id="3202a-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="3202a-391">Birçok bileşen genelinde durumu kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="3202a-391">To consume state across many components.</span></span>
* <span data-ttu-id="3202a-392">Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="3202a-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="3202a-393">Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, kalıcı durumu küresel olarak önlemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="3202a-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: zone-end
