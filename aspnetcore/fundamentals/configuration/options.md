---
title: ASP.NET Core için seçenek kalıbı
author: rick-anderson
description: ASP.NET Core uygulamalarında ilgili ayarların gruplarını temsil etmek için seçenekler deseninin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: efce2caf37534823016c12b298afd277bab22030
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769942"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="ff27d-103">ASP.NET Core için seçenek kalıbı</span><span class="sxs-lookup"><span data-stu-id="ff27d-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff27d-104">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-104">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="ff27d-105">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-105">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="ff27d-106">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-106">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="ff27d-107">Uygulamanın farklı parçaları için sorun &ndash; ayarlarının [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı değildir veya birbirlerine aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-107">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="ff27d-108">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-108">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="ff27d-109">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-109">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="ff27d-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff27d-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="ff27d-111">Paket</span><span class="sxs-lookup"><span data-stu-id="ff27d-111">Package</span></span>

<span data-ttu-id="ff27d-112">[Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine ASP.NET Core uygulamalarında örtük olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="ff27d-112">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="ff27d-113">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="ff27d-113">Options interfaces</span></span>

<span data-ttu-id="ff27d-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için `TOptions` seçenek bildirimlerini yönetmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-114"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="ff27d-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="ff27d-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="ff27d-116">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="ff27d-116">Change notifications</span></span>
* [<span data-ttu-id="ff27d-117">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-117">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="ff27d-118">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-118">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="ff27d-119">Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="ff27d-119">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="ff27d-120">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-120">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="ff27d-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="ff27d-121"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="ff27d-122">Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir metodu vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-122">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="ff27d-123">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="ff27d-123">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="ff27d-124">Ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-124">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="ff27d-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, örnekleri önbelleğe <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `TOptions` almak için tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-125"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="ff27d-126">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="ff27d-126">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="ff27d-127">Değerler ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>el ile tanıtılamaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-127">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="ff27d-128">Yöntemi <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> , tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="ff27d-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-129"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="ff27d-130">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="ff27d-130">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="ff27d-131"><xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-131"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="ff27d-132">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-132">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="ff27d-133">Zaten <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-133">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="ff27d-134">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ff27d-134">General options configuration</span></span>

<span data-ttu-id="ff27d-135">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-135">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="ff27d-136">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-136">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="ff27d-137">Aşağıdaki sınıfının, `MyOptions`, `Option1` ve `Option2`iki özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-137">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="ff27d-138">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-138">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="ff27d-139">`Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-139">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="ff27d-140">`MyOptions` Sınıfı, ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-140">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="ff27d-141">Aşağıdaki sayfa modeli, ayarlarına erişmek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-141">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="ff27d-142">Örneğin `option1` `option2` *appSettings. JSON* dosyası ve değerlerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-142">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="ff27d-143">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-143">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="ff27d-144">Bir ayarlar dosyasından yükleme <xref:System.Configuration.ConfigurationBuilder> seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-144">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="ff27d-145">İle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-145">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="ff27d-146">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-146">Configure simple options with a delegate</span></span>

<span data-ttu-id="ff27d-147">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-147">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="ff27d-148">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-148">Use a delegate to set options values.</span></span> <span data-ttu-id="ff27d-149">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-149">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="ff27d-150">Aşağıdaki kodda, hizmet kapsayıcısına ikinci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-150">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff27d-151">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="ff27d-151">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="ff27d-152">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff27d-152">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="ff27d-153">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-153">You can add multiple configuration providers.</span></span> <span data-ttu-id="ff27d-154">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-154">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="ff27d-155">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ff27d-156">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> , hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="ff27d-156">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="ff27d-157">Yukarıdaki örnekte, `Option1` ve `Option2` değerleri *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-157">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="ff27d-158">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-158">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="ff27d-159">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-159">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="ff27d-160">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ff27d-160">Suboptions configuration</span></span>

<span data-ttu-id="ff27d-161">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-161">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="ff27d-162">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-162">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="ff27d-163">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-163">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="ff27d-164">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun `property[:sub-property:]`bir yapılandırma anahtarına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-164">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="ff27d-165">Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan anahtara `Option1`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-165">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="ff27d-166">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-166">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff27d-167">`MySubOptions` *AppSettings. JSON* dosyasının bölümüne `subsection` bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-167">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="ff27d-168">`GetSection` Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-168">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="ff27d-169">Örnek *appSettings. JSON* dosyası ve `subsection` `suboption1` `suboption2`için anahtarlar içeren bir üyeyi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-169">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="ff27d-170">`MySubOptions` Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2`seçenekler değerlerini tutmak Için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-170">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="ff27d-171">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-171">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="ff27d-172">Uygulama çalıştırıldığında, `OnGet` Yöntem, alt sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-172">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="ff27d-173">Seçeneklere ekleme</span><span class="sxs-lookup"><span data-stu-id="ff27d-173">Options injection</span></span>

<span data-ttu-id="ff27d-174">Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-174">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="ff27d-175">Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="ff27d-175">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="ff27d-176">Yönergeyle Razor [`@inject`](xref:mvc/views/razor#inject) bir sayfa veya MVC görünümü. Razor</span><span class="sxs-lookup"><span data-stu-id="ff27d-176">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="ff27d-177">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="ff27d-177">A page or view model.</span></span>

<span data-ttu-id="ff27d-178">Örnek uygulamadan <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> alınan aşağıdaki örnek, bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-178">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="ff27d-179">Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl ekleneceğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="ff27d-180">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="ff27d-182">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="ff27d-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="ff27d-183">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="ff27d-184">' <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-184">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="ff27d-185">Ve `IOptionsMonitor` `IOptionsSnapshot` arasındaki fark şudur:</span><span class="sxs-lookup"><span data-stu-id="ff27d-185">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="ff27d-186">`IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="ff27d-186">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="ff27d-187">`IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve `IOptionsSnapshot<T>` nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-187">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="ff27d-188">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-188">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="ff27d-189">Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> sonra yeni bir oluşturma işlemi gösterir (*Pages/Index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="ff27d-189">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="ff27d-190">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="ff27d-190">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="ff27d-191">Aşağıdaki görüntüde, *appSettings. JSON* dosyasından `option2` yüklenen ilk `option1` ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-191">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="ff27d-192">*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile `200`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-192">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="ff27d-193">*AppSettings. JSON* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-193">Save the *appsettings.json* file.</span></span> <span data-ttu-id="ff27d-194">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="ff27d-194">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="ff27d-195">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="ff27d-195">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="ff27d-196">İle <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> adlandırılmış seçenek desteği örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-196">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="ff27d-197">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-197">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="ff27d-198">Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [\<configurenamedooptıons TOptions> çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-198">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="ff27d-199">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-199">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="ff27d-200">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="ff27d-201">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="ff27d-202">`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="ff27d-203">`named_options_2`değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="ff27d-204">İçin `named_options_2` `Option1`içindeki `ConfigureServices` temsilci.</span><span class="sxs-lookup"><span data-stu-id="ff27d-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="ff27d-205">`MyOptions` Sınıfı tarafından `Option2` sağlanacak varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="ff27d-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="ff27d-206">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="ff27d-207">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="ff27d-208">Aşağıdaki kod, ortak `Option1` bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="ff27d-209">`Startup.ConfigureServices` Yöntemine el ile aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ff27d-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="ff27d-210">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="ff27d-211">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-211">All options are named instances.</span></span> <span data-ttu-id="ff27d-212">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, `Options.DefaultName` örneği hedefleme olarak değerlendirilir `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="ff27d-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="ff27d-214">Varsayılan uygulamasının, <xref:Microsoft.Extensions.Options.IOptionsFactory%601> her birini uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="ff27d-215">`null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="ff27d-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="ff27d-216">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="ff27d-216">OptionsBuilder API</span></span>

<span data-ttu-id="ff27d-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak `TOptions` için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="ff27d-218">`OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk `AddOptions<TOptions>(string optionsName)` çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="ff27d-219">Seçenekler doğrulaması ve hizmet `ConfigureOptions` bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla `OptionsBuilder`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="ff27d-220">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="ff27d-220">Use DI services to configure options</span></span>

<span data-ttu-id="ff27d-221">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ff27d-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="ff27d-222">[Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="ff27d-223">`OptionsBuilder<TOptions>`, seçenekleri yapılandırmak için en fazla beş hizmet kullanılmasına izin veren [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-223">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="ff27d-224">' İ uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff27d-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="ff27d-225">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="ff27d-226">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="ff27d-227">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ff27d-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="ff27d-228">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="ff27d-228">Options validation</span></span>

<span data-ttu-id="ff27d-229">Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="ff27d-230">Seçenekler `Validate` geçerliyse ve `false` geçerli değillerse, döndüren `true` bir doğrulama yöntemiyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="ff27d-231">Önceki örnekte, adlandırılmış seçenekler örneği olarak `optionalOptionsName`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="ff27d-232">Varsayılan Seçenekler örneği `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="ff27d-233">Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="ff27d-234">Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-234">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ff27d-235">Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-235">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="ff27d-236">Örneğin, `IOptionsSnapshot` seçenekler ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-236">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="ff27d-237">Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-237">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="ff27d-238">`Validate` Yöntemi bir `Func<TOptions, bool>`kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ff27d-238">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="ff27d-239">Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>`izin veren uygulayın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-239">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="ff27d-240">Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="ff27d-240">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="ff27d-241">Başka bir seçenek türüne bağlı olan doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="ff27d-241">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="ff27d-242">`IValidateOptions`doğrular</span><span class="sxs-lookup"><span data-stu-id="ff27d-242">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="ff27d-243">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="ff27d-243">A specific named options instance.</span></span>
* <span data-ttu-id="ff27d-244">Olduğunda `name` tüm seçenekler `null`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-244">All options when `name` is `null`.</span></span>

<span data-ttu-id="ff27d-245">`ValidateOptionsResult` Arabirimini uygulamanızdan döndürün:</span><span class="sxs-lookup"><span data-stu-id="ff27d-245">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="ff27d-246">Veri eki tabanlı doğrulama, üzerinde <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>`yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-246">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="ff27d-247">`Microsoft.Extensions.Options.DataAnnotations`ASP.NET Core uygulamalarda örtülü olarak başvurulur.</span><span class="sxs-lookup"><span data-stu-id="ff27d-247">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="ff27d-248">Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-248">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="ff27d-249">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-249">Options post-configuration</span></span>

<span data-ttu-id="ff27d-250">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-250">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="ff27d-251">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-251">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ff27d-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-252"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ff27d-253">Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-253">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="ff27d-254">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="ff27d-254">Accessing options during startup</span></span>

<span data-ttu-id="ff27d-255"><xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' de, Hizmetler `Startup.Configure` `Configure` Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-255"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="ff27d-256">Veya <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> içinde `Startup.ConfigureServices`kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-256">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff27d-257">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-257">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ff27d-258">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-258">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="ff27d-259">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-259">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="ff27d-260">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-260">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="ff27d-261">Uygulamanın farklı parçaları için sorun &ndash; ayarlarının [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı değildir veya birbirlerine aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-261">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="ff27d-262">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-262">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="ff27d-263">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-263">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="ff27d-264">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff27d-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff27d-265">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="ff27d-265">Prerequisites</span></span>

<span data-ttu-id="ff27d-266">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-266">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="ff27d-267">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="ff27d-267">Options interfaces</span></span>

<span data-ttu-id="ff27d-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için `TOptions` seçenek bildirimlerini yönetmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-268"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="ff27d-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="ff27d-269"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="ff27d-270">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="ff27d-270">Change notifications</span></span>
* [<span data-ttu-id="ff27d-271">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-271">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="ff27d-272">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-272">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="ff27d-273">Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="ff27d-273">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="ff27d-274">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-274">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="ff27d-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="ff27d-275"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="ff27d-276">Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir metodu vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-276">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="ff27d-277">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="ff27d-277">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="ff27d-278">Ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-278">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="ff27d-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, örnekleri önbelleğe <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `TOptions` almak için tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="ff27d-280">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="ff27d-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="ff27d-281">Değerler ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>el ile tanıtılamaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-281">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="ff27d-282">Yöntemi <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> , tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-282">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="ff27d-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-283"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="ff27d-284">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="ff27d-284">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="ff27d-285"><xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-285"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="ff27d-286">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-286">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="ff27d-287">Zaten <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-287">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="ff27d-288">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ff27d-288">General options configuration</span></span>

<span data-ttu-id="ff27d-289">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-289">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="ff27d-290">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-290">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="ff27d-291">Aşağıdaki sınıfının, `MyOptions`, `Option1` ve `Option2`iki özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-291">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="ff27d-292">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-292">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="ff27d-293">`Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-293">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="ff27d-294">`MyOptions` Sınıfı, ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-294">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="ff27d-295">Aşağıdaki sayfa modeli, ayarlarına erişmek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-295">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="ff27d-296">Örneğin `option1` `option2` *appSettings. JSON* dosyası ve değerlerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-296">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="ff27d-297">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-297">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="ff27d-298">Bir ayarlar dosyasından yükleme <xref:System.Configuration.ConfigurationBuilder> seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-298">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="ff27d-299">İle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-299">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="ff27d-300">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-300">Configure simple options with a delegate</span></span>

<span data-ttu-id="ff27d-301">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-301">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="ff27d-302">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-302">Use a delegate to set options values.</span></span> <span data-ttu-id="ff27d-303">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-303">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="ff27d-304">Aşağıdaki kodda, hizmet kapsayıcısına ikinci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-304">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff27d-305">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="ff27d-305">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="ff27d-306">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff27d-306">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="ff27d-307">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-307">You can add multiple configuration providers.</span></span> <span data-ttu-id="ff27d-308">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-308">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="ff27d-309">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-309">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ff27d-310">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> , hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="ff27d-310">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="ff27d-311">Yukarıdaki örnekte, `Option1` ve `Option2` değerleri *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-311">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="ff27d-312">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-312">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="ff27d-313">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-313">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="ff27d-314">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ff27d-314">Suboptions configuration</span></span>

<span data-ttu-id="ff27d-315">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-315">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="ff27d-316">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-316">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="ff27d-317">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-317">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="ff27d-318">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun `property[:sub-property:]`bir yapılandırma anahtarına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-318">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="ff27d-319">Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan anahtara `Option1`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-319">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="ff27d-320">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-320">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff27d-321">`MySubOptions` *AppSettings. JSON* dosyasının bölümüne `subsection` bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-321">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="ff27d-322">`GetSection` Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-322">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="ff27d-323">Örnek *appSettings. JSON* dosyası ve `subsection` `suboption1` `suboption2`için anahtarlar içeren bir üyeyi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-323">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="ff27d-324">`MySubOptions` Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2`seçenekler değerlerini tutmak Için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-324">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="ff27d-325">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-325">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="ff27d-326">Uygulama çalıştırıldığında, `OnGet` Yöntem, alt sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-326">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="ff27d-327">Seçeneklere ekleme</span><span class="sxs-lookup"><span data-stu-id="ff27d-327">Options injection</span></span>

<span data-ttu-id="ff27d-328">Seçenekler ekleme, örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-328">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="ff27d-329">Ekle <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :</span><span class="sxs-lookup"><span data-stu-id="ff27d-329">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="ff27d-330">Yönergeyle Razor [`@inject`](xref:mvc/views/razor#inject) bir sayfa veya MVC görünümü. Razor</span><span class="sxs-lookup"><span data-stu-id="ff27d-330">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="ff27d-331">Bir sayfa veya görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="ff27d-331">A page or view model.</span></span>

<span data-ttu-id="ff27d-332">Örnek uygulamadan <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> alınan aşağıdaki örnek, bir sayfa modeline (*Sayfalar/Index. cshtml. cs*) sahiptir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-332">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="ff27d-333">Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl ekleneceğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-333">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="ff27d-334">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-334">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="ff27d-336">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="ff27d-336">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="ff27d-337">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-337">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="ff27d-338">' <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Yi kullanarak, her erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında Seçenekler her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-338">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="ff27d-339">Ve `IOptionsMonitor` `IOptionsSnapshot` arasındaki fark şudur:</span><span class="sxs-lookup"><span data-stu-id="ff27d-339">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="ff27d-340">`IOptionsMonitor`, özellikle tek bağımlılıklarda yararlı olan herhangi bir zamanda geçerli seçenek değerlerini alan bir [tek hizmettir](xref:fundamentals/dependency-injection#singleton) .</span><span class="sxs-lookup"><span data-stu-id="ff27d-340">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="ff27d-341">`IOptionsSnapshot`kapsamlı bir [hizmettir](xref:fundamentals/dependency-injection#scoped) ve `IOptionsSnapshot<T>` nesnenin oluşturulduğu sırada seçeneklerin anlık görüntüsünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-341">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="ff27d-342">Seçenekler anlık görüntüleri geçici ve kapsamlı bağımlılıklarla kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-342">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="ff27d-343">Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> sonra yeni bir oluşturma işlemi gösterir (*Pages/Index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="ff27d-343">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="ff27d-344">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="ff27d-344">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="ff27d-345">Aşağıdaki görüntüde, *appSettings. JSON* dosyasından `option2` yüklenen ilk `option1` ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-345">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="ff27d-346">*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile `200`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-346">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="ff27d-347">*AppSettings. JSON* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-347">Save the *appsettings.json* file.</span></span> <span data-ttu-id="ff27d-348">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="ff27d-348">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="ff27d-349">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="ff27d-349">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="ff27d-350">İle <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> adlandırılmış seçenek desteği örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-350">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="ff27d-351">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-351">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="ff27d-352">Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [\<configurenamedooptıons TOptions> çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-352">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="ff27d-353">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-353">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="ff27d-354">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-354">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="ff27d-355">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-355">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="ff27d-356">`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-356">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="ff27d-357">`named_options_2`değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-357">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="ff27d-358">İçin `named_options_2` `Option1`içindeki `ConfigureServices` temsilci.</span><span class="sxs-lookup"><span data-stu-id="ff27d-358">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="ff27d-359">`MyOptions` Sınıfı tarafından `Option2` sağlanacak varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="ff27d-359">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="ff27d-360">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-360">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="ff27d-361">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-361">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="ff27d-362">Aşağıdaki kod, ortak `Option1` bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-362">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="ff27d-363">`Startup.ConfigureServices` Yöntemine el ile aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ff27d-363">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="ff27d-364">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-364">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="ff27d-365">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-365">All options are named instances.</span></span> <span data-ttu-id="ff27d-366">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, `Options.DefaultName` örneği hedefleme olarak değerlendirilir `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-366">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="ff27d-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-367"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="ff27d-368">Varsayılan uygulamasının, <xref:Microsoft.Extensions.Options.IOptionsFactory%601> her birini uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-368">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="ff27d-369">`null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="ff27d-369">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="ff27d-370">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="ff27d-370">OptionsBuilder API</span></span>

<span data-ttu-id="ff27d-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak `TOptions` için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-371"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="ff27d-372">`OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk `AddOptions<TOptions>(string optionsName)` çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-372">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="ff27d-373">Seçenekler doğrulaması ve hizmet `ConfigureOptions` bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla `OptionsBuilder`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-373">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="ff27d-374">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="ff27d-374">Use DI services to configure options</span></span>

<span data-ttu-id="ff27d-375">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ff27d-375">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="ff27d-376">[Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-376">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="ff27d-377">[Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-377">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="ff27d-378">' İ uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff27d-378">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="ff27d-379">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-379">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="ff27d-380">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-380">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="ff27d-381">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ff27d-381">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="ff27d-382">Seçenekler doğrulaması</span><span class="sxs-lookup"><span data-stu-id="ff27d-382">Options validation</span></span>

<span data-ttu-id="ff27d-383">Seçenekler doğrulaması seçenekler yapılandırıldığında seçenekleri doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-383">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="ff27d-384">Seçenekler `Validate` geçerliyse ve `false` geçerli değillerse, döndüren `true` bir doğrulama yöntemiyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-384">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="ff27d-385">Önceki örnekte, adlandırılmış seçenekler örneği olarak `optionalOptionsName`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-385">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="ff27d-386">Varsayılan Seçenekler örneği `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-386">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="ff27d-387">Seçenekler örneği oluşturulduğunda doğrulama çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-387">Validation runs when the options instance is created.</span></span> <span data-ttu-id="ff27d-388">Bir seçenek örneği, ilk erişildiği zaman doğrulamayı geçecek garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-388">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ff27d-389">Seçenekler örneği oluşturulduktan sonra Seçenekler doğrulaması, seçenek değişikliklerine karşı koruma yapmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-389">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="ff27d-390">Örneğin, `IOptionsSnapshot` seçenekler ilk kez erişildiğinde Seçenekler her istek için oluşturulur ve onaylanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-390">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="ff27d-391">Bu `IOptionsSnapshot` Seçenekler *, aynı istek için*sonraki erişim denemelerinde yeniden doğrulanmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-391">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="ff27d-392">`Validate` Yöntemi bir `Func<TOptions, bool>`kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ff27d-392">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="ff27d-393">Doğrulamayı tamamen özelleştirmek için, aşağıdakileri `IValidateOptions<TOptions>`izin veren uygulayın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-393">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="ff27d-394">Birden çok seçenek türünün doğrulanması:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="ff27d-394">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="ff27d-395">Başka bir seçenek türüne bağlı olan doğrulama:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="ff27d-395">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="ff27d-396">`IValidateOptions`doğrular</span><span class="sxs-lookup"><span data-stu-id="ff27d-396">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="ff27d-397">Belirli bir adlandırılmış seçenekler örneği.</span><span class="sxs-lookup"><span data-stu-id="ff27d-397">A specific named options instance.</span></span>
* <span data-ttu-id="ff27d-398">Olduğunda `name` tüm seçenekler `null`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-398">All options when `name` is `null`.</span></span>

<span data-ttu-id="ff27d-399">`ValidateOptionsResult` Arabirimini uygulamanızdan döndürün:</span><span class="sxs-lookup"><span data-stu-id="ff27d-399">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="ff27d-400">Veri eki tabanlı doğrulama, üzerinde <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> `OptionsBuilder<TOptions>`yöntemi çağırarak [Microsoft. Extensions. Options. DataAnnotation](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) paketinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-400">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="ff27d-401">`Microsoft.Extensions.Options.DataAnnotations`, [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-401">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="ff27d-402">Eager doğrulaması (başlangıçta hızlı başarısız), gelecek bir sürüm için dikkate alınmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-402">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="ff27d-403">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-403">Options post-configuration</span></span>

<span data-ttu-id="ff27d-404">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-404">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="ff27d-405">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-405">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ff27d-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-406"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ff27d-407">Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-407">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="ff27d-408">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="ff27d-408">Accessing options during startup</span></span>

<span data-ttu-id="ff27d-409"><xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' de, Hizmetler `Startup.Configure` `Configure` Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-409"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="ff27d-410">Veya <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> içinde `Startup.ConfigureServices`kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-410">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff27d-411">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-411">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ff27d-412">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-412">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="ff27d-413">[Yapılandırma ayarları](xref:fundamentals/configuration/index) senaryo tarafından ayrı sınıflara ayrılmışsa, uygulama iki önemli yazılım mühendisliği ilkelerine uyar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-413">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="ff27d-414">Yapılandırma ayarlarına bağlı olan [arabirim ayırma ilkesi (ISS) veya kapsülleme](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; senaryoları (sınıflar) yalnızca kullandıkları yapılandırma ayarlarına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-414">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="ff27d-415">Uygulamanın farklı parçaları için sorun &ndash; ayarlarının [ayrılması,](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) birbirine bağımlı değildir veya birbirlerine aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-415">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="ff27d-416">Seçenekler Ayrıca yapılandırma verilerini doğrulamaya yönelik bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-416">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="ff27d-417">Daha fazla bilgi için [Seçenekler doğrulama](#options-validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-417">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="ff27d-418">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff27d-418">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff27d-419">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="ff27d-419">Prerequisites</span></span>

<span data-ttu-id="ff27d-420">Microsoft. [AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Options. configurationextensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-420">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="ff27d-421">Seçenekler arabirimleri</span><span class="sxs-lookup"><span data-stu-id="ff27d-421">Options interfaces</span></span>

<span data-ttu-id="ff27d-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, seçenekleri almak ve örnekler için `TOptions` seçenek bildirimlerini yönetmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-422"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="ff27d-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="ff27d-423"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="ff27d-424">Değişiklik bildirimleri</span><span class="sxs-lookup"><span data-stu-id="ff27d-424">Change notifications</span></span>
* [<span data-ttu-id="ff27d-425">Adlandırılmış seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-425">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="ff27d-426">Yeniden yüklenebilir yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-426">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="ff27d-427">Seçmeli seçenekler geçersiz kılma (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="ff27d-427">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="ff27d-428">[Yapılandırma sonrası](#options-post-configuration) senaryolar, tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra seçenekleri ayarlamanıza veya değiştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-428">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="ff27d-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>yeni seçenek örnekleri oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="ff27d-429"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="ff27d-430">Tek <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> bir metodu vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-430">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="ff27d-431">Varsayılan uygulama tüm kayıtlı <xref:Microsoft.Extensions.Options.IConfigureOptions%601> ve <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> sonrasında tüm yapılandırmaları çalıştırır ve sonrasında yapılandırma sonrası.</span><span class="sxs-lookup"><span data-stu-id="ff27d-431">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="ff27d-432">Ve <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> arasında ayrım yapar ve yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-432">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="ff27d-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>, örnekleri önbelleğe <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> `TOptions` almak için tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-433"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="ff27d-434">, <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Değer yeniden hesaplanabilmesi için izleyici içindeki seçenek örneklerini geçersiz kılar (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="ff27d-434">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="ff27d-435">Değerler ile <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>el ile tanıtılamaz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-435">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="ff27d-436">Yöntemi <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> , tüm adlandırılmış örneklerin isteğe bağlı olarak yeniden oluşturulması gerektiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-436">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="ff27d-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>seçeneklerin her istekte yeniden hesaplanması gereken senaryolarda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-437"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="ff27d-438">Daha fazla bilgi için bkz. [ıoptionssnapshot ile yapılandırma verilerini yeniden yükleme](#reload-configuration-data-with-ioptionssnapshot) bölümü.</span><span class="sxs-lookup"><span data-stu-id="ff27d-438">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="ff27d-439"><xref:Microsoft.Extensions.Options.IOptions%601>, seçenekleri desteklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-439"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="ff27d-440">Ancak, <xref:Microsoft.Extensions.Options.IOptions%601> önceki senaryolarını desteklemez <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-440">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="ff27d-441">Zaten <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptions%601> arabirimini kullanan ve tarafından <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>sağlanmış senaryolara gerek olmayan mevcut çerçeveler ve kitaplıklarda kullanmaya devam edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-441">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="ff27d-442">Genel Seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ff27d-442">General options configuration</span></span>

<span data-ttu-id="ff27d-443">Genel Seçenekler yapılandırması örnek uygulamada 1 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-443">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="ff27d-444">Bir seçenek sınıfı ortak parametresiz bir Oluşturucu ile soyut olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-444">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="ff27d-445">Aşağıdaki sınıfının, `MyOptions`, `Option1` ve `Option2`iki özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-445">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="ff27d-446">Varsayılan değerleri ayarlama isteğe bağlıdır, ancak aşağıdaki örnekteki sınıf Oluşturucusu varsayılan değerini ayarlar `Option1`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-446">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="ff27d-447">`Option2`, özelliği doğrudan başlatarak ayarlanmış varsayılan bir değere sahiptir (*modeller/MyOptions. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-447">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="ff27d-448">`MyOptions` Sınıfı, ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-448">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="ff27d-449">Aşağıdaki sayfa modeli, ayarlarına erişmek <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> için [Oluşturucu bağımlılığı ekleme](xref:mvc/controllers/dependency-injection) işlemini kullanır (*Pages/Index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-449">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="ff27d-450">Örneğin `option1` `option2` *appSettings. JSON* dosyası ve değerlerini belirtir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-450">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="ff27d-451">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-451">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="ff27d-452">Bir ayarlar dosyasından yükleme <xref:System.Configuration.ConfigurationBuilder> seçenekleri yapılandırması için özel ' i kullanırken, temel yolun doğru şekilde ayarlandığını onaylayın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-452">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="ff27d-453">İle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ayarlar dosyasından seçenek yapılandırması yüklenirken taban yolunu açıkça ayarlama gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-453">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="ff27d-454">Bir temsilciyle basit seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-454">Configure simple options with a delegate</span></span>

<span data-ttu-id="ff27d-455">Basit seçenekleri bir temsilciyle yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-455">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="ff27d-456">Seçenek değerlerini ayarlamak için bir temsilci kullanın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-456">Use a delegate to set options values.</span></span> <span data-ttu-id="ff27d-457">Örnek uygulama, `MyOptionsWithDelegateConfig` sınıfını (*modeller/MyOptionsWithDelegateConfig. cs*) kullanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-457">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="ff27d-458">Aşağıdaki kodda, hizmet kapsayıcısına ikinci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-458">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff27d-459">Bağlamayı yapılandırmak için bir temsilci kullanır `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="ff27d-459">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="ff27d-460">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff27d-460">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="ff27d-461">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-461">You can add multiple configuration providers.</span></span> <span data-ttu-id="ff27d-462">Yapılandırma sağlayıcıları NuGet paketlerinde kullanılabilir ve kayıtlı oldukları sırayla uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-462">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="ff27d-463">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-463">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ff27d-464">Her bir çağrı <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> , hizmet <xref:Microsoft.Extensions.Options.IConfigureOptions%601> kapsayıcısına bir hizmet ekler.</span><span class="sxs-lookup"><span data-stu-id="ff27d-464">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="ff27d-465">Yukarıdaki örnekte, `Option1` ve `Option2` değerleri *appSettings. JSON*içinde belirtilmiştir, ancak değerleri `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-465">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="ff27d-466">Birden fazla yapılandırma hizmeti etkinleştirildiğinde, son yapılandırma kaynağı *WINS* ' i ve yapılandırma değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ff27d-466">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="ff27d-467">Uygulama çalıştırıldığında, sayfa modelinin `OnGet` metodu, seçenek sınıfı değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-467">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="ff27d-468">Alt seçenekler yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ff27d-468">Suboptions configuration</span></span>

<span data-ttu-id="ff27d-469">Alt seçenekler yapılandırması örnek uygulamada 3 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-469">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="ff27d-470">Uygulamalar, uygulamadaki belirli senaryo gruplarına (sınıflar) ait seçenek sınıfları oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-470">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="ff27d-471">Uygulamanın yapılandırma değerleri gerektiren bölümlerinin yalnızca kullandıkları yapılandırma değerlerine erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-471">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="ff27d-472">Seçenekleri yapılandırmaya bağlama sırasında, seçenek türündeki her bir özellik, formun `property[:sub-property:]`bir yapılandırma anahtarına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-472">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="ff27d-473">Örneğin, `MyOptions.Option1` özelliği *appSettings. JSON*içindeki `option1` özelliğinden okunan anahtara `Option1`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-473">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="ff27d-474">Aşağıdaki kodda, hizmet kapsayıcısına üçüncü <xref:Microsoft.Extensions.Options.IConfigureOptions%601> bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-474">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ff27d-475">`MySubOptions` *AppSettings. JSON* dosyasının bölümüne `subsection` bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-475">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="ff27d-476">`GetSection` Yöntemi için <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> ad alanı gerekir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-476">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="ff27d-477">Örnek *appSettings. JSON* dosyası ve `subsection` `suboption1` `suboption2`için anahtarlar içeren bir üyeyi tanımlar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-477">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="ff27d-478">`MySubOptions` Sınıfı özellikleri tanımlar `SubOption1` ve `SubOption2`seçenekler değerlerini tutmak Için (*modeller/myalt seçenekler. cs*):</span><span class="sxs-lookup"><span data-stu-id="ff27d-478">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="ff27d-479">Sayfa modelinin `OnGet` metodu, Seçenekler değerleriyle (*Pages/Index. cshtml. cs*) bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-479">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="ff27d-480">Uygulama çalıştırıldığında, `OnGet` Yöntem, alt sınıf değerlerini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-480">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="ff27d-481">Bir görünüm modeli veya doğrudan görünüm ekleme ile belirtilen seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-481">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="ff27d-482">Bir görünüm modeli veya doğrudan görünüm ekleme ile sunulan seçenekler örnek uygulamada 4 olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-482">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="ff27d-483">Seçenekler bir görünüm modelinde veya ekleme <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> doğrudan bir görünüme (*Sayfalar/Index. cshtml. cs*) sağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-483">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="ff27d-484">Örnek uygulama, bir `IOptionsMonitor<MyOptions>` `@inject` yönergeyle nasıl ekleneceğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-484">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="ff27d-485">Uygulama çalıştırıldığında, seçenek değerleri işlenen sayfada gösterilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-485">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek1: value1_from_json ve Seçenek2:-1 seçenek değerleri modelden yüklenir ve görünüme ekleme yapılır.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="ff27d-487">Ioptionssnapshot ile yapılandırma verilerini yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="ff27d-487">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="ff27d-488">Yapılandırma verilerini ile <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> yeniden yükleme, örnek uygulamada 5. örnekte gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-488">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="ff27d-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>minimum işleme yüküyle yeniden yükleme seçeneklerini destekler.</span><span class="sxs-lookup"><span data-stu-id="ff27d-489"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="ff27d-490">Seçenekler erişildiğinde ve isteğin ömrü boyunca önbelleğe alındığında her istek için bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-490">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="ff27d-491">Aşağıdaki örnek, *appSettings. JSON* değişikliklerinden <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> sonra yeni bir oluşturma işlemi gösterir (*Pages/Index. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="ff27d-491">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="ff27d-492">Sunucu için birden çok istek, dosya değiştirilene ve yapılandırma yeniden yükleninceye kadar *appSettings. JSON* dosyası tarafından belirtilen sabit değerler döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="ff27d-492">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="ff27d-493">Aşağıdaki görüntüde, *appSettings. JSON* dosyasından `option2` yüklenen ilk `option1` ve değerler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-493">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="ff27d-494">*AppSettings. JSON* dosyasındaki değerleri `value1_from_json UPDATED` ve ile `200`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-494">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="ff27d-495">*AppSettings. JSON* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-495">Save the *appsettings.json* file.</span></span> <span data-ttu-id="ff27d-496">Seçenekler değerlerinin güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="ff27d-496">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="ff27d-497">IController Enamedooptıons ile adlandırılmış seçenekler desteği</span><span class="sxs-lookup"><span data-stu-id="ff27d-497">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="ff27d-498">İle <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> adlandırılmış seçenek desteği örnek uygulamada 6 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-498">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="ff27d-499">Adlandırılmış seçenekler desteği, uygulamanın adlandırılmış seçenek yapılandırmalarının ayırt etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-499">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="ff27d-500">Örnek uygulamada, adlandırılmış Seçenekler [OptionsServiceCollectionExtensions. configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)ile bildirilmiştir ve bu, [\<configurenamedooptıons TOptions> çağırır. ](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)Uzantı yöntemini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-500">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="ff27d-501">Adlandırılmış seçenekler büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-501">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="ff27d-502">Örnek uygulama, adlandırılan seçeneklere <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index. cshtml. cs*) erişir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-502">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="ff27d-503">Örnek uygulama çalıştırıldığında, adlandırılmış seçenekler döndürülür:</span><span class="sxs-lookup"><span data-stu-id="ff27d-503">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="ff27d-504">`named_options_1`değerler, *appSettings. JSON* dosyasından yüklenen yapılandırmadan sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-504">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="ff27d-505">`named_options_2`değerleri tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-505">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="ff27d-506">İçin `named_options_2` `Option1`içindeki `ConfigureServices` temsilci.</span><span class="sxs-lookup"><span data-stu-id="ff27d-506">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="ff27d-507">`MyOptions` Sınıfı tarafından `Option2` sağlanacak varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="ff27d-507">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="ff27d-508">Tüm seçenekleri ConfigureAll yöntemiyle yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ff27d-508">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="ff27d-509">Tüm seçenek örneklerini <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> yöntemiyle yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-509">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="ff27d-510">Aşağıdaki kod, ortak `Option1` bir değere sahip tüm yapılandırma örneklerini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-510">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="ff27d-511">`Startup.ConfigureServices` Yöntemine el ile aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ff27d-511">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="ff27d-512">Kodu ekledikten sonra örnek uygulamayı çalıştırmak aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-512">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="ff27d-513">Tüm seçenekler adlandırılmış örneklerdir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-513">All options are named instances.</span></span> <span data-ttu-id="ff27d-514">Mevcut <xref:Microsoft.Extensions.Options.IConfigureOptions%601> örnekler, `Options.DefaultName` örneği hedefleme olarak değerlendirilir `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="ff27d-514">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="ff27d-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>Ayrıca uygular <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-515"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="ff27d-516">Varsayılan uygulamasının, <xref:Microsoft.Extensions.Options.IOptionsFactory%601> her birini uygun şekilde kullanma mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-516">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="ff27d-517">`null` Adlandırılmış seçenek, belirli bir adlandırılmış örnek yerine tüm adlandırılmış örnekleri hedeflemek için kullanılır (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> ve <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="ff27d-517">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="ff27d-518">Seçenekno Oluşturucu API 'SI</span><span class="sxs-lookup"><span data-stu-id="ff27d-518">OptionsBuilder API</span></span>

<span data-ttu-id="ff27d-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>örnekleri yapılandırmak `TOptions` için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-519"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="ff27d-520">`OptionsBuilder`adlandırılmış seçenekleri, sonraki çağrıların tümünde olmak yerine ilk `AddOptions<TOptions>(string optionsName)` çağrının tek bir parametresi olacak şekilde oluşturmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="ff27d-520">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="ff27d-521">Seçenekler doğrulaması ve hizmet `ConfigureOptions` bağımlılıklarını kabul eden aşırı yüklemeler yalnızca aracılığıyla `OptionsBuilder`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-521">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="ff27d-522">Ayarları yapılandırmak için dı hizmetlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="ff27d-522">Use DI services to configure options</span></span>

<span data-ttu-id="ff27d-523">Seçenekleri iki şekilde yapılandırırken, bağımlılık ekleme işleminden diğer hizmetlere erişebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ff27d-523">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="ff27d-524">[Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)üzerinde [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) için bir yapılandırma temsilcisi geçirin.</span><span class="sxs-lookup"><span data-stu-id="ff27d-524">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="ff27d-525">[Options Builder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , seçenekleri yapılandırmak için en fazla beş hizmeti kullanmanıza olanak tanıyan, [yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) yüklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="ff27d-525">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="ff27d-526">' İ uygulayan <xref:Microsoft.Extensions.Options.IConfigureOptions%601> veya <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> hizmet olarak türü kaydeden kendi türünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff27d-526">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="ff27d-527">Bir hizmetin oluşturulması daha karmaşık olduğundan, [yapılandırmak](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)için bir yapılandırma temsilcisinin geçirilmesini öneririz.</span><span class="sxs-lookup"><span data-stu-id="ff27d-527">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="ff27d-528">Kendi türünü oluşturmak, [Yapılandır](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)'ı kullandığınızda çerçevenin sizin için yaptığı işe eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-528">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="ff27d-529">[Yapılandırma](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) çağrısı, belirtilen genel hizmet <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>türlerini kabul eden bir oluşturucuya sahip olan geçici bir genel kayıt kaydeder.</span><span class="sxs-lookup"><span data-stu-id="ff27d-529">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="ff27d-530">Yapılandırma sonrası seçenekler</span><span class="sxs-lookup"><span data-stu-id="ff27d-530">Options post-configuration</span></span>

<span data-ttu-id="ff27d-531">Yapılandırma sonrası ' i ayarlayın <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="ff27d-531">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="ff27d-532">Yapılandırma sonrası tüm <xref:Microsoft.Extensions.Options.IConfigureOptions%601> yapılandırma oluştuktan sonra çalışır:</span><span class="sxs-lookup"><span data-stu-id="ff27d-532">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ff27d-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>, adlandırılmış seçenekleri yapılandırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ff27d-533"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ff27d-534">Tüm <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> yapılandırma örneklerini yapılandırmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="ff27d-534">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="ff27d-535">Başlangıç sırasında seçeneklere erişme</span><span class="sxs-lookup"><span data-stu-id="ff27d-535">Accessing options during startup</span></span>

<span data-ttu-id="ff27d-536"><xref:Microsoft.Extensions.Options.IOptions%601>ve <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ' de, Hizmetler `Startup.Configure` `Configure` Yöntem yürütmeden önce oluşturulduğundan ' de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-536"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="ff27d-537">Veya <xref:Microsoft.Extensions.Options.IOptions%601> <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> içinde `Startup.ConfigureServices`kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="ff27d-537">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ff27d-538">Hizmet kayıtlarının sıralaması nedeniyle tutarsız bir seçenek durumu var olabilir.</span><span class="sxs-lookup"><span data-stu-id="ff27d-538">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ff27d-539">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ff27d-539">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
