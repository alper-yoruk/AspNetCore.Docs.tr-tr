---
title: ASP.NET Çekirdekteki seçenekler deseni
author: rick-anderson
description: ASP.NET Core uygulamalarında ilgili ayar gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665461"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="3a96c-103">ASP.NET Çekirdekteki seçenekler deseni</span><span class="sxs-lookup"><span data-stu-id="3a96c-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a96c-104">Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3a96c-105">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara yalıtıldığında, uygulama iki önemli yazılım mühendisliği ilkesine bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3a96c-106">Yapılandırma ayarlarına bağlı olan [Arabirim Ayırma İlkesi (ISS) veya Kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Senaryoları (sınıflar), yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3a96c-107">[Uygulamanın](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; farklı bölümleri için Endişelerin Ayrılması Ayarları bağımlı değildir veya birbirine bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3a96c-108">Seçenekler, yapılandırma verilerini doğrulamak için bir mekanizma da sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3a96c-109">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3a96c-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a96c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="3a96c-111">Paket</span><span class="sxs-lookup"><span data-stu-id="3a96c-111">Package</span></span>

<span data-ttu-id="3a96c-112">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketi ASP.NET Core uygulamalarında örtülü olarak başvurulabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3a96c-113">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="3a96c-113">Options interfaces</span></span>

<span data-ttu-id="3a96c-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>seçenekleri almak ve örneğin seçenek bildirimlerini yönetmek için `TOptions` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3a96c-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="3a96c-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3a96c-116">Bildirimleri değiştirme</span><span class="sxs-lookup"><span data-stu-id="3a96c-116">Change notifications</span></span>
* [<span data-ttu-id="3a96c-117">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3a96c-118">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3a96c-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3a96c-119">Seçici seçenekler geçersiz<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>liği ( )</span><span class="sxs-lookup"><span data-stu-id="3a96c-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3a96c-120">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3a96c-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenekler örneklerinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3a96c-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3a96c-122">Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir yöntemi var.</span><span class="sxs-lookup"><span data-stu-id="3a96c-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3a96c-123">Varsayılan uygulama tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kayıtlı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> alır ve tüm yapılandırmaları ilk olarak çalıştırın, ardından yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="3a96c-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3a96c-124">Bu ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> sadece <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uygun arabirimi çağırır ayırt eder.</span><span class="sxs-lookup"><span data-stu-id="3a96c-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3a96c-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>`TOptions` örnekleri <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> önbelleğe almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3a96c-126">Değerin <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> yeniden hesaplanması için monitördeki seçenekler örneklerini geçersiz kılır (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3a96c-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3a96c-127">Değerler ile el ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>tanıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3a96c-128">Yöntem, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> adlandırılmış tüm örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3a96c-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3a96c-130">Daha fazla bilgi için [IOptionsSnapshot bölümüyle yapılandırma verilerini yeniden yükle](#reload-configuration-data-with-ioptionssnapshot) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3a96c-131"><xref:Microsoft.Extensions.Options.IOptions%601>seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3a96c-132">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryoları <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>desteklemez.</span><span class="sxs-lookup"><span data-stu-id="3a96c-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3a96c-133">Arabirimi zaten <xref:Microsoft.Extensions.Options.IOptions%601> kullanan ve sağladığı <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>senaryoları gerektirmeyen varolan çerçeveleri ve kitaplıkları kullanmaya devam edebilirsiniz. <xref:Microsoft.Extensions.Options.IOptions%601></span><span class="sxs-lookup"><span data-stu-id="3a96c-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3a96c-134">Genel seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3a96c-134">General options configuration</span></span>

<span data-ttu-id="3a96c-135">Genel seçenekler yapılandırması örnek uygulamada Örnek 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="3a96c-136">Bir seçenek sınıfı, ortak parametresiz bir oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3a96c-137">Aşağıdaki sınıf, `MyOptions`iki özelliği `Option1` vardır `Option2`ve .</span><span class="sxs-lookup"><span data-stu-id="3a96c-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3a96c-138">Varsayılan değerleri ayarlamak isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf `Option1`oluşturucusu varsayılan değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3a96c-139">`Option2`özelliği doğrudan başharfe alarak belirlenen varsayılan bir değere sahiptir (*Modeller/MyOptions.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3a96c-140">Sınıf, `MyOptions` yapılandırmaya <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> bağlı ve hizmet kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3a96c-141">Aşağıdaki sayfa modeli <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ayarlara erişmek için [constructor bağımlılık enjeksiyonu](xref:mvc/controllers/dependency-injection) kullanır (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3a96c-142">Örneğin *appsettings.json* dosyası için `option1` değerleri belirtir `option2`ve:</span><span class="sxs-lookup"><span data-stu-id="3a96c-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3a96c-143">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3a96c-144">Ayarlar dosyasından <xref:System.Configuration.ConfigurationBuilder> seçenekler yapılandırmasını yüklemek için özel bir madde kullanırken, temel yolun doğru ayarlandığından onaylayın:</span><span class="sxs-lookup"><span data-stu-id="3a96c-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3a96c-145">Ayarlar dosyasından seçenekler yapılandırmasını ayarlarken temel yolu açıkça <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlamak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3a96c-146">Bir temsilci ile basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3a96c-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="3a96c-147">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada Örnek 2 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="3a96c-148">Seçenekler değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-148">Use a delegate to set options values.</span></span> <span data-ttu-id="3a96c-149">Örnek uygulama `MyOptionsWithDelegateConfig` sınıfı kullanır ( Modeller */MyOptionsWithDelegeConfig.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3a96c-150">Aşağıdaki kodda, servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına ikinci bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3a96c-151">Bu ile bağlama yapılandırmak için `MyOptionsWithDelegateConfig`bir temsilci kullanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3a96c-152">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a96c-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3a96c-153">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="3a96c-154">Yapılandırma sağlayıcıları NuGet paketlerinden edinilebilir ve kayıtlı oldukları sırada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3a96c-155">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3a96c-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3a96c-156">Her arama <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="3a96c-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3a96c-157">Önceki örnekte, değerleri `Option1` ve `Option2` her ikisi de *appsettings.json*belirtilir, `Option1` `Option2` ancak değerleri ve yapılandırılan temsilci tarafından geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="3a96c-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3a96c-158">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, belirtilen son yapılandırma kaynağı *kazanır* ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3a96c-159">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3a96c-160">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3a96c-160">Suboptions configuration</span></span>

<span data-ttu-id="3a96c-161">Alt seçenek yapılandırması örnek uygulamada Örnek 3 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="3a96c-162">Uygulamalar, uygulamadaki belirli senaryo gruplarıyla (sınıflar) ilgili seçenekler sınıfları oluşturmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3a96c-163">Uygulamanın yapılandırma değerleri gerektiren bölümleri yalnızca kullandıkları yapılandırma değerlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3a96c-164">Seçenekleri yapılandırmaya bağlarken, seçenekler türündeki her özellik formun `property[:sub-property:]`yapılandırma anahtarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3a96c-165">Örneğin, `MyOptions.Option1` özellik `Option1` *appsettings.json*özelliğinden `option1` okunan anahtara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3a96c-166">Aşağıdaki kodda, hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına üçüncü bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3a96c-167">*Appsettings.json* dosyasının `MySubOptions` bölümüne `subsection` bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3a96c-168">Yöntem `GetSection` <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3a96c-169">Örneğin *appsettings.json* dosyası için `subsection` `suboption1` anahtarları olan bir `suboption2`üye tanımlar ve:</span><span class="sxs-lookup"><span data-stu-id="3a96c-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3a96c-170">Sınıf `MySubOptions` özellikleri tanımlar `SubOption1` ve `SubOption2`, seçenekleri değerlerini tutmak için *(Modeller / MySubOptions.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3a96c-171">Sayfa modelinin `OnGet` yöntemi seçenekleri değerleri *(Pages/Index.cshtml.cs)* ile bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3a96c-172">Uygulama çalıştırıldığında, `OnGet` yöntem alt seçenek sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="3a96c-173">Seçenekler enjeksiyon</span><span class="sxs-lookup"><span data-stu-id="3a96c-173">Options injection</span></span>

<span data-ttu-id="3a96c-174">Seçenekler enjeksiyon örnek uygulamada Örnek 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="3a96c-175">Enjekte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> edin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="3a96c-176">Razor direktifi [`@inject`](xref:mvc/views/razor#inject) ile bir Razor sayfası veya MVC görünümü.</span><span class="sxs-lookup"><span data-stu-id="3a96c-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="3a96c-177">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="3a96c-177">A page or view model.</span></span>

<span data-ttu-id="3a96c-178">Örnek uygulamadan aşağıdaki örnek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline enjekte edilir (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3a96c-179">Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl enjekte edilebildiğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3a96c-180">Uygulama çalıştırıldığında, seçenekler değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Opsiyon değerleri Option1: value1_from_json ve Option2: -1 modelden ve görünüme enjeksiyon ile yüklenir.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3a96c-182">IOptionsSnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="3a96c-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3a96c-183">Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yüklemek örnek uygulamada Örnek 5'te gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="3a96c-184">Kullanma, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçenekler istek kullanım ömrü boyunca erişildiğinde ve önbelleğe aldığında istek başına bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3a96c-185">Arasındaki `IOptionsMonitor` fark `IOptionsSnapshot` ve şudur:</span><span class="sxs-lookup"><span data-stu-id="3a96c-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="3a96c-186">`IOptionsMonitor`geçerli seçenek değerlerini herhangi bir zamanda alan ve özellikle singleton bağımlılıklarında kullanışlı olan [tek tonluk](xref:fundamentals/dependency-injection#singleton) bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="3a96c-187">`IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduruldýığı sınırdaki seçeneklerin anlık görüntüsünü saÄ `IOptionsSnapshot<T>` lıyor.</span><span class="sxs-lookup"><span data-stu-id="3a96c-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="3a96c-188">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklar ile kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="3a96c-189">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* değişikliklerden sonra yeni bir yeninin nasıl oluşturulduğunu gösterir (*Pages/Index.cshtml.cs).*</span><span class="sxs-lookup"><span data-stu-id="3a96c-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3a96c-190">Sunucuya gelen birden çok istek, dosya değiştirilene ve yapılandırma yeniden yüklenene kadar *appsettings.json* dosyası tarafından sağlanan sabit değerleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="3a96c-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3a96c-191">Aşağıdaki resim, `option1` *appsettings.json* dosyasından yüklenen başlangıç ve `option2` değerleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3a96c-192">*appsettings.json* dosyasındaki değerleri `value1_from_json UPDATED` değiştirin `200`ve .</span><span class="sxs-lookup"><span data-stu-id="3a96c-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3a96c-193">*appsettings.json* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3a96c-194">Seçeneklerin güncelleştirdiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3a96c-195">IConfigureNamedOptions ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="3a96c-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3a96c-196">Adlandırılmış seçenekler <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> desteği örnek uygulamada Örnek 6 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="3a96c-197">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenekler yapılandırmaları arasında ayrım yapmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3a96c-198">Örnek uygulamada, adlandırılmış [seçenekler,](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) [ConfigureNamedOptions\<TOptions> çağıran OptionsServiceCollectionExtensions.Configure ile bildirilir. Uzatma yöntemini yapılandırın.](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)</span><span class="sxs-lookup"><span data-stu-id="3a96c-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="3a96c-199">Adlandırılmış seçenekler büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3a96c-200">Örnek uygulama ( <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> *Pages/Index.cshtml.cs)* ile adlandırılmış seçeneklere erişir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3a96c-201">Örnek uygulama çalıştırılırken, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3a96c-202">`named_options_1`değerler, *appsettings.json* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3a96c-203">`named_options_2`değerler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3a96c-204">Delege `named_options_2` `ConfigureServices` için `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3a96c-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3a96c-205">Sınıf tarafından `Option2` sağlanan varsayılan değer. `MyOptions`</span><span class="sxs-lookup"><span data-stu-id="3a96c-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3a96c-206">YapılandırmaTüm yöntemiyle tüm seçenekleri yapılandır</span><span class="sxs-lookup"><span data-stu-id="3a96c-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3a96c-207">Tüm seçenekler örneklerini yöntemle yapılandırın. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*></span><span class="sxs-lookup"><span data-stu-id="3a96c-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3a96c-208">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örnekleri için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3a96c-209">`Startup.ConfigureServices` Yönteme el ile aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3a96c-210">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu üretir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3a96c-211">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-211">All options are named instances.</span></span> <span data-ttu-id="3a96c-212">Varolan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> `Options.DefaultName` örnekler, örneği hedefleyen olarak kabul `string.Empty`edilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3a96c-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>ayrıca <xref:Microsoft.Extensions.Options.IConfigureOptions%601>uygular.</span><span class="sxs-lookup"><span data-stu-id="3a96c-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3a96c-214">Varsayılan uygulama her <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun kullanmak için mantık vardır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3a96c-215">`null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek (ve<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanmak) için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3a96c-216">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="3a96c-216">OptionsBuilder API</span></span>

<span data-ttu-id="3a96c-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>`TOptions` örnekleri yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3a96c-218">`OptionsBuilder`sonraki tüm aramalarda görünmek yerine ilk `AddOptions<TOptions>(string optionsName)` çağrıya yalnızca tek bir parametre olarak adlandırılmış seçenekleri oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3a96c-219">Seçenekler doğrulaması `ConfigureOptions` ve hizmet bağımlılıklarını kabul eden `OptionsBuilder`aşırı yüklemeler yalnızca .</span><span class="sxs-lookup"><span data-stu-id="3a96c-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3a96c-220">Seçenekleri yapılandırmak için DI hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="3a96c-220">Use DI services to configure options</span></span>

<span data-ttu-id="3a96c-221">Seçenekleri iki şekilde yapılandırırken bağımlılık enjeksiyonundan diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="3a96c-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3a96c-222">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) [üzerinde Yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3a96c-223">`OptionsBuilder<TOptions>`seçenekleri yapılandırmak için en fazla beş hizmetkullanımına izin [veren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yapılandırma aşırı yükleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="3a96c-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3a96c-224">Uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünüzü oluşturun ve türü hizmet olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3a96c-225">Bir hizmet oluşturmak daha karmaşık olduğundan, [Yapılandırma'ya](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)bir yapılandırma temsilcisi geçmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3a96c-226">Kendi türünüzü oluşturmak, [Yapıl'ı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)kullandığınızda çerçevenin sizin için yaptığıyla eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3a96c-227">[Yapıyı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) Çağırma, belirtilen genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>hizmet türlerini kabul eden bir oluşturucuya sahip geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3a96c-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="3a96c-228">Seçenekler doğrulama</span><span class="sxs-lookup"><span data-stu-id="3a96c-228">Options validation</span></span>

<span data-ttu-id="3a96c-229">Seçenekler doğrulaması, seçenekler yapılandırıldığınızda seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="3a96c-230">Seçenekler `Validate` geçerliyse ve `false` geçerli `true` değilse döndüren bir doğrulama yöntemiyle arama:</span><span class="sxs-lookup"><span data-stu-id="3a96c-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3a96c-231">Önceki örnek, adlandırılmış seçenekler `optionalOptionsName`örneğini .</span><span class="sxs-lookup"><span data-stu-id="3a96c-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="3a96c-232">Varsayılan seçenekler örneği. `Options.DefaultName`</span><span class="sxs-lookup"><span data-stu-id="3a96c-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="3a96c-233">Doğrulama, seçenekler örneği oluşturulduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="3a96c-234">Bir seçenek örneğinin, ilk erişime de doğrulamayı geçirmesi garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3a96c-235">Seçenekler doğrulaması, seçenekler örneği oluşturulduktan sonra seçenekler intibaklarına karşı koruma zedler.</span><span class="sxs-lookup"><span data-stu-id="3a96c-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="3a96c-236">Örneğin, `IOptionsSnapshot` seçenekler ilk erişildiğinde istek başına bir kez oluşturulur ve doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="3a96c-237">Seçenekler, `IOptionsSnapshot` aynı istek için sonraki erişim *denemelerinde*yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="3a96c-238">Yöntem `Validate` bir `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="3a96c-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="3a96c-239">Doğrulamayı tam olarak özelleştirmek için aşağıdakileri uygulayın: `IValidateOptions<TOptions>`</span><span class="sxs-lookup"><span data-stu-id="3a96c-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="3a96c-240">Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="3a96c-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="3a96c-241">Başka bir seçenek türüne bağlı doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="3a96c-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="3a96c-242">`IValidateOptions`Doğrulama:</span><span class="sxs-lookup"><span data-stu-id="3a96c-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="3a96c-243">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="3a96c-243">A specific named options instance.</span></span>
* <span data-ttu-id="3a96c-244">Tüm seçenekler `name` `null`ne zaman .</span><span class="sxs-lookup"><span data-stu-id="3a96c-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="3a96c-245">Arabirimi `ValidateOptionsResult` uygulamanızdan bir döndürme:</span><span class="sxs-lookup"><span data-stu-id="3a96c-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="3a96c-246">Veri Ek Açıklama tabanlı doğrulama [microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> yöntemi arayarak `OptionsBuilder<TOptions>`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="3a96c-247">`Microsoft.Extensions.Options.DataAnnotations`ASP.NET Core uygulamalarında dolaylı olarak başvurulmusur.</span><span class="sxs-lookup"><span data-stu-id="3a96c-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
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

<span data-ttu-id="3a96c-248">Istekli doğrulama (başlangıçta hızlı başarısız) gelecekteki bir sürüm için düşünülmektedir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="3a96c-249">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-249">Options post-configuration</span></span>

<span data-ttu-id="3a96c-250">Yapılandırma sonrası'nı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>' ile ayarlama</span><span class="sxs-lookup"><span data-stu-id="3a96c-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3a96c-251">Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra yapılandırma sonrası çalışır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3a96c-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>adlı seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3a96c-253">Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="3a96c-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3a96c-254">Başlatma sırasında seçeneklere erişim</span><span class="sxs-lookup"><span data-stu-id="3a96c-254">Accessing options during startup</span></span>

<span data-ttu-id="3a96c-255"><xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> hizmetler `Configure` yöntem `Startup.Configure`yürütülmeden önce inşa olduğundan, kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3a96c-256">Kullanmayın <xref:Microsoft.Extensions.Options.IOptions%601> ya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> da `Startup.ConfigureServices`içinde.</span><span class="sxs-lookup"><span data-stu-id="3a96c-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3a96c-257">Hizmet kayıtlarının sıralanması nedeniyle tutarsız bir seçenek durumu oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3a96c-258">Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3a96c-259">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara yalıtıldığında, uygulama iki önemli yazılım mühendisliği ilkesine bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3a96c-260">Yapılandırma ayarlarına bağlı olan [Arabirim Ayırma İlkesi (ISS) veya Kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Senaryoları (sınıflar), yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3a96c-261">[Uygulamanın](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; farklı bölümleri için Endişelerin Ayrılması Ayarları bağımlı değildir veya birbirine bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3a96c-262">Seçenekler, yapılandırma verilerini doğrulamak için bir mekanizma da sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3a96c-263">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3a96c-264">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a96c-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a96c-265">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="3a96c-265">Prerequisites</span></span>

<span data-ttu-id="3a96c-266">[Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun veya [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3a96c-267">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="3a96c-267">Options interfaces</span></span>

<span data-ttu-id="3a96c-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>seçenekleri almak ve örneğin seçenek bildirimlerini yönetmek için `TOptions` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3a96c-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="3a96c-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3a96c-270">Bildirimleri değiştirme</span><span class="sxs-lookup"><span data-stu-id="3a96c-270">Change notifications</span></span>
* [<span data-ttu-id="3a96c-271">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3a96c-272">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3a96c-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3a96c-273">Seçici seçenekler geçersiz<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>liği ( )</span><span class="sxs-lookup"><span data-stu-id="3a96c-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3a96c-274">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3a96c-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenekler örneklerinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3a96c-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3a96c-276">Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir yöntemi var.</span><span class="sxs-lookup"><span data-stu-id="3a96c-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3a96c-277">Varsayılan uygulama tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kayıtlı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> alır ve tüm yapılandırmaları ilk olarak çalıştırın, ardından yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="3a96c-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3a96c-278">Bu ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> sadece <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uygun arabirimi çağırır ayırt eder.</span><span class="sxs-lookup"><span data-stu-id="3a96c-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3a96c-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>`TOptions` örnekleri <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> önbelleğe almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3a96c-280">Değerin <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> yeniden hesaplanması için monitördeki seçenekler örneklerini geçersiz kılır (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3a96c-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3a96c-281">Değerler ile el ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>tanıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3a96c-282">Yöntem, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> adlandırılmış tüm örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3a96c-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3a96c-284">Daha fazla bilgi için [IOptionsSnapshot bölümüyle yapılandırma verilerini yeniden yükle](#reload-configuration-data-with-ioptionssnapshot) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3a96c-285"><xref:Microsoft.Extensions.Options.IOptions%601>seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3a96c-286">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryoları <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>desteklemez.</span><span class="sxs-lookup"><span data-stu-id="3a96c-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3a96c-287">Arabirimi zaten <xref:Microsoft.Extensions.Options.IOptions%601> kullanan ve sağladığı <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>senaryoları gerektirmeyen varolan çerçeveleri ve kitaplıkları kullanmaya devam edebilirsiniz. <xref:Microsoft.Extensions.Options.IOptions%601></span><span class="sxs-lookup"><span data-stu-id="3a96c-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3a96c-288">Genel seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3a96c-288">General options configuration</span></span>

<span data-ttu-id="3a96c-289">Genel seçenekler yapılandırması örnek uygulamada Örnek 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="3a96c-290">Bir seçenek sınıfı, ortak parametresiz bir oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3a96c-291">Aşağıdaki sınıf, `MyOptions`iki özelliği `Option1` vardır `Option2`ve .</span><span class="sxs-lookup"><span data-stu-id="3a96c-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3a96c-292">Varsayılan değerleri ayarlamak isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf `Option1`oluşturucusu varsayılan değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3a96c-293">`Option2`özelliği doğrudan başharfe alarak belirlenen varsayılan bir değere sahiptir (*Modeller/MyOptions.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3a96c-294">Sınıf, `MyOptions` yapılandırmaya <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> bağlı ve hizmet kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3a96c-295">Aşağıdaki sayfa modeli <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ayarlara erişmek için [constructor bağımlılık enjeksiyonu](xref:mvc/controllers/dependency-injection) kullanır (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3a96c-296">Örneğin *appsettings.json* dosyası için `option1` değerleri belirtir `option2`ve:</span><span class="sxs-lookup"><span data-stu-id="3a96c-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3a96c-297">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3a96c-298">Ayarlar dosyasından <xref:System.Configuration.ConfigurationBuilder> seçenekler yapılandırmasını yüklemek için özel bir madde kullanırken, temel yolun doğru ayarlandığından onaylayın:</span><span class="sxs-lookup"><span data-stu-id="3a96c-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3a96c-299">Ayarlar dosyasından seçenekler yapılandırmasını ayarlarken temel yolu açıkça <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlamak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3a96c-300">Bir temsilci ile basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3a96c-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="3a96c-301">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada Örnek 2 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="3a96c-302">Seçenekler değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-302">Use a delegate to set options values.</span></span> <span data-ttu-id="3a96c-303">Örnek uygulama `MyOptionsWithDelegateConfig` sınıfı kullanır ( Modeller */MyOptionsWithDelegeConfig.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3a96c-304">Aşağıdaki kodda, servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına ikinci bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3a96c-305">Bu ile bağlama yapılandırmak için `MyOptionsWithDelegateConfig`bir temsilci kullanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3a96c-306">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a96c-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3a96c-307">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="3a96c-308">Yapılandırma sağlayıcıları NuGet paketlerinden edinilebilir ve kayıtlı oldukları sırada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3a96c-309">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3a96c-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3a96c-310">Her arama <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="3a96c-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3a96c-311">Önceki örnekte, değerleri `Option1` ve `Option2` her ikisi de *appsettings.json*belirtilir, `Option1` `Option2` ancak değerleri ve yapılandırılan temsilci tarafından geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="3a96c-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3a96c-312">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, belirtilen son yapılandırma kaynağı *kazanır* ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3a96c-313">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3a96c-314">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3a96c-314">Suboptions configuration</span></span>

<span data-ttu-id="3a96c-315">Alt seçenek yapılandırması örnek uygulamada Örnek 3 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="3a96c-316">Uygulamalar, uygulamadaki belirli senaryo gruplarıyla (sınıflar) ilgili seçenekler sınıfları oluşturmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3a96c-317">Uygulamanın yapılandırma değerleri gerektiren bölümleri yalnızca kullandıkları yapılandırma değerlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3a96c-318">Seçenekleri yapılandırmaya bağlarken, seçenekler türündeki her özellik formun `property[:sub-property:]`yapılandırma anahtarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3a96c-319">Örneğin, `MyOptions.Option1` özellik `Option1` *appsettings.json*özelliğinden `option1` okunan anahtara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3a96c-320">Aşağıdaki kodda, hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına üçüncü bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3a96c-321">*Appsettings.json* dosyasının `MySubOptions` bölümüne `subsection` bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3a96c-322">Yöntem `GetSection` <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3a96c-323">Örneğin *appsettings.json* dosyası için `subsection` `suboption1` anahtarları olan bir `suboption2`üye tanımlar ve:</span><span class="sxs-lookup"><span data-stu-id="3a96c-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3a96c-324">Sınıf `MySubOptions` özellikleri tanımlar `SubOption1` ve `SubOption2`, seçenekleri değerlerini tutmak için *(Modeller / MySubOptions.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3a96c-325">Sayfa modelinin `OnGet` yöntemi seçenekleri değerleri *(Pages/Index.cshtml.cs)* ile bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3a96c-326">Uygulama çalıştırıldığında, `OnGet` yöntem alt seçenek sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="3a96c-327">Seçenekler enjeksiyon</span><span class="sxs-lookup"><span data-stu-id="3a96c-327">Options injection</span></span>

<span data-ttu-id="3a96c-328">Seçenekler enjeksiyon örnek uygulamada Örnek 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="3a96c-329">Enjekte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> edin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="3a96c-330">Razor direktifi [`@inject`](xref:mvc/views/razor#inject) ile bir Razor sayfası veya MVC görünümü.</span><span class="sxs-lookup"><span data-stu-id="3a96c-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="3a96c-331">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="3a96c-331">A page or view model.</span></span>

<span data-ttu-id="3a96c-332">Örnek uygulamadan aşağıdaki örnek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir sayfa modeline enjekte edilir (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3a96c-333">Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl enjekte edilebildiğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3a96c-334">Uygulama çalıştırıldığında, seçenekler değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-334">When the app is run, the options values are shown in the rendered page:</span></span>

![Opsiyon değerleri Option1: value1_from_json ve Option2: -1 modelden ve görünüme enjeksiyon ile yüklenir.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3a96c-336">IOptionsSnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="3a96c-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3a96c-337">Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yüklemek örnek uygulamada Örnek 5'te gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="3a96c-338">Kullanma, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçenekler istek kullanım ömrü boyunca erişildiğinde ve önbelleğe aldığında istek başına bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3a96c-339">Arasındaki `IOptionsMonitor` fark `IOptionsSnapshot` ve şudur:</span><span class="sxs-lookup"><span data-stu-id="3a96c-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="3a96c-340">`IOptionsMonitor`geçerli seçenek değerlerini herhangi bir zamanda alan ve özellikle singleton bağımlılıklarında kullanışlı olan [tek tonluk](xref:fundamentals/dependency-injection#singleton) bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="3a96c-341">`IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve nesnenin oluşturulduruldýığı sınırdaki seçeneklerin anlık görüntüsünü saÄ `IOptionsSnapshot<T>` lıyor.</span><span class="sxs-lookup"><span data-stu-id="3a96c-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="3a96c-342">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklar ile kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="3a96c-343">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* değişikliklerden sonra yeni bir yeninin nasıl oluşturulduğunu gösterir (*Pages/Index.cshtml.cs).*</span><span class="sxs-lookup"><span data-stu-id="3a96c-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3a96c-344">Sunucuya gelen birden çok istek, dosya değiştirilene ve yapılandırma yeniden yüklenene kadar *appsettings.json* dosyası tarafından sağlanan sabit değerleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="3a96c-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3a96c-345">Aşağıdaki resim, `option1` *appsettings.json* dosyasından yüklenen başlangıç ve `option2` değerleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3a96c-346">*appsettings.json* dosyasındaki değerleri `value1_from_json UPDATED` değiştirin `200`ve .</span><span class="sxs-lookup"><span data-stu-id="3a96c-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3a96c-347">*appsettings.json* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3a96c-348">Seçeneklerin güncelleştirdiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3a96c-349">IConfigureNamedOptions ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="3a96c-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3a96c-350">Adlandırılmış seçenekler <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> desteği örnek uygulamada Örnek 6 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="3a96c-351">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenekler yapılandırmaları arasında ayrım yapmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3a96c-352">Örnek uygulamada, adlandırılmış [seçenekler,](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) [ConfigureNamedOptions\<TOptions> çağıran OptionsServiceCollectionExtensions.Configure ile bildirilir. Uzatma yöntemini yapılandırın.](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)</span><span class="sxs-lookup"><span data-stu-id="3a96c-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="3a96c-353">Adlandırılmış seçenekler büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3a96c-354">Örnek uygulama ( <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> *Pages/Index.cshtml.cs)* ile adlandırılmış seçeneklere erişir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3a96c-355">Örnek uygulama çalıştırılırken, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3a96c-356">`named_options_1`değerler, *appsettings.json* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3a96c-357">`named_options_2`değerler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3a96c-358">Delege `named_options_2` `ConfigureServices` için `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3a96c-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3a96c-359">Sınıf tarafından `Option2` sağlanan varsayılan değer. `MyOptions`</span><span class="sxs-lookup"><span data-stu-id="3a96c-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3a96c-360">YapılandırmaTüm yöntemiyle tüm seçenekleri yapılandır</span><span class="sxs-lookup"><span data-stu-id="3a96c-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3a96c-361">Tüm seçenekler örneklerini yöntemle yapılandırın. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*></span><span class="sxs-lookup"><span data-stu-id="3a96c-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3a96c-362">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örnekleri için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3a96c-363">`Startup.ConfigureServices` Yönteme el ile aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3a96c-364">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu üretir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3a96c-365">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-365">All options are named instances.</span></span> <span data-ttu-id="3a96c-366">Varolan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> `Options.DefaultName` örnekler, örneği hedefleyen olarak kabul `string.Empty`edilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3a96c-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>ayrıca <xref:Microsoft.Extensions.Options.IConfigureOptions%601>uygular.</span><span class="sxs-lookup"><span data-stu-id="3a96c-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3a96c-368">Varsayılan uygulama her <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun kullanmak için mantık vardır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3a96c-369">`null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek (ve<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanmak) için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3a96c-370">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="3a96c-370">OptionsBuilder API</span></span>

<span data-ttu-id="3a96c-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>`TOptions` örnekleri yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3a96c-372">`OptionsBuilder`sonraki tüm aramalarda görünmek yerine ilk `AddOptions<TOptions>(string optionsName)` çağrıya yalnızca tek bir parametre olarak adlandırılmış seçenekleri oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3a96c-373">Seçenekler doğrulaması `ConfigureOptions` ve hizmet bağımlılıklarını kabul eden `OptionsBuilder`aşırı yüklemeler yalnızca .</span><span class="sxs-lookup"><span data-stu-id="3a96c-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3a96c-374">Seçenekleri yapılandırmak için DI hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="3a96c-374">Use DI services to configure options</span></span>

<span data-ttu-id="3a96c-375">Seçenekleri iki şekilde yapılandırırken bağımlılık enjeksiyonundan diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="3a96c-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3a96c-376">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) [üzerinde Yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3a96c-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) seçenekleri yapılandırmak için en fazla beş hizmet kullanmanıza olanak sağlayan [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aşırı yükleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="3a96c-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3a96c-378">Uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünüzü oluşturun ve türü hizmet olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3a96c-379">Bir hizmet oluşturmak daha karmaşık olduğundan, [Yapılandırma'ya](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)bir yapılandırma temsilcisi geçmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3a96c-380">Kendi türünüzü oluşturmak, [Yapıl'ı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)kullandığınızda çerçevenin sizin için yaptığıyla eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3a96c-381">[Yapıyı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) Çağırma, belirtilen genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>hizmet türlerini kabul eden bir oluşturucuya sahip geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3a96c-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="3a96c-382">Seçenekler doğrulama</span><span class="sxs-lookup"><span data-stu-id="3a96c-382">Options validation</span></span>

<span data-ttu-id="3a96c-383">Seçenekler doğrulaması, seçenekler yapılandırıldığınızda seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="3a96c-384">Seçenekler `Validate` geçerliyse ve `false` geçerli `true` değilse döndüren bir doğrulama yöntemiyle arama:</span><span class="sxs-lookup"><span data-stu-id="3a96c-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="3a96c-385">Önceki örnek, adlandırılmış seçenekler `optionalOptionsName`örneğini .</span><span class="sxs-lookup"><span data-stu-id="3a96c-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="3a96c-386">Varsayılan seçenekler örneği. `Options.DefaultName`</span><span class="sxs-lookup"><span data-stu-id="3a96c-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="3a96c-387">Doğrulama, seçenekler örneği oluşturulduğunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="3a96c-388">Bir seçenek örneğinin, ilk erişime de doğrulamayı geçirmesi garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3a96c-389">Seçenekler doğrulaması, seçenekler örneği oluşturulduktan sonra seçenekler intibaklarına karşı koruma zedler.</span><span class="sxs-lookup"><span data-stu-id="3a96c-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="3a96c-390">Örneğin, `IOptionsSnapshot` seçenekler ilk erişildiğinde istek başına bir kez oluşturulur ve doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="3a96c-391">Seçenekler, `IOptionsSnapshot` aynı istek için sonraki erişim *denemelerinde*yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="3a96c-392">Yöntem `Validate` bir `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="3a96c-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="3a96c-393">Doğrulamayı tam olarak özelleştirmek için aşağıdakileri uygulayın: `IValidateOptions<TOptions>`</span><span class="sxs-lookup"><span data-stu-id="3a96c-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="3a96c-394">Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="3a96c-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="3a96c-395">Başka bir seçenek türüne bağlı doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="3a96c-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="3a96c-396">`IValidateOptions`Doğrulama:</span><span class="sxs-lookup"><span data-stu-id="3a96c-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="3a96c-397">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="3a96c-397">A specific named options instance.</span></span>
* <span data-ttu-id="3a96c-398">Tüm seçenekler `name` `null`ne zaman .</span><span class="sxs-lookup"><span data-stu-id="3a96c-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="3a96c-399">Arabirimi `ValidateOptionsResult` uygulamanızdan bir döndürme:</span><span class="sxs-lookup"><span data-stu-id="3a96c-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="3a96c-400">Veri Ek Açıklama tabanlı doğrulama [microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> yöntemi arayarak `OptionsBuilder<TOptions>`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="3a96c-401">`Microsoft.Extensions.Options.DataAnnotations`[Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="3a96c-402">Istekli doğrulama (başlangıçta hızlı başarısız) gelecekteki bir sürüm için düşünülmektedir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="3a96c-403">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-403">Options post-configuration</span></span>

<span data-ttu-id="3a96c-404">Yapılandırma sonrası'nı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>' ile ayarlama</span><span class="sxs-lookup"><span data-stu-id="3a96c-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3a96c-405">Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra yapılandırma sonrası çalışır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3a96c-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>adlı seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3a96c-407">Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="3a96c-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3a96c-408">Başlatma sırasında seçeneklere erişim</span><span class="sxs-lookup"><span data-stu-id="3a96c-408">Accessing options during startup</span></span>

<span data-ttu-id="3a96c-409"><xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> hizmetler `Configure` yöntem `Startup.Configure`yürütülmeden önce inşa olduğundan, kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3a96c-410">Kullanmayın <xref:Microsoft.Extensions.Options.IOptions%601> ya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> da `Startup.ConfigureServices`içinde.</span><span class="sxs-lookup"><span data-stu-id="3a96c-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3a96c-411">Hizmet kayıtlarının sıralanması nedeniyle tutarsız bir seçenek durumu oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3a96c-412">Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3a96c-413">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara yalıtıldığında, uygulama iki önemli yazılım mühendisliği ilkesine bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3a96c-414">Yapılandırma ayarlarına bağlı olan [Arabirim Ayırma İlkesi (ISS) veya Kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Senaryoları (sınıflar), yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3a96c-415">[Uygulamanın](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; farklı bölümleri için Endişelerin Ayrılması Ayarları bağımlı değildir veya birbirine bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3a96c-416">Seçenekler, yapılandırma verilerini doğrulamak için bir mekanizma da sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3a96c-417">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3a96c-418">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a96c-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a96c-419">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="3a96c-419">Prerequisites</span></span>

<span data-ttu-id="3a96c-420">[Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun veya [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3a96c-421">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="3a96c-421">Options interfaces</span></span>

<span data-ttu-id="3a96c-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>seçenekleri almak ve örneğin seçenek bildirimlerini yönetmek için `TOptions` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3a96c-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="3a96c-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3a96c-424">Bildirimleri değiştirme</span><span class="sxs-lookup"><span data-stu-id="3a96c-424">Change notifications</span></span>
* [<span data-ttu-id="3a96c-425">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3a96c-426">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3a96c-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3a96c-427">Seçici seçenekler geçersiz<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>liği ( )</span><span class="sxs-lookup"><span data-stu-id="3a96c-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3a96c-428">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3a96c-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenekler örneklerinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3a96c-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3a96c-430">Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir yöntemi var.</span><span class="sxs-lookup"><span data-stu-id="3a96c-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3a96c-431">Varsayılan uygulama tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kayıtlı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> alır ve tüm yapılandırmaları ilk olarak çalıştırın, ardından yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="3a96c-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3a96c-432">Bu ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> sadece <xref:Microsoft.Extensions.Options.IConfigureOptions%601> uygun arabirimi çağırır ayırt eder.</span><span class="sxs-lookup"><span data-stu-id="3a96c-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3a96c-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>`TOptions` örnekleri <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> önbelleğe almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3a96c-434">Değerin <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> yeniden hesaplanması için monitördeki seçenekler örneklerini geçersiz kılır (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3a96c-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3a96c-435">Değerler ile el ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>tanıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3a96c-436">Yöntem, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> adlandırılmış tüm örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3a96c-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3a96c-438">Daha fazla bilgi için [IOptionsSnapshot bölümüyle yapılandırma verilerini yeniden yükle](#reload-configuration-data-with-ioptionssnapshot) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3a96c-439"><xref:Microsoft.Extensions.Options.IOptions%601>seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3a96c-440">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryoları <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>desteklemez.</span><span class="sxs-lookup"><span data-stu-id="3a96c-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3a96c-441">Arabirimi zaten <xref:Microsoft.Extensions.Options.IOptions%601> kullanan ve sağladığı <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>senaryoları gerektirmeyen varolan çerçeveleri ve kitaplıkları kullanmaya devam edebilirsiniz. <xref:Microsoft.Extensions.Options.IOptions%601></span><span class="sxs-lookup"><span data-stu-id="3a96c-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3a96c-442">Genel seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3a96c-442">General options configuration</span></span>

<span data-ttu-id="3a96c-443">Genel seçenekler yapılandırması örnek uygulamada Örnek 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="3a96c-444">Bir seçenek sınıfı, ortak parametresiz bir oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3a96c-445">Aşağıdaki sınıf, `MyOptions`iki özelliği `Option1` vardır `Option2`ve .</span><span class="sxs-lookup"><span data-stu-id="3a96c-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3a96c-446">Varsayılan değerleri ayarlamak isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf `Option1`oluşturucusu varsayılan değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3a96c-447">`Option2`özelliği doğrudan başharfe alarak belirlenen varsayılan bir değere sahiptir (*Modeller/MyOptions.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3a96c-448">Sınıf, `MyOptions` yapılandırmaya <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> bağlı ve hizmet kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3a96c-449">Aşağıdaki sayfa modeli <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ayarlara erişmek için [constructor bağımlılık enjeksiyonu](xref:mvc/controllers/dependency-injection) kullanır (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3a96c-450">Örneğin *appsettings.json* dosyası için `option1` değerleri belirtir `option2`ve:</span><span class="sxs-lookup"><span data-stu-id="3a96c-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3a96c-451">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3a96c-452">Ayarlar dosyasından <xref:System.Configuration.ConfigurationBuilder> seçenekler yapılandırmasını yüklemek için özel bir madde kullanırken, temel yolun doğru ayarlandığından onaylayın:</span><span class="sxs-lookup"><span data-stu-id="3a96c-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3a96c-453">Ayarlar dosyasından seçenekler yapılandırmasını ayarlarken temel yolu açıkça <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlamak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3a96c-454">Bir temsilci ile basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3a96c-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="3a96c-455">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada Örnek 2 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="3a96c-456">Seçenekler değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="3a96c-456">Use a delegate to set options values.</span></span> <span data-ttu-id="3a96c-457">Örnek uygulama `MyOptionsWithDelegateConfig` sınıfı kullanır ( Modeller */MyOptionsWithDelegeConfig.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3a96c-458">Aşağıdaki kodda, servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına ikinci bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3a96c-459">Bu ile bağlama yapılandırmak için `MyOptionsWithDelegateConfig`bir temsilci kullanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3a96c-460">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a96c-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3a96c-461">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="3a96c-462">Yapılandırma sağlayıcıları NuGet paketlerinden edinilebilir ve kayıtlı oldukları sırada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3a96c-463">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3a96c-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3a96c-464">Her arama <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> servis <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="3a96c-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3a96c-465">Önceki örnekte, değerleri `Option1` ve `Option2` her ikisi de *appsettings.json*belirtilir, `Option1` `Option2` ancak değerleri ve yapılandırılan temsilci tarafından geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="3a96c-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3a96c-466">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, belirtilen son yapılandırma kaynağı *kazanır* ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3a96c-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3a96c-467">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` yöntemi seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3a96c-468">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3a96c-468">Suboptions configuration</span></span>

<span data-ttu-id="3a96c-469">Alt seçenek yapılandırması örnek uygulamada Örnek 3 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="3a96c-470">Uygulamalar, uygulamadaki belirli senaryo gruplarıyla (sınıflar) ilgili seçenekler sınıfları oluşturmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3a96c-471">Uygulamanın yapılandırma değerleri gerektiren bölümleri yalnızca kullandıkları yapılandırma değerlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3a96c-472">Seçenekleri yapılandırmaya bağlarken, seçenekler türündeki her özellik formun `property[:sub-property:]`yapılandırma anahtarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3a96c-473">Örneğin, `MyOptions.Option1` özellik `Option1` *appsettings.json*özelliğinden `option1` okunan anahtara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3a96c-474">Aşağıdaki kodda, hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına üçüncü bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3a96c-475">*Appsettings.json* dosyasının `MySubOptions` bölümüne `subsection` bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3a96c-476">Yöntem `GetSection` <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3a96c-477">Örneğin *appsettings.json* dosyası için `subsection` `suboption1` anahtarları olan bir `suboption2`üye tanımlar ve:</span><span class="sxs-lookup"><span data-stu-id="3a96c-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3a96c-478">Sınıf `MySubOptions` özellikleri tanımlar `SubOption1` ve `SubOption2`, seçenekleri değerlerini tutmak için *(Modeller / MySubOptions.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3a96c-479">Sayfa modelinin `OnGet` yöntemi seçenekleri değerleri *(Pages/Index.cshtml.cs)* ile bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3a96c-480">Uygulama çalıştırıldığında, `OnGet` yöntem alt seçenek sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="3a96c-481">Bir görünüm modeli veya doğrudan görünüm enjeksiyonu ile sağlanan seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="3a96c-482">Bir görünüm modeli veya doğrudan görüntüleme enjeksiyonu ile sağlanan seçenekler örnek uygulamada Örnek 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="3a96c-483">Seçenekler bir görünüm modelinde veya doğrudan <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bir görünüme enjekte edilerek sağlanabilir (*Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="3a96c-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3a96c-484">Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl enjekte edilebildiğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3a96c-485">Uygulama çalıştırıldığında, seçenekler değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-485">When the app is run, the options values are shown in the rendered page:</span></span>

![Opsiyon değerleri Option1: value1_from_json ve Option2: -1 modelden ve görünüme enjeksiyon ile yüklenir.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3a96c-487">IOptionsSnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="3a96c-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3a96c-488">Yapılandırma verilerini <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yüklemek örnek uygulamada Örnek 5'te gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="3a96c-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>en az işlem yükü ile yeniden yükleme seçeneklerini destekler.</span><span class="sxs-lookup"><span data-stu-id="3a96c-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="3a96c-490">Seçenekler, isteğin kullanım ömrü boyunca erişildiğinde ve önbelleğe alındığunda istek başına bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3a96c-491">Aşağıdaki örnek, <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* değişikliklerden sonra yeni bir yeninin nasıl oluşturulduğunu gösterir (*Pages/Index.cshtml.cs).*</span><span class="sxs-lookup"><span data-stu-id="3a96c-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3a96c-492">Sunucuya gelen birden çok istek, dosya değiştirilene ve yapılandırma yeniden yüklenene kadar *appsettings.json* dosyası tarafından sağlanan sabit değerleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="3a96c-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3a96c-493">Aşağıdaki resim, `option1` *appsettings.json* dosyasından yüklenen başlangıç ve `option2` değerleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3a96c-494">*appsettings.json* dosyasındaki değerleri `value1_from_json UPDATED` değiştirin `200`ve .</span><span class="sxs-lookup"><span data-stu-id="3a96c-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3a96c-495">*appsettings.json* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3a96c-496">Seçeneklerin güncelleştirdiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3a96c-497">IConfigureNamedOptions ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="3a96c-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3a96c-498">Adlandırılmış seçenekler <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> desteği örnek uygulamada Örnek 6 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="3a96c-499">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenekler yapılandırmaları arasında ayrım yapmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3a96c-500">Örnek uygulamada, adlandırılmış [seçenekler,](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) [ConfigureNamedOptions\<TOptions> çağıran OptionsServiceCollectionExtensions.Configure ile bildirilir. Uzatma yöntemini yapılandırın.](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)</span><span class="sxs-lookup"><span data-stu-id="3a96c-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="3a96c-501">Adlandırılmış seçenekler büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3a96c-502">Örnek uygulama ( <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> *Pages/Index.cshtml.cs)* ile adlandırılmış seçeneklere erişir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3a96c-503">Örnek uygulama çalıştırılırken, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="3a96c-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3a96c-504">`named_options_1`değerler, *appsettings.json* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3a96c-505">`named_options_2`değerler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3a96c-506">Delege `named_options_2` `ConfigureServices` için `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3a96c-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3a96c-507">Sınıf tarafından `Option2` sağlanan varsayılan değer. `MyOptions`</span><span class="sxs-lookup"><span data-stu-id="3a96c-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3a96c-508">YapılandırmaTüm yöntemiyle tüm seçenekleri yapılandır</span><span class="sxs-lookup"><span data-stu-id="3a96c-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3a96c-509">Tüm seçenekler örneklerini yöntemle yapılandırın. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*></span><span class="sxs-lookup"><span data-stu-id="3a96c-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3a96c-510">Aşağıdaki kod, `Option1` ortak bir değere sahip tüm yapılandırma örnekleri için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3a96c-511">`Startup.ConfigureServices` Yönteme el ile aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a96c-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3a96c-512">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu üretir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3a96c-513">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-513">All options are named instances.</span></span> <span data-ttu-id="3a96c-514">Varolan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> `Options.DefaultName` örnekler, örneği hedefleyen olarak kabul `string.Empty`edilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3a96c-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>ayrıca <xref:Microsoft.Extensions.Options.IConfigureOptions%601>uygular.</span><span class="sxs-lookup"><span data-stu-id="3a96c-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3a96c-516">Varsayılan uygulama her <xref:Microsoft.Extensions.Options.IOptionsFactory%601> uygun kullanmak için mantık vardır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3a96c-517">`null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek (ve<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanmak) için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3a96c-518">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="3a96c-518">OptionsBuilder API</span></span>

<span data-ttu-id="3a96c-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>`TOptions` örnekleri yapılandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3a96c-520">`OptionsBuilder`sonraki tüm aramalarda görünmek yerine ilk `AddOptions<TOptions>(string optionsName)` çağrıya yalnızca tek bir parametre olarak adlandırılmış seçenekleri oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="3a96c-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3a96c-521">Seçenekler doğrulaması `ConfigureOptions` ve hizmet bağımlılıklarını kabul eden `OptionsBuilder`aşırı yüklemeler yalnızca .</span><span class="sxs-lookup"><span data-stu-id="3a96c-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3a96c-522">Seçenekleri yapılandırmak için DI hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="3a96c-522">Use DI services to configure options</span></span>

<span data-ttu-id="3a96c-523">Seçenekleri iki şekilde yapılandırırken bağımlılık enjeksiyonundan diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="3a96c-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3a96c-524">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) [üzerinde Yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3a96c-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) seçenekleri yapılandırmak için en fazla beş hizmet kullanmanıza olanak sağlayan [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aşırı yükleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="3a96c-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3a96c-526">Uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> uygulayan kendi türünüzü oluşturun ve türü hizmet olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="3a96c-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3a96c-527">Bir hizmet oluşturmak daha karmaşık olduğundan, [Yapılandırma'ya](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)bir yapılandırma temsilcisi geçmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="3a96c-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3a96c-528">Kendi türünüzü oluşturmak, [Yapıl'ı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)kullandığınızda çerçevenin sizin için yaptığıyla eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3a96c-529">[Yapıyı](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) Çağırma, belirtilen genel <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>hizmet türlerini kabul eden bir oluşturucuya sahip geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3a96c-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="3a96c-530">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="3a96c-530">Options post-configuration</span></span>

<span data-ttu-id="3a96c-531">Yapılandırma sonrası'nı <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>' ile ayarlama</span><span class="sxs-lookup"><span data-stu-id="3a96c-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3a96c-532">Tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra yapılandırma sonrası çalışır:</span><span class="sxs-lookup"><span data-stu-id="3a96c-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3a96c-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>adlı seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3a96c-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3a96c-534">Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="3a96c-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3a96c-535">Başlatma sırasında seçeneklere erişim</span><span class="sxs-lookup"><span data-stu-id="3a96c-535">Accessing options during startup</span></span>

<span data-ttu-id="3a96c-536"><xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> hizmetler `Configure` yöntem `Startup.Configure`yürütülmeden önce inşa olduğundan, kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3a96c-537">Kullanmayın <xref:Microsoft.Extensions.Options.IOptions%601> ya <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> da `Startup.ConfigureServices`içinde.</span><span class="sxs-lookup"><span data-stu-id="3a96c-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3a96c-538">Hizmet kayıtlarının sıralanması nedeniyle tutarsız bir seçenek durumu oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="3a96c-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3a96c-539">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3a96c-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
