---
<span data-ttu-id="2e477-101">Başlık: ' ASP.NET Core Blazor Durum Yönetimi ' Yazar: Açıklama: ' sunucu uygulamalarında durumu kalıcı hale getirme hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="2e477-101">title: 'ASP.NET Core Blazor state management' author: description: 'Learn how to persist state in Blazor Server apps.'</span></span>
<span data-ttu-id="2e477-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2e477-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e477-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e477-103">'Blazor'</span></span>
- <span data-ttu-id="2e477-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e477-104">'Identity'</span></span>
- <span data-ttu-id="2e477-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e477-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e477-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e477-106">'Razor'</span></span>
- <span data-ttu-id="2e477-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2e477-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="2e477-108">ASP.NET Core Blazor durum yönetimi</span><span class="sxs-lookup"><span data-stu-id="2e477-108">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="2e477-109">[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="2e477-109">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

Blazor<span data-ttu-id="2e477-110">Sunucu, durum bilgisi olan bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="2e477-110"> Server is a stateful app framework.</span></span> <span data-ttu-id="2e477-111">Çoğu zaman, uygulama sunucuya devam eden bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e477-111">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="2e477-112">Kullanıcının durumu, sunucu belleğinde bir *devrende*tutulur.</span><span class="sxs-lookup"><span data-stu-id="2e477-112">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="2e477-113">Bir kullanıcının devresi için durum tutulan örnekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2e477-113">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="2e477-114">İşlenmiş Kullanıcı arabirimi: bileşen örneklerinin hiyerarşisi ve en son işleme çıktısı.</span><span class="sxs-lookup"><span data-stu-id="2e477-114">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="2e477-115">Bileşen örneklerinde alanların ve özelliklerin değerleri.</span><span class="sxs-lookup"><span data-stu-id="2e477-115">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="2e477-116">Devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veriler.</span><span class="sxs-lookup"><span data-stu-id="2e477-116">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="2e477-117">Bu makale, sunucu uygulamalarında durum kalıcılığını ele alınmaktadır Blazor .</span><span class="sxs-lookup"><span data-stu-id="2e477-117">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="2e477-118">WebAssembly uygulamaları [Tarayıcıda istemci tarafı durum kalıcılığından](#client-side-in-the-browser) yararlanabilir, ancak bu makalenin kapsamı dışında özel çözümler veya üçüncü taraf paketleri gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-118"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a>Blazor<span data-ttu-id="2e477-119">uygulanıp</span><span class="sxs-lookup"><span data-stu-id="2e477-119"> circuits</span></span>

<span data-ttu-id="2e477-120">Bir Kullanıcı geçici bir ağ bağlantısı kaybıyla karşılaşıyorsa, Blazor uygulamayı kullanmaya devam edebilmek için kullanıcıyı özgün devresine yeniden bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2e477-120">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="2e477-121">Ancak, bir kullanıcıyı sunucunun belleğindeki özgün devresine yeniden bağlamak her zaman mümkün değildir:</span><span class="sxs-lookup"><span data-stu-id="2e477-121">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="2e477-122">Sunucu, bağlantısı kesilen bir devreni süresiz olarak sürdüremez.</span><span class="sxs-lookup"><span data-stu-id="2e477-122">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="2e477-123">Sunucu, bir zaman aşımından sonra veya sunucu bellek baskısı altında olduğunda, bağlantısı kesilen bir bağlantı hattını serbest bırakmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e477-123">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="2e477-124">Çoklu sunucu, yük dengeli dağıtım ortamlarında, herhangi bir zamanda herhangi bir sunucu işleme isteği kullanılamaz hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-124">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="2e477-125">Tek tek sunucular, tüm istek hacmini işlemek için artık gerekli olmadığında veya otomatik olarak kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-125">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="2e477-126">Kullanıcı yeniden bağlanmaya çalıştığında, özgün sunucu kullanılamayabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-126">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="2e477-127">Kullanıcı tarayıcıyı kapatabilir ve yeniden açabilir veya sayfayı yeniden yükleyerek tarayıcı belleğinde tutulan tüm durumları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2e477-127">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="2e477-128">Örneğin, JavaScript birlikte çalışabilirlik çağrıları aracılığıyla ayarlanan değerler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="2e477-128">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="2e477-129">Kullanıcı özgün devresine yeniden bağlanalınmayacaksa, Kullanıcı boş duruma sahip yeni bir devre alır.</span><span class="sxs-lookup"><span data-stu-id="2e477-129">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="2e477-130">Bu, bir masaüstü uygulamasını kapatıp yeniden açmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="2e477-130">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="2e477-131">Devreler arasında durumu koru</span><span class="sxs-lookup"><span data-stu-id="2e477-131">Preserve state across circuits</span></span>

<span data-ttu-id="2e477-132">Bazı senaryolarda, devre genelinde durum koruma istenebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-132">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="2e477-133">Bir uygulama, şu durumlarda bir kullanıcı için önemli verileri koruyabilir:</span><span class="sxs-lookup"><span data-stu-id="2e477-133">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="2e477-134">Web sunucusu kullanılamaz hale gelir.</span><span class="sxs-lookup"><span data-stu-id="2e477-134">The web server becomes unavailable.</span></span>
* <span data-ttu-id="2e477-135">Kullanıcının tarayıcısı yeni bir Web sunucusuyla yeni bir devre başlatmaya zorlanır.</span><span class="sxs-lookup"><span data-stu-id="2e477-135">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="2e477-136">Genel olarak, devrelerde durumu korumak, kullanıcıların zaten var olan verileri okurken değil, etkin bir şekilde veri oluşturmakta olduğu senaryolar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2e477-136">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="2e477-137">Tek bir devrenin ötesinde durumu korumak için, *verileri yalnızca sunucunun belleğine depolamayın*.</span><span class="sxs-lookup"><span data-stu-id="2e477-137">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="2e477-138">Uygulama, verileri başka bir depolama konumuna kalıcı hale vermelidir.</span><span class="sxs-lookup"><span data-stu-id="2e477-138">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="2e477-139">Durum kalıcılığı otomatik değildir.</span><span class="sxs-lookup"><span data-stu-id="2e477-139">State persistence isn't automatic.</span></span> <span data-ttu-id="2e477-140">Durum bilgisi olan veri kalıcılığını uygulamak üzere uygulamayı geliştirirken adımları uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e477-140">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="2e477-141">Veri kalıcılığı genellikle yalnızca kullanıcıların oluşturma çabasında olduğu yüksek değerli durum için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2e477-141">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="2e477-142">Aşağıdaki örneklerde, kalıcı durum ticari etkinliklerdeki zaman veya yardımlarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="2e477-142">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="2e477-143">Çok adımlı WebForm: bir kullanıcının, durumları kaybedilmişse, çok adımlı bir işlemin birkaç tamamlanmış adımı için verileri yeniden girmesi zaman alır.</span><span class="sxs-lookup"><span data-stu-id="2e477-143">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="2e477-144">Kullanıcı, çok adımlı formdan uzaklaştıklarında ve daha sonra forma geri döndüğünüzde bu senaryodaki durumu kaybeder.</span><span class="sxs-lookup"><span data-stu-id="2e477-144">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="2e477-145">Alışveriş sepeti: potansiyel geliri temsil eden, bir uygulamanın ticari olarak önemli bir bileşeni korunabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-145">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="2e477-146">Durumlarını kaybettikleri bir Kullanıcı ve bu nedenle alışveriş sepeti, siteye daha sonra geri döntiklerinde daha az ürün veya hizmet satın alabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-146">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="2e477-147">Genellikle, gönderilmemiş bir oturum açma iletişim kutusuna girilen Kullanıcı adı gibi, kolayca yeniden oluşturulmuş durumu korumak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2e477-147">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2e477-148">Uygulama, *uygulama durumunu*yalnızca kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="2e477-148">An app can only persist *app state*.</span></span> <span data-ttu-id="2e477-149">Usıs, bileşen örnekleri ve bunların işleme ağaçları gibi kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-149">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="2e477-150">Bileşenler ve işleme ağaçları genellikle seri hale getirilebilir değildir.</span><span class="sxs-lookup"><span data-stu-id="2e477-150">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="2e477-151">Bir TreeView 'un genişletilmiş düğümleri gibi kullanıcı arabirimi durumuna benzer bir şeyi sürdürmek için, uygulamanın davranışı seri hale getirilebilir uygulama durumu olarak modelleyeceği özel kodu olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e477-151">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="2e477-152">Durumun nerede kalıcı olduğu</span><span class="sxs-lookup"><span data-stu-id="2e477-152">Where to persist state</span></span>

<span data-ttu-id="2e477-153">Sunucu uygulamasındaki kalıcı durum için üç ortak konum vardır Blazor .</span><span class="sxs-lookup"><span data-stu-id="2e477-153">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="2e477-154">Her yaklaşım farklı senaryolara en iyi şekilde uygundur ve farklı uyarılar içerir:</span><span class="sxs-lookup"><span data-stu-id="2e477-154">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="2e477-155">Veritabanında sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="2e477-155">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="2e477-156">URL</span><span class="sxs-lookup"><span data-stu-id="2e477-156">URL</span></span>](#url)
* [<span data-ttu-id="2e477-157">Tarayıcıda istemci tarafı</span><span class="sxs-lookup"><span data-stu-id="2e477-157">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="2e477-158">Veritabanında sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="2e477-158">Server-side in a database</span></span>

<span data-ttu-id="2e477-159">Kalıcı veri kalıcılığı veya birden çok kullanıcı veya cihaza yayılması gereken veriler için, bağımsız bir sunucu tarafı veritabanı neredeyse en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="2e477-159">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="2e477-160">Seçeneklere şunlar dahildir:</span><span class="sxs-lookup"><span data-stu-id="2e477-160">Options include:</span></span>

* <span data-ttu-id="2e477-161">İlişkisel SQL veritabanı</span><span class="sxs-lookup"><span data-stu-id="2e477-161">Relational SQL database</span></span>
* <span data-ttu-id="2e477-162">Anahtar değeri deposu</span><span class="sxs-lookup"><span data-stu-id="2e477-162">Key-value store</span></span>
* <span data-ttu-id="2e477-163">Blob deposu</span><span class="sxs-lookup"><span data-stu-id="2e477-163">Blob store</span></span>
* <span data-ttu-id="2e477-164">Tablo deposu</span><span class="sxs-lookup"><span data-stu-id="2e477-164">Table store</span></span>

<span data-ttu-id="2e477-165">Veriler veritabanına kaydedildikten sonra, bir kullanıcı tarafından herhangi bir zamanda yeni bir devre başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-165">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="2e477-166">Kullanıcının verileri korunur ve yeni bir devrede kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-166">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="2e477-167">Azure veri depolama seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama belgeleri](/azure/storage/) ve [Azure veritabanları](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="2e477-167">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="2e477-168">URL</span><span class="sxs-lookup"><span data-stu-id="2e477-168">URL</span></span>

<span data-ttu-id="2e477-169">Gezinti durumunu temsil eden geçici veriler için, verileri URL 'nin bir parçası olarak modelleyin.</span><span class="sxs-lookup"><span data-stu-id="2e477-169">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="2e477-170">URL 'de modellenen durum örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="2e477-170">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="2e477-171">Görüntülenen varlığın KIMLIĞI.</span><span class="sxs-lookup"><span data-stu-id="2e477-171">The ID of a viewed entity.</span></span>
* <span data-ttu-id="2e477-172">Disk belleğine alınmış bir kılavuzdaki geçerli sayfa numarası.</span><span class="sxs-lookup"><span data-stu-id="2e477-172">The current page number in a paged grid.</span></span>

<span data-ttu-id="2e477-173">Tarayıcının adres çubuğunun içeriği korunur:</span><span class="sxs-lookup"><span data-stu-id="2e477-173">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="2e477-174">Kullanıcı sayfayı el ile yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="2e477-174">If the user manually reloads the page.</span></span>
* <span data-ttu-id="2e477-175">Web sunucusu kullanılamaz hale gelirse ve Kullanıcı farklı bir sunucuya bağlanmak için sayfayı yeniden yüklemeye zorlanır.</span><span class="sxs-lookup"><span data-stu-id="2e477-175">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="2e477-176">Yönergeyle URL desenleri tanımlama hakkında bilgi için `@page` bkz <xref:blazor/routing> ..</span><span class="sxs-lookup"><span data-stu-id="2e477-176">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="2e477-177">Tarayıcıda istemci tarafı</span><span class="sxs-lookup"><span data-stu-id="2e477-177">Client-side in the browser</span></span>

<span data-ttu-id="2e477-178">Kullanıcının etkin şekilde oluşturmakta olduğu geçici veriler için, yaygın bir yedekleme deposu tarayıcının `localStorage` ve `sessionStorage` koleksiyonlarıdır.</span><span class="sxs-lookup"><span data-stu-id="2e477-178">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="2e477-179">Devre dışı bırakılırsa, sunucu tarafı depolama alanının avantajlarından yararlanan uygulama, saklı durumu yönetmek veya temizlemek için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2e477-179">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="2e477-180">Bu bölümdeki "istemci tarafı", [ Blazor webassembly barındırma modelinde](xref:blazor/hosting-models#blazor-webassembly)değil, tarayıcıdaki istemci tarafı senaryolarına başvurur.</span><span class="sxs-lookup"><span data-stu-id="2e477-180">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="2e477-181">`localStorage`ve `sessionStorage` Blazor yalnızca özel kod yazarak veya 3. taraf paketini kullanarak webassembly uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-181">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="2e477-182">`localStorage`ve `sessionStorage` aşağıdaki gibi farklılık gösterir:</span><span class="sxs-lookup"><span data-stu-id="2e477-182">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="2e477-183">`localStorage`, kullanıcının tarayıcısına kapsamlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2e477-183">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="2e477-184">Kullanıcı sayfayı yeniden yüklediğinde veya tarayıcıyı kapatıp yeniden açarsa durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="2e477-184">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="2e477-185">Kullanıcı birden çok tarayıcı sekmesi açarsa, durum sekmeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="2e477-185">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="2e477-186">Veriler `localStorage` Açık olarak temizlenene kadar içinde devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="2e477-186">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="2e477-187">`sessionStorage`kullanıcının tarayıcı sekmesi kapsamıdır. Kullanıcı sekmeyi yeniden yüklediğinde durum devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="2e477-187">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="2e477-188">Kullanıcı sekmeyi veya tarayıcıyı kapatırsa durum kaybedilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-188">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="2e477-189">Kullanıcı birden çok tarayıcı sekmesi açarsa, her sekmenin kendi bağımsız bir veri sürümü vardır.</span><span class="sxs-lookup"><span data-stu-id="2e477-189">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="2e477-190">Genellikle, `sessionStorage` kullanmak daha güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="2e477-190">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="2e477-191">`sessionStorage`bir kullanıcının birden çok sekme açmasını ve aşağıdaki gibi karşılaştığı riskleri önler:</span><span class="sxs-lookup"><span data-stu-id="2e477-191">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="2e477-192">Sekmelerde durum depolamadaki hatalar.</span><span class="sxs-lookup"><span data-stu-id="2e477-192">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="2e477-193">Sekme diğer sekmelerin durumunun üzerine yazdığınızda kafa karıştırıcı davranışı.</span><span class="sxs-lookup"><span data-stu-id="2e477-193">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="2e477-194">`localStorage`uygulamanın kapatma ve tarayıcıyı yeniden açma genelinde durumu kalıcı hale getirilmesi gerekiyorsa, daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="2e477-194">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="2e477-195">Tarayıcı depolamayı kullanmaya yönelik uyarılar:</span><span class="sxs-lookup"><span data-stu-id="2e477-195">Caveats for using browser storage:</span></span>

* <span data-ttu-id="2e477-196">Sunucu tarafı veritabanının kullanımına benzer şekilde veri yükleme ve kaydetme zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="2e477-196">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="2e477-197">Sunucu tarafı veritabanının aksine, istenen sayfa prerendering aşamasında tarayıcıda bulunmadığından, depolama alanı prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2e477-197">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="2e477-198">Birkaç kilobayt veri depolama alanı sunucu uygulamalarında kalıcı hale getiriyoruz Blazor .</span><span class="sxs-lookup"><span data-stu-id="2e477-198">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="2e477-199">Birkaç kilobayt dışında, veriler ağ üzerinden yüklenip kaydedildiğinden performans etkilerini göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e477-199">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="2e477-200">Kullanıcılar verileri görüntüleyebilir veya bunlarla karşılaşabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-200">Users may view or tamper with the data.</span></span> <span data-ttu-id="2e477-201">ASP.NET Core [veri koruma](xref:security/data-protection/introduction) riski azaltabilirler.</span><span class="sxs-lookup"><span data-stu-id="2e477-201">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="2e477-202">Üçüncü taraf tarayıcı depolama çözümleri</span><span class="sxs-lookup"><span data-stu-id="2e477-202">Third-party browser storage solutions</span></span>

<span data-ttu-id="2e477-203">Üçüncü taraf NuGet paketleri ve ile çalışmaya yönelik API 'Ler `localStorage` sağlar `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2e477-203">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="2e477-204">ASP.NET Core [veri korumasını](xref:security/data-protection/introduction)saydam olarak kullanan bir paket seçmeyi düşünülüyor.</span><span class="sxs-lookup"><span data-stu-id="2e477-204">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2e477-205">ASP.NET Core veri koruma, depolanan verileri şifreler ve depolanan verilerle yapılan değişikliklere karşı olası riskleri azaltır.</span><span class="sxs-lookup"><span data-stu-id="2e477-205">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="2e477-206">JSON seri hale getirilmiş veriler düz metin halinde depolanıyorsa, kullanıcılar tarayıcı geliştirici araçlarını kullanarak verileri görebilir ve depolanan verileri de değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-206">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="2e477-207">Verilerin güvenliğini sağlamak her zaman bir sorun değildir çünkü veriler önemsiz olarak olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-207">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="2e477-208">Örneğin, bir kullanıcı ARABIRIMI öğesinin saklı rengini okumak veya değiştirmek, Kullanıcı veya kuruluş için önemli bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="2e477-208">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="2e477-209">Kullanıcıların *hassas verileri*incelemesine veya değiştirmesine izin vermeyi önleyin.</span><span class="sxs-lookup"><span data-stu-id="2e477-209">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="2e477-210">Korumalı tarayıcı depolaması deneysel paket</span><span class="sxs-lookup"><span data-stu-id="2e477-210">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="2e477-211">[Data Protection](xref:security/data-protection/introduction) `localStorage` `sessionStorage` [Microsoft. Aspnetcore. Protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)ve için veri koruması sağlayan bir NuGet paketi örneği.</span><span class="sxs-lookup"><span data-stu-id="2e477-211">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="2e477-212">`Microsoft.AspNetCore.ProtectedBrowserStorage`, şu anda üretim kullanımı için uygun olmayan, desteklenmeyen bir deneysel paket.</span><span class="sxs-lookup"><span data-stu-id="2e477-212">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="2e477-213">Yükleme</span><span class="sxs-lookup"><span data-stu-id="2e477-213">Installation</span></span>

<span data-ttu-id="2e477-214">Paketi yüklemek için `Microsoft.AspNetCore.ProtectedBrowserStorage` :</span><span class="sxs-lookup"><span data-stu-id="2e477-214">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="2e477-215">BlazorSunucu uygulaması projesinde, [Microsoft. aspnetcore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e477-215">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="2e477-216">Üst düzey HTML 'de (örneğin, varsayılan Proje şablonundaki *Pages/_Host. cshtml* dosyasında) aşağıdaki `<script>` etiketi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2e477-216">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="2e477-217">`Startup.ConfigureServices`Yönteminde, `AddProtectedBrowserStorage` `localStorage` hizmet koleksiyonuna Ekle ve hizmetler ' i çağırın `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="2e477-217">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="2e477-218">Bir bileşen içindeki verileri kaydetme ve yükleme</span><span class="sxs-lookup"><span data-stu-id="2e477-218">Save and load data within a component</span></span>

<span data-ttu-id="2e477-219">Tarayıcı depolamaya veri yüklemeyi veya kaydetmeyi gerektiren herhangi bir bileşende, [`@inject`](xref:mvc/views/razor#inject) aşağıdakilerden birinin bir örneğini eklemek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="2e477-219">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="2e477-220">Seçim, hangi yedekleme deposunu kullanmak istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2e477-220">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="2e477-221">Aşağıdaki örnekte, `sessionStorage` kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2e477-221">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="2e477-222">`@using`İfade, bileşen yerine bir *_Imports. Razor* dosyasına yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-222">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="2e477-223">*_Imports. Razor* dosyası kullanımı, ad alanını uygulamanın daha büyük kesimlerine veya uygulamanın tamamına kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="2e477-223">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="2e477-224">`currentCount`Proje şablonunun bileşenindeki değeri kalıcı hale getirmek için `Counter` , `IncrementCount` kullanmak üzere yöntemi değiştirin `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="2e477-224">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="2e477-225">Daha büyük, daha gerçekçi uygulamalar, tek tek alanların depolanması ise olası bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="2e477-225">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="2e477-226">Uygulamalar karmaşık durum içeren tüm model nesnelerini depolamaya daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="2e477-226">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="2e477-227">`ProtectedSessionStore`JSON verilerini otomatik olarak serileştirir ve seri hale getirir.</span><span class="sxs-lookup"><span data-stu-id="2e477-227">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="2e477-228">Yukarıdaki kod örneğinde, `currentCount` veriler `sessionStorage['count']` kullanıcının tarayıcısında olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e477-228">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="2e477-229">Veriler düz metin biçiminde depolanmaz, bunun yerine ASP.NET Core [veri koruma](xref:security/data-protection/introduction)kullanılarak korunur.</span><span class="sxs-lookup"><span data-stu-id="2e477-229">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2e477-230">Şifrelenmiş veriler, `sessionStorage['count']` tarayıcının geliştirici konsolunda değerlendirildiğinde görülebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-230">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="2e477-231">`currentCount`Kullanıcı daha sonra bileşene geri dönerse verileri kurtarmak için `Counter` (tamamen yeni bir devrede olanlar dahil), şunu kullanın `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="2e477-231">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="2e477-232">Bileşenin parametreleri gezinti durumu içeriyorsa, `ProtectedSessionStore.GetAsync` sonucunu çağırın ve ' de atayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="2e477-232">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="2e477-233"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Yalnızca bileşenin ilk örneği oluşturulduğunda bir kez çağırılır.</span><span class="sxs-lookup"><span data-stu-id="2e477-233"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="2e477-234"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Kullanıcı aynı sayfada kaldığında farklı bir URL 'ye gittiğinde daha sonra yeniden çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="2e477-234"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="2e477-235">Daha fazla bilgi için bkz. <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="2e477-235">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="2e477-236">Bu bölümdeki örnekler yalnızca sunucuda prerendering etkinleştirilmemişse çalışır.</span><span class="sxs-lookup"><span data-stu-id="2e477-236">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="2e477-237">Prerendering etkinken şuna benzer bir hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2e477-237">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="2e477-238">JavaScript birlikte çalışabilirlik çağrıları şu an için verilemez.</span><span class="sxs-lookup"><span data-stu-id="2e477-238">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="2e477-239">Bunun nedeni, bileşenin ön işlenmiş olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="2e477-239">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="2e477-240">Prerendering 'ı devre dışı bırakın ya da prerendering ile çalışmak için ek kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e477-240">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="2e477-241">Prerendering ile birlikte çalışarak kodu yazma hakkında daha fazla bilgi için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="2e477-241">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="2e477-242">Yükleme durumunu işle</span><span class="sxs-lookup"><span data-stu-id="2e477-242">Handle the loading state</span></span>

<span data-ttu-id="2e477-243">Tarayıcı depolaması zaman uyumsuz olduğundan (bir ağ bağlantısı üzerinden erişilir), veriler yüklenmeden ve bir bileşen tarafından kullanıma sunulmadan önce her zaman bir zaman dilimi vardır.</span><span class="sxs-lookup"><span data-stu-id="2e477-243">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="2e477-244">En iyi sonuçlar için, yükleme sırasında, boş veya varsayılan verileri görüntülemek yerine bir yükleme durumu iletisi işleme devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="2e477-244">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="2e477-245">Bir yaklaşım, verilerin `null` (hala yükleme) olup olmadığını izlemedir.</span><span class="sxs-lookup"><span data-stu-id="2e477-245">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="2e477-246">Varsayılan `Counter` bileşende, sayı bir içinde tutulur `int` .</span><span class="sxs-lookup"><span data-stu-id="2e477-246">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="2e477-247">`currentCount`Türe () bir soru işareti () ekleyerek null yapılabilir yapın `?` `int` :</span><span class="sxs-lookup"><span data-stu-id="2e477-247">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="2e477-248">Sayı ve **artış** düğmesini koşullu olarak görüntülemediğinizden, bu öğeleri yalnızca veriler yüklenmişse görüntülemeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="2e477-248">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="2e477-249">Tanıtıcı prerendering</span><span class="sxs-lookup"><span data-stu-id="2e477-249">Handle prerendering</span></span>

<span data-ttu-id="2e477-250">Prerendering sırasında:</span><span class="sxs-lookup"><span data-stu-id="2e477-250">During prerendering:</span></span>

* <span data-ttu-id="2e477-251">Kullanıcının tarayıcısına etkileşimli bir bağlantı yok.</span><span class="sxs-lookup"><span data-stu-id="2e477-251">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="2e477-252">Tarayıcıda, JavaScript kodunu çalıştırabildiği bir sayfa yok.</span><span class="sxs-lookup"><span data-stu-id="2e477-252">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="2e477-253">`localStorage`veya `sessionStorage` prerendering sırasında kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2e477-253">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="2e477-254">Bileşen depolama ile etkileşim kurmayı denerse şuna benzer bir hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2e477-254">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="2e477-255">JavaScript birlikte çalışabilirlik çağrıları şu an için verilemez.</span><span class="sxs-lookup"><span data-stu-id="2e477-255">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="2e477-256">Bunun nedeni, bileşenin ön işlenmiş olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="2e477-256">This is because the component is being prerendered.</span></span>

<span data-ttu-id="2e477-257">Hatayı çözmek için bir yol prerendering devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="2e477-257">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="2e477-258">Bu genellikle uygulama tarayıcı tabanlı depolamanın yoğun bir şekilde kullanımını yapıyorsa en iyi seçenektir.</span><span class="sxs-lookup"><span data-stu-id="2e477-258">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="2e477-259">Prerendering karmaşıklık ekler ve uygulama, kullanılabilir olana kadar faydalı içeriğe gidemediği için uygulamaya yarar `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2e477-259">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="2e477-260">Prerendering 'yi devre dışı bırakmak için, *Pages/_Host. cshtml* dosyasını açın ve `render-mode` [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) ' nı ile değiştirin <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> .</span><span class="sxs-lookup"><span data-stu-id="2e477-260">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="2e477-261">Prerendering, veya kullanmayan diğer sayfalar için yararlı olabilir `localStorage` `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2e477-261">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="2e477-262">Prerendering etkin tutmak için, tarayıcı devreye bağlanana kadar yükleme işlemini erteleyin.</span><span class="sxs-lookup"><span data-stu-id="2e477-262">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="2e477-263">Aşağıda, bir sayaç değeri depolamak için bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2e477-263">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="2e477-264">Durum korumasını ortak bir konuma ayırın</span><span class="sxs-lookup"><span data-stu-id="2e477-264">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="2e477-265">Birçok bileşen tarayıcı tabanlı depolamaya güveniyorsa, durum sağlayıcısı kodu birçok kez yeniden uygulama kod yinelemesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2e477-265">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="2e477-266">Kod çoğaltmaktan kaçınmanın bir seçeneği, durum sağlayıcısı mantığını kapsülleyen bir *durum sağlayıcısı ana bileşeni* oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="2e477-266">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="2e477-267">Alt bileşenler, durum kalıcılığı mekanizmasına bakılmaksızın kalıcı verilerle çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-267">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="2e477-268">Aşağıdaki bir `CounterStateProvider` bileşen örneğinde, sayaç verileri kalıcıdır:</span><span class="sxs-lookup"><span data-stu-id="2e477-268">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

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

<span data-ttu-id="2e477-269">`CounterStateProvider`Bileşen, yükleme tamamlanana kadar alt içeriğini işlemeden Yükleme aşamasını işler.</span><span class="sxs-lookup"><span data-stu-id="2e477-269">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="2e477-270">Bileşeni kullanmak için `CounterStateProvider` , bileşenin bir örneğini sayaç durumuna erişimi gerektiren diğer tüm bileşenler etrafında sarmalayın.</span><span class="sxs-lookup"><span data-stu-id="2e477-270">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="2e477-271">Durumu bir uygulamadaki tüm bileşenler için erişilebilir hale getirmek üzere bileşeni `CounterStateProvider` <xref:Microsoft.AspNetCore.Components.Routing.Router> `App` bileşende (*app. Razor*) içine sarmalayın:</span><span class="sxs-lookup"><span data-stu-id="2e477-271">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="2e477-272">Sarmalanan bileşenler, kalıcı sayaç durumunu alır ve değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-272">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="2e477-273">Aşağıdaki `Counter` Bileşen, bu kalıbı uygular:</span><span class="sxs-lookup"><span data-stu-id="2e477-273">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="2e477-274">Yukarıdaki bileşen, ile etkileşimde bulunmak `ProtectedBrowserStorage` veya bir "yükleme" aşaması ile uğraşmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2e477-274">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="2e477-275">Daha önce açıklandığı gibi prerendering ile başa çıkmak için, `CounterStateProvider` sayaç verilerini kullanan tüm bileşenlerin prerendering ile otomatik olarak çalışmasını sağlayacak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2e477-275">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="2e477-276">Ayrıntılar için bkz. [Handle prerendering](#handle-prerendering) bölümü.</span><span class="sxs-lookup"><span data-stu-id="2e477-276">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="2e477-277">Genel olarak, *durum sağlayıcısı üst bileşen* deseninin kullanılması önerilir:</span><span class="sxs-lookup"><span data-stu-id="2e477-277">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="2e477-278">Diğer birçok bileşenin durumunu kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="2e477-278">To consume state in many other components.</span></span>
* <span data-ttu-id="2e477-279">Kalıcı olacak yalnızca bir üst düzey durum nesnesi varsa.</span><span class="sxs-lookup"><span data-stu-id="2e477-279">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="2e477-280">Birçok farklı durum nesnesini kalıcı hale getirmek ve farklı yerlerde nesnelerin farklı alt kümelerini kullanmak için, durumu küresel olarak yükleme ve kaydetme işlemlerini yapmaktan kaçınmak daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="2e477-280">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
