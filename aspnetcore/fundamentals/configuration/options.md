---
<span data-ttu-id="56dbd-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="56dbd-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="56dbd-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="56dbd-102">'Blazor'</span></span>
- <span data-ttu-id="56dbd-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="56dbd-103">'Identity'</span></span>
- <span data-ttu-id="56dbd-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="56dbd-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="56dbd-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="56dbd-105">'Razor'</span></span>
- <span data-ttu-id="56dbd-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="56dbd-106">'SignalR' uid:</span></span> 

--- 
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="56dbd-107">ASP.NET Core için seçenek kalıbı</span><span class="sxs-lookup"><span data-stu-id="56dbd-107">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56dbd-108">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="56dbd-108">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="56dbd-109">Seçenekler stili, ilişkili ayarlar gruplarına kesin olarak belirlenmiş erişim sağlamak için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-109">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="56dbd-110">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="56dbd-110">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="56dbd-111">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-111">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="56dbd-112">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="56dbd-112">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="56dbd-113">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-113">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="56dbd-114">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-114">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="56dbd-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56dbd-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="56dbd-116">Hiyerarşik yapılandırmayı bağlama</span><span class="sxs-lookup"><span data-stu-id="56dbd-116">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="56dbd-117">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="56dbd-117">Options interfaces</span></span>

<span data-ttu-id="56dbd-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="56dbd-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="56dbd-119">Şunları ***desteklemez:***</span><span class="sxs-lookup"><span data-stu-id="56dbd-119">Does ***not*** support:</span></span>
  * <span data-ttu-id="56dbd-120">Uygulama başladıktan sonra yapılandırma verilerinin okunması.</span><span class="sxs-lookup"><span data-stu-id="56dbd-120">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="56dbd-121">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-121">Named options</span></span>](#named)
* <span data-ttu-id="56dbd-122">[Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-122">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="56dbd-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="56dbd-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="56dbd-124">Seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-124">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="56dbd-125">Daha fazla bilgi için bkz. [güncelleştirilmiş verileri okumak Için ıoptionssnapshot kullanma](#ios).</span><span class="sxs-lookup"><span data-stu-id="56dbd-125">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="56dbd-126">[Kapsamlı](xref:fundamentals/dependency-injection#scoped) olarak kaydedilir ve bu nedenle tek bir hizmete eklenemez.</span><span class="sxs-lookup"><span data-stu-id="56dbd-126">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="56dbd-127">[Adlandırılmış seçenekleri](#named) destekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-127">Supports [named options](#named)</span></span>

<span data-ttu-id="56dbd-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="56dbd-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="56dbd-129">, Seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-129">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="56dbd-130">[Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-130">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="56dbd-131">Desteklememektedir</span><span class="sxs-lookup"><span data-stu-id="56dbd-131">Supports:</span></span>
  * <span data-ttu-id="56dbd-132">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="56dbd-132">Change notifications</span></span>
  * [<span data-ttu-id="56dbd-133">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-133">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="56dbd-134">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-134">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="56dbd-135">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="56dbd-135">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="56dbd-136">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra ayarları veya değiştirme seçeneklerini etkinleştirir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-136">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="56dbd-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="56dbd-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="56dbd-138">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-138">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="56dbd-139">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="56dbd-139">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="56dbd-140">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-140">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="56dbd-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="56dbd-142">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="56dbd-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="56dbd-143">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-143">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="56dbd-144"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-144">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="56dbd-145">Güncelleştirilmiş verileri okumak için ıoptionssnapshot kullanın</span><span class="sxs-lookup"><span data-stu-id="56dbd-145">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="56dbd-146"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-146">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="56dbd-147">Güncelleştirilmiş yapılandırma değerlerini okumayı destekleyen yapılandırma sağlayıcıları kullanılırken, yapılandırma değişiklikleri uygulama başladıktan sonra okundu.</span><span class="sxs-lookup"><span data-stu-id="56dbd-147">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="56dbd-148">Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:</span><span class="sxs-lookup"><span data-stu-id="56dbd-148">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="56dbd-149">`IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="56dbd-149">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="56dbd-150">`IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-150">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="56dbd-151">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-151">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="56dbd-152">Aşağıdaki kod kullanır <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-152">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="56dbd-153">Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-153">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-154">Yukarıdaki kodda, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okundu.</span><span class="sxs-lookup"><span data-stu-id="56dbd-154">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="56dbd-155">Ioptionsmonitor</span><span class="sxs-lookup"><span data-stu-id="56dbd-155">IOptionsMonitor</span></span>

<span data-ttu-id="56dbd-156">Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-156">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-157">Aşağıdaki örnek şunu kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="56dbd-157">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="56dbd-158">Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.</span><span class="sxs-lookup"><span data-stu-id="56dbd-158">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="56dbd-159">IController Enamedooptıons kullanarak adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="56dbd-159">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="56dbd-160">Adlandırılmış seçenekler:</span><span class="sxs-lookup"><span data-stu-id="56dbd-160">Named options:</span></span>

* <span data-ttu-id="56dbd-161">Aynı özelliklere birden çok yapılandırma bölümü bağlandığı zaman faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-161">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="56dbd-162">Büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-162">Are case sensitive.</span></span>

<span data-ttu-id="56dbd-163">Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="56dbd-163">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="56dbd-164">Ve bağlamak için iki sınıf oluşturmak yerine `TopItem:Month` `TopItem:Year` , her bölüm için aşağıdaki sınıf kullanılır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-164">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="56dbd-165">Aşağıdaki kod, adlandırılmış seçenekleri yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-165">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-166">Aşağıdaki kod, adlandırılmış seçenekleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-166">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="56dbd-167">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-167">All options are named instances.</span></span> <span data-ttu-id="56dbd-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="56dbd-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="56dbd-170">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-170">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="56dbd-171">`null`Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-171">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="56dbd-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="56dbd-173">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="56dbd-173">OptionsBuilder API</span></span>

<span data-ttu-id="56dbd-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="56dbd-175">`OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-175">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="56dbd-176">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-176">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="56dbd-177">`OptionsBuilder`[Seçenekler doğrulama](#val) bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-177">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="56dbd-178">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="56dbd-178">Use DI services to configure options</span></span>

<span data-ttu-id="56dbd-179">Seçenekleri iki şekilde yapılandırırken, hizmetlere bağımlılık ekleme işleminden erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-179">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="56dbd-180">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-180">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="56dbd-181">`OptionsBuilder<TOptions>`, seçenekleri yapılandırmak için en fazla beş hizmet kullanılmasına izin veren [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="56dbd-181">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="56dbd-182">Veya uygulayan bir tür oluşturun <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ve türü bir hizmet olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-182">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="56dbd-183">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-183">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="56dbd-184">Bir tür oluşturmak,, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)çağrılırken çerçevenin yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-184">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="56dbd-185">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="56dbd-185">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="56dbd-186">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="56dbd-186">Options validation</span></span>

<span data-ttu-id="56dbd-187">Seçenekler doğrulaması seçenek değerlerinin doğrulanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-187">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="56dbd-188">Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="56dbd-188">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="56dbd-189">Aşağıdaki sınıf `"MyConfig"` yapılandırma bölümüne bağlanır ve birkaç `DataAnnotations` kural uygular:</span><span class="sxs-lookup"><span data-stu-id="56dbd-189">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="56dbd-190">Aşağıdaki kod, <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> sınıfa bağlanan ve doğrulamayı sağlayan bir [SeçenekKodu Oluşturucusu \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) almak için çağırır `MyConfigOptions` `DataAnnotations` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-190">The following code calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an  [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class and enables `DataAnnotations` validation:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="56dbd-191">Aşağıdaki kod yapılandırma değerlerini veya doğrulama hatalarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="56dbd-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="56dbd-192">Aşağıdaki kod, bir temsilci kullanarak daha karmaşık bir doğrulama kuralı uygular:</span><span class="sxs-lookup"><span data-stu-id="56dbd-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="56dbd-193">Karmaşık doğrulama için ıvalidateoptions</span><span class="sxs-lookup"><span data-stu-id="56dbd-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="56dbd-194">Aşağıdaki sınıf şunları uygular <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="56dbd-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="56dbd-195">`IValidateOptions`doğrulama kodunu `StartUp` bir sınıfa ve sınıfına taşımaya izin vermez.</span><span class="sxs-lookup"><span data-stu-id="56dbd-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="56dbd-196">Önceki kodu kullanarak, doğrulama ' de `Startup.ConfigureServices` aşağıdaki kodla etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="56dbd-197">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-197">Options post-configuration</span></span>

<span data-ttu-id="56dbd-198">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="56dbd-199">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="56dbd-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="56dbd-201"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="56dbd-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="56dbd-202">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="56dbd-202">Accessing options during startup</span></span>

<span data-ttu-id="56dbd-203"><xref:Microsoft.Extensions.Options.IOptions%601>ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="56dbd-204"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56dbd-205">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="56dbd-206">Options. ConfigurationExtensions NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="56dbd-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="56dbd-207">[Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine ASP.NET Core uygulamalarında örtük olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="56dbd-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="56dbd-208">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="56dbd-209">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="56dbd-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="56dbd-210">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="56dbd-211">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="56dbd-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="56dbd-212">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="56dbd-213">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="56dbd-214">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56dbd-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56dbd-215">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="56dbd-215">Prerequisites</span></span>

<span data-ttu-id="56dbd-216">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="56dbd-217">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="56dbd-217">Options interfaces</span></span>

<span data-ttu-id="56dbd-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="56dbd-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="56dbd-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="56dbd-220">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="56dbd-220">Change notifications</span></span>
* [<span data-ttu-id="56dbd-221">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="56dbd-222">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="56dbd-223">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="56dbd-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="56dbd-224">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="56dbd-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="56dbd-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="56dbd-226">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="56dbd-227">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="56dbd-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="56dbd-228">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="56dbd-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="56dbd-230">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="56dbd-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="56dbd-231">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="56dbd-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="56dbd-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="56dbd-234">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="56dbd-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="56dbd-235"><xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="56dbd-236">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="56dbd-237"><xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="56dbd-238">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56dbd-238">General options configuration</span></span>

<span data-ttu-id="56dbd-239">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="56dbd-240">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="56dbd-241">Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="56dbd-242">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="56dbd-243">`Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="56dbd-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="56dbd-244">`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="56dbd-245">Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="56dbd-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="56dbd-246">Örneğin *appSettings. JSON* dosyası ve değerlerini belirtir `option1` `option2` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="56dbd-247">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="56dbd-248"><xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="56dbd-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="56dbd-249">İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="56dbd-250">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="56dbd-251">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="56dbd-252">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-252">Use a delegate to set options values.</span></span> <span data-ttu-id="56dbd-253">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="56dbd-254">Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="56dbd-255">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="56dbd-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-257">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="56dbd-258">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="56dbd-259">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="56dbd-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="56dbd-260">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="56dbd-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="56dbd-261">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="56dbd-262">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="56dbd-263">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="56dbd-264">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56dbd-264">Suboptions configuration</span></span>

<span data-ttu-id="56dbd-265">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="56dbd-266">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="56dbd-267">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="56dbd-268">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="56dbd-269">Örneğin, `MyOptions.Option1` özelliği `Option1` `option1` *appSettings. JSON*içindeki özelliğinden okunan anahtara bağlanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="56dbd-270">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="56dbd-271">`MySubOptions` `subsection` *AppSettings. JSON* dosyasının bölümüne bağlanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="56dbd-272">`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="56dbd-273">Örnek *appSettings. JSON* dosyası `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="56dbd-274">`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="56dbd-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="56dbd-275">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="56dbd-276">Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="56dbd-277">Seçeneklere ekleme</span><span class="sxs-lookup"><span data-stu-id="56dbd-277">Options injection</span></span>

<span data-ttu-id="56dbd-278">Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="56dbd-279">Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="56dbd-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="56dbd-280">RazorYönergeyle bir sayfa veya MVC görünümü [`@inject`](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="56dbd-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="56dbd-281">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="56dbd-281">A page or view model.</span></span>

<span data-ttu-id="56dbd-282">Örnek uygulamadan alınan aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="56dbd-283">Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="56dbd-284">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="56dbd-286">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="56dbd-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="56dbd-287">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="56dbd-288"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="56dbd-289">Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:</span><span class="sxs-lookup"><span data-stu-id="56dbd-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="56dbd-290">`IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="56dbd-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="56dbd-291">`IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="56dbd-292">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="56dbd-293">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appSettings. JSON* değişikliklerinden sonra yeni bir oluşturma Işlemi gösterir (*Pages/Index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="56dbd-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="56dbd-294">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="56dbd-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="56dbd-295">Aşağıdaki görüntüde, `option1` `option2` *appSettings. JSON* dosyasından yüklenen ilk ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="56dbd-296">*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile değiştirin `200` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="56dbd-297">*AppSettings. JSON* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="56dbd-298">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="56dbd-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="56dbd-299">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="56dbd-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="56dbd-300">İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="56dbd-301">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="56dbd-302">Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu yapılandırma, [yapılandırılaenamedooptıons ' i çağırır \<TOptions> . ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="56dbd-303">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="56dbd-304">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="56dbd-305">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="56dbd-306">`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="56dbd-307">`named_options_2`değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="56dbd-308">`named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="56dbd-309">`Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="56dbd-310">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="56dbd-311">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="56dbd-312">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="56dbd-313">Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="56dbd-314">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="56dbd-315">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-315">All options are named instances.</span></span> <span data-ttu-id="56dbd-316">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="56dbd-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="56dbd-318">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="56dbd-319">Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="56dbd-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="56dbd-320">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="56dbd-320">OptionsBuilder API</span></span>

<span data-ttu-id="56dbd-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="56dbd-322">`OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="56dbd-323">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="56dbd-324">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="56dbd-324">Use DI services to configure options</span></span>

<span data-ttu-id="56dbd-325">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="56dbd-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="56dbd-326">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="56dbd-327">[Seçenekseçenekleri \<TOptions> Oluşturucusu](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza imkan tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="56dbd-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="56dbd-328"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="56dbd-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="56dbd-329">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="56dbd-330">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="56dbd-331">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="56dbd-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="56dbd-332">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="56dbd-332">Options validation</span></span>

<span data-ttu-id="56dbd-333">Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="56dbd-334">`Validate` `true` Seçenekler geçerliyse ve geçerli değillerse, döndüren bir doğrulama yöntemiyle çağırın `false` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="56dbd-335">Önceki örnekte, adlandırılmış seçenekler örneği olarak ayarlanır `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="56dbd-336">Varsayılan Seçenekler örneği `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="56dbd-337">Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="56dbd-338">Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="56dbd-339">Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="56dbd-340">Örneğin, Seçenekler `IOptionsSnapshot` ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="56dbd-341">Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="56dbd-342">`Validate`Yöntemi bir kabul eder `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="56dbd-343">Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>` izin veren uygulayın:</span><span class="sxs-lookup"><span data-stu-id="56dbd-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="56dbd-344">Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="56dbd-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="56dbd-345">Başka bir seçenek türüne bağlı olan doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="56dbd-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="56dbd-346">`IValidateOptions`doğrular</span><span class="sxs-lookup"><span data-stu-id="56dbd-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="56dbd-347">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="56dbd-347">A specific named options instance.</span></span>
* <span data-ttu-id="56dbd-348">Olduğunda tüm seçenekler `name` `null` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="56dbd-349">Arabirimini uygulamanızdan döndürün `ValidateOptionsResult` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="56dbd-350">Veri eki tabanlı doğrulama, üzerinde yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="56dbd-351">`Microsoft.Extensions.Options.DataAnnotations`, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="56dbd-352">Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="56dbd-353">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-353">Options post-configuration</span></span>

<span data-ttu-id="56dbd-354">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="56dbd-355">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="56dbd-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="56dbd-357"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="56dbd-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="56dbd-358">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="56dbd-358">Accessing options during startup</span></span>

<span data-ttu-id="56dbd-359"><xref:Microsoft.Extensions.Options.IOptions%601>ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="56dbd-360"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56dbd-361">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="56dbd-362">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="56dbd-363">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="56dbd-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="56dbd-364">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="56dbd-365">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="56dbd-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="56dbd-366">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="56dbd-367">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="56dbd-368">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56dbd-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56dbd-369">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="56dbd-369">Prerequisites</span></span>

<span data-ttu-id="56dbd-370">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="56dbd-371">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="56dbd-371">Options interfaces</span></span>

<span data-ttu-id="56dbd-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="56dbd-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="56dbd-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="56dbd-374">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="56dbd-374">Change notifications</span></span>
* [<span data-ttu-id="56dbd-375">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="56dbd-376">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="56dbd-377">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="56dbd-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="56dbd-378">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="56dbd-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="56dbd-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="56dbd-380">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="56dbd-381">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="56dbd-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="56dbd-382">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="56dbd-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="56dbd-384">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="56dbd-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="56dbd-385">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="56dbd-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="56dbd-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="56dbd-388">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="56dbd-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="56dbd-389"><xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="56dbd-390">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="56dbd-391"><xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="56dbd-392">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56dbd-392">General options configuration</span></span>

<span data-ttu-id="56dbd-393">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="56dbd-394">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="56dbd-395">Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="56dbd-396">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="56dbd-397">`Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="56dbd-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="56dbd-398">`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="56dbd-399">Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="56dbd-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="56dbd-400">Örneğin *appSettings. JSON* dosyası ve değerlerini belirtir `option1` `option2` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="56dbd-401">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="56dbd-402"><xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="56dbd-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="56dbd-403">İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="56dbd-404">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="56dbd-405">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="56dbd-406">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-406">Use a delegate to set options values.</span></span> <span data-ttu-id="56dbd-407">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="56dbd-408">Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="56dbd-409">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="56dbd-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="56dbd-411">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="56dbd-412">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="56dbd-413">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="56dbd-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="56dbd-414">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="56dbd-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="56dbd-415">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="56dbd-416">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="56dbd-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="56dbd-417">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="56dbd-418">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="56dbd-418">Suboptions configuration</span></span>

<span data-ttu-id="56dbd-419">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="56dbd-420">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="56dbd-421">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="56dbd-422">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="56dbd-423">Örneğin, `MyOptions.Option1` özelliği `Option1` `option1` *appSettings. JSON*içindeki özelliğinden okunan anahtara bağlanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="56dbd-424">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="56dbd-425">`MySubOptions` `subsection` *AppSettings. JSON* dosyasının bölümüne bağlanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="56dbd-426">`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="56dbd-427">Örnek *appSettings. JSON* dosyası `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="56dbd-428">`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="56dbd-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="56dbd-429">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="56dbd-430">Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="56dbd-431">Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="56dbd-432">Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="56dbd-433">Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="56dbd-434">Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="56dbd-435">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="56dbd-437">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="56dbd-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="56dbd-438">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="56dbd-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.</span><span class="sxs-lookup"><span data-stu-id="56dbd-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="56dbd-440">Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="56dbd-441">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appSettings. JSON* değişikliklerinden sonra yeni bir oluşturma Işlemi gösterir (*Pages/Index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="56dbd-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="56dbd-442">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="56dbd-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="56dbd-443">Aşağıdaki görüntüde, `option1` `option2` *appSettings. JSON* dosyasından yüklenen ilk ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="56dbd-444">*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile değiştirin `200` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="56dbd-445">*AppSettings. JSON* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="56dbd-446">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="56dbd-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="56dbd-447">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="56dbd-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="56dbd-448">İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="56dbd-449">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="56dbd-450">Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu yapılandırma, [yapılandırılaenamedooptıons ' i çağırır \<TOptions> . ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="56dbd-451">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="56dbd-452">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="56dbd-453">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="56dbd-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="56dbd-454">`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="56dbd-455">`named_options_2`değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="56dbd-456">`named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="56dbd-457">`Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="56dbd-458">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="56dbd-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="56dbd-459">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="56dbd-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="56dbd-460">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="56dbd-461">Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="56dbd-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="56dbd-462">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="56dbd-463">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-463">All options are named instances.</span></span> <span data-ttu-id="56dbd-464">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="56dbd-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="56dbd-466">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="56dbd-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="56dbd-467">Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="56dbd-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="56dbd-468">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="56dbd-468">OptionsBuilder API</span></span>

<span data-ttu-id="56dbd-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="56dbd-470">`OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="56dbd-471">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="56dbd-472">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="56dbd-472">Use DI services to configure options</span></span>

<span data-ttu-id="56dbd-473">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="56dbd-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="56dbd-474">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="56dbd-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="56dbd-475">[Seçenekseçenekleri \<TOptions> Oluşturucusu](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza imkan tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="56dbd-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="56dbd-476"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="56dbd-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="56dbd-477">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="56dbd-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="56dbd-478">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="56dbd-479">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="56dbd-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="56dbd-480">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="56dbd-480">Options post-configuration</span></span>

<span data-ttu-id="56dbd-481">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="56dbd-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="56dbd-482">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="56dbd-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="56dbd-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="56dbd-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="56dbd-484"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="56dbd-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="56dbd-485">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="56dbd-485">Accessing options during startup</span></span>

<span data-ttu-id="56dbd-486"><xref:Microsoft.Extensions.Options.IOptions%601>ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="56dbd-487"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="56dbd-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="56dbd-488">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="56dbd-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="56dbd-489">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="56dbd-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
