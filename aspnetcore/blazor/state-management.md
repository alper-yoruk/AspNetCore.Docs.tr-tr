---
title: ASP.NET Blazor Çekirdek devlet yönetimi
author: guardrex
description: Sunucu uygulamalarında Blazor durumu nasıl devam edersiniz öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218875"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="66a32-103">ASP.NET Blazor Çekirdek devlet yönetimi</span><span class="sxs-lookup"><span data-stu-id="66a32-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="66a32-104">Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="66a32-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="66a32-105">Sunucu, durum salkı bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="66a32-106">Çoğu zaman, uygulama sunucuya sürekli bir bağlantı tutar.</span><span class="sxs-lookup"><span data-stu-id="66a32-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="66a32-107">Kullanıcının durumu bir *devrede*sunucunun belleğinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="66a32-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="66a32-108">Bir kullanıcının devresi için tutulan duruma örnekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="66a32-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="66a32-109">İşlenen UI&mdash;bileşen örnekleri hiyerarşisi ve bunların en son işlenme çıktısı.</span><span class="sxs-lookup"><span data-stu-id="66a32-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="66a32-110">Bileşen örneklerindeki tüm alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="66a32-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="66a32-111">Bağımlı [enjeksiyon (DI)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan ve devreye giren veriler.</span><span class="sxs-lookup"><span data-stu-id="66a32-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="66a32-112">Bu makale, Sunucu uygulamalarındaki Blazor durum kalıcılığını gidermelerini giderer.</span><span class="sxs-lookup"><span data-stu-id="66a32-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="66a32-113">WebAssembly uygulamaları [tarayıcıda istemci tarafı durum kalıcılığından](#client-side-in-the-browser) yararlanabilir, ancak bu makalenin kapsamı dışında özel çözümler veya üçüncü taraf paketleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="66a32-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="66a32-114">Devre</span><span class="sxs-lookup"><span data-stu-id="66a32-114"> circuits</span></span>

<span data-ttu-id="66a32-115">Bir kullanıcı geçici bir ağ Blazor bağlantısı kaybı yla karşılanırsa, uygulamayı kullanmaya devam edebilmesi için kullanıcıyı özgün devresine yeniden bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="66a32-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="66a32-116">Ancak, bir kullanıcıyı sunucunun belleğindeki orijinal devresine yeniden bağlamak her zaman mümkün değildir:</span><span class="sxs-lookup"><span data-stu-id="66a32-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="66a32-117">Sunucu bağlantısı kesilen bir devreyi sonsuza kadar tutamaz.</span><span class="sxs-lookup"><span data-stu-id="66a32-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="66a32-118">Sunucu, bir zaman anından sonra veya sunucu bellek baskısı altındayken bağlantısı kesilmiş bir devre serbest bırakmalıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="66a32-119">Çok sunuculu, yük dengeli dağıtım ortamlarında, sunucu işleme istekleri herhangi bir zamanda kullanılamaz hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="66a32-120">Tek tek sunucular, isteklerin genel hacmini işlemek için artık gerek kıldığında başarısız olabilir veya otomatik olarak kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="66a32-121">Kullanıcı yeniden bağlanmaya çalıştığında özgün sunucu kullanılamayabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="66a32-122">Kullanıcı tarayıcısını kapatıp yeniden açabilir veya tarayıcının belleğinde tutulan tüm durumları kaldıran sayfayı yeniden yükleyebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="66a32-123">Örneğin, JavaScript interop aramaları aracılığıyla ayarlanan değerler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="66a32-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="66a32-124">Bir kullanıcı özgün devresine yeniden bağlanadığında, kullanıcı boş bir durumda yeni bir devre alır.</span><span class="sxs-lookup"><span data-stu-id="66a32-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="66a32-125">Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="66a32-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="66a32-126">Devreler arasında durumu koruma</span><span class="sxs-lookup"><span data-stu-id="66a32-126">Preserve state across circuits</span></span>

<span data-ttu-id="66a32-127">Bazı senaryolarda, devreler arasında durumu korumak arzu edilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="66a32-128">Bir uygulama, şu nedense, kullanıcı için önemli verileri saklayabilir:</span><span class="sxs-lookup"><span data-stu-id="66a32-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="66a32-129">Web sunucusu kullanılamaz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="66a32-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="66a32-130">Kullanıcının tarayıcısı yeni bir web sunucusu ile yeni bir devre başlatmak zorunda kalır.</span><span class="sxs-lookup"><span data-stu-id="66a32-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="66a32-131">Genel olarak, devreler arasında durumu korumak, kullanıcıların yalnızca zaten var olan verileri okumak için değil, etkin olarak veri oluşturduğu senaryolar için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="66a32-132">Durumu tek bir devrenin ötesinde korumak için, *verileri yalnızca sunucunun belleğinde depolamayın.*</span><span class="sxs-lookup"><span data-stu-id="66a32-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="66a32-133">Uygulama, verileri başka bir depolama konumuna kadar devam ettirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="66a32-134">Durum kalıcılığı otomatik&mdash;değildir, durumu durumlu veri kalıcılığını uygulamak için uygulamayı geliştirirken adımlar atmalısınız.</span><span class="sxs-lookup"><span data-stu-id="66a32-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="66a32-135">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturmak için çaba harcadığı yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="66a32-136">Aşağıdaki örneklerde, kalıcı durum ya zaman kazandırır ya da ticari faaliyetlerde yardımcı olur:</span><span class="sxs-lookup"><span data-stu-id="66a32-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="66a32-137">Çok adımlı &ndash; web formu Bir kullanıcının durumu kaybolursa, çok aşamalı bir işlemin tamamlanmış birkaç adımı için verileri yeniden girmek zaman alır.</span><span class="sxs-lookup"><span data-stu-id="66a32-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="66a32-138">Kullanıcı, çok aşamalı formdan uzaklaşıp daha sonra forma dönerse bu senaryodaki durumunu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="66a32-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="66a32-139">Alışveriş &ndash; sepeti Potansiyel geliri temsil eden bir uygulamanın ticari açıdan önemli herhangi bir bileşeni korunabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="66a32-140">Durumunu ve dolayısıyla alışveriş sepetini kaybeden bir kullanıcı, siteye daha sonra döndüklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="66a32-141">Genellikle, gönderilmemiş bir oturum açma iletişim kutusuna girilen kullanıcı adı gibi, kolayca yeniden oluşturulan durumu korumak gerekmez.</span><span class="sxs-lookup"><span data-stu-id="66a32-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="66a32-142">Bir uygulama yalnızca *uygulama durumunu*devam ettir.</span><span class="sxs-lookup"><span data-stu-id="66a32-142">An app can only persist *app state*.</span></span> <span data-ttu-id="66a32-143">UI'ler, bileşen örnekleri ve bunların oluşturma ağaçları gibi kalıcı olamaz.</span><span class="sxs-lookup"><span data-stu-id="66a32-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="66a32-144">Bileşenler ve render ağaçları genellikle serileştirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="66a32-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="66a32-145">Kullanıcı Arabirimi durumuna benzer bir şeyi (TreeView'in genişletilmiş düğümleri gibi) devam ettirebilmek için, uygulamanın davranışı serileştirilebilir uygulama durumu olarak modellemek için özel kodu olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="66a32-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="66a32-146">Nerede durum devam etmek</span><span class="sxs-lookup"><span data-stu-id="66a32-146">Where to persist state</span></span>

<span data-ttu-id="66a32-147">Bir Blazor Server uygulamasında kalıcı durum için üç ortak konum vardır.</span><span class="sxs-lookup"><span data-stu-id="66a32-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="66a32-148">Her yaklaşım en iyi farklı senaryolar için uygundur ve farklı uyarılar vardır:</span><span class="sxs-lookup"><span data-stu-id="66a32-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="66a32-149">Veritabanında sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="66a32-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="66a32-150">URL</span><span class="sxs-lookup"><span data-stu-id="66a32-150">URL</span></span>](#url)
* [<span data-ttu-id="66a32-151">Tarayıcıda istemci tarafı</span><span class="sxs-lookup"><span data-stu-id="66a32-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="66a32-152">Veritabanında sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="66a32-152">Server-side in a database</span></span>

<span data-ttu-id="66a32-153">Kalıcı veri kalıcılığı veya birden çok kullanıcı veya aygıta yayılması gereken veriler için, bağımsız bir sunucu tarafı veritabanı neredeyse kesinlikle en iyi seçimdir.</span><span class="sxs-lookup"><span data-stu-id="66a32-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="66a32-154">Seçeneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="66a32-154">Options include:</span></span>

* <span data-ttu-id="66a32-155">İlişkisel SQL veritabanı</span><span class="sxs-lookup"><span data-stu-id="66a32-155">Relational SQL database</span></span>
* <span data-ttu-id="66a32-156">Anahtar değeri deposu</span><span class="sxs-lookup"><span data-stu-id="66a32-156">Key-value store</span></span>
* <span data-ttu-id="66a32-157">Blob mağazası</span><span class="sxs-lookup"><span data-stu-id="66a32-157">Blob store</span></span>
* <span data-ttu-id="66a32-158">Tablo deposu</span><span class="sxs-lookup"><span data-stu-id="66a32-158">Table store</span></span>

<span data-ttu-id="66a32-159">Veriler veritabanına kaydedildikten sonra, yeni bir devre herhangi bir zamanda bir kullanıcı tarafından başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="66a32-160">Kullanıcının verileri korunur ve herhangi bir yeni devrede kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="66a32-161">Azure veri depolama seçenekleri hakkında daha fazla bilgi için [Azure Depolama Belgeleri](/azure/storage/) ve Azure [Veritabanları'na](https://azure.microsoft.com/product-categories/databases/)bakın.</span><span class="sxs-lookup"><span data-stu-id="66a32-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="66a32-162">URL'si</span><span class="sxs-lookup"><span data-stu-id="66a32-162">URL</span></span>

<span data-ttu-id="66a32-163">Gezinme durumunu temsil eden geçici veriler için, verileri URL'nin bir parçası olarak modellendirin.</span><span class="sxs-lookup"><span data-stu-id="66a32-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="66a32-164">URL'de modellenen durum örnekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="66a32-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="66a32-165">Görüntülenen varlığın kimliği.</span><span class="sxs-lookup"><span data-stu-id="66a32-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="66a32-166">Sayfalı kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="66a32-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="66a32-167">Tarayıcının adres çubuğunun içeriği korunur:</span><span class="sxs-lookup"><span data-stu-id="66a32-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="66a32-168">Kullanıcı sayfayı el ile yeniden yüklerse.</span><span class="sxs-lookup"><span data-stu-id="66a32-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="66a32-169">Web sunucusu kullanılamaz&mdash;hale gelirse, kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemek zorunda kalır.</span><span class="sxs-lookup"><span data-stu-id="66a32-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="66a32-170">`@page` Yönergeyle URL desenlerini tanımlama hakkında <xref:blazor/routing>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="66a32-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="66a32-171">Tarayıcıda istemci tarafı</span><span class="sxs-lookup"><span data-stu-id="66a32-171">Client-side in the browser</span></span>

<span data-ttu-id="66a32-172">Kullanıcının etkin olarak oluşturduğu geçici veriler için, tarayıcının `localStorage` ve `sessionStorage` koleksiyonların ortak bir destek deposudur.</span><span class="sxs-lookup"><span data-stu-id="66a32-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="66a32-173">Uygulamanın, devre terk edilmişse depolanan durumu yönetmesi veya temizlemesi gerekmez, bu da sunucu tarafındaki depolamaya göre bir avantajdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="66a32-174">Bu bölümdeki "istemci tarafı" tarayıcıdaki istemci tarafı senaryolarını ifade eder, [ Blazor WebAssembly barındırma modeline](xref:blazor/hosting-models#blazor-webassembly)değil.</span><span class="sxs-lookup"><span data-stu-id="66a32-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="66a32-175">`localStorage`ve `sessionStorage` WebAssembly Blazor uygulamalarında ancak özel kod yazarak veya üçüncü taraf paketi kullanılarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="66a32-176">`localStorage`ve `sessionStorage` aşağıdaki gibi farklıdır:</span><span class="sxs-lookup"><span data-stu-id="66a32-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="66a32-177">`localStorage`kullanıcının tarayıcısına kapsamlıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="66a32-178">Kullanıcı sayfayı yeniden yüklerse veya tarayıcıyı kapatıp yeniden açarsa, durum devam eder.</span><span class="sxs-lookup"><span data-stu-id="66a32-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="66a32-179">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="66a32-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="66a32-180">Veriler açıkça `localStorage` temizlenene kadar devam eder.</span><span class="sxs-lookup"><span data-stu-id="66a32-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="66a32-181">`sessionStorage`kullanıcının tarayıcı sekmesine göre dir. Kullanıcı sekmeyi yeniden yüklerse, durum devam eder.</span><span class="sxs-lookup"><span data-stu-id="66a32-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="66a32-182">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa, durum kaybolur.</span><span class="sxs-lookup"><span data-stu-id="66a32-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="66a32-183">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekme de verilerin kendi bağımsız sürümüne sahiptir.</span><span class="sxs-lookup"><span data-stu-id="66a32-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="66a32-184">Genellikle, `sessionStorage` kullanımı daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="66a32-185">`sessionStorage`bir kullanıcının birden çok sekme açması ve aşağıdakilerle karşılaşma sı riski nden kaçınır:</span><span class="sxs-lookup"><span data-stu-id="66a32-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="66a32-186">Sekmeler arasında devlet depolama hataları.</span><span class="sxs-lookup"><span data-stu-id="66a32-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="66a32-187">Sekme diğer sekmelerin durumunun üzerine yazdığında kafa karıştırıcı davranış.</span><span class="sxs-lookup"><span data-stu-id="66a32-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="66a32-188">`localStorage`uygulamanın tarayıcıyı kapatma ve yeniden açma durumunda durum devam etmesi gerekiyorsa daha iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="66a32-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="66a32-189">Tarayıcı depolama alanı kullanmak için uyarılar:</span><span class="sxs-lookup"><span data-stu-id="66a32-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="66a32-190">Sunucu tarafındaki veritabanıkullanımına benzer şekilde, veri yükleme ve kaydetme eşzamanlıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="66a32-191">Sunucu tarafındaki veritabanının aksine, istenen sayfa ön oluşturma aşamasında tarayıcıda bulunmadığından, ön işleme sırasında depolama alanı kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="66a32-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="66a32-192">Birkaç kilobaytlık verinin depolanması, Sunucu Blazor uygulamaları için kalıcı olması mantıklıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="66a32-193">Birkaç kilobayt ötesinde, veriler ağ üzerinden yüklendiğinden ve kaydedildiklerinden performans etkilerini göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="66a32-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="66a32-194">Kullanıcılar verileri görüntüleyebilir veya kurcalayabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="66a32-195">ASP.NET Çekirdek [Veri Koruması](xref:security/data-protection/introduction) riski azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="66a32-196">Üçüncü taraf tarayıcı depolama çözümleri</span><span class="sxs-lookup"><span data-stu-id="66a32-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="66a32-197">Üçüncü taraf NuGet paketleri ile `localStorage` çalışmak `sessionStorage`için API'ler sağlar ve.</span><span class="sxs-lookup"><span data-stu-id="66a32-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="66a32-198">Core'un [Veri Koruması'nı](xref:security/data-protection/introduction)saydam olarak kullanan bir paketi ASP.NET dikkate almaya değer.</span><span class="sxs-lookup"><span data-stu-id="66a32-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="66a32-199">ASP.NET Çekirdek Veri Koruması depolanan verileri şifreler ve depolanan verilerle oynama riskini azaltır.</span><span class="sxs-lookup"><span data-stu-id="66a32-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="66a32-200">JSON serileştirilmiş veriler düz metinolarak depolanırsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="66a32-201">Veriler doğada önemsiz olabileceğinden, verileri güvence altına almak her zaman sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="66a32-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="66a32-202">Örneğin, kullanıcı arabirimi öğesinin depolanan rengini okumak veya değiştirmek kullanıcı veya kuruluş için önemli bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="66a32-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="66a32-203">Kullanıcıların *hassas verileri*incelemesine veya kurcalanmasına izin vermekten kaçının.</span><span class="sxs-lookup"><span data-stu-id="66a32-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="66a32-204">Korumalı Tarayıcı Depolama deneysel paketi</span><span class="sxs-lookup"><span data-stu-id="66a32-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="66a32-205">[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)için `localStorage` `sessionStorage` [Veri Koruması](xref:security/data-protection/introduction) sağlayan nuget paketinin bir örneğidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="66a32-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`şu anda üretim için uygun olmayan desteklenmeyen bir deneysel pakettir.</span><span class="sxs-lookup"><span data-stu-id="66a32-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="66a32-207">Yükleme</span><span class="sxs-lookup"><span data-stu-id="66a32-207">Installation</span></span>

<span data-ttu-id="66a32-208">Paketi yüklemek `Microsoft.AspNetCore.ProtectedBrowserStorage` için:</span><span class="sxs-lookup"><span data-stu-id="66a32-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="66a32-209">Server Blazor uygulama projesinde, [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)bir paket başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="66a32-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="66a32-210">Üst düzey HTML'de (örneğin, varsayılan proje şablonundaki *Pages/_Host.cshtml* dosyasında) aşağıdaki `<script>` etiketi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="66a32-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="66a32-211">`Startup.ConfigureServices` Yöntemde, hizmet `AddProtectedBrowserStorage` koleksiyonuna ekleme `localStorage` çağrısı ve `sessionStorage` hizmetleri:</span><span class="sxs-lookup"><span data-stu-id="66a32-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="66a32-212">Bir bileşen içinde veri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="66a32-212">Save and load data within a component</span></span>

<span data-ttu-id="66a32-213">Tarayıcı depolamasına veri yüklemeyi veya kaydetmeyi [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) gerektiren herhangi bir bileşende, aşağıdakilerden birini enjekte etmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="66a32-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="66a32-214">Seçim, hangi destek mağazasını kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="66a32-215">Aşağıdaki örnekte `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="66a32-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="66a32-216">İfade, `@using` bileşen yerine *_Imports.razor* dosyasına yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="66a32-217">*_Imports.razor* dosyasının kullanımı, ad alanını uygulamanın daha büyük kesimleri veya uygulamanın tüm bölümleri için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="66a32-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="66a32-218">Proje `_currentCount` şablonunun `Counter` bileşenindeki değeri sürdürmek için, `IncrementCount` kullanılacak `ProtectedSessionStore.SetAsync`yöntemi değiştirin:</span><span class="sxs-lookup"><span data-stu-id="66a32-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="66a32-219">Daha büyük, daha gerçekçi uygulamalarda, tek tek alanların depolanması olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="66a32-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="66a32-220">Uygulamaların karmaşık durum içeren tüm model nesnelerini depolama olasılığı daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="66a32-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="66a32-221">`ProtectedSessionStore`json verilerini otomatik olarak seri hale eder ve deserialize eder.</span><span class="sxs-lookup"><span data-stu-id="66a32-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="66a32-222">Önceki kod örneğinde, `_currentCount` veriler kullanıcının `sessionStorage['count']` tarayıcısında olduğu gibi depolanır.</span><span class="sxs-lookup"><span data-stu-id="66a32-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="66a32-223">Veriler düz metinolarak depolanır, daha çok ASP.NET Core's [Data Protection](xref:security/data-protection/introduction)kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="66a32-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="66a32-224">Şifrelenmiş veriler tarayıcının `sessionStorage['count']` geliştirici konsolunda değerlendirilip değerlendirilip değerlendirildiği görülebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="66a32-225">Kullanıcı `_currentCount` `Counter` daha sonra bileşene dönerse (tamamen yeni bir devredeyse dahil) verileri `ProtectedSessionStore.GetAsync`kurtarmak için şunları kullanın:</span><span class="sxs-lookup"><span data-stu-id="66a32-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="66a32-226">Bileşenin parametreleri navigasyon durumunu içeriyorsa, sonucu `OnParametersSetAsync`değil, `OnInitializedAsync`' da çağırın `ProtectedSessionStore.GetAsync` ve atayın.</span><span class="sxs-lookup"><span data-stu-id="66a32-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="66a32-227">`OnInitializedAsync`bileşen ilk anlık olduğunda yalnızca bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="66a32-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="66a32-228">`OnInitializedAsync`kullanıcı aynı sayfada kalırken farklı bir URL'ye giderse daha sonra tekrar çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="66a32-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="66a32-229">Daha fazla bilgi için bkz. <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="66a32-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="66a32-230">Bu bölümdeki örnekler yalnızca sunucu önceden oluşturma etkin değilse çalışır.</span><span class="sxs-lookup"><span data-stu-id="66a32-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="66a32-231">Ön işleme etkin olduğunda, şuna benzer bir hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="66a32-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="66a32-232">JavaScript interop aramaları şu anda düzenlenemez.</span><span class="sxs-lookup"><span data-stu-id="66a32-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="66a32-233">Bunun nedeni, bileşenin önceden işlenmiş olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="66a32-234">Ön oluşturmayı devre dışı kılabilir veya ön oluşturmayla çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="66a32-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="66a32-235">Ön oluşturmayla çalışan kod yazma hakkında daha fazla bilgi edinmek için [Tutvet ön oluşturma](#handle-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="66a32-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="66a32-236">Yükleme durumunu işleme</span><span class="sxs-lookup"><span data-stu-id="66a32-236">Handle the loading state</span></span>

<span data-ttu-id="66a32-237">Tarayıcı depolama asynchronous (bir ağ bağlantısı üzerinden erişilen) olduğundan, verilerin yüklenmesi ve bir bileşen tarafından kullanılabilir önce her zaman bir süre vardır.</span><span class="sxs-lookup"><span data-stu-id="66a32-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="66a32-238">En iyi sonuçlar için, boş veya varsayılan verileri görüntülemek yerine yükleme devam ederken bir yükleme durumu iletisi oluşturma.</span><span class="sxs-lookup"><span data-stu-id="66a32-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="66a32-239">Bir yaklaşım, verilerin `null` (hala yüklenip yüklenmediğini) izlemektir.</span><span class="sxs-lookup"><span data-stu-id="66a32-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="66a32-240">Varsayılan `Counter` bileşende, sayım bir `int`.</span><span class="sxs-lookup"><span data-stu-id="66a32-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="66a32-241">Türe soru işareti ( `_currentCount` `?`) ekleyerek nullable olun (`int`):</span><span class="sxs-lookup"><span data-stu-id="66a32-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="66a32-242">Sayım ve **Artış** düğmesini koşulsuz görüntülemek yerine, bu öğeleri yalnızca veriler yüklendiğinde görüntülemeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="66a32-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="66a32-243">Ön işleme yi işle</span><span class="sxs-lookup"><span data-stu-id="66a32-243">Handle prerendering</span></span>

<span data-ttu-id="66a32-244">Ön işleme sırasında:</span><span class="sxs-lookup"><span data-stu-id="66a32-244">During prerendering:</span></span>

* <span data-ttu-id="66a32-245">Kullanıcının tarayıcısına etkileşimli bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="66a32-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="66a32-246">Tarayıcıda henüz JavaScript kodunu çalıştırabileceği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="66a32-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="66a32-247">`localStorage`veya `sessionStorage` ön işleme sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="66a32-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="66a32-248">Bileşen depolama yla etkileşime girmezse, aşağıdakilere benzer bir hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="66a32-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="66a32-249">JavaScript interop aramaları şu anda düzenlenemez.</span><span class="sxs-lookup"><span data-stu-id="66a32-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="66a32-250">Bunun nedeni, bileşenin önceden işlenmiş olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="66a32-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="66a32-251">Hatayı gidermenin bir yolu, ön oluşturmayı devre dışı bilebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="66a32-252">Uygulama tarayıcı tabanlı depolamayı yoğun olarak kullanıyorsa, bu genellikle en iyi seçimdir.</span><span class="sxs-lookup"><span data-stu-id="66a32-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="66a32-253">Ön oluşturma karmaşıklık ekler ve uygulama kullanılabilir olana kadar `localStorage` `sessionStorage` yararlı bir içeriği önceden işleyemediği için uygulamadan yarar lanamaz.</span><span class="sxs-lookup"><span data-stu-id="66a32-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="66a32-254">Ön oluşturmayı devre dışı kardırmak için `render-mode` *Pages/_Host.cshtml* dosyasını açTırım ve Bileşen Etiket <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>Yardımcısını ' nın ' ı ' nı değiştir [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="66a32-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="66a32-255">Ön oluşturma, kullanmayan `localStorage` veya `sessionStorage`başka sayfalar için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="66a32-256">Ön işlemeyi etkin tutmak için, yükleme işlemini tarayıcı devreye bağlanana kadar erteleyin.</span><span class="sxs-lookup"><span data-stu-id="66a32-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="66a32-257">Aşağıdaki sayaç değeri depolamak için bir örnektir:</span><span class="sxs-lookup"><span data-stu-id="66a32-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="66a32-258">Devlet korumasını ortak bir konuma faktör haline getirmek</span><span class="sxs-lookup"><span data-stu-id="66a32-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="66a32-259">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcı kodunu birçok kez yeniden uygulamak kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="66a32-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="66a32-260">Kod yinelemesini önlemek için bir seçenek, devlet sağlayıcı mantığı kapsülleyen bir *durum sağlayıcı üst bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="66a32-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="66a32-261">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="66a32-262">Aşağıdaki `CounterStateProvider` bileşen örneğinde, sayaç verileri kalıcıdır:</span><span class="sxs-lookup"><span data-stu-id="66a32-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
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
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="66a32-263">Bileşen, `CounterStateProvider` yükleme tamamlanana kadar alt içeriğini oluşturmayarak yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="66a32-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="66a32-264">`CounterStateProvider` Bileşeni kullanmak için, karşı duruma erişim gerektiren başka bir bileşenin etrafında bileşenin bir örneğini sarın.</span><span class="sxs-lookup"><span data-stu-id="66a32-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="66a32-265">Durumu bir uygulamadaki tüm bileşenler için erişilebilir `CounterStateProvider` hale `Router` getirmek `App` için, bileşeni bileşenin etrafına sarın *(App.razor):*</span><span class="sxs-lookup"><span data-stu-id="66a32-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="66a32-266">Sarılmış bileşenler kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="66a32-267">Aşağıdaki `Counter` bileşen deseni uygular:</span><span class="sxs-lookup"><span data-stu-id="66a32-267">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="66a32-268">Önceki bileşen ile `ProtectedBrowserStorage`etkileşim için gerekli değildir , ne de bir "yükleme" aşaması ile anlaşma yok.</span><span class="sxs-lookup"><span data-stu-id="66a32-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="66a32-269">Daha önce açıklandığı gibi ön `CounterStateProvider` işleme ile başa çıkmak için, sayaç verilerini tüketen tüm bileşenlerin otomatik olarak önişleme ile çalışması için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="66a32-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="66a32-270">Ayrıntılar için [Tutamaç ön oluşturma](#handle-prerendering) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="66a32-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="66a32-271">Genel olarak, *durum sağlayıcı üst bileşen* deseni önerilir:</span><span class="sxs-lookup"><span data-stu-id="66a32-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="66a32-272">Diğer birçok bileşende durum tüketmek için.</span><span class="sxs-lookup"><span data-stu-id="66a32-272">To consume state in many other components.</span></span>
* <span data-ttu-id="66a32-273">Eğer devam etmek için sadece bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="66a32-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="66a32-274">Birçok farklı durum nesnesini devam ettirmek ve farklı yerlerdefarklı nesne alt kümelerini tüketmek için, genel olarak devletin yüklenmesi ve kaydedilmesinden kaçınmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="66a32-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
