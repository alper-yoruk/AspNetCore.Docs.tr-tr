---
title: ASP.NET Core'da seçenek deseni
author: rick-anderson
description: ASP.NET Core uygulamalarında ilgili ayarların gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061099"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="57b92-103">ASP.NET Core'da seçenek deseni</span><span class="sxs-lookup"><span data-stu-id="57b92-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57b92-104">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="57b92-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="57b92-105">Seçenekler stili, ilişkili ayarlar gruplarına kesin olarak belirlenmiş erişim sağlamak için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="57b92-106">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="57b92-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="57b92-107">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="57b92-108">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="57b92-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="57b92-109">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="57b92-110">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="57b92-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="57b92-111">Bu konu, ASP.NET Core seçenekler düzeniyle ilgili bilgiler sağlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="57b92-112">Konsol uygulamalarında seçenekler deseninin kullanımı hakkında bilgi için bkz. [.net 'Teki seçenekler stili](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="57b92-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="57b92-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57b92-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="57b92-114">Hiyerarşik yapılandırmayı bağlama</span><span class="sxs-lookup"><span data-stu-id="57b92-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="57b92-115">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="57b92-115">Options interfaces</span></span>

<span data-ttu-id="57b92-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="57b92-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="57b92-117">\* **Not** _ support: _ uygulama başladıktan sonra yapılandırma verilerinin okunması.</span><span class="sxs-lookup"><span data-stu-id="57b92-117">Does \* **not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="57b92-118">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-118">Named options</span></span>](#named)
* <span data-ttu-id="57b92-119">[Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="57b92-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="57b92-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="57b92-121">Seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="57b92-122">Daha fazla bilgi için bkz. [güncelleştirilmiş verileri okumak Için ıoptionssnapshot kullanma](#ios).</span><span class="sxs-lookup"><span data-stu-id="57b92-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="57b92-123">[Kapsamlı](xref:fundamentals/dependency-injection#scoped) olarak kaydedilir ve bu nedenle tek bir hizmete eklenemez.</span><span class="sxs-lookup"><span data-stu-id="57b92-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="57b92-124">[Adlandırılmış seçenekleri](#named) destekler</span><span class="sxs-lookup"><span data-stu-id="57b92-124">Supports [named options](#named)</span></span>

<span data-ttu-id="57b92-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="57b92-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="57b92-126">, Seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="57b92-127">[Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="57b92-128">Desteklememektedir</span><span class="sxs-lookup"><span data-stu-id="57b92-128">Supports:</span></span>
  * <span data-ttu-id="57b92-129">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="57b92-129">Change notifications</span></span>
  * [<span data-ttu-id="57b92-130">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="57b92-131">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="57b92-132">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="57b92-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="57b92-133">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra ayarları veya değiştirme seçeneklerini etkinleştirir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="57b92-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="57b92-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="57b92-135">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="57b92-136">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="57b92-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="57b92-137">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="57b92-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="57b92-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> , <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="57b92-139">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="57b92-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="57b92-140">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="57b92-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="57b92-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="57b92-142">Güncelleştirilmiş verileri okumak için ıoptionssnapshot kullanın</span><span class="sxs-lookup"><span data-stu-id="57b92-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="57b92-143"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="57b92-144">Güncelleştirilmiş yapılandırma değerlerini okumayı destekleyen yapılandırma sağlayıcıları kullanılırken, yapılandırma değişiklikleri uygulama başladıktan sonra okundu.</span><span class="sxs-lookup"><span data-stu-id="57b92-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="57b92-145">Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:</span><span class="sxs-lookup"><span data-stu-id="57b92-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="57b92-146">`IOptionsMonitor` , özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="57b92-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="57b92-147">`IOptionsSnapshot` kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` .</span><span class="sxs-lookup"><span data-stu-id="57b92-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="57b92-148">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="57b92-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="57b92-149">Aşağıdaki kod kullanır <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="57b92-150">Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="57b92-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-151">Yukarıdaki kodda, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okundu.</span><span class="sxs-lookup"><span data-stu-id="57b92-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="57b92-152">Ioptionsmonitor</span><span class="sxs-lookup"><span data-stu-id="57b92-152">IOptionsMonitor</span></span>

<span data-ttu-id="57b92-153">Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-154">Aşağıdaki örnek şunu kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="57b92-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="57b92-155">Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.</span><span class="sxs-lookup"><span data-stu-id="57b92-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="57b92-156">IController Enamedooptıons kullanarak adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="57b92-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="57b92-157">Adlandırılmış seçenekler:</span><span class="sxs-lookup"><span data-stu-id="57b92-157">Named options:</span></span>

* <span data-ttu-id="57b92-158">Aynı özelliklere birden çok yapılandırma bölümü bağlandığı zaman faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="57b92-159">Büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-159">Are case sensitive.</span></span>

<span data-ttu-id="57b92-160">Aşağıdaki dosyayı göz önünde bulundurun *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="57b92-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="57b92-161">Ve bağlamak için iki sınıf oluşturmak yerine `TopItem:Month` `TopItem:Year` , her bölüm için aşağıdaki sınıf kullanılır:</span><span class="sxs-lookup"><span data-stu-id="57b92-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="57b92-162">Aşağıdaki kod, adlandırılmış seçenekleri yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="57b92-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-163">Aşağıdaki kod, adlandırılmış seçenekleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="57b92-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="57b92-164">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="57b92-164">All options are named instances.</span></span> <span data-ttu-id="57b92-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="57b92-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="57b92-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="57b92-167">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="57b92-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="57b92-168">`null`Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="57b92-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="57b92-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın.</span><span class="sxs-lookup"><span data-stu-id="57b92-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="57b92-170">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="57b92-170">OptionsBuilder API</span></span>

<span data-ttu-id="57b92-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="57b92-172">`OptionsBuilder` adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="57b92-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="57b92-173">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="57b92-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="57b92-174">`OptionsBuilder`[Seçenekler doğrulama](#val) bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="57b92-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="57b92-175">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="57b92-175">Use DI services to configure options</span></span>

<span data-ttu-id="57b92-176">Seçenekleri iki şekilde yapılandırırken, hizmetlere bağımlılık ekleme işleminden erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="57b92-177">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="57b92-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="57b92-178">`OptionsBuilder<TOptions>` , seçenekleri yapılandırmak için en fazla beş hizmet kullanılmasına izin veren [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="57b92-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="57b92-179">Veya uygulayan bir tür oluşturun <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ve türü bir hizmet olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="57b92-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="57b92-180">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="57b92-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="57b92-181">Bir tür oluşturmak,, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)çağrılırken çerçevenin yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="57b92-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="57b92-182">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="57b92-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="57b92-183">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="57b92-183">Options validation</span></span>

<span data-ttu-id="57b92-184">Seçenekler doğrulaması seçenek değerlerinin doğrulanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="57b92-185">Aşağıdaki dosyayı göz önünde bulundurun *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="57b92-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="57b92-186">Aşağıdaki sınıf `"MyConfig"` yapılandırma bölümüne bağlanır ve birkaç `DataAnnotations` kural uygular:</span><span class="sxs-lookup"><span data-stu-id="57b92-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="57b92-187">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="57b92-187">The following code:</span></span>

* <span data-ttu-id="57b92-188"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>Sınıfına bağlanan bir [seçenekoluşturucu Oluşturucu \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) almak için çağırır `MyConfigOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="57b92-189"><xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A>Kullanarak doğrulamayı etkinleştirmek için çağırır `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="57b92-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="57b92-190">`ValidateDataAnnotations`Genişletme yöntemi, [Microsoft. Extensions. Options. dataaçıklamalarda](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet paketinde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="57b92-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="57b92-191">SDK kullanan Web uygulamaları için `Microsoft.NET.Sdk.Web` Bu pakete, dolaylı olarak paylaşılan çerçeveden başvurulur.</span><span class="sxs-lookup"><span data-stu-id="57b92-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="57b92-192">Aşağıdaki kod yapılandırma değerlerini veya doğrulama hatalarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="57b92-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="57b92-193">Aşağıdaki kod, bir temsilci kullanarak daha karmaşık bir doğrulama kuralı uygular:</span><span class="sxs-lookup"><span data-stu-id="57b92-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="57b92-194">Karmaşık doğrulama için ıvalidateoptions</span><span class="sxs-lookup"><span data-stu-id="57b92-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="57b92-195">Aşağıdaki sınıf şunları uygular <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="57b92-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="57b92-196">`IValidateOptions` doğrulama kodunu `StartUp` bir sınıfa ve sınıfına taşımaya izin vermez.</span><span class="sxs-lookup"><span data-stu-id="57b92-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="57b92-197">Önceki kodu kullanarak, doğrulama ' de `Startup.ConfigureServices` aşağıdaki kodla etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="57b92-198">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-198">Options post-configuration</span></span>

<span data-ttu-id="57b92-199">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="57b92-200">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="57b92-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="57b92-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> , adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="57b92-202"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="57b92-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="57b92-203">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="57b92-203">Accessing options during startup</span></span>

<span data-ttu-id="57b92-204"><xref:Microsoft.Extensions.Options.IOptions%601> ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="57b92-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="57b92-205"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="57b92-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57b92-206">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="57b92-207">Options.ConfigurationExtensions NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="57b92-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="57b92-208">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine ASP.NET Core uygulamalarda örtülü olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="57b92-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="57b92-209">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="57b92-210">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="57b92-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="57b92-211">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="57b92-212">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="57b92-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="57b92-213">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="57b92-214">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="57b92-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="57b92-215">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57b92-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57b92-216">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="57b92-216">Prerequisites</span></span>

<span data-ttu-id="57b92-217">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft.Extensions.Options.Configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="57b92-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="57b92-218">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="57b92-218">Options interfaces</span></span>

<span data-ttu-id="57b92-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> , seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="57b92-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="57b92-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="57b92-221">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="57b92-221">Change notifications</span></span>
* [<span data-ttu-id="57b92-222">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="57b92-223">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="57b92-224">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="57b92-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="57b92-225">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="57b92-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="57b92-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="57b92-227">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="57b92-228">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="57b92-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="57b92-229">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="57b92-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="57b92-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> , <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="57b92-231">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="57b92-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="57b92-232">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="57b92-233"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="57b92-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="57b92-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="57b92-235">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="57b92-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="57b92-236"><xref:Microsoft.Extensions.Options.IOptions%601> , seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="57b92-237">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="57b92-238"><xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="57b92-239">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="57b92-239">General options configuration</span></span>

<span data-ttu-id="57b92-240">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="57b92-241">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="57b92-242">Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` .</span><span class="sxs-lookup"><span data-stu-id="57b92-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="57b92-243">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` .</span><span class="sxs-lookup"><span data-stu-id="57b92-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="57b92-244">`Option2` , özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir ( *modeller/MyOptions. cs* ):</span><span class="sxs-lookup"><span data-stu-id="57b92-244">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="57b92-245">`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="57b92-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="57b92-246">Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ( *Pages/Index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="57b92-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="57b92-247">Örnek *appsettings.json* dosyası ve değerlerini belirtir `option1` `option2` :</span><span class="sxs-lookup"><span data-stu-id="57b92-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="57b92-248">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="57b92-249"><xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="57b92-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="57b92-250">İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="57b92-251">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="57b92-252">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="57b92-253">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="57b92-253">Use a delegate to set options values.</span></span> <span data-ttu-id="57b92-254">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını ( *modeller/MyOptionsWithDelegateConfig. cs* ) kullanır:</span><span class="sxs-lookup"><span data-stu-id="57b92-254">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="57b92-255">Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="57b92-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="57b92-256">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="57b92-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-257">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="57b92-257">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-258">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="57b92-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="57b92-259">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="57b92-260">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="57b92-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="57b92-261">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="57b92-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="57b92-262">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` içinde belirtilmiştir *appsettings.json* , ancak değerleri `Option1` `Option2` yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="57b92-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="57b92-263">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="57b92-264">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="57b92-265">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="57b92-265">Suboptions configuration</span></span>

<span data-ttu-id="57b92-266">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="57b92-267">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="57b92-268">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="57b92-269">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="57b92-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="57b92-270">Örneğin, özelliği, `MyOptions.Option1` `Option1` içindeki özelliğinden okunan anahtara bağlanır `option1` *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="57b92-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json* .</span></span>

<span data-ttu-id="57b92-271">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="57b92-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="57b92-272">`MySubOptions`Dosyanın bölümüne bağlanır `subsection` *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="57b92-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="57b92-273">`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="57b92-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="57b92-274">Örnek *appsettings.json* dosyası `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="57b92-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="57b92-275">`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için ( *modeller/myalt seçenekler. cs* ):</span><span class="sxs-lookup"><span data-stu-id="57b92-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="57b92-276">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle ( *Pages/Index. cshtml. cs* ) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-276">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="57b92-277">Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="57b92-278">Seçeneklere ekleme</span><span class="sxs-lookup"><span data-stu-id="57b92-278">Options injection</span></span>

<span data-ttu-id="57b92-279">Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="57b92-280">Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="57b92-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="57b92-281">RazorYönergeyle bir sayfa veya MVC görünümü [`@inject`](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="57b92-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="57b92-282">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="57b92-282">A page or view model.</span></span>

<span data-ttu-id="57b92-283">Örnek uygulamadan alınan aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline ( *Sayfalar/Index. cshtml. cs* ) sahiptir:</span><span class="sxs-lookup"><span data-stu-id="57b92-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="57b92-284">Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="57b92-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="57b92-285">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-285">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="57b92-287">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="57b92-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="57b92-288">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="57b92-289"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="57b92-290">Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:</span><span class="sxs-lookup"><span data-stu-id="57b92-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="57b92-291">`IOptionsMonitor` , özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="57b92-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="57b92-292">`IOptionsSnapshot` kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` .</span><span class="sxs-lookup"><span data-stu-id="57b92-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="57b92-293">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="57b92-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="57b92-294">Aşağıdaki örnek, değişikliklerden sonra yeni bir <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> oluşturma işlemi gösterir *appsettings.json* ( *Pages/Index. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="57b92-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="57b92-295">*appsettings.json* Dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar sunucuya yönelik birden çok istek dosya tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="57b92-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="57b92-296">Aşağıdaki görüntüde, `option1` dosyadan yüklenen ilk ve `option2` değerler gösterilmektedir *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="57b92-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="57b92-297">*appsettings.json* Dosyadaki değerleri `value1_from_json UPDATED` ve ile değiştirin `200` .</span><span class="sxs-lookup"><span data-stu-id="57b92-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="57b92-298">*appsettings.json* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="57b92-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="57b92-299">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="57b92-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="57b92-300">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="57b92-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="57b92-301">İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="57b92-302">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="57b92-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="57b92-303">Örnek uygulamada, adlandırılmış seçenekler, Yapılandırılaenamedolarını çağıran [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir [ \<TOptions> . ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="57b92-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="57b92-304">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="57b92-305">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index. cshtml. cs* ) erişir:</span><span class="sxs-lookup"><span data-stu-id="57b92-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="57b92-306">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="57b92-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="57b92-307">`named_options_1` değerler, dosyadan yüklenen yapılandırmadan sağlanır *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="57b92-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="57b92-308">`named_options_2` değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="57b92-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="57b92-309">`named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .</span><span class="sxs-lookup"><span data-stu-id="57b92-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="57b92-310">`Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="57b92-311">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="57b92-312">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="57b92-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="57b92-313">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="57b92-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="57b92-314">Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="57b92-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="57b92-315">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="57b92-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="57b92-316">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="57b92-316">All options are named instances.</span></span> <span data-ttu-id="57b92-317">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="57b92-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="57b92-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="57b92-319">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="57b92-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="57b92-320">Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="57b92-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="57b92-321">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="57b92-321">OptionsBuilder API</span></span>

<span data-ttu-id="57b92-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="57b92-323">`OptionsBuilder` adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="57b92-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="57b92-324">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="57b92-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="57b92-325">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="57b92-325">Use DI services to configure options</span></span>

<span data-ttu-id="57b92-326">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="57b92-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="57b92-327">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="57b92-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="57b92-328">[Seçenekseçenekleri \<TOptions> Oluşturucusu](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza imkan tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="57b92-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="57b92-329"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="57b92-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="57b92-330">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="57b92-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="57b92-331">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="57b92-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="57b92-332">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="57b92-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="57b92-333">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="57b92-333">Options validation</span></span>

<span data-ttu-id="57b92-334">Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="57b92-335">`Validate` `true` Seçenekler geçerliyse ve geçerli değillerse, döndüren bir doğrulama yöntemiyle çağırın `false` :</span><span class="sxs-lookup"><span data-stu-id="57b92-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="57b92-336">Önceki örnekte, adlandırılmış seçenekler örneği olarak ayarlanır `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="57b92-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="57b92-337">Varsayılan Seçenekler örneği `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="57b92-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="57b92-338">Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="57b92-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="57b92-339">Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57b92-340">Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz.</span><span class="sxs-lookup"><span data-stu-id="57b92-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="57b92-341">Örneğin, Seçenekler `IOptionsSnapshot` ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="57b92-342">Bu `IOptionsSnapshot` Seçenekler *, aynı istek için* sonraki erişim denemelerinde yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="57b92-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request* .</span></span>

<span data-ttu-id="57b92-343">`Validate`Yöntemi bir kabul eder `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="57b92-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="57b92-344">Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>` izin veren uygulayın:</span><span class="sxs-lookup"><span data-stu-id="57b92-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="57b92-345">Birden çok seçenek türünün doğrulanması: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="57b92-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="57b92-346">Başka bir seçenek türüne bağlı olan doğrulama: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="57b92-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="57b92-347">`IValidateOptions` doğrular</span><span class="sxs-lookup"><span data-stu-id="57b92-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="57b92-348">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="57b92-348">A specific named options instance.</span></span>
* <span data-ttu-id="57b92-349">Olduğunda tüm seçenekler `name` `null` .</span><span class="sxs-lookup"><span data-stu-id="57b92-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="57b92-350">Arabirimini uygulamanızdan döndürün `ValidateOptionsResult` :</span><span class="sxs-lookup"><span data-stu-id="57b92-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="57b92-351">Veri eki tabanlı doğrulama, üzerinde yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="57b92-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="57b92-352">`Microsoft.Extensions.Options.DataAnnotations` , [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="57b92-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="57b92-353">Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.</span><span class="sxs-lookup"><span data-stu-id="57b92-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="57b92-354">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-354">Options post-configuration</span></span>

<span data-ttu-id="57b92-355">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="57b92-356">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="57b92-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="57b92-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> , adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="57b92-358"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="57b92-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="57b92-359">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="57b92-359">Accessing options during startup</span></span>

<span data-ttu-id="57b92-360"><xref:Microsoft.Extensions.Options.IOptions%601> ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="57b92-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="57b92-361"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="57b92-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57b92-362">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="57b92-363">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="57b92-364">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="57b92-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="57b92-365">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="57b92-366">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="57b92-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="57b92-367">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="57b92-368">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="57b92-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="57b92-369">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57b92-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57b92-370">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="57b92-370">Prerequisites</span></span>

<span data-ttu-id="57b92-371">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft.Extensions.Options.Configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="57b92-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="57b92-372">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="57b92-372">Options interfaces</span></span>

<span data-ttu-id="57b92-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> , seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="57b92-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="57b92-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="57b92-375">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="57b92-375">Change notifications</span></span>
* [<span data-ttu-id="57b92-376">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="57b92-377">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="57b92-378">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="57b92-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="57b92-379">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="57b92-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="57b92-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="57b92-381">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="57b92-382">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="57b92-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="57b92-383">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="57b92-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="57b92-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> , <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="57b92-385">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="57b92-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="57b92-386">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="57b92-387"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="57b92-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="57b92-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="57b92-389">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="57b92-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="57b92-390"><xref:Microsoft.Extensions.Options.IOptions%601> , seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="57b92-391">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="57b92-392"><xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="57b92-393">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="57b92-393">General options configuration</span></span>

<span data-ttu-id="57b92-394">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="57b92-395">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="57b92-396">Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` .</span><span class="sxs-lookup"><span data-stu-id="57b92-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="57b92-397">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` .</span><span class="sxs-lookup"><span data-stu-id="57b92-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="57b92-398">`Option2` , özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir ( *modeller/MyOptions. cs* ):</span><span class="sxs-lookup"><span data-stu-id="57b92-398">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="57b92-399">`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="57b92-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="57b92-400">Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ( *Pages/Index. cshtml. cs* ):</span><span class="sxs-lookup"><span data-stu-id="57b92-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="57b92-401">Örnek *appsettings.json* dosyası ve değerlerini belirtir `option1` `option2` :</span><span class="sxs-lookup"><span data-stu-id="57b92-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="57b92-402">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="57b92-403"><xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="57b92-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="57b92-404">İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="57b92-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="57b92-405">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="57b92-406">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="57b92-407">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="57b92-407">Use a delegate to set options values.</span></span> <span data-ttu-id="57b92-408">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını ( *modeller/MyOptionsWithDelegateConfig. cs* ) kullanır:</span><span class="sxs-lookup"><span data-stu-id="57b92-408">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="57b92-409">Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="57b92-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="57b92-410">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="57b92-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-411">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="57b92-411">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="57b92-412">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="57b92-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="57b92-413">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="57b92-414">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="57b92-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="57b92-415">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="57b92-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="57b92-416">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` içinde belirtilmiştir *appsettings.json* , ancak değerleri `Option1` `Option2` yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="57b92-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="57b92-417">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="57b92-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="57b92-418">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="57b92-419">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="57b92-419">Suboptions configuration</span></span>

<span data-ttu-id="57b92-420">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="57b92-421">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="57b92-422">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="57b92-423">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="57b92-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="57b92-424">Örneğin, özelliği, `MyOptions.Option1` `Option1` içindeki özelliğinden okunan anahtara bağlanır `option1` *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="57b92-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json* .</span></span>

<span data-ttu-id="57b92-425">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="57b92-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="57b92-426">`MySubOptions`Dosyanın bölümüne bağlanır `subsection` *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="57b92-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="57b92-427">`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="57b92-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="57b92-428">Örnek *appsettings.json* dosyası `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="57b92-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="57b92-429">`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için ( *modeller/myalt seçenekler. cs* ):</span><span class="sxs-lookup"><span data-stu-id="57b92-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="57b92-430">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle ( *Pages/Index. cshtml. cs* ) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-430">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="57b92-431">Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="57b92-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="57b92-432">Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="57b92-433">Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="57b92-434">Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> doğrudan bir görünüme ( *Sayfalar/Index. cshtml. cs* ) sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="57b92-435">Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="57b92-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="57b92-436">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-436">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="57b92-438">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="57b92-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="57b92-439">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="57b92-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.</span><span class="sxs-lookup"><span data-stu-id="57b92-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="57b92-441">Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="57b92-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="57b92-442">Aşağıdaki örnek, değişikliklerden sonra yeni bir <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> oluşturma işlemi gösterir *appsettings.json* ( *Pages/Index. cshtml. cs* ).</span><span class="sxs-lookup"><span data-stu-id="57b92-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="57b92-443">*appsettings.json* Dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar sunucuya yönelik birden çok istek dosya tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="57b92-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="57b92-444">Aşağıdaki görüntüde, `option1` dosyadan yüklenen ilk ve `option2` değerler gösterilmektedir *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="57b92-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="57b92-445">*appsettings.json* Dosyadaki değerleri `value1_from_json UPDATED` ve ile değiştirin `200` .</span><span class="sxs-lookup"><span data-stu-id="57b92-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="57b92-446">*appsettings.json* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="57b92-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="57b92-447">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="57b92-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="57b92-448">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="57b92-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="57b92-449">İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="57b92-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="57b92-450">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="57b92-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="57b92-451">Örnek uygulamada, adlandırılmış seçenekler, Yapılandırılaenamedolarını çağıran [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir [ \<TOptions> . ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="57b92-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="57b92-452">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="57b92-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="57b92-453">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index. cshtml. cs* ) erişir:</span><span class="sxs-lookup"><span data-stu-id="57b92-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="57b92-454">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="57b92-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="57b92-455">`named_options_1` değerler, dosyadan yüklenen yapılandırmadan sağlanır *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="57b92-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="57b92-456">`named_options_2` değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="57b92-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="57b92-457">`named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .</span><span class="sxs-lookup"><span data-stu-id="57b92-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="57b92-458">`Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="57b92-459">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="57b92-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="57b92-460">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="57b92-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="57b92-461">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="57b92-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="57b92-462">Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="57b92-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="57b92-463">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="57b92-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="57b92-464">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="57b92-464">All options are named instances.</span></span> <span data-ttu-id="57b92-465">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="57b92-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="57b92-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="57b92-467">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="57b92-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="57b92-468">Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="57b92-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="57b92-469">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="57b92-469">OptionsBuilder API</span></span>

<span data-ttu-id="57b92-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="57b92-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="57b92-471">`OptionsBuilder` adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="57b92-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="57b92-472">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="57b92-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="57b92-473">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="57b92-473">Use DI services to configure options</span></span>

<span data-ttu-id="57b92-474">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="57b92-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="57b92-475">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="57b92-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="57b92-476">[Seçenekseçenekleri \<TOptions> Oluşturucusu](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza imkan tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="57b92-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="57b92-477"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="57b92-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="57b92-478">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="57b92-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="57b92-479">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="57b92-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="57b92-480">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="57b92-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="57b92-481">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="57b92-481">Options post-configuration</span></span>

<span data-ttu-id="57b92-482">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="57b92-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="57b92-483">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="57b92-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="57b92-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> , adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="57b92-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="57b92-485"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="57b92-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="57b92-486">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="57b92-486">Accessing options during startup</span></span>

<span data-ttu-id="57b92-487"><xref:Microsoft.Extensions.Options.IOptions%601> ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="57b92-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="57b92-488"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="57b92-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57b92-489">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="57b92-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="57b92-490">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="57b92-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
