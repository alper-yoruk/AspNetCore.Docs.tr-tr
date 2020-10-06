---
title: ASP.NET Core'da seçenek deseni
author: rick-anderson
description: ASP.NET Core uygulamalarında ilgili ayarların gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
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
uid: fundamentals/configuration/options
ms.openlocfilehash: 6eafad63bd4c49cee6a85108b37a8b61e1214bce
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762340"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="26fa3-103">ASP.NET Core'da seçenek deseni</span><span class="sxs-lookup"><span data-stu-id="26fa3-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26fa3-104">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="26fa3-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="26fa3-105">Seçenekler stili, ilişkili ayarlar gruplarına kesin olarak belirlenmiş erişim sağlamak için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="26fa3-106">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="26fa3-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="26fa3-107">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="26fa3-108">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="26fa3-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="26fa3-109">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="26fa3-110">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="26fa3-111">Bu konu, ASP.NET Core seçenekler düzeniyle ilgili bilgiler sağlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="26fa3-112">Konsol uygulamalarında seçenekler deseninin kullanımı hakkında bilgi için bkz. [.net 'Teki seçenekler stili](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="26fa3-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="26fa3-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26fa3-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="26fa3-114">Hiyerarşik yapılandırmayı bağlama</span><span class="sxs-lookup"><span data-stu-id="26fa3-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="26fa3-115">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="26fa3-115">Options interfaces</span></span>

<span data-ttu-id="26fa3-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="26fa3-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="26fa3-117">Şunları ***desteklemez:***</span><span class="sxs-lookup"><span data-stu-id="26fa3-117">Does ***not*** support:</span></span>
  * <span data-ttu-id="26fa3-118">Uygulama başladıktan sonra yapılandırma verilerinin okunması.</span><span class="sxs-lookup"><span data-stu-id="26fa3-118">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="26fa3-119">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-119">Named options</span></span>](#named)
* <span data-ttu-id="26fa3-120">[Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-120">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="26fa3-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="26fa3-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="26fa3-122">Seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-122">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="26fa3-123">Daha fazla bilgi için bkz. [güncelleştirilmiş verileri okumak Için ıoptionssnapshot kullanma](#ios).</span><span class="sxs-lookup"><span data-stu-id="26fa3-123">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="26fa3-124">[Kapsamlı](xref:fundamentals/dependency-injection#scoped) olarak kaydedilir ve bu nedenle tek bir hizmete eklenemez.</span><span class="sxs-lookup"><span data-stu-id="26fa3-124">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="26fa3-125">[Adlandırılmış seçenekleri](#named) destekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-125">Supports [named options](#named)</span></span>

<span data-ttu-id="26fa3-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="26fa3-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="26fa3-127">, Seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-127">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="26fa3-128">[Tek](xref:fundamentals/dependency-injection#singleton) bir olarak kaydedilir ve herhangi bir [hizmet ömrüne](xref:fundamentals/dependency-injection#service-lifetimes)eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-128">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="26fa3-129">Desteklememektedir</span><span class="sxs-lookup"><span data-stu-id="26fa3-129">Supports:</span></span>
  * <span data-ttu-id="26fa3-130">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="26fa3-130">Change notifications</span></span>
  * [<span data-ttu-id="26fa3-131">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-131">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="26fa3-132">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-132">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="26fa3-133">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="26fa3-133">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="26fa3-134">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra ayarları veya değiştirme seçeneklerini etkinleştirir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-134">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="26fa3-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="26fa3-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="26fa3-136">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-136">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="26fa3-137">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="26fa3-137">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="26fa3-138">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-138">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="26fa3-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> , <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="26fa3-140">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="26fa3-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="26fa3-141">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-141">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="26fa3-142"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="26fa3-143">Güncelleştirilmiş verileri okumak için ıoptionssnapshot kullanın</span><span class="sxs-lookup"><span data-stu-id="26fa3-143">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="26fa3-144"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-144">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="26fa3-145">Güncelleştirilmiş yapılandırma değerlerini okumayı destekleyen yapılandırma sağlayıcıları kullanılırken, yapılandırma değişiklikleri uygulama başladıktan sonra okundu.</span><span class="sxs-lookup"><span data-stu-id="26fa3-145">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="26fa3-146">Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:</span><span class="sxs-lookup"><span data-stu-id="26fa3-146">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="26fa3-147">`IOptionsMonitor` , özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="26fa3-147">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="26fa3-148">`IOptionsSnapshot` kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-148">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="26fa3-149">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-149">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="26fa3-150">Aşağıdaki kod kullanır <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-150">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="26fa3-151">Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-151">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-152">Yukarıdaki kodda, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okundu.</span><span class="sxs-lookup"><span data-stu-id="26fa3-152">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="26fa3-153">Ioptionsmonitor</span><span class="sxs-lookup"><span data-stu-id="26fa3-153">IOptionsMonitor</span></span>

<span data-ttu-id="26fa3-154">Aşağıdaki kod, ' a bağlanan bir yapılandırma örneği kaydeder `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-154">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-155">Aşağıdaki örnek şunu kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="26fa3-155">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="26fa3-156">Yukarıdaki kodda, varsayılan olarak, uygulama başladıktan sonra JSON yapılandırma dosyasında yapılan değişiklikler okunurdur.</span><span class="sxs-lookup"><span data-stu-id="26fa3-156">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="26fa3-157">IController Enamedooptıons kullanarak adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="26fa3-157">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="26fa3-158">Adlandırılmış seçenekler:</span><span class="sxs-lookup"><span data-stu-id="26fa3-158">Named options:</span></span>

* <span data-ttu-id="26fa3-159">Aynı özelliklere birden çok yapılandırma bölümü bağlandığı zaman faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-159">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="26fa3-160">Büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-160">Are case sensitive.</span></span>

<span data-ttu-id="26fa3-161">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="26fa3-161">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="26fa3-162">Ve bağlamak için iki sınıf oluşturmak yerine `TopItem:Month` `TopItem:Year` , her bölüm için aşağıdaki sınıf kullanılır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-162">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="26fa3-163">Aşağıdaki kod, adlandırılmış seçenekleri yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-163">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-164">Aşağıdaki kod, adlandırılmış seçenekleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-164">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="26fa3-165">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-165">All options are named instances.</span></span> <span data-ttu-id="26fa3-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="26fa3-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="26fa3-168">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-168">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="26fa3-169">`null`Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-169">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="26fa3-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="26fa3-171">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="26fa3-171">OptionsBuilder API</span></span>

<span data-ttu-id="26fa3-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="26fa3-173">`OptionsBuilder` adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-173">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="26fa3-174">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-174">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="26fa3-175">`OptionsBuilder`[Seçenekler doğrulama](#val) bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-175">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="26fa3-176">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="26fa3-176">Use DI services to configure options</span></span>

<span data-ttu-id="26fa3-177">Seçenekleri iki şekilde yapılandırırken, hizmetlere bağımlılık ekleme işleminden erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-177">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="26fa3-178">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-178">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="26fa3-179">`OptionsBuilder<TOptions>` , seçenekleri yapılandırmak için en fazla beş hizmet kullanılmasına izin veren [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="26fa3-179">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="26fa3-180">Veya uygulayan bir tür oluşturun <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ve türü bir hizmet olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-180">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="26fa3-181">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-181">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="26fa3-182">Bir tür oluşturmak,, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)çağrılırken çerçevenin yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-182">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="26fa3-183">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="26fa3-183">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="26fa3-184">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="26fa3-184">Options validation</span></span>

<span data-ttu-id="26fa3-185">Seçenekler doğrulaması seçenek değerlerinin doğrulanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-185">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="26fa3-186">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="26fa3-186">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="26fa3-187">Aşağıdaki sınıf `"MyConfig"` yapılandırma bölümüne bağlanır ve birkaç `DataAnnotations` kural uygular:</span><span class="sxs-lookup"><span data-stu-id="26fa3-187">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="26fa3-188">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="26fa3-188">The following code:</span></span>

* <span data-ttu-id="26fa3-189"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>Sınıfına bağlanan bir [seçenekoluşturucu Oluşturucu \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) almak için çağırır `MyConfigOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="26fa3-190"><xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A>Kullanarak doğrulamayı etkinleştirmek için çağırır `DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-190">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="26fa3-191">`ValidateDataAnnotations`Genişletme yöntemi, [Microsoft. Extensions. Options. dataaçıklamalarda](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet paketinde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-191">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="26fa3-192">SDK kullanan Web uygulamaları için `Microsoft.NET.Sdk.Web` Bu pakete, dolaylı olarak paylaşılan çerçeveden başvurulur.</span><span class="sxs-lookup"><span data-stu-id="26fa3-192">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="26fa3-193">Aşağıdaki kod yapılandırma değerlerini veya doğrulama hatalarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="26fa3-193">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="26fa3-194">Aşağıdaki kod, bir temsilci kullanarak daha karmaşık bir doğrulama kuralı uygular:</span><span class="sxs-lookup"><span data-stu-id="26fa3-194">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="26fa3-195">Karmaşık doğrulama için ıvalidateoptions</span><span class="sxs-lookup"><span data-stu-id="26fa3-195">IValidateOptions for complex validation</span></span>

<span data-ttu-id="26fa3-196">Aşağıdaki sınıf şunları uygular <xref:Microsoft.Extensions.Options.IValidateOptions`1> :</span><span class="sxs-lookup"><span data-stu-id="26fa3-196">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="26fa3-197">`IValidateOptions` doğrulama kodunu `StartUp` bir sınıfa ve sınıfına taşımaya izin vermez.</span><span class="sxs-lookup"><span data-stu-id="26fa3-197">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="26fa3-198">Önceki kodu kullanarak, doğrulama ' de `Startup.ConfigureServices` aşağıdaki kodla etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-198">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="26fa3-199">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-199">Options post-configuration</span></span>

<span data-ttu-id="26fa3-200">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-200">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="26fa3-201">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-201">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="26fa3-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> , adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="26fa3-203"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="26fa3-203">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="26fa3-204">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="26fa3-204">Accessing options during startup</span></span>

<span data-ttu-id="26fa3-205"><xref:Microsoft.Extensions.Options.IOptions%601> ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-205"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="26fa3-206"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-206">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="26fa3-207">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-207">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="26fa3-208">Options.ConfigurationExtensions NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="26fa3-208">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="26fa3-209">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine ASP.NET Core uygulamalarda örtülü olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="26fa3-209">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="26fa3-210">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-210">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="26fa3-211">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="26fa3-211">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="26fa3-212">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-212">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="26fa3-213">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="26fa3-213">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="26fa3-214">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-214">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="26fa3-215">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-215">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="26fa3-216">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26fa3-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26fa3-217">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="26fa3-217">Prerequisites</span></span>

<span data-ttu-id="26fa3-218">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft.Extensions.Options.Configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-218">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="26fa3-219">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="26fa3-219">Options interfaces</span></span>

<span data-ttu-id="26fa3-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> , seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="26fa3-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="26fa3-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="26fa3-222">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="26fa3-222">Change notifications</span></span>
* [<span data-ttu-id="26fa3-223">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-223">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="26fa3-224">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-224">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="26fa3-225">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="26fa3-225">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="26fa3-226">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-226">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="26fa3-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="26fa3-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="26fa3-228">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-228">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="26fa3-229">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="26fa3-229">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="26fa3-230">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-230">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="26fa3-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> , <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="26fa3-232">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="26fa3-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="26fa3-233">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-233">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="26fa3-234"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-234">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="26fa3-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="26fa3-236">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="26fa3-236">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="26fa3-237"><xref:Microsoft.Extensions.Options.IOptions%601> , seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-237"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="26fa3-238">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-238">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="26fa3-239"><xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-239">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="26fa3-240">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="26fa3-240">General options configuration</span></span>

<span data-ttu-id="26fa3-241">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-241">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="26fa3-242">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-242">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="26fa3-243">Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-243">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="26fa3-244">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-244">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="26fa3-245">`Option2` , özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="26fa3-245">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="26fa3-246">`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-246">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="26fa3-247">Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="26fa3-247">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="26fa3-248">Örneğin dosyadaki *appsettings.js* , ve değerlerini belirtir `option1` `option2` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-248">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="26fa3-249">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-249">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="26fa3-250"><xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="26fa3-250">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="26fa3-251">İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-251">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="26fa3-252">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-252">Configure simple options with a delegate</span></span>

<span data-ttu-id="26fa3-253">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-253">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="26fa3-254">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-254">Use a delegate to set options values.</span></span> <span data-ttu-id="26fa3-255">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-255">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="26fa3-256">Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-256">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="26fa3-257">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-257">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-258">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="26fa3-258">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-259">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-259">You can add multiple configuration providers.</span></span> <span data-ttu-id="26fa3-260">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-260">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="26fa3-261">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="26fa3-261">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="26fa3-262">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="26fa3-262">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="26fa3-263">Yukarıdaki örnekte, `Option1` ve değerlerinin `Option2` her ikisi de *appsettings.js*' de belirtilmiştir, ancak `Option1` ve değerleri `Option2` yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-263">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="26fa3-264">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-264">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="26fa3-265">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-265">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="26fa3-266">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="26fa3-266">Suboptions configuration</span></span>

<span data-ttu-id="26fa3-267">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-267">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="26fa3-268">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-268">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="26fa3-269">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-269">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="26fa3-270">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-270">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="26fa3-271">Örneğin, özelliği, `MyOptions.Option1` `Option1` `option1` \* üzerindeappsettings.js\*özelliğinden okunan anahtara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-271">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="26fa3-272">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-272">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="26fa3-273">`MySubOptions` `subsection` Dosyadaki *appsettings.js* bölümüne bağlanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-273">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="26fa3-274">`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-274">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="26fa3-275">Örneğin dosyasındaki *appsettings.js* , `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-275">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="26fa3-276">`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="26fa3-276">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="26fa3-277">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-277">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="26fa3-278">Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-278">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="26fa3-279">Seçeneklere ekleme</span><span class="sxs-lookup"><span data-stu-id="26fa3-279">Options injection</span></span>

<span data-ttu-id="26fa3-280">Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-280">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="26fa3-281">Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="26fa3-281">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="26fa3-282">RazorYönergeyle bir sayfa veya MVC görünümü [`@inject`](xref:mvc/views/razor#inject) Razor .</span><span class="sxs-lookup"><span data-stu-id="26fa3-282">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="26fa3-283">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="26fa3-283">A page or view model.</span></span>

<span data-ttu-id="26fa3-284">Örnek uygulamadan alınan aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-284">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="26fa3-285">Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-285">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="26fa3-286">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-286">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="26fa3-288">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="26fa3-288">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="26fa3-289">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-289">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="26fa3-290"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>' Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-290">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="26fa3-291">Ve arasındaki fark `IOptionsMonitor` `IOptionsSnapshot` şudur:</span><span class="sxs-lookup"><span data-stu-id="26fa3-291">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="26fa3-292">`IOptionsMonitor` , özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="26fa3-292">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="26fa3-293">`IOptionsSnapshot` kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar `IOptionsSnapshot<T>` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-293">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="26fa3-294">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-294">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="26fa3-295">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> değişiklikler \* üzerindeappsettings.js\* sonrasında yeni bir oluşturma Işlemi gösterir (*sayfa/dizin. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="26fa3-295">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="26fa3-296">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar dosyadaki *appsettings.js* tarafından belirtilen sabit değerler döndürür.</span><span class="sxs-lookup"><span data-stu-id="26fa3-296">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="26fa3-297">Aşağıdaki görüntüde `option1` `option2` *appsettings.jsdosya üzerinde* yüklenen ilk ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-297">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="26fa3-298">Dosyadaki *appsettings.js* değerlerini `value1_from_json UPDATED` ve ile değiştirin `200` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-298">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="26fa3-299">*appsettings.js* dosyaya kaydedin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-299">Save the *appsettings.json* file.</span></span> <span data-ttu-id="26fa3-300">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="26fa3-300">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="26fa3-301">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="26fa3-301">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="26fa3-302">İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-302">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="26fa3-303">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-303">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="26fa3-304">Örnek uygulamada, adlandırılmış seçenekler, Yapılandırılaenamedolarını çağıran [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir [ \<TOptions> . ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-304">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="26fa3-305">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-305">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="26fa3-306">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-306">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="26fa3-307">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-307">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="26fa3-308">`named_options_1` değerler, *appsettings.js* dosyadan yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-308">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="26fa3-309">`named_options_2` değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-309">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="26fa3-310">`named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-310">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="26fa3-311">`Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-311">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="26fa3-312">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-312">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="26fa3-313">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-313">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="26fa3-314">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-314">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="26fa3-315">Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-315">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="26fa3-316">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-316">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="26fa3-317">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-317">All options are named instances.</span></span> <span data-ttu-id="26fa3-318">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-318">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="26fa3-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="26fa3-320">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-320">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="26fa3-321">Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="26fa3-321">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="26fa3-322">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="26fa3-322">OptionsBuilder API</span></span>

<span data-ttu-id="26fa3-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="26fa3-324">`OptionsBuilder` adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-324">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="26fa3-325">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-325">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="26fa3-326">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="26fa3-326">Use DI services to configure options</span></span>

<span data-ttu-id="26fa3-327">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="26fa3-327">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="26fa3-328">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-328">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="26fa3-329">[Seçenekseçenekleri \<TOptions> Oluşturucusu](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza imkan tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="26fa3-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="26fa3-330"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="26fa3-330">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="26fa3-331">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-331">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="26fa3-332">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-332">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="26fa3-333">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="26fa3-333">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="26fa3-334">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="26fa3-334">Options validation</span></span>

<span data-ttu-id="26fa3-335">Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-335">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="26fa3-336">`Validate` `true` Seçenekler geçerliyse ve geçerli değillerse, döndüren bir doğrulama yöntemiyle çağırın `false` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-336">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="26fa3-337">Önceki örnekte, adlandırılmış seçenekler örneği olarak ayarlanır `optionalOptionsName` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-337">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="26fa3-338">Varsayılan Seçenekler örneği `Options.DefaultName` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-338">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="26fa3-339">Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-339">Validation runs when the options instance is created.</span></span> <span data-ttu-id="26fa3-340">Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-340">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="26fa3-341">Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-341">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="26fa3-342">Örneğin, Seçenekler `IOptionsSnapshot` ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-342">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="26fa3-343">Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-343">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="26fa3-344">`Validate`Yöntemi bir kabul eder `Func<TOptions, bool>` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-344">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="26fa3-345">Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>` izin veren uygulayın:</span><span class="sxs-lookup"><span data-stu-id="26fa3-345">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="26fa3-346">Birden çok seçenek türünün doğrulanması: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="26fa3-346">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="26fa3-347">Başka bir seçenek türüne bağlı olan doğrulama: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="26fa3-347">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="26fa3-348">`IValidateOptions` doğrular</span><span class="sxs-lookup"><span data-stu-id="26fa3-348">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="26fa3-349">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="26fa3-349">A specific named options instance.</span></span>
* <span data-ttu-id="26fa3-350">Olduğunda tüm seçenekler `name` `null` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-350">All options when `name` is `null`.</span></span>

<span data-ttu-id="26fa3-351">Arabirimini uygulamanızdan döndürün `ValidateOptionsResult` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-351">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="26fa3-352">Veri eki tabanlı doğrulama, üzerinde yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-352">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="26fa3-353">`Microsoft.Extensions.Options.DataAnnotations` , [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-353">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="26fa3-354">Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-354">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="26fa3-355">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-355">Options post-configuration</span></span>

<span data-ttu-id="26fa3-356">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-356">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="26fa3-357">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-357">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="26fa3-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> , adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="26fa3-359"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="26fa3-359">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="26fa3-360">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="26fa3-360">Accessing options during startup</span></span>

<span data-ttu-id="26fa3-361"><xref:Microsoft.Extensions.Options.IOptions%601> ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-361"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="26fa3-362"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-362">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="26fa3-363">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-363">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="26fa3-364">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-364">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="26fa3-365">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="26fa3-365">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="26fa3-366">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): senaryolar (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-366">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="26fa3-367">[Kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): uygulamanın farklı bölümlerinin ayarları birbirlerine bağımlı değil veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="26fa3-367">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="26fa3-368">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-368">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="26fa3-369">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-369">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="26fa3-370">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26fa3-370">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26fa3-371">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="26fa3-371">Prerequisites</span></span>

<span data-ttu-id="26fa3-372">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft.Extensions.Options.Configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-372">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="26fa3-373">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="26fa3-373">Options interfaces</span></span>

<span data-ttu-id="26fa3-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> , seçenekleri almak ve örnekler için seçenek bildirimlerini yönetmek için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="26fa3-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="26fa3-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="26fa3-376">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="26fa3-376">Change notifications</span></span>
* [<span data-ttu-id="26fa3-377">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-377">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="26fa3-378">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-378">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="26fa3-379">Seçmeli seçenekler geçersiz kılma ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )</span><span class="sxs-lookup"><span data-stu-id="26fa3-379">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="26fa3-380">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-380">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="26fa3-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="26fa3-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="26fa3-382">Tek bir metodu vardır <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-382">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="26fa3-383">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="26fa3-383">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="26fa3-384">Ve arasında ayrım yapar ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-384">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="26fa3-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> , <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> örnekleri önbelleğe almak için tarafından kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="26fa3-386">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ).</span><span class="sxs-lookup"><span data-stu-id="26fa3-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="26fa3-387">Değerler ile el ile tanıtılamaz <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-387">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="26fa3-388"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Yöntemi, tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-388">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="26fa3-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="26fa3-390">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="26fa3-390">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="26fa3-391"><xref:Microsoft.Extensions.Options.IOptions%601> , seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-391"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="26fa3-392">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-392">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="26fa3-393"><xref:Microsoft.Extensions.Options.IOptions%601>Zaten <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-393">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="26fa3-394">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="26fa3-394">General options configuration</span></span>

<span data-ttu-id="26fa3-395">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-395">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="26fa3-396">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-396">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="26fa3-397">Aşağıdaki sınıfının, `MyOptions` , ve iki özelliği vardır `Option1` `Option2` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-397">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="26fa3-398">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-398">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="26fa3-399">`Option2` , özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="26fa3-399">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="26fa3-400">`MyOptions`Sınıfı, ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-400">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="26fa3-401">Aşağıdaki sayfa modeli, ayarlarına erişmek için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="26fa3-401">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="26fa3-402">Örneğin dosyadaki *appsettings.js* , ve değerlerini belirtir `option1` `option2` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-402">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="26fa3-403">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-403">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="26fa3-404"><xref:System.Configuration.ConfigurationBuilder>Bir ayarlar dosyasından yükleme seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="26fa3-404">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="26fa3-405">İle ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-405">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="26fa3-406">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-406">Configure simple options with a delegate</span></span>

<span data-ttu-id="26fa3-407">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-407">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="26fa3-408">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-408">Use a delegate to set options values.</span></span> <span data-ttu-id="26fa3-409">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-409">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="26fa3-410">Aşağıdaki kodda, hizmet kapsayıcısına ikinci bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-410">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="26fa3-411">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-411">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-412">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="26fa3-412">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="26fa3-413">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-413">You can add multiple configuration providers.</span></span> <span data-ttu-id="26fa3-414">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-414">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="26fa3-415">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="26fa3-415">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="26fa3-416">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> , hizmet kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="26fa3-416">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="26fa3-417">Yukarıdaki örnekte, `Option1` ve değerlerinin `Option2` her ikisi de *appsettings.js*' de belirtilmiştir, ancak `Option1` ve değerleri `Option2` yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-417">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="26fa3-418">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="26fa3-418">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="26fa3-419">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-419">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="26fa3-420">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="26fa3-420">Suboptions configuration</span></span>

<span data-ttu-id="26fa3-421">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-421">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="26fa3-422">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-422">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="26fa3-423">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-423">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="26fa3-424">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun bir yapılandırma anahtarına bağlanır `property[:sub-property:]` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-424">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="26fa3-425">Örneğin, özelliği, `MyOptions.Option1` `Option1` `option1` \* üzerindeappsettings.js\*özelliğinden okunan anahtara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-425">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="26fa3-426">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü bir <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-426">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="26fa3-427">`MySubOptions` `subsection` Dosyadaki *appsettings.js* bölümüne bağlanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-427">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="26fa3-428">`GetSection`Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-428">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="26fa3-429">Örneğin dosyasındaki *appsettings.js* , `subsection` ve için anahtarlar içeren bir üyeyi tanımlar `suboption1` `suboption2` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-429">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="26fa3-430">`MySubOptions`Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2` Seçenekler değerlerini tutmak için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="26fa3-430">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="26fa3-431">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-431">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="26fa3-432">Uygulama çalıştırıldığında, yöntem, alt `OnGet` sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-432">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="26fa3-433">Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-433">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="26fa3-434">Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-434">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="26fa3-435">Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-435">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="26fa3-436">Örnek uygulama, `IOptionsMonitor<MyOptions>` bir yönergeyle nasıl ekleneceğini gösterir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-436">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="26fa3-437">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-437">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="26fa3-439">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="26fa3-439">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="26fa3-440">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-440">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="26fa3-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.</span><span class="sxs-lookup"><span data-stu-id="26fa3-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="26fa3-442">Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-442">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="26fa3-443">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> değişiklikler \* üzerindeappsettings.js\* sonrasında yeni bir oluşturma Işlemi gösterir (*sayfa/dizin. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="26fa3-443">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="26fa3-444">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar dosyadaki *appsettings.js* tarafından belirtilen sabit değerler döndürür.</span><span class="sxs-lookup"><span data-stu-id="26fa3-444">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="26fa3-445">Aşağıdaki görüntüde `option1` `option2` *appsettings.jsdosya üzerinde* yüklenen ilk ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-445">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="26fa3-446">Dosyadaki *appsettings.js* değerlerini `value1_from_json UPDATED` ve ile değiştirin `200` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-446">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="26fa3-447">*appsettings.js* dosyaya kaydedin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-447">Save the *appsettings.json* file.</span></span> <span data-ttu-id="26fa3-448">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="26fa3-448">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="26fa3-449">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="26fa3-449">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="26fa3-450">İle adlandırılmış seçenek desteği <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-450">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="26fa3-451">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-451">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="26fa3-452">Örnek uygulamada, adlandırılmış seçenekler, Yapılandırılaenamedolarını çağıran [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir [ \<TOptions> . ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-452">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="26fa3-453">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-453">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="26fa3-454">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-454">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="26fa3-455">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="26fa3-455">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="26fa3-456">`named_options_1` değerler, *appsettings.js* dosyadan yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-456">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="26fa3-457">`named_options_2` değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-457">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="26fa3-458">`named_options_2`İçin içindeki temsilci `ConfigureServices` `Option1` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-458">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="26fa3-459">`Option2`Sınıfı tarafından sağlanacak varsayılan değer `MyOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-459">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="26fa3-460">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="26fa3-460">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="26fa3-461">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="26fa3-461">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="26fa3-462">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-462">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="26fa3-463">Yöntemine el ile aşağıdaki kodu ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26fa3-463">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="26fa3-464">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-464">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="26fa3-465">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-465">All options are named instances.</span></span> <span data-ttu-id="26fa3-466">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, örneği hedefleme olarak değerlendirilir `Options.DefaultName` `string.Empty` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-466">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="26fa3-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="26fa3-468">Varsayılan uygulamasının, her birini <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="26fa3-468">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="26fa3-469">Adlandırılmış `null` seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="26fa3-469">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="26fa3-470">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="26fa3-470">OptionsBuilder API</span></span>

<span data-ttu-id="26fa3-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> örnekleri yapılandırmak için kullanılır `TOptions` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="26fa3-472">`OptionsBuilder` adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır `AddOptions<TOptions>(string optionsName)` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-472">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="26fa3-473">Seçenekler doğrulaması ve `ConfigureOptions` hizmet bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla kullanılabilir `OptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-473">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="26fa3-474">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="26fa3-474">Use DI services to configure options</span></span>

<span data-ttu-id="26fa3-475">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="26fa3-475">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="26fa3-476">[Options Builder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)'da [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="26fa3-476">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="26fa3-477">[Seçenekseçenekleri \<TOptions> Oluşturucusu](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza imkan tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="26fa3-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="26fa3-478"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ' İ uygulayan veya hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="26fa3-478">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="26fa3-479">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="26fa3-479">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="26fa3-480">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-480">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="26fa3-481">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , belirtilen genel hizmet türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="26fa3-481">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="26fa3-482">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="26fa3-482">Options post-configuration</span></span>

<span data-ttu-id="26fa3-483">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> .</span><span class="sxs-lookup"><span data-stu-id="26fa3-483">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="26fa3-484">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="26fa3-484">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="26fa3-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> , adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="26fa3-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="26fa3-486"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>Tüm yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="26fa3-486">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="26fa3-487">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="26fa3-487">Accessing options during startup</span></span>

<span data-ttu-id="26fa3-488"><xref:Microsoft.Extensions.Options.IOptions%601> ve ' <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> de `Startup.Configure` , hizmetler Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-488"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="26fa3-489"><xref:Microsoft.Extensions.Options.IOptions%601>Veya içinde kullanmayın <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="26fa3-489">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="26fa3-490">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="26fa3-490">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="26fa3-491">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="26fa3-491">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
