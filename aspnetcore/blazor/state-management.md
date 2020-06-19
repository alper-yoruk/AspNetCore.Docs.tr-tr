---
title: ASP.NET Core Blazor durum yönetimi
author: guardrex
description: Sunucu uygulamalarında durumu kalıcı hale getirme hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 3cc75406a1680dff4727527153a62856a594c8c7
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102498"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="0be14-103">ASP.NET Core Blazor durum yönetimi</span><span class="sxs-lookup"><span data-stu-id="0be14-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="0be14-104">[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="0be14-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

Blazor<span data-ttu-id="0be14-105">Sunucu, durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="0be14-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="0be14-106">Çoğu zaman, uygulama sunucuya devam eden bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="0be14-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="0be14-107">Kullanıcının durumu, sunucu belleğinde bir *devrende*tutulur.</span><span class="sxs-lookup"><span data-stu-id="0be14-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="0be14-108">Bir kullanıcının devresi için durum tutulan örnekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0be14-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="0be14-109">İşlenmiş Kullanıcı arabirimi: bileşen örneklerinin hiyerarşisi ve en son işleme çıktısı.</span><span class="sxs-lookup"><span data-stu-id="0be14-109">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="0be14-110">Bileşen örneklerinde alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="0be14-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="0be14-111">Devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.</span><span class="sxs-lookup"><span data-stu-id="0be14-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="0be14-112">Bu makale, sunucu uygulamalarında durum kalıcılığını ele alınmaktadır Blazor .</span><span class="sxs-lookup"><span data-stu-id="0be14-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="0be14-113">WebAssembly uygulamaları [Tarayıcıda istemci tarafı durum kalıcılığından](#client-side-in-the-browser) yararlanabilir, ancak bu makalenin kapsamı dışında özel çözümler veya üçüncü taraf paketleri gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a>Blazor<span data-ttu-id="0be14-114">uygulanıp</span><span class="sxs-lookup"><span data-stu-id="0be14-114"> circuits</span></span>

<span data-ttu-id="0be14-115">Bir Kullanıcı geçici bir ağ bağlantısı kaybıyla karşılaşıyorsa, Blazor uygulamayı kullanmaya devam edebilmek için kullanıcıyı özgün devresine yeniden bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="0be14-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="0be14-116">Ancak, bir kullanıcıyı sunucunun belleğindeki özgün devresine yeniden bağlamak her zaman mümkün değildir:</span><span class="sxs-lookup"><span data-stu-id="0be14-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="0be14-117">Sunucu, bağlantısı kesilen bir devreni süresiz olarak sürdüremez.</span><span class="sxs-lookup"><span data-stu-id="0be14-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="0be14-118">Sunucu, bir zaman aşımından sonra veya sunucu bellek baskısı altında olduğunda, bağlantısı kesilen bir bağlantı hattını serbest bırakmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0be14-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="0be14-119">Çoklu sunucu, yük dengeli dağıtım ortamlarında, herhangi bir zamanda herhangi bir sunucu işleme isteği kullanılamaz hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="0be14-120">Tek tek sunucular, tüm istek hacmini işlemek için artık gerekli olmadığında veya otomatik olarak kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="0be14-121">Kullanıcı yeniden bağlanmaya çalıştığında, özgün sunucu kullanılamayabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="0be14-122">Kullanıcı tarayıcıyı kapatabilir ve yeniden açabilir veya sayfayı yeniden yükleyerek tarayıcı belleğinde tutulan tüm durumları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0be14-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="0be14-123">Örneğin, JavaScript birlikte çalışabilirlik çağrıları aracılığıyla ayarlanan değerler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="0be14-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="0be14-124">Kullanıcı özgün devresine yeniden bağlanalınmayacaksa, Kullanıcı boş duruma sahip yeni bir devre alır.</span><span class="sxs-lookup"><span data-stu-id="0be14-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="0be14-125">Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="0be14-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="0be14-126">Devreler arasında durumu koru</span><span class="sxs-lookup"><span data-stu-id="0be14-126">Preserve state across circuits</span></span>

<span data-ttu-id="0be14-127">Bazı senaryolarda, devre genelinde durum koruma istenebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="0be14-128">Bir uygulama, şu durumlarda bir kullanıcı için önemli verileri koruyabilir:</span><span class="sxs-lookup"><span data-stu-id="0be14-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="0be14-129">Web sunucusu kullanılamaz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="0be14-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="0be14-130">Kullanıcının tarayıcısı yeni bir Web sunucusuyla yeni bir devre başlatmaya zorlanır.</span><span class="sxs-lookup"><span data-stu-id="0be14-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="0be14-131">Genel olarak, devrelerde durumu korumak, kullanıcıların zaten var olan verileri okurken değil, etkin bir şekilde veri oluşturmakta olduğu senaryolar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0be14-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="0be14-132">Tek bir devrenin ötesinde durumu korumak için, *verileri yalnızca sunucunun belleğine depolamayın*.</span><span class="sxs-lookup"><span data-stu-id="0be14-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="0be14-133">Uygulama, verileri başka bir depolama konumuna kalıcı hale vermelidir.</span><span class="sxs-lookup"><span data-stu-id="0be14-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="0be14-134">Durum kalıcılığı otomatik değildir.</span><span class="sxs-lookup"><span data-stu-id="0be14-134">State persistence isn't automatic.</span></span> <span data-ttu-id="0be14-135">Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0be14-135">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="0be14-136">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0be14-136">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="0be14-137">Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="0be14-137">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="0be14-138">Çok adımlı WebForm: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir işlemin birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır.</span><span class="sxs-lookup"><span data-stu-id="0be14-138">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="0be14-139">Kullanıcı, çok adımlı formdan uzaklaştıklarında ve daha sonra forma geri döndüğünüzde bu senaryodaki durumu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="0be14-139">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="0be14-140">Alışveriş sepeti: potansiyel geliri temsil eden, bir uygulamanın ticari olarak önemli bir bileşeni korunabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-140">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="0be14-141">Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-141">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="0be14-142">Genellikle, gönderilmemiş bir oturum açma iletişim kutusuna girilen Kullanıcı adı gibi, kolayca yeniden oluşturulmuş durumu korumak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0be14-142">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0be14-143">Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="0be14-143">An app can only persist *app state*.</span></span> <span data-ttu-id="0be14-144">Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-144">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="0be14-145">Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="0be14-145">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="0be14-146">Bir TreeView 'un genişletilmiş düğümleri gibi kullanıcı arabirimi durumuna benzer bir şeyi sürdürmek için, uygulamanın davranışı seri hale getirilebilir uygulama durumu olarak modelleyeceği özel kodu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0be14-146">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="0be14-147">Durumun nerede kalıcı olduğu</span><span class="sxs-lookup"><span data-stu-id="0be14-147">Where to persist state</span></span>

<span data-ttu-id="0be14-148">Sunucu uygulamasındaki kalıcı durum için üç ortak konum vardır Blazor .</span><span class="sxs-lookup"><span data-stu-id="0be14-148">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="0be14-149">Her yaklaşım farklı senaryolara en iyi şekilde uygundur ve farklı uyarılar içerir:</span><span class="sxs-lookup"><span data-stu-id="0be14-149">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="0be14-150">Veritabanında sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="0be14-150">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="0be14-151">URL</span><span class="sxs-lookup"><span data-stu-id="0be14-151">URL</span></span>](#url)
* [<span data-ttu-id="0be14-152">Tarayıcıda istemci tarafı</span><span class="sxs-lookup"><span data-stu-id="0be14-152">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="0be14-153">Veritabanında sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="0be14-153">Server-side in a database</span></span>

<span data-ttu-id="0be14-154">Kalıcı veri kalıcılığı veya birden çok kullanıcı veya cihaza yayılması gereken veriler için, bağımsız bir sunucu tarafı veritabanı neredeyse en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="0be14-154">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="0be14-155">Seçeneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="0be14-155">Options include:</span></span>

* <span data-ttu-id="0be14-156">İlişkisel SQL veritabanı</span><span class="sxs-lookup"><span data-stu-id="0be14-156">Relational SQL database</span></span>
* <span data-ttu-id="0be14-157">Anahtar değeri deposu</span><span class="sxs-lookup"><span data-stu-id="0be14-157">Key-value store</span></span>
* <span data-ttu-id="0be14-158">Blob deposu</span><span class="sxs-lookup"><span data-stu-id="0be14-158">Blob store</span></span>
* <span data-ttu-id="0be14-159">Tablo deposu</span><span class="sxs-lookup"><span data-stu-id="0be14-159">Table store</span></span>

<span data-ttu-id="0be14-160">Veriler veritabanına kaydedildikten sonra, bir kullanıcı tarafından herhangi bir zamanda yeni bir devre başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-160">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="0be14-161">Kullanıcının verileri korunur ve yeni bir devrede kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-161">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="0be14-162">Azure veri depolama seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama belgeleri](/azure/storage/) ve [Azure veritabanları](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="0be14-162">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="0be14-163">URL</span><span class="sxs-lookup"><span data-stu-id="0be14-163">URL</span></span>

<span data-ttu-id="0be14-164">Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin.</span><span class="sxs-lookup"><span data-stu-id="0be14-164">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="0be14-165">URL 'de modellenen durum örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="0be14-165">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="0be14-166">Görüntülenen varlığın KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="0be14-166">The ID of a viewed entity.</span></span>
* <span data-ttu-id="0be14-167">Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="0be14-167">The current page number in a paged grid.</span></span>

<span data-ttu-id="0be14-168">Tarayıcının adres çubuğunun içeriği korunur:</span><span class="sxs-lookup"><span data-stu-id="0be14-168">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="0be14-169">Kullanıcı sayfayı el ile yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="0be14-169">If the user manually reloads the page.</span></span>
* <span data-ttu-id="0be14-170">Web sunucusu kullanılamaz hale gelirse ve Kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="0be14-170">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="0be14-171">Yönergeyle URL desenleri tanımlama hakkında bilgi için `@page` bkz <xref:blazor/fundamentals/routing> ..</span><span class="sxs-lookup"><span data-stu-id="0be14-171">For information on defining URL patterns with the `@page` directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="0be14-172">Tarayıcıda istemci tarafı</span><span class="sxs-lookup"><span data-stu-id="0be14-172">Client-side in the browser</span></span>

<span data-ttu-id="0be14-173">Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın bir yedekleme deposu tarayıcının `localStorage` ve `sessionStorage` koleksiyonlarıdır.</span><span class="sxs-lookup"><span data-stu-id="0be14-173">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="0be14-174">Devre dışı bırakılırsa, sunucu tarafı depolama alanının avantajlarından yararlanan uygulama, saklı durumu yönetmek veya temizlemek için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0be14-174">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="0be14-175">Bu bölümdeki "istemci tarafı", [ Blazor webassembly barındırma modelinde](xref:blazor/hosting-models#blazor-webassembly)değil, tarayıcıdaki istemci tarafı senaryolarına başvurur.</span><span class="sxs-lookup"><span data-stu-id="0be14-175">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="0be14-176">`localStorage`ve `sessionStorage` Blazor yalnızca özel kod yazarak veya 3. taraf paketini kullanarak webassembly uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-176">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="0be14-177">`localStorage`ve `sessionStorage` aşağıdaki gibi farklılık gösterir:</span><span class="sxs-lookup"><span data-stu-id="0be14-177">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="0be14-178">`localStorage`, kullanıcının tarayıcısına kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0be14-178">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="0be14-179">Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="0be14-179">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="0be14-180">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="0be14-180">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="0be14-181">Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="0be14-181">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="0be14-182">`sessionStorage`kullanıcının tarayıcı sekmesi kapsamıdır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="0be14-182">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="0be14-183">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-183">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="0be14-184">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.</span><span class="sxs-lookup"><span data-stu-id="0be14-184">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="0be14-185">Genellikle, `sessionStorage` kullanmak daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="0be14-185">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="0be14-186">`sessionStorage`bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:</span><span class="sxs-lookup"><span data-stu-id="0be14-186">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="0be14-187">Sekmelerde durum depolamadaki hatalar.</span><span class="sxs-lookup"><span data-stu-id="0be14-187">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="0be14-188">Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.</span><span class="sxs-lookup"><span data-stu-id="0be14-188">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="0be14-189">`localStorage`uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="0be14-189">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="0be14-190">Tarayıcı depolamayı kullanmaya yönelik uyarılar:</span><span class="sxs-lookup"><span data-stu-id="0be14-190">Caveats for using browser storage:</span></span>

* <span data-ttu-id="0be14-191">Sunucu tarafı veritabanının kullanımına benzer şekilde veri yükleme ve kaydetme zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="0be14-191">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="0be14-192">Sunucu tarafı veritabanının aksine, istenen sayfa prerendering aşamasında tarayıcıda bulunmadığından, depolama alanı prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="0be14-192">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="0be14-193">Birkaç kilobayt veri depolama alanı sunucu uygulamalarında kalıcı hale getiriyoruz Blazor .</span><span class="sxs-lookup"><span data-stu-id="0be14-193">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="0be14-194">Birkaç kilobayt dışında, veriler ağ üzerinden yüklenip kaydedildiğinden performans etkilerini göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0be14-194">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="0be14-195">Kullanıcılar verileri görüntüleyebilir veya bunlarla karşılaşabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-195">Users may view or tamper with the data.</span></span> <span data-ttu-id="0be14-196">ASP.NET Core [veri koruma](xref:security/data-protection/introduction) riski azaltabilirler.</span><span class="sxs-lookup"><span data-stu-id="0be14-196">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="0be14-197">Üçüncü taraf tarayıcı depolama çözümleri</span><span class="sxs-lookup"><span data-stu-id="0be14-197">Third-party browser storage solutions</span></span>

<span data-ttu-id="0be14-198">Üçüncü taraf NuGet paketleri ve ile çalışmaya yönelik API 'Ler `localStorage` sağlar `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0be14-198">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="0be14-199">ASP.NET Core [veri korumasını](xref:security/data-protection/introduction)saydam olarak kullanan bir paket seçmeyi düşünülüyor.</span><span class="sxs-lookup"><span data-stu-id="0be14-199">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0be14-200">ASP.NET Core veri koruma, depolanan verileri şifreler ve depolanan verilerle yapılan değişikliklere karşı olası riskleri azaltır.</span><span class="sxs-lookup"><span data-stu-id="0be14-200">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="0be14-201">JSON seri hale getirilmiş veriler düz metin halinde depolanıyorsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri de değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-201">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="0be14-202">Verilerin güvenliğini sağlamak her zaman bir sorun değildir çünkü veriler önemsiz olarak olabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-202">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="0be14-203">Örneğin, bir kullanıcı ARABIRIMI öğesinin saklı rengini okumak veya değiştirmek, Kullanıcı veya kuruluş için önemli bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="0be14-203">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="0be14-204">Kullanıcıların *hassas verileri*incelemesine veya değiştirmesine izin vermeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="0be14-204">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="0be14-205">Korumalı tarayıcı depolaması deneysel paket</span><span class="sxs-lookup"><span data-stu-id="0be14-205">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="0be14-206">[Data Protection](xref:security/data-protection/introduction) `localStorage` `sessionStorage` [Microsoft. Aspnetcore. Protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)ve için veri koruması sağlayan bir NuGet paketi örneği.</span><span class="sxs-lookup"><span data-stu-id="0be14-206">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="0be14-207">`Microsoft.AspNetCore.ProtectedBrowserStorage`, şu anda üretim kullanımı için uygun olmayan, desteklenmeyen bir deneysel paket.</span><span class="sxs-lookup"><span data-stu-id="0be14-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="0be14-208">Yükleme</span><span class="sxs-lookup"><span data-stu-id="0be14-208">Installation</span></span>

<span data-ttu-id="0be14-209">Paketi yüklemek için `Microsoft.AspNetCore.ProtectedBrowserStorage` :</span><span class="sxs-lookup"><span data-stu-id="0be14-209">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="0be14-210">BlazorSunucu uygulaması projesinde, [Microsoft. aspnetcore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0be14-210">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="0be14-211">Üst düzey HTML 'de (örneğin, varsayılan Proje şablonundaki *Pages/_Host. cshtml* dosyasında) aşağıdaki `<script>` etiketi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0be14-211">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="0be14-212">`Startup.ConfigureServices`Yönteminde, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="0be14-212">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="0be14-213">Bir bileşen içindeki verileri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="0be14-213">Save and load data within a component</span></span>

<span data-ttu-id="0be14-214">Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birinin bir örneğini eklemek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="0be14-214">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="0be14-215">Seçim, hangi yedekleme deposunu kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="0be14-215">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="0be14-216">Aşağıdaki örnekte, `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0be14-216">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="0be14-217">`@using`İfade, bileşen yerine bir *_Imports. Razor* dosyasına yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-217">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="0be14-218">*_Imports. Razor* dosyası kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0be14-218">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="0be14-219">`currentCount`Proje şablonunun bileşenindeki değeri kalıcı hale getirmek için `Counter` , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="0be14-219">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="0be14-220">Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="0be14-220">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="0be14-221">Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="0be14-221">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="0be14-222">`ProtectedSessionStore`JSON verilerini otomatik olarak serileştirir ve seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0be14-222">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="0be14-223">Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="0be14-223">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="0be14-224">Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core [veri koruma](xref:security/data-protection/introduction)kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="0be14-224">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0be14-225">Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiğinde görülebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-225">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="0be14-226">`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için `Counter` (tamamen yeni bir devrede olanlar dahil), şunu kullanın `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="0be14-226">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="0be14-227">Bileşenin parametreleri gezinti durumu içeriyorsa, `ProtectedSessionStore.GetAsync` sonucunu çağırın ve ' de atayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="0be14-227">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="0be14-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Yalnızca bileşenin ilk örneği oluşturulduğunda bir kez çağırılır.</span><span class="sxs-lookup"><span data-stu-id="0be14-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="0be14-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="0be14-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="0be14-230">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="0be14-230">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="0be14-231">Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır.</span><span class="sxs-lookup"><span data-stu-id="0be14-231">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="0be14-232">Prerendering etkinken şuna benzer bir hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0be14-232">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="0be14-233">JavaScript birlikte çalışabilirlik çağrıları şu an için verilemez.</span><span class="sxs-lookup"><span data-stu-id="0be14-233">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="0be14-234">Bunun nedeni, bileşenin ön işlenmiş olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="0be14-234">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="0be14-235">Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0be14-235">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="0be14-236">Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="0be14-236">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="0be14-237">Yükleme durumunu işle</span><span class="sxs-lookup"><span data-stu-id="0be14-237">Handle the loading state</span></span>

<span data-ttu-id="0be14-238">Tarayıcı depolaması zaman uyumsuz olduğundan (bir ağ bağlantısı üzerinden erişilir), veriler yüklenmeden ve bir bileşen tarafından kullanıma sunulmadan önce her zaman bir zaman dilimi vardır.</span><span class="sxs-lookup"><span data-stu-id="0be14-238">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="0be14-239">En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="0be14-239">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="0be14-240">Bir yaklaşım, verilerin `null` (hala yükleme) olup olmadığını izlemedir.</span><span class="sxs-lookup"><span data-stu-id="0be14-240">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="0be14-241">Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` .</span><span class="sxs-lookup"><span data-stu-id="0be14-241">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="0be14-242">`currentCount`Türe () bir soru işareti () ekleyerek null yapılabilir yapın `?` `int` :</span><span class="sxs-lookup"><span data-stu-id="0be14-242">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="0be14-243">Sayı ve **artış** düğmesini koşullu olarak görüntülemediğinizden, bu öğeleri yalnızca veriler yüklenmişse görüntülemeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="0be14-243">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="0be14-244">Tanıtıcı prerendering</span><span class="sxs-lookup"><span data-stu-id="0be14-244">Handle prerendering</span></span>

<span data-ttu-id="0be14-245">Prerendering sırasında:</span><span class="sxs-lookup"><span data-stu-id="0be14-245">During prerendering:</span></span>

* <span data-ttu-id="0be14-246">Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="0be14-246">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="0be14-247">Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="0be14-247">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="0be14-248">`localStorage`veya `sessionStorage` prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="0be14-248">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="0be14-249">Bileşen depolama ile etkileşim kurmayı denerse şuna benzer bir hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0be14-249">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="0be14-250">JavaScript birlikte çalışabilirlik çağrıları şu an için verilemez.</span><span class="sxs-lookup"><span data-stu-id="0be14-250">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="0be14-251">Bunun nedeni, bileşenin ön işlenmiş olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="0be14-251">This is because the component is being prerendered.</span></span>

<span data-ttu-id="0be14-252">Hatayı çözmek için bir yol prerendering devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="0be14-252">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="0be14-253">Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="0be14-253">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="0be14-254">Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0be14-254">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="0be14-255">Prerendering 'yi devre dışı bırakmak için, *Pages/_Host. cshtml* dosyasını açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) ' nı ile değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> .</span><span class="sxs-lookup"><span data-stu-id="0be14-255">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="0be14-256">Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="0be14-256">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="0be14-257">Prerendering etkin tutmak için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin.</span><span class="sxs-lookup"><span data-stu-id="0be14-257">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="0be14-258">Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0be14-258">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="0be14-259">Durum korumasını ortak bir konuma ayırın</span><span class="sxs-lookup"><span data-stu-id="0be14-259">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="0be14-260">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0be14-260">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="0be14-261">Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="0be14-261">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="0be14-262">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-262">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="0be14-263">Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri kalıcıdır:</span><span class="sxs-lookup"><span data-stu-id="0be14-263">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="0be14-264">`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="0be14-264">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="0be14-265">Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın.</span><span class="sxs-lookup"><span data-stu-id="0be14-265">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="0be14-266">Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşende (*app. Razor*) içine sarmalayın:</span><span class="sxs-lookup"><span data-stu-id="0be14-266">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="0be14-267">Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-267">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="0be14-268">Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:</span><span class="sxs-lookup"><span data-stu-id="0be14-268">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="0be14-269">Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0be14-269">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="0be14-270">Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="0be14-270">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="0be14-271">Ayrıntılar için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="0be14-271">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="0be14-272">Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:</span><span class="sxs-lookup"><span data-stu-id="0be14-272">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="0be14-273">Diğer birçok bileşenin durumunu kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="0be14-273">To consume state in many other components.</span></span>
* <span data-ttu-id="0be14-274">Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="0be14-274">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="0be14-275">Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, durumu küresel olarak yükleme ve kaydetme işlemlerini yapmaktan kaçınmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="0be14-275">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
