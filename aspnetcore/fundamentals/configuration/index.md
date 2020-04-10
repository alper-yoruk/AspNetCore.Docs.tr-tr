---
title: ASP.NET Core'da Yapılandırma
author: rick-anderson
description: ASP.NET Core uygulamasını yapılandırmak için Yapılandırma API'sini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007164"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="63bb1-103">ASP.NET Core'da Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="63bb1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="63bb1-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="63bb1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63bb1-105">ASP.NET Core'daki yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="63bb1-106">Yapılandırma sağlayıcıları, çeşitli yapılandırma kaynaklarını kullanarak anahtar değer çiftlerinden gelen yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="63bb1-107">*settings.json* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="63bb1-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-108">Environment variables</span></span>
* <span data-ttu-id="63bb1-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="63bb1-109">Azure Key Vault</span></span>
* <span data-ttu-id="63bb1-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-110">Azure App Configuration</span></span>
* <span data-ttu-id="63bb1-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-111">Command-line arguments</span></span>
* <span data-ttu-id="63bb1-112">Özel sağlayıcılar, yüklü veya oluşturulmuş</span><span class="sxs-lookup"><span data-stu-id="63bb1-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="63bb1-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-113">Directory files</span></span>
* <span data-ttu-id="63bb1-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-114">In-memory .NET objects</span></span>

<span data-ttu-id="63bb1-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63bb1-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="63bb1-116">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="63bb1-116">Default configuration</span></span>

<span data-ttu-id="63bb1-117">ASP.NET Core web uygulamaları [dotnet yeni](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan aşağıdaki kodu oluşturmak:</span><span class="sxs-lookup"><span data-stu-id="63bb1-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="63bb1-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>aşağıdaki sırada uygulama için varsayılan yapılandırma sağlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="63bb1-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Kaynak `IConfiguration` olarak var olan bir kuruluşu ekler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="63bb1-120">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırmasının ilk kaynağı olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="63bb1-121">[appsettings.json](#appsettingsjson) [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanarak .</span><span class="sxs-lookup"><span data-stu-id="63bb1-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="63bb1-122">*ayarları.* `Environment` *.json* [JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak .</span><span class="sxs-lookup"><span data-stu-id="63bb1-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="63bb1-123">Örneğin, *uygulama ayarları*. ***Üretim***. *json* ve *appsettings*. ***Geliştirme***. *json*.</span><span class="sxs-lookup"><span data-stu-id="63bb1-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="63bb1-124">[Uygulama](xref:security/app-secrets) `Development` çevrede çalıştığında uygulama sırları.</span><span class="sxs-lookup"><span data-stu-id="63bb1-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="63bb1-125">[Çevre Değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="63bb1-126">Komut satırı yapılandırma [sağlayıcısını](#command-line)kullanarak komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="63bb1-127">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="63bb1-128">Örneğin, `MyKey` hem *appsettings.json* hem de ortamda ayarlanmışsa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="63bb1-129">[Komut satırı yapılandırma sağlayıcısı,](#command-line-configuration-provider) varsayılan yapılandırma sağlayıcılarını kullanarak diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="63bb1-130">Hakkında daha `CreateDefaultBuilder`fazla bilgi için Varsayılan [oluşturucu ayarlarına](xref:fundamentals/host/generic-host#default-builder-settings)bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="63bb1-131">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendikleri sırada görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="63bb1-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="63bb1-132">appsettings.json</span></span>

<span data-ttu-id="63bb1-133">Aşağıdaki *appsettings.json* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="63bb1-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="63bb1-134">[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-135">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yükler yapılandırması aşağıdaki sırayla:</span><span class="sxs-lookup"><span data-stu-id="63bb1-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="63bb1-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="63bb1-136">*appsettings.json*</span></span>
1. <span data-ttu-id="63bb1-137">*ayarları.* `Environment` *.json* : Örneğin, *uygulama ayarları*. ***Üretim***. *json* ve *appsettings*. ***Geliştirme***. *json* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="63bb1-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="63bb1-138">Dosyanın ortam sürümü [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)dayalı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="63bb1-139">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="63bb1-140">*appsettings*. `Environment`. *json* değerleri *appsettings.json*tuşları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="63bb1-141">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="63bb1-141">For example, by default:</span></span>

* <span data-ttu-id="63bb1-142">Geliştirme, *appsettings*. ***Geliştirme***. *json* yapılandırma *appsettings.json*bulunan değerleri üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="63bb1-143">Üretimde, *uygulama ayarları*. ***Üretim***. *json* yapılandırma *appsettings.json*bulunan değerleri üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="63bb1-144">Örneğin, uygulamayı Azure'a dağıtırken.</span><span class="sxs-lookup"><span data-stu-id="63bb1-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="63bb1-145">Seçenekler deseni kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="63bb1-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="63bb1-146">İlgili yapılandırma değerlerini okumanın tercih edilen yolu [seçenekler deseni](xref:fundamentals/configuration/options)kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="63bb1-147">Örneğin, aşağıdaki yapılandırma değerlerini okumak için:</span><span class="sxs-lookup"><span data-stu-id="63bb1-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="63bb1-148">Aşağıdaki `PositionOptions` sınıfı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="63bb1-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="63bb1-149">Türünün tüm ortak okuma-yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="63bb1-150">Alanlar bağlı ***değildir.***</span><span class="sxs-lookup"><span data-stu-id="63bb1-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="63bb1-151">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="63bb1-151">The following code:</span></span>

* <span data-ttu-id="63bb1-152">`PositionOptions` Sınıfı `Position` bölüme bağlamak için [ConfigurationBinder.Bind'i](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) çağırır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="63bb1-153">Yapılandırma `Position` verilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)biner ve belirtilen türü döndürür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="63bb1-155">`ConfigurationBinder.Get<T>`kullanmaktan `ConfigurationBinder.Bind`daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="63bb1-156">Aşağıdaki kod, sınıfla `ConfigurationBinder.Get<T>` nasıl `PositionOptions` kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-157">***Seçenekler deseni*** kullanırken alternatif bir yaklaşım `Position` bölümü bağlamak ve [bağımlılık enjeksiyon servis konteyner](xref:fundamentals/dependency-injection)eklemektir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="63bb1-158">Aşağıdaki kodda, `PositionOptions` yapılandırmaile ve <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> yapılandırmaya bağlı servis kapsayıcısına eklenir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="63bb1-159">Yukarıdaki kodu kullanarak, aşağıdaki kod konum seçeneklerini okur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-160">[Varsayılan](#default) yapılandırmayı, *appsettings.json* ve *uygulama ayarlarını kullanarak.* `Environment` *.json* dosyaları [reloadOnChange](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir: true .</span><span class="sxs-lookup"><span data-stu-id="63bb1-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="63bb1-161">*appsettings.json* ve *appsettings'te* yapılan değişiklikler. `Environment` *.json* dosyası uygulama ***başladıktan sonra*** [JSON yapılandırma sağlayıcısı](#jcp)tarafından okunur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="63bb1-162">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="63bb1-163">Güvenlik ve gizli müdür</span><span class="sxs-lookup"><span data-stu-id="63bb1-163">Security and secret manager</span></span>

<span data-ttu-id="63bb1-164">Yapılandırma veri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="63bb1-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="63bb1-165">Parolaları veya diğer hassas verileri asla yapılandırma sağlayıcı kodunda veya düz metin yapılandırma dosyalarında depolamayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="63bb1-166">[Gizli yönetici](xref:security/app-secrets) geliştirme sırlarını saklamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="63bb1-167">Üretim sırlarını geliştirme veya test ortamlarında kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="63bb1-168">Yanlışlıkla bir kaynak kodu deposuna işlenmeyecek şekilde projenin dışındaki sırları belirtin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="63bb1-169">[Varsayılan olarak,](#default) [Secret yöneticisi](xref:security/app-secrets) *appsettings.json* ve uygulama ayarlarını sonra yapılandırma ayarlarını *okur.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="63bb1-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="63bb1-170">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="63bb1-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="63bb1-171"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerinin kullanılmasına ilişkin tavsiyeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="63bb1-172">Gizli Yönetici, kullanıcı sırlarını yerel sistemdeki bir JSON dosyasında depolamak için [Dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="63bb1-173">[Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamaları için uygulama sırlarını güvenle saklar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="63bb1-174">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="63bb1-175">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-175">Environment variables</span></span>

<span data-ttu-id="63bb1-176">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> *appsettings.json, appsettings*okuduktan sonra ortam değişken anahtar değeri çiftleri yükler *yapılandırma.* `Environment` *.json*ve [Gizli müdür.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="63bb1-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="63bb1-177">Bu nedenle, ortamdan okunan temel değerler *appsettings.json*, appsettings okunan değerleri geçersiz *kılar.* `Environment` *.json*ve gizli müdür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="63bb1-178">Aşağıdaki `set` komutlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-178">The following `set` commands:</span></span>

* <span data-ttu-id="63bb1-179">Windows'da [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="63bb1-180">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="63bb1-181">Komut `dotnet run` proje dizininde çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="63bb1-182">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="63bb1-182">The preceding environment settings:</span></span>

* <span data-ttu-id="63bb1-183">Yalnızca ayarlandıkları komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="63bb1-184">Visual Studio ile başlatılan tarayıcılar tarafından okunmaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="63bb1-185">Aşağıdaki [setx](/windows-server/administration/windows-commands/setx) komutları Windows'daki ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="63bb1-186">`set`Aksine, `setx` ayarlar kalıcıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="63bb1-187">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="63bb1-188">Anahtar `/M` kullanılmazsa, bir kullanıcı ortamı değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="63bb1-189">Önceki komutların *appsettings.json* ve appsettings geçersiz kılınmasını test etmek *için.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="63bb1-190">Visual Studio ile: Visual Studio'dan çıkın ve yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="63bb1-191">CLI ile: Yeni bir komut `dotnet run`penceresi başlatın ve girin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="63bb1-192">Ortam <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> değişkenleri için bir önek belirtmek için dizeyle arama:</span><span class="sxs-lookup"><span data-stu-id="63bb1-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="63bb1-193">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="63bb1-193">In the preceding code:</span></span>

* <span data-ttu-id="63bb1-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`varsayılan yapılandırma [sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="63bb1-195">Yapılandırma sağlayıcılarını sıralamak için [Bkz. JSON yapılandırma sağlayıcısı.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="63bb1-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="63bb1-196">Öneki ile ayarlanan ortam değişkenleri [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar. `MyCustomPrefix_`</span><span class="sxs-lookup"><span data-stu-id="63bb1-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="63bb1-197">Bu önek olmadan ortam değişkenleri içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="63bb1-198">Yapılandırma anahtar değeri çiftleri okunduğunda önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="63bb1-199">Aşağıdaki komutlar özel önek test:</span><span class="sxs-lookup"><span data-stu-id="63bb1-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="63bb1-200">[Varsayılan yapılandırma,](#default) ortam değişkenlerini ve komut `DOTNET_` satırı `ASPNETCORE_`bağımsız değişkenlerini önceden belirlenmiş ve .</span><span class="sxs-lookup"><span data-stu-id="63bb1-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="63bb1-201">`DOTNET_` Önekleri `ASPNETCORE_` ve önekleri [ana bilgisayar ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="63bb1-202">Ana bilgisayar ve uygulama yapılandırması hakkında daha fazla bilgi için [.NET Genel Ana Bilgisayar'a](xref:fundamentals/host/generic-host)bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="63bb1-203">[Azure Uygulama Hizmeti'nde](https://azure.microsoft.com/services/app-service/)Ayarlar **> Yapılandırma** sayfasında Yeni **uygulama ayarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="63bb1-204">Azure Uygulama Hizmeti uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="63bb1-205">Istirahatte şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="63bb1-206">Çevre değişkenleri olarak ortaya çıktı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-206">Exposed as environment variables.</span></span>

<span data-ttu-id="63bb1-207">Daha fazla bilgi için Azure [Uygulamaları: Azure Portalı'nı kullanarak uygulama yapılandırmasını geçersiz kılın.](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)</span><span class="sxs-lookup"><span data-stu-id="63bb1-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="63bb1-208">Azure veritabanı bağlantı dizeleri hakkında bilgi için [Bağlantı dizelerine](#constr) bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="63bb1-209">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="63bb1-209">Command-line</span></span>

<span data-ttu-id="63bb1-210">[Varsayılan](#default) yapılandırmayı kullanarak, aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişken anahtar değeri çiftlerinden <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> yükler yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="63bb1-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="63bb1-211">*appsettings.json* ve *appsettings*. `Environment`. *json* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="63bb1-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="63bb1-212">Geliştirme ortamında [uygulama sırları (Gizli Yönetici).](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="63bb1-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="63bb1-213">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-213">Environment variables.</span></span>

<span data-ttu-id="63bb1-214">[Varsayılan olarak,](#default)komut satırında ayarlanan yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla birlikte ayarlanan yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="63bb1-215">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-215">Command-line arguments</span></span>

<span data-ttu-id="63bb1-216">Aşağıdaki komut tuşları ve `=`değerleri kullanarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="63bb1-217">Aşağıdaki komut tuşları ve `/`değerleri kullanarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="63bb1-218">Aşağıdaki komut tuşları ve `--`değerleri kullanarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="63bb1-219">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="63bb1-219">The key value:</span></span>

* <span data-ttu-id="63bb1-220">Takip `=`etmeli veya anahtar da bir `--` öneki ye sahip olmalıdır veya `/` değer bir alanı takip ettiğinde.</span><span class="sxs-lookup"><span data-stu-id="63bb1-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="63bb1-221">Kullanılıyorsa `=` gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="63bb1-222">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="63bb1-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="63bb1-223">Aynı komut içinde, boşluk kullanan anahtar değer çiftleri ile `=` kullanılan komut satırı bağımsız değişken anahtar değeri çiftleri karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="63bb1-224">Anahtar eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-224">Switch mappings</span></span>

<span data-ttu-id="63bb1-225">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="63bb1-226"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Yönteme geçiş değiştirmesözlüğü sağlayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="63bb1-227">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük komut satırı bağımsız değişkeni tarafından sağlanan anahtarla eşleşen bir anahtar için işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="63bb1-228">Komut satırı anahtarı sözlükte bulunursa, anahtar değeri çiftini uygulamanın yapılandırmasına ayarlamak için sözlük değeri geri aktarılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="63bb1-229">Tek bir çizgi ile önceden belirlenmiş herhangi bir komut`-`satırı tuşu için bir anahtar eşlemesi gereklidir ( ).</span><span class="sxs-lookup"><span data-stu-id="63bb1-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="63bb1-230">Haritalama ları sözlük anahtar kurallarını değiştirin:</span><span class="sxs-lookup"><span data-stu-id="63bb1-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="63bb1-231">Anahtarlar ile `-` başlamalı veya `--`.</span><span class="sxs-lookup"><span data-stu-id="63bb1-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="63bb1-232">Anahtar eşlemeler sözlüğü yinelenen anahtarları içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="63bb1-233">Bir anahtar eşleme sözlüğü kullanmak için, `AddCommandLine`çağrı içine geçirin:</span><span class="sxs-lookup"><span data-stu-id="63bb1-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="63bb1-234">Aşağıdaki kod değiştirilen anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-235">Anahtar değiştirmesini test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="63bb1-236">Not: Şu `=` anda, tek bir çizgi `-`ile anahtar değiştirme değerleri ayarlamak için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="63bb1-237">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="63bb1-238">Anahtar değiştirmesini test etmek için aşağıdaki komut çalışır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="63bb1-239">Anahtar eşlemeleri kullanan uygulamalar için, `CreateDefaultBuilder` çağrı bağımsız değişkenleri geçmemelidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="63bb1-240">Yöntemin `CreateDefaultBuilder` `AddCommandLine` araması eşlenen anahtarları içermez ve anahtar eşleme sözlüğünden `CreateDefaultBuilder`''ye geçmenin bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="63bb1-241">Çözüm bağımsız değişkenleri geçirmek `CreateDefaultBuilder` için değil, bunun `ConfigurationBuilder` yerine `AddCommandLine` yöntemin yöntemihem bağımsız değişkenleri hem de anahtar eşleme sözlüğü işlemek için izin vermektir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="63bb1-242">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="63bb1-242">Hierarchical configuration data</span></span>

<span data-ttu-id="63bb1-243">Yapılandırma API'si, hiyerarşik verileri yapılandırma tuşlarında bir sınırlayıcı kullanarak düzleştirmek tarafından hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="63bb1-244">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *appsettings.json* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="63bb1-245">[Örnek indirmedeki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-246">Hiyerarşik yapılandırma verilerini okumanın tercih edilen yolu seçenekler deseni kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="63bb1-247">Daha fazla bilgi için bu belgedeki [Bind hiyerarşik yapılandırma verilerine](#optpat) bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="63bb1-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verilerindeki bir bölümün bölümlerini ve altlarını yalıtmak için yöntemler kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="63bb1-249">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)'de açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="63bb1-250">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-250">Configuration keys and values</span></span>

<span data-ttu-id="63bb1-251">Yapılandırma tuşları:</span><span class="sxs-lookup"><span data-stu-id="63bb1-251">Configuration keys:</span></span>

* <span data-ttu-id="63bb1-252">Büyük/küçük harf duyarsız.</span><span class="sxs-lookup"><span data-stu-id="63bb1-252">Are case-insensitive.</span></span> <span data-ttu-id="63bb1-253">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="63bb1-254">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısında ayarlanırsa, eklenen son sağlayıcının değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="63bb1-255">Daha fazla bilgi için [Varsayılan yapılandırmaya](#default)bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="63bb1-256">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="63bb1-256">Hierarchical keys</span></span>
  * <span data-ttu-id="63bb1-257">Yapılandırma API'si içinde,`:`bir iki nokta üst üste ayırıcı ( ) tüm platformlarda çalışır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="63bb1-258">Ortam değişkenlerinde, bir kolon ayırıcısı tüm platformlarda çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="63bb1-259">Bir çift alt `__`çizgi, , tüm platformlar tarafından desteklenen ve `:`otomatik olarak bir üst üste dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="63bb1-260">Azure Anahtar Kasası'nda `--` hiyerarşik tuşlar ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="63bb1-261">[Azure Key Vault yapılandırma sağlayıcısı,](xref:security/key-vault-configuration) sırların uygulamanın yapılandırmasına yüklendiği zaman otomatik olarak bir `--` `:` anahtarla değiştirir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="63bb1-262">Yapılandırma <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="63bb1-263">Dizi bağlama, [Bind dizisinde bir sınıf bölümüne](#boa) açıklanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="63bb1-264">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="63bb1-264">Configuration values:</span></span>

* <span data-ttu-id="63bb1-265">İpler vardır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-265">Are strings.</span></span>
* <span data-ttu-id="63bb1-266">Null değerleri yapılandırmada depolanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="63bb1-267">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="63bb1-267">Configuration providers</span></span>

<span data-ttu-id="63bb1-268">Aşağıdaki tablo, ASP.NET Core uygulamalarının kullanabileceği yapılandırma sağlayıcılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="63bb1-269">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="63bb1-269">Provider</span></span> | <span data-ttu-id="63bb1-270">Yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="63bb1-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="63bb1-271">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="63bb1-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="63bb1-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="63bb1-273">Azure App yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="63bb1-274">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="63bb1-275">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="63bb1-276">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-276">Command-line parameters</span></span> |
| [<span data-ttu-id="63bb1-277">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="63bb1-278">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="63bb1-278">Custom source</span></span> |
| [<span data-ttu-id="63bb1-279">Çevre Değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="63bb1-280">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-280">Environment variables</span></span> |
| [<span data-ttu-id="63bb1-281">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="63bb1-282">INI, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="63bb1-283">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="63bb1-284">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-284">Directory files</span></span> |
| [<span data-ttu-id="63bb1-285">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="63bb1-286">Bellek içi koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="63bb1-286">In-memory collections</span></span> |
| [<span data-ttu-id="63bb1-287">Gizli Müdür</span><span class="sxs-lookup"><span data-stu-id="63bb1-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="63bb1-288">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="63bb1-288">File in the user profile directory</span></span> |

<span data-ttu-id="63bb1-289">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtildiği şekilde okunur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="63bb1-290">Yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynaklarının önceliklerine uyacak şekilde kodolarak sipariş edin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="63bb1-291">Yapılandırma sağlayıcılarının tipik bir sırası:</span><span class="sxs-lookup"><span data-stu-id="63bb1-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="63bb1-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="63bb1-292">*appsettings.json*</span></span>
1. <span data-ttu-id="63bb1-293">*appsettings*. `Environment`. *json*</span><span class="sxs-lookup"><span data-stu-id="63bb1-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="63bb1-294">Gizli Müdür</span><span class="sxs-lookup"><span data-stu-id="63bb1-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="63bb1-295">[Çevre Değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="63bb1-296">Komut satırı yapılandırma [sağlayıcısını](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="63bb1-297">Yaygın bir uygulama komut satırı yapılandırma sağlayıcısı diğer sağlayıcılar tarafından ayarlanan yapılandırma geçersiz kılmak için komut satırı bağımsız değişkenleri sağlamak için bir dizi sağlayıcılar son eklemektir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="63bb1-298">Sağlayıcıların önceki sırası [varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="63bb1-299">Bağlantı dizeleri önekleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-299">Connection string prefixes</span></span>

<span data-ttu-id="63bb1-300">Yapılandırma API'sinin dört bağlantı dizesi ortamı değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="63bb1-301">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizeleri yapılandırmada yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="63bb1-302">Tabloda gösterilen öneklerle ortam değişkenleri [varsayılan yapılandırmayla](#default) veya önek sağıldığında uygulamaya `AddEnvironmentVariables`yüklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="63bb1-303">Bağlantı dize öneki</span><span class="sxs-lookup"><span data-stu-id="63bb1-303">Connection string prefix</span></span> | <span data-ttu-id="63bb1-304">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="63bb1-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="63bb1-305">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="63bb1-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="63bb1-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="63bb1-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="63bb1-307">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="63bb1-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="63bb1-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="63bb1-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="63bb1-309">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önek ile yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="63bb1-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="63bb1-310">Yapılandırma anahtarı, ortam değişkeni önekikaldırılarak ve yapılandırma anahtar`ConnectionStrings`bölümü eklenerek oluşturulur ( ).</span><span class="sxs-lookup"><span data-stu-id="63bb1-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="63bb1-311">Veritabanı bağlantı sağlayıcısını temsil eden (belirtilen sağlayıcı bulunmayanlar `CUSTOMCONNSTR_`hariç) yeni bir yapılandırma anahtar değeri çifti oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="63bb1-312">Ortam değişken anahtarı</span><span class="sxs-lookup"><span data-stu-id="63bb1-312">Environment variable key</span></span> | <span data-ttu-id="63bb1-313">Dönüştürülmüş yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="63bb1-313">Converted configuration key</span></span> | <span data-ttu-id="63bb1-314">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="63bb1-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-315">Yapılandırma girişi oluşturulmamadı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-316">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="63bb1-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="63bb1-317">Değer:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="63bb1-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-318">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="63bb1-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="63bb1-319">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="63bb1-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-320">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="63bb1-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="63bb1-321">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="63bb1-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="63bb1-322">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-322">JSON configuration provider</span></span>

<span data-ttu-id="63bb1-323">JSON dosya anahtar değeri çiftlerinden <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yükler yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="63bb1-324">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-324">Overloads can specify:</span></span>

* <span data-ttu-id="63bb1-325">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-325">Whether the file is optional.</span></span>
* <span data-ttu-id="63bb1-326">Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.</span><span class="sxs-lookup"><span data-stu-id="63bb1-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="63bb1-327">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="63bb1-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="63bb1-328">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="63bb1-328">The preceding code:</span></span>

* <span data-ttu-id="63bb1-329">*MyConfig.json* dosyasını aşağıdaki seçeneklerle yüklemek için JSON yapılandırma sağlayıcısını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="63bb1-330">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="63bb1-331">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="63bb1-332">*MyConfig.json* dosyasından önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="63bb1-333">*MyConfig.json* dosyasındaki ayarlar, [Çevre değişkenleri yapılandırma sağlayıcısı](#evcp) ve Komut satırı yapılandırma [sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki ayarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="63bb1-334">Genellikle, [Çevre değişkenleri yapılandırma sağlayıcısında](#evcp) ve [Komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanan değerleri geçersiz kılan özel bir JSON dosyası ***istemezsinüz.***</span><span class="sxs-lookup"><span data-stu-id="63bb1-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="63bb1-335">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve birkaç yapılandırma sağlayıcısı ekler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="63bb1-336">Önceki kodda, *MyConfig.json* ve *MyConfig*ayarları . `Environment`. *json* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="63bb1-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="63bb1-337">*appsettings.json* ve *uygulama ayarları*geçersiz kılın. `Environment`. *json* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="63bb1-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="63bb1-338">[Çevre değişkenleri yapılandırma sağlayıcısı](#evcp) ve Komut satırı [yapılandırma sağlayıcısındaki](#clcp)ayarlar tarafından geçersiz kılınmıştır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="63bb1-339">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *MyConfig.json* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="63bb1-340">[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="63bb1-341">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-341">File configuration provider</span></span>

<span data-ttu-id="63bb1-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>dosya sisteminden yapılandırma yükleme için taban sınıftır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="63bb1-343">Aşağıdaki yapılandırma sağlayıcıları ndan `FileConfigurationProvider`kaynaklanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="63bb1-344">INI yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="63bb1-345">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="63bb1-346">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="63bb1-347">INI yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-347">INI configuration provider</span></span>

<span data-ttu-id="63bb1-348">Çalışma <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> zamanında INI dosya anahtar değeri çiftlerinden yükler yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="63bb1-349">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve birkaç yapılandırma sağlayıcısı ekler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="63bb1-350">Önceki kodda, *MyIniConfig.ini* ve *MyIniConfig*ayarları . `Environment`. *ini* dosyaları aşağıdaki ayarlartarafından geçersiz kılınmaktadır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="63bb1-351">Çevre değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="63bb1-352">[Komut satırı yapılandırma sağlayıcısı.](#clcp)</span><span class="sxs-lookup"><span data-stu-id="63bb1-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="63bb1-353">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *MyIniConfig.ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="63bb1-354">[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="63bb1-355">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-355">XML configuration provider</span></span>

<span data-ttu-id="63bb1-356">XML <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> dosya anahtar değeri çiftlerinden gelen yükler yapılandırması çalışma zamanında.</span><span class="sxs-lookup"><span data-stu-id="63bb1-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="63bb1-357">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve birkaç yapılandırma sağlayıcısı ekler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="63bb1-358">Önceki kodda, *MyXMLFile.xml* ve *MyXMLFile*ayarları . `Environment`. *xml* dosyaları aşağıdaki ayarlarla geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="63bb1-359">Çevre değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="63bb1-360">[Komut satırı yapılandırma sağlayıcısı.](#clcp)</span><span class="sxs-lookup"><span data-stu-id="63bb1-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="63bb1-361">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *MyXMLFile.xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="63bb1-362">[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-363">`name` Öznitelik öğeleri ayırt etmek için kullanılırsa aynı öğe adı kullanan yinelenen öğeler çalışır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="63bb1-364">Aşağıdaki kod önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-365">Öznitelikler değerleri sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="63bb1-366">Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="63bb1-367">key:öznitelik</span><span class="sxs-lookup"><span data-stu-id="63bb1-367">key:attribute</span></span>
* <span data-ttu-id="63bb1-368">bölüm:key:öznitelik</span><span class="sxs-lookup"><span data-stu-id="63bb1-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="63bb1-369">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="63bb1-370">Yapılandırma <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> anahtar değeri çiftleri olarak bir dizin dosyaları kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="63bb1-371">Anahtar dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-371">The key is the file name.</span></span> <span data-ttu-id="63bb1-372">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-372">The value contains the file's contents.</span></span> <span data-ttu-id="63bb1-373">Dosya başına Anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="63bb1-374">Dosya başına anahtar yapılandırmasını etkinleştirmek <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> için, uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini bir .'</span><span class="sxs-lookup"><span data-stu-id="63bb1-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="63bb1-375">`directoryPath` Dosyalara mutlak bir yol olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="63bb1-376">Aşırı yükleme izni belirterek:</span><span class="sxs-lookup"><span data-stu-id="63bb1-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="63bb1-377">Kaynağı `Action<KeyPerFileConfigurationSource>` yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="63bb1-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="63bb1-378">Dizinin isteğe bağlı olup olmadığı ve dizine giden yol.</span><span class="sxs-lookup"><span data-stu-id="63bb1-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="63bb1-379">Çift alt çizgi`__`( ) dosya adlarında yapılandırma anahtarı delimiter olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="63bb1-380">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını `Logging:LogLevel:System`üretir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="63bb1-381">Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="63bb1-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="63bb1-382">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-382">Memory configuration provider</span></span>

<span data-ttu-id="63bb1-383">Yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> anahtar değeri çiftleri olarak bellek içi bir koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="63bb1-384">Aşağıdaki kod yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="63bb1-385">[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-386">Önceki kodda, `config.AddInMemoryCollection(Dict)` varsayılan yapılandırma [sağlayıcıları](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="63bb1-387">Yapılandırma sağlayıcılarını sıralamak için [Bkz. JSON yapılandırma sağlayıcısı.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="63bb1-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="63bb1-388">Yapılandırma sağlayıcılarını sıralamak için [Bkz. JSON yapılandırma sağlayıcısı.](#jcp)</span><span class="sxs-lookup"><span data-stu-id="63bb1-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="63bb1-389">Bkz. Başka bir örnek `MemoryConfigurationProvider`için [bind bir dizi](#boa) kullanarak.</span><span class="sxs-lookup"><span data-stu-id="63bb1-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="63bb1-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="63bb1-390">GetValue</span></span>

<span data-ttu-id="63bb1-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirtilen bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-392">Önceki kodda, yapılandırmada `NumberKey` bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="63bb1-393">GetSection, GetChildren ve Var</span><span class="sxs-lookup"><span data-stu-id="63bb1-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="63bb1-394">Aşağıdaki örnekler için aşağıdaki *MyAlt.json* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="63bb1-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="63bb1-395">Aşağıdaki kod yapılandırma sağlayıcılarına *MyAlt.json* ekler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="63bb1-396">GetSection</span><span class="sxs-lookup"><span data-stu-id="63bb1-396">GetSection</span></span>

<span data-ttu-id="63bb1-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) belirtilen alt bölüm tuşu ile bir yapılandırma alt bölümü döndürür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="63bb1-398">Aşağıdaki kod için `section1`değerleri döndürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-399">Aşağıdaki kod için `section2:subsection0`değerleri döndürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-400">`GetSection`asla `null`dönmez.</span><span class="sxs-lookup"><span data-stu-id="63bb1-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="63bb1-401">Eşleşen bir bölüm bulunamazsa, `IConfigurationSection` boş döndürülür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="63bb1-402">Eşleşen `GetSection` bir bölüm <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> döndürdüğünde doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="63bb1-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> ve bölüm olduğunda döndürülür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="63bb1-404">GetChildren ve Var</span><span class="sxs-lookup"><span data-stu-id="63bb1-404">GetChildren and Exists</span></span>

<span data-ttu-id="63bb1-405">Aşağıdaki kod [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağırır ve `section2:subsection0`değerleri döndürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-406">Önceki kod [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) bölümünün var olduğunu doğrulamak için çağırır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="63bb1-407">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="63bb1-407">Bind an array</span></span>

<span data-ttu-id="63bb1-408">[ConfigurationBinder.Bind,](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) yapılandırma anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="63bb1-409">Sayısal anahtar kesimini ortaya çıkaran herhangi bir dizi [biçimi, bir POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="63bb1-410">[Örnek indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.json* düşünün:</span><span class="sxs-lookup"><span data-stu-id="63bb1-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="63bb1-411">Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.json* ekler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="63bb1-412">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-413">Önceki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="63bb1-414">Önceki çıktıda, Dizin 3 `value40`değeri `"4": "value40",` vardır , *MyArray.json*karşılık gelen .</span><span class="sxs-lookup"><span data-stu-id="63bb1-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="63bb1-415">Bağlı dizi endeksleri süreklidir ve yapılandırma anahtar dizini bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="63bb1-416">Yapılandırma bağlayıcısı null değerlerini bağlama veya bağlı nesnelerde null girişleri oluşturma yeteneğine sahip değildir</span><span class="sxs-lookup"><span data-stu-id="63bb1-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="63bb1-417">Aşağıdaki <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> kod, `array:entries` yapılandırmayı uzantı yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="63bb1-418">Aşağıdaki kod yapılandırmayı `arrayDict` `Dictionary` okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-419">Önceki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="63bb1-420">Bağlı &num;nesnedeki Dizin `array:4` 3, yapılandırma anahtarı ve değeri `value4`için yapılandırma verilerini tutar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="63bb1-421">Bir dizi içeren yapılandırma verileri bağlandığında, nesne oluşturulurken yapılandırma verilerini yinelemek için yapılandırma anahtarlarındaki dizi dizinleri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="63bb1-422">Yapılandırma verilerinde null değeri tutulamaz ve yapılandırma anahtarlarındaki bir dizi bir veya daha fazla endeksi atladığında bağlı bir nesnede null değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="63bb1-423">Dizin &num;3 için eksik yapılandırma öğesi, dizin `ArrayExample` &num;3 anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğine bağlanmadan önce sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="63bb1-424">Örnek indirmeden aşağıdaki *Value3.json* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="63bb1-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="63bb1-425">Aşağıdaki kod *Value3.json* ve yapılandırma `arrayDict` `Dictionary`içerir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="63bb1-426">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="63bb1-427">Önceki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="63bb1-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="63bb1-428">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcılarının gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="63bb1-429">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-429">Custom configuration provider</span></span>

<span data-ttu-id="63bb1-430">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar değeri çiftleri okuyan temel bir yapılandırma sağlayıcısının nasıl oluşturulturolduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="63bb1-431">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="63bb1-432">EF bellek veritabanı gösteri amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="63bb1-433">Bağlantı dizesi gerektiren bir veritabanı kullanmak `ConfigurationBuilder` için, bağlantı dizesini başka bir yapılandırma sağlayıcısından sağlamak için ikincil bir uygulama uygulayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="63bb1-434">Sağlayıcı başlangıçta yapılandırma içine bir veritabanı tablosu okur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="63bb1-435">Sağlayıcı, veritabanını anahtar başına sorgulamıyor.</span><span class="sxs-lookup"><span data-stu-id="63bb1-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="63bb1-436">Yeniden yükleme-on-change uygulanmaz, bu nedenle uygulama başladıktan sonra veritabanını güncelleştirmenin uygulamanın yapılandırması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="63bb1-437">Yapılandırma `EFConfigurationValue` değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="63bb1-438">*Modeller/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="63bb1-439">Yapılandırılan `EFConfigurationContext` değerleri depolamak ve erişmek için bir ekleyin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="63bb1-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="63bb1-441">Uygulayan bir sınıf <xref:Microsoft.Extensions.Configuration.IConfigurationSource>oluşturun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="63bb1-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="63bb1-443">'den <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>devralarak özel yapılandırma sağlayıcısı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="63bb1-444">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="63bb1-445">[Yapılandırma anahtarları büyük/küçük harf duyarsız olduğundan,](#keys)veritabanını başlatmak için kullanılan sözlük büyük/küçük harf duyarlı karşılaştırıcı[(StringComparer.OrdinalIgnoreCase)](xref:System.StringComparer.OrdinalIgnoreCase)ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="63bb1-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="63bb1-447">Uzatma `AddEFConfiguration` yöntemi, yapılandırma kaynağını bir `ConfigurationBuilder`.'ye eklemeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="63bb1-448">*Uzantılar/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="63bb1-449">Aşağıdaki `EFConfigurationProvider` *kod, Program.cs*özel in nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="63bb1-450">Başlangıç'ta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-450">Access configuration in Startup</span></span>

<span data-ttu-id="63bb1-451">Aşağıdaki kod yapılandırma verilerini `Startup` yöntemlerle görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="63bb1-452">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişim örneği için Bkz. [Uygulama başlangıç: Kolaylık yöntemleri.](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="63bb1-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="63bb1-453">Razor Pages'de erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="63bb1-454">Aşağıdaki kod, bir Jilet Sayfasında yapılandırma verilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="63bb1-455">MVC görünüm dosyasındaki yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="63bb1-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="63bb1-456">Aşağıdaki kod, yapılandırma verilerini MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="63bb1-457">Ana bilgisayar ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-457">Host versus app configuration</span></span>

<span data-ttu-id="63bb1-458">Uygulama yapılandırılmadan ve başlatılmadan önce, bir *ana bilgisayar* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="63bb1-459">Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="63bb1-460">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="63bb1-461">Ana bilgisayar yapılandırma anahtar değeri çiftleri de uygulamanın yapılandırmasında yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="63bb1-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="63bb1-462">Ana bilgisayar oluşturulurken yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının ana <xref:fundamentals/index#host>bilgisayar yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="63bb1-463">Varsayılan ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-463">Default host configuration</span></span>

<span data-ttu-id="63bb1-464">[Web Ana Bilgisayar'ı](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırma yla ilgili ayrıntılar [için, bu konunun ASP.NET Core 2.2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="63bb1-465">Ana bilgisayar yapılandırması şu andan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="63bb1-466">Çevre Değişkenleri yapılandırma `DOTNET_` sağlayıcısını `DOTNET_ENVIRONMENT`kullanarak (örneğin, ) önceden belirlenmiş ortam [değişkenleri.](#environment-variables-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="63bb1-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="63bb1-467">Yapılandırma anahtar`DOTNET_`değeri çiftleri yüklendiğinde önek ( ) çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="63bb1-468">Komut satırı yapılandırma [sağlayıcısını](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="63bb1-469">Web Host varsayılan yapılandırma`ConfigureWebHostDefaults`kuruldu ( ):</span><span class="sxs-lookup"><span data-stu-id="63bb1-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="63bb1-470">Kerkenez web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="63bb1-471">Ana Bilgisayar Filtreleme Ara Ware ekleyin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="63bb1-472">Çevre değişkeni `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true`' ne ayarlanmışsa, İlezli Üstbilgi Aralığı ekle.</span><span class="sxs-lookup"><span data-stu-id="63bb1-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="63bb1-473">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="63bb1-474">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="63bb1-474">Other configuration</span></span>

<span data-ttu-id="63bb1-475">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="63bb1-476">Core uygulamalarını çalıştırmanın ve ASP.NET barındırmanın diğer yönleri, bu başlıkta yer almayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="63bb1-477">*launch.json*/*launchSettings.json* Geliştirme ortamı için yapılandırma dosyaları araç, açıklanan:</span><span class="sxs-lookup"><span data-stu-id="63bb1-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="63bb1-478">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="63bb1-479">Dosyaların Geliştirme senaryoları için ASP.NET Temel uygulamaları yapılandırmak için kullanıldığı belgeler kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="63bb1-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="63bb1-480">*web.config* bir sunucu yapılandırma dosyası, aşağıdaki konularda açıklanan:</span><span class="sxs-lookup"><span data-stu-id="63bb1-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="63bb1-481">ASP.NET önceki sürümlerinden uygulama yapılandırmasının geçirilmesi <xref:migration/proper-to-2x/index#store-configurations>hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="63bb1-482">Harici bir montajdan yapılandırma ekleme</span><span class="sxs-lookup"><span data-stu-id="63bb1-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="63bb1-483">Uygulama, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> uygulamanın sınıfının dışındaki harici bir derlemeden başlangıçta bir `Startup` uygulamaya geliştirme eklemeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="63bb1-484">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63bb1-485">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="63bb1-485">Additional resources</span></span>

* [<span data-ttu-id="63bb1-486">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="63bb1-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="63bb1-487">ASP.NET Core'daki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından kurulan anahtar değer çiftlerini temel adamıştır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="63bb1-488">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar değer çiftleri halinde okur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="63bb1-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="63bb1-489">Azure Key Vault</span></span>
* <span data-ttu-id="63bb1-490">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-490">Azure App Configuration</span></span>
* <span data-ttu-id="63bb1-491">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-491">Command-line arguments</span></span>
* <span data-ttu-id="63bb1-492">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="63bb1-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="63bb1-493">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-493">Directory files</span></span>
* <span data-ttu-id="63bb1-494">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-494">Environment variables</span></span>
* <span data-ttu-id="63bb1-495">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-495">In-memory .NET objects</span></span>
* <span data-ttu-id="63bb1-496">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-496">Settings files</span></span>

<span data-ttu-id="63bb1-497">Yaygın yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri[(Microsoft.Extensions.Configuration)](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahildir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="63bb1-498">Örnek uygulamada ve örnek uygulamada yer <xref:Microsoft.Extensions.Configuration> alan kod örnekleri ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="63bb1-499">*Seçenekler deseni,* bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="63bb1-500">Seçenekler, ilgili ayar gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="63bb1-501">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="63bb1-502">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63bb1-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="63bb1-503">Ana bilgisayar ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-503">Host versus app configuration</span></span>

<span data-ttu-id="63bb1-504">Uygulama yapılandırılmadan ve başlatılmadan önce, bir *ana bilgisayar* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="63bb1-505">Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="63bb1-506">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="63bb1-507">Ana bilgisayar yapılandırma anahtar değeri çiftleri de uygulamanın yapılandırmasında yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="63bb1-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="63bb1-508">Ana bilgisayar oluşturulurken yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının ana <xref:fundamentals/index#host>bilgisayar yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="63bb1-509">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="63bb1-509">Other configuration</span></span>

<span data-ttu-id="63bb1-510">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="63bb1-511">Core uygulamalarını çalıştırmanın ve ASP.NET barındırmanın diğer yönleri, bu başlıkta yer almayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="63bb1-512">*launch.json*/*launchSettings.json* Geliştirme ortamı için yapılandırma dosyaları araç, açıklanan:</span><span class="sxs-lookup"><span data-stu-id="63bb1-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="63bb1-513">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="63bb1-514">Dosyaların Geliştirme senaryoları için ASP.NET Temel uygulamaları yapılandırmak için kullanıldığı belgeler kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="63bb1-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="63bb1-515">*web.config* bir sunucu yapılandırma dosyası, aşağıdaki konularda açıklanan:</span><span class="sxs-lookup"><span data-stu-id="63bb1-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="63bb1-516">ASP.NET önceki sürümlerinden uygulama yapılandırmasının geçirilmesi <xref:migration/proper-to-2x/index#store-configurations>hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="63bb1-517">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="63bb1-517">Default configuration</span></span>

<span data-ttu-id="63bb1-518">Core [dotnet ASP.NET](/dotnet/core/tools/dotnet-new) yeni şablonları temel <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> alan web uygulamaları, bir ana bilgisayar inşa ederken çağrı yapar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="63bb1-519">`CreateDefaultBuilder`aşağıdaki sırada uygulama için varsayılan yapılandırma sağlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="63bb1-520">Aşağıdakiler [Web Barındırma'nı](xref:fundamentals/host/web-host)kullanan uygulamalar için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="63bb1-521">[Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için [bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="63bb1-522">Ana bilgisayar yapılandırması şu andan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="63bb1-523">Çevre Değişkenleri Yapılandırma `ASPNETCORE_` Sağlayıcısı'nı `ASPNETCORE_ENVIRONMENT`kullanarak (örneğin, ) önceden belirlenmiş ortam [değişkenleri.](#environment-variables-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="63bb1-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="63bb1-524">Yapılandırma anahtar`ASPNETCORE_`değeri çiftleri yüklendiğinde önek ( ) çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="63bb1-525">Komut satırı Yapılandırma [Sağlayıcısı'nı](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="63bb1-526">Uygulama yapılandırması şu andan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="63bb1-527">[dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanarak *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="63bb1-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="63bb1-528">*ayarları. {Environment}.json* [Dosya Yapılandırma Sağlayıcısı'nı](#file-configuration-provider)kullanarak.</span><span class="sxs-lookup"><span data-stu-id="63bb1-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="63bb1-529">[Uygulama](xref:security/app-secrets) giriş montajını `Development` kullanarak ortamda çalıştığında Gizli Yönetici.</span><span class="sxs-lookup"><span data-stu-id="63bb1-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="63bb1-530">Çevre Değişkenleri Yapılandırma Sağlayıcısı'nı kullanarak ortam [değişkenleri.](#environment-variables-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="63bb1-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="63bb1-531">Komut satırı Yapılandırma [Sağlayıcısı'nı](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="63bb1-532">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="63bb1-532">Security</span></span>

<span data-ttu-id="63bb1-533">Hassas yapılandırma verilerini güvence altına almak için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="63bb1-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="63bb1-534">Parolaları veya diğer hassas verileri asla yapılandırma sağlayıcı kodunda veya düz metin yapılandırma dosyalarında depolamayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="63bb1-535">Üretim sırlarını geliştirme veya test ortamlarında kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="63bb1-536">Yanlışlıkla bir kaynak kodu deposuna işlenmeyecek şekilde projenin dışındaki sırları belirtin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="63bb1-537">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="63bb1-538"><xref:security/app-secrets>&ndash; Hassas verileri depolamak için ortam değişkenlerini kullanma konusunda öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="63bb1-539">Gizli Yönetici, kullanıcı sırlarını yerel sistemdeki bir JSON dosyasında depolamak için Dosya Yapılandırma Sağlayıcısı'nı kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="63bb1-540">Dosya Yapılandırma Sağlayıcısı daha sonra bu konuda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="63bb1-541">[Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamaları için uygulama sırlarını güvenle saklar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="63bb1-542">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="63bb1-543">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="63bb1-543">Hierarchical configuration data</span></span>

<span data-ttu-id="63bb1-544">Yapılandırma API'si, hiyerarşik verileri yapılandırma tuşlarında bir sınırlayıcı kullanarak düzleştirmek le hiyerarşik yapılandırma verilerini koruyabilme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="63bb1-545">Aşağıdaki JSON dosyasında, dört anahtar iki bölümden oluşan yapılandırılmış bir hiyerarşide bulunur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="63bb1-546">Dosya yapılandırmaya okunduğunda, yapılandırma kaynağının özgün hiyerarşik veri yapısını korumak için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="63bb1-547">Bölümler ve anahtarlar, orijinal yapıyı korumak`:`için bir kolon () kullanımı ile düzleştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="63bb1-548">bölüm0:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-548">section0:key0</span></span>
* <span data-ttu-id="63bb1-549">bölüm0:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-549">section0:key1</span></span>
* <span data-ttu-id="63bb1-550">bölüm1:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-550">section1:key0</span></span>
* <span data-ttu-id="63bb1-551">bölüm1:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-551">section1:key1</span></span>

<span data-ttu-id="63bb1-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verilerindeki bir bölümün bölümlerini ve altlarını yalıtmak için yöntemler kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="63bb1-553">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)'de açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="63bb1-554">Kurallar</span><span class="sxs-lookup"><span data-stu-id="63bb1-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="63bb1-555">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="63bb1-555">Sources and providers</span></span>

<span data-ttu-id="63bb1-556">Uygulama başlatmada, yapılandırma kaynakları yapılandırma sağlayıcılarının belirtildiği şekilde okunur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="63bb1-557">Değişiklik algılamasını uygulayan yapılandırma sağlayıcıları, temel bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="63bb1-558">Örneğin, Dosya Yapılandırma Sağlayıcısı (bu konuda daha sonra açıklanmıştır) ve [Azure Anahtar Kasası Yapılandırma Sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılaması uygular.</span><span class="sxs-lookup"><span data-stu-id="63bb1-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="63bb1-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) konteynerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="63bb1-560"><xref:Microsoft.Extensions.Configuration.IConfiguration>sınıf için yapılandırma elde <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> etmek için <xref:Microsoft.AspNetCore.Mvc.Controller> bir Razor Pages veya MVC içine enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="63bb1-561">Aşağıdaki örneklerde, `_config` alan yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="63bb1-562">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandığında kullanılamadığından, DI'den yararlanamaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="63bb1-563">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="63bb1-563">Keys</span></span>

<span data-ttu-id="63bb1-564">Yapılandırma anahtarları aşağıdaki kuralları benimser:</span><span class="sxs-lookup"><span data-stu-id="63bb1-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="63bb1-565">Anahtarlar büyük/küçük harf duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-565">Keys are case-insensitive.</span></span> <span data-ttu-id="63bb1-566">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="63bb1-567">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlanırsa, anahtar üzerinde ayarlanan son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="63bb1-568">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="63bb1-568">Hierarchical keys</span></span>
  * <span data-ttu-id="63bb1-569">Yapılandırma API'si içinde,`:`bir iki nokta üst üste ayırıcı ( ) tüm platformlarda çalışır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="63bb1-570">Ortam değişkenlerinde, bir kolon ayırıcısı tüm platformlarda çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="63bb1-571">Bir çift alt`__`çizgi ( ) tüm platformlar tarafından desteklenir ve otomatik olarak bir üst üste dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="63bb1-572">Azure Anahtar Kasası'nda `--` hiyerarşik tuşlar ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="63bb1-573">Sırlar uygulamanın yapılandırmasına yüklendiğinde tireleri bir üst nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="63bb1-574">Yapılandırma <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="63bb1-575">Dizi bağlama, [Bind dizisinde bir sınıf bölümüne](#bind-an-array-to-a-class) açıklanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="63bb1-576">Değerler</span><span class="sxs-lookup"><span data-stu-id="63bb1-576">Values</span></span>

<span data-ttu-id="63bb1-577">Yapılandırma değerleri aşağıdaki kuralları benimser:</span><span class="sxs-lookup"><span data-stu-id="63bb1-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="63bb1-578">Değerler dizeleri vardır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-578">Values are strings.</span></span>
* <span data-ttu-id="63bb1-579">Null değerleri yapılandırmada depolanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="63bb1-580">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="63bb1-580">Providers</span></span>

<span data-ttu-id="63bb1-581">Aşağıdaki tablo, ASP.NET Core uygulamalarının kullanabileceği yapılandırma sağlayıcılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="63bb1-582">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="63bb1-582">Provider</span></span> | <span data-ttu-id="63bb1-583">Yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="63bb1-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="63bb1-584">[Azure Key Vault Yapılandırma Sağlayıcısı](xref:security/key-vault-configuration) (*Güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="63bb1-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="63bb1-585">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="63bb1-585">Azure Key Vault</span></span> |
| <span data-ttu-id="63bb1-586">[Azure Uygulama Yapılandırma Sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="63bb1-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="63bb1-587">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63bb1-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="63bb1-588">Komut Satırı Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="63bb1-589">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-589">Command-line parameters</span></span> |
| [<span data-ttu-id="63bb1-590">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="63bb1-591">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="63bb1-591">Custom source</span></span> |
| [<span data-ttu-id="63bb1-592">Çevre Değişkenleri Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="63bb1-593">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-593">Environment variables</span></span> |
| [<span data-ttu-id="63bb1-594">Dosya Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="63bb1-595">Dosyalar (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="63bb1-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="63bb1-596">Dosya başına anahtar Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="63bb1-597">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="63bb1-597">Directory files</span></span> |
| [<span data-ttu-id="63bb1-598">Bellek Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="63bb1-599">Bellek içi koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="63bb1-599">In-memory collections</span></span> |
| <span data-ttu-id="63bb1-600">[Kullanıcı sırları (Secret Manager)](xref:security/app-secrets) *(Güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="63bb1-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="63bb1-601">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="63bb1-601">File in the user profile directory</span></span> |

<span data-ttu-id="63bb1-602">Yapılandırma kaynakları, yapılandırma sağlayıcılarının başlangıçta belirtilmiş olması sırasına göre okunur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="63bb1-603">Bu konuda açıklanan yapılandırma sağlayıcıları, kodun düzenlediği sırada değil, alfabetik sırada açıklanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="63bb1-604">Yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynaklarının önceliklerine uyacak şekilde kodolarak sipariş edin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="63bb1-605">Yapılandırma sağlayıcılarının tipik bir sırası:</span><span class="sxs-lookup"><span data-stu-id="63bb1-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="63bb1-606">Dosyalar (*appsettings.json*, *appsettings.{ Environment}.json*, `{Environment}` uygulamanın geçerli barındırma ortamı nerededir)</span><span class="sxs-lookup"><span data-stu-id="63bb1-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="63bb1-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="63bb1-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="63bb1-608">[Kullanıcı sırları (Secret Manager)](xref:security/app-secrets) (Yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="63bb1-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="63bb1-609">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-609">Environment variables</span></span>
1. <span data-ttu-id="63bb1-610">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-610">Command-line arguments</span></span>

<span data-ttu-id="63bb1-611">Komut satırı yapılandırma sağlayıcısını, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için bir dizi sağlayıcıya son olarak konumlandırmak yaygın bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="63bb1-612">Yeni bir ana bilgisayar oluşturucu ile `CreateDefaultBuilder`başharfe geçtiğinde, önceki sağlayıcı sıraları kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="63bb1-613">Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="63bb1-614">Ev sahibi oluşturucuyu UseConfiguration ile yapılandırın</span><span class="sxs-lookup"><span data-stu-id="63bb1-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="63bb1-615">Ana bilgisayar oluşturucuyu <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırmak için, yapılandırmaile birlikte ana bilgisayar oluşturucuyu çağırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="63bb1-616">YapılandırmaAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="63bb1-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="63bb1-617">Aşağıdakiler tarafından otomatik olarak eklenenlere ek olarak uygulamanın yapılandırma `CreateDefaultBuilder`sağlayıcılarını belirtmek için ana bilgisayarını kurarken arayın: `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="63bb1-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="63bb1-618">Komut satırı bağımsız değişkenleriyle önceki yapılandırmayı geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="63bb1-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="63bb1-619">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilecek uygulama yapılandırmasını sağlamak için son çağrıyı arayın: `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="63bb1-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="63bb1-620">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldırma</span><span class="sxs-lookup"><span data-stu-id="63bb1-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="63bb1-621">Tarafından eklenen sağlayıcıları `CreateDefaultBuilder`kaldırmak için, [önce IConfigurationBuilder.Sources'da](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) [Clear'i](/dotnet/api/system.collections.generic.icollection-1.clear) arayın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="63bb1-622">Uygulama başlatma sırasında yapılandırmayı tüketin</span><span class="sxs-lookup"><span data-stu-id="63bb1-622">Consume configuration during app startup</span></span>

<span data-ttu-id="63bb1-623">Uygulamada sağlanan `ConfigureAppConfiguration` yapılandırma, uygulamanın başlatılması sırasında kullanılabilir. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="63bb1-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="63bb1-624">Daha fazla bilgi için başlangıç bölümünde [Access yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="63bb1-625">Komut Satırı Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="63bb1-626">Çalışma <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> zamanında komut satırı bağımsız değişken anahtar değeri çiftlerinden yükler yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="63bb1-627">Komut satırı yapılandırmasını <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> etkinleştirmek için, uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemi .</span><span class="sxs-lookup"><span data-stu-id="63bb1-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="63bb1-628">`AddCommandLine`çağrıldığında `CreateDefaultBuilder(string [])` otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="63bb1-629">Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="63bb1-630">`CreateDefaultBuilder`ayrıca yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="63bb1-631">*Appsettings.json* ve appsettings'ten isteğe bağlı *yapılandırma.{ Çevre}.json* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="63bb1-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="63bb1-632">Geliştirme ortamında [kullanıcı sırları (Gizli Yönetici).](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="63bb1-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="63bb1-633">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-633">Environment variables.</span></span>

<span data-ttu-id="63bb1-634">`CreateDefaultBuilder`Komut satırı Yapılandırma Sağlayıcısı son ekler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="63bb1-635">Çalışma zamanında geçirilen komut satırı bağımsız değişkenleri, diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="63bb1-636">`CreateDefaultBuilder`ev sahibi inşa edildiğinde hareket eder.</span><span class="sxs-lookup"><span data-stu-id="63bb1-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="63bb1-637">Bu nedenle, komut satırı `CreateDefaultBuilder` yapılandırması tarafından etkinleştirilen ana bilgisayar yapısının nasıl yapılandırıldığı etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="63bb1-638">ASP.NET Core şablonlarına dayalı uygulamalar `AddCommandLine` için, zaten `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="63bb1-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="63bb1-639">Ek yapılandırma sağlayıcıları eklemek ve komut satırı bağımsız değişkenleri olan bu sağlayıcılardan yapılandırmayı geçersiz `ConfigureAppConfiguration` kılma `AddCommandLine` yeteneğini korumak için, uygulamanın ek sağlayıcılarını çağırın ve en son çağırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="63bb1-640">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="63bb1-640">**Example**</span></span>

<span data-ttu-id="63bb1-641">Örnek uygulama, ana bilgisayarı oluşturmak `CreateDefaultBuilder` için <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>statik kolaylık yönteminden yararlanır ve bu yöntem .</span><span class="sxs-lookup"><span data-stu-id="63bb1-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="63bb1-642">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="63bb1-643">Komuta bir komut satırı `dotnet run` bağımsız `dotnet run CommandLineKey=CommandLineValue`değişkeni ver.</span><span class="sxs-lookup"><span data-stu-id="63bb1-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="63bb1-644">Uygulama çalışmaya devam ettikten sonra, uygulamanın `http://localhost:5000`bir tarayıcısını .</span><span class="sxs-lookup"><span data-stu-id="63bb1-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="63bb1-645">Çıktının, ''ye sağlanan yapılandırma komut satırı bağımsız değişkeni için anahtar değer çiftini `dotnet run`içerdiğini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="63bb1-646">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="63bb1-646">Arguments</span></span>

<span data-ttu-id="63bb1-647">Değer, eşitler işaretini`=`(), veya değer bir alanı`--` `/`takip ettiğinde anahtarın bir önek (veya ) olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="63bb1-648">Eşitler işareti kullanılıyorsa (örneğin, `CommandLineKey=`değer gerekli değildir).</span><span class="sxs-lookup"><span data-stu-id="63bb1-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="63bb1-649">Anahtar öneki</span><span class="sxs-lookup"><span data-stu-id="63bb1-649">Key prefix</span></span>               | <span data-ttu-id="63bb1-650">Örnek</span><span class="sxs-lookup"><span data-stu-id="63bb1-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="63bb1-651">Önek yok</span><span class="sxs-lookup"><span data-stu-id="63bb1-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="63bb1-652">İki tire (`--`)</span><span class="sxs-lookup"><span data-stu-id="63bb1-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="63bb1-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="63bb1-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="63bb1-654">İleri eğik çizgi (`/`)</span><span class="sxs-lookup"><span data-stu-id="63bb1-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="63bb1-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="63bb1-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="63bb1-656">Aynı komut içinde, eşit işareti kullanan komut satırı bağımsız değişken anahtar değer çiftlerini boşluk kullanan anahtar değer çiftleri ile karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="63bb1-657">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="63bb1-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="63bb1-658">Anahtar eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="63bb1-658">Switch mappings</span></span>

<span data-ttu-id="63bb1-659">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="63bb1-660">Bir ile el ile <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yapılandırma inşa ederken, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> yöntemiçin anahtar değiştirmeleri bir sözlük sağlayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="63bb1-661">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük komut satırı bağımsız değişkeni tarafından sağlanan anahtarla eşleşen bir anahtar için işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="63bb1-662">Komut satırı anahtarı sözlükte bulunursa, anahtar değeri çiftini uygulamanın yapılandırmasına ayarlamak için sözlük değeri (anahtar değiştirme) geri aktarılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="63bb1-663">Tek bir çizgi ile önceden belirlenmiş herhangi bir komut`-`satırı tuşu için bir anahtar eşlemesi gereklidir ( ).</span><span class="sxs-lookup"><span data-stu-id="63bb1-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="63bb1-664">Haritalama ları sözlük anahtar kurallarını değiştirin:</span><span class="sxs-lookup"><span data-stu-id="63bb1-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="63bb1-665">Anahtarlar bir çizgi (`-`) veya çift`--`çizgi ( ile başlamalıdır).</span><span class="sxs-lookup"><span data-stu-id="63bb1-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="63bb1-666">Anahtar eşlemeler sözlüğü yinelenen anahtarları içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="63bb1-667">Anahtar eşlemesözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="63bb1-668">Aşağıdaki örnekte, iki anahtar eşleme oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="63bb1-669">Ana bilgisayar oluşturulduğunda, anahtar eşlemeleri sözlüğüyle arayın: `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="63bb1-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="63bb1-670">Anahtar eşlemeleri kullanan uygulamalar için, `CreateDefaultBuilder` çağrı bağımsız değişkenleri geçmemelidir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="63bb1-671">Yöntemin `CreateDefaultBuilder` `AddCommandLine` araması eşlenen anahtarları içermez ve anahtar eşleme sözlüğünden `CreateDefaultBuilder`''ye geçmenin bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="63bb1-672">Çözüm bağımsız değişkenleri geçirmek `CreateDefaultBuilder` için değil, bunun `ConfigurationBuilder` yerine `AddCommandLine` yöntemin yöntemihem bağımsız değişkenleri hem de anahtar eşleme sözlüğü işlemek için izin vermektir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="63bb1-673">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="63bb1-674">Anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-674">Key</span></span>       | <span data-ttu-id="63bb1-675">Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="63bb1-676">Uygulamayı başlatırken anahtar eşlenen tuşlar kullanılırsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="63bb1-677">Önceki komutu çalıştırdıktan sonra yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="63bb1-678">Anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-678">Key</span></span>               | <span data-ttu-id="63bb1-679">Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="63bb1-680">Çevre Değişkenleri Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="63bb1-681">Çalışma <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> zamanında ortam değişken anahtar değeri çiftlerinden yükler yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="63bb1-682">Ortam değişkenleri yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir .'</span><span class="sxs-lookup"><span data-stu-id="63bb1-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="63bb1-683">[Azure Uygulama Hizmeti,](https://azure.microsoft.com/services/app-service/) Azure Portalı'nda Çevre Değişkenleri Yapılandırma Sağlayıcısı'nı kullanarak uygulama yapılandırmasını geçersiz kılabilecek ortam değişkenleri ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="63bb1-684">Daha fazla bilgi için Azure [Uygulamaları: Azure Portalı'nı kullanarak uygulama yapılandırmasını geçersiz kılın.](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)</span><span class="sxs-lookup"><span data-stu-id="63bb1-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="63bb1-685">`AddEnvironmentVariables`yeni bir ana bilgisayar [oluşturucu Web Host](xref:fundamentals/host/web-host) ile baş harfe döndüğünde ve `CreateDefaultBuilder` çağrıldığında ana bilgisayar [host configuration](#host-versus-app-configuration) `ASPNETCORE_` yapılandırması için önceden belirlenmiş ortam değişkenlerini yüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="63bb1-686">Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="63bb1-687">`CreateDefaultBuilder`ayrıca yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="63bb1-688">Önek olmadan arayarak `AddEnvironmentVariables` önceden ayarlanmamış ortam değişkenlerinden uygulama yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="63bb1-689">*Appsettings.json* ve appsettings'ten isteğe bağlı *yapılandırma.{ Çevre}.json* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="63bb1-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="63bb1-690">Geliştirme ortamında [kullanıcı sırları (Gizli Yönetici).](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="63bb1-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="63bb1-691">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-691">Command-line arguments.</span></span>

<span data-ttu-id="63bb1-692">Yapılandırma kullanıcı sırları ve *uygulama* dosyalarından oluşturulduktan sonra Çevre Değişkenleri Yapılandırma Sağlayıcısı çağrılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="63bb1-693">Sağlayıcıyı bu konumda çağırmak, çalışma zamanında okunan ortam değişkenlerinin kullanıcı sırları ve *uygulama* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="63bb1-694">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamanın ek sağlayıcılarını arayın `ConfigureAppConfiguration` ve önek ile arayın: `AddEnvironmentVariables`</span><span class="sxs-lookup"><span data-stu-id="63bb1-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="63bb1-695">Diğer `AddEnvironmentVariables` sağlayıcılardan gelen değerleri geçersiz kılmak için verilen önek içeren ortam değişkenlerine izin vermek için son çağrıyı verin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="63bb1-696">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="63bb1-696">**Example**</span></span>

<span data-ttu-id="63bb1-697">Örnek uygulama, ana bilgisayarı oluşturmak `CreateDefaultBuilder` için `AddEnvironmentVariables`statik kolaylık yönteminden yararlanır ve bu yöntem .</span><span class="sxs-lookup"><span data-stu-id="63bb1-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="63bb1-698">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-698">Run the sample app.</span></span> <span data-ttu-id="63bb1-699">Uygulamanın tarayıcısını ' `http://localhost:5000`dan aç.</span><span class="sxs-lookup"><span data-stu-id="63bb1-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="63bb1-700">Çıktının ortam değişkeni `ENVIRONMENT`için anahtar değer çiftini içerdiğini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="63bb1-701">Değer, genellikle `Development` yerel olarak çalışırken uygulamanın çalıştırıldığı ortamı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="63bb1-702">Uygulama tarafından işlenen ortam değişkenlerinin listesini kısa tutmak için uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="63bb1-703">Örnek uygulamanın *Pages/Index.cshtml.cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="63bb1-704">Uygulamanın kullanabileceği tüm ortam değişkenlerini ortaya çıkarmak `FilteredConfiguration` için *Pages/Index.cshtml.cs'deki* leri aşağıdaki lerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="63bb1-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="63bb1-705">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="63bb1-705">Prefixes</span></span>

<span data-ttu-id="63bb1-706">`AddEnvironmentVariables` Yönteme bir önek verilirken uygulamanın yapılandırmasına yüklenen ortam değişkenleri filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="63bb1-707">Örneğin, önekteki `CUSTOM_`ortam değişkenlerini filtrelemek için önek'i yapılandırma sağlayıcısına sağlayın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="63bb1-708">Yapılandırma anahtar değeri çiftleri oluşturulduğunda önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="63bb1-709">Ana bilgisayar oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="63bb1-710">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="63bb1-711">**Bağlantı dizeleri önekleri**</span><span class="sxs-lookup"><span data-stu-id="63bb1-711">**Connection string prefixes**</span></span>

<span data-ttu-id="63bb1-712">Yapılandırma API'si, uygulama ortamı için Azure bağlantı dizelerini yapılandırmada yer alan dört bağlantı dize skalası değişkeni için özel işleme kurallarına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="63bb1-713">Tabloda gösterilen öneklerle ortam değişkenleri, ''ye önek sağedilmezse uygulamaya `AddEnvironmentVariables`yüklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="63bb1-714">Bağlantı dize öneki</span><span class="sxs-lookup"><span data-stu-id="63bb1-714">Connection string prefix</span></span> | <span data-ttu-id="63bb1-715">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="63bb1-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="63bb1-716">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="63bb1-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="63bb1-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="63bb1-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="63bb1-718">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="63bb1-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="63bb1-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="63bb1-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="63bb1-720">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önek ile yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="63bb1-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="63bb1-721">Yapılandırma anahtarı, ortam değişkeni önekikaldırılarak ve yapılandırma anahtar`ConnectionStrings`bölümü eklenerek oluşturulur ( ).</span><span class="sxs-lookup"><span data-stu-id="63bb1-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="63bb1-722">Veritabanı bağlantı sağlayıcısını temsil eden (belirtilen sağlayıcı bulunmayanlar `CUSTOMCONNSTR_`hariç) yeni bir yapılandırma anahtar değeri çifti oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="63bb1-723">Ortam değişken anahtarı</span><span class="sxs-lookup"><span data-stu-id="63bb1-723">Environment variable key</span></span> | <span data-ttu-id="63bb1-724">Dönüştürülmüş yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="63bb1-724">Converted configuration key</span></span> | <span data-ttu-id="63bb1-725">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="63bb1-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-726">Yapılandırma girişi oluşturulmamadı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-727">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="63bb1-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="63bb1-728">Değer:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="63bb1-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-729">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="63bb1-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="63bb1-730">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="63bb1-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="63bb1-731">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="63bb1-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="63bb1-732">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="63bb1-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="63bb1-733">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="63bb1-733">**Example**</span></span>

<span data-ttu-id="63bb1-734">Sunucuda özel bir bağlantı dizesi ortamı değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="63bb1-735">İsim &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="63bb1-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="63bb1-736">Değer &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="63bb1-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="63bb1-737">Enjekte `IConfiguration` edilip adlı `_config`bir alana atanmışsa, değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="63bb1-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="63bb1-738">Dosya Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-738">File Configuration Provider</span></span>

<span data-ttu-id="63bb1-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>dosya sisteminden yapılandırma yükleme için taban sınıftır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="63bb1-740">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine adanmıştır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="63bb1-741">INI Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="63bb1-742">JSON Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="63bb1-743">XML Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="63bb1-744">INI Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-744">INI Configuration Provider</span></span>

<span data-ttu-id="63bb1-745">Çalışma <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> zamanında INI dosya anahtar değeri çiftlerinden yükler yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="63bb1-746">INI dosya yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örnekte çağırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="63bb1-747">Üst üste INI dosya yapılandırmasında bir kesit sınırlayıcı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="63bb1-748">Aşırı yükleme izni belirterek:</span><span class="sxs-lookup"><span data-stu-id="63bb1-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="63bb1-749">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-749">Whether the file is optional.</span></span>
* <span data-ttu-id="63bb1-750">Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.</span><span class="sxs-lookup"><span data-stu-id="63bb1-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="63bb1-751">Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="63bb1-752">Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="63bb1-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="63bb1-753">INI yapılandırma dosyasının genel bir örneği:</span><span class="sxs-lookup"><span data-stu-id="63bb1-753">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="63bb1-754">Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="63bb1-755">bölüm0:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-755">section0:key0</span></span>
* <span data-ttu-id="63bb1-756">bölüm0:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-756">section0:key1</span></span>
* <span data-ttu-id="63bb1-757">bölüm1:alt bölüm:anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-757">section1:subsection:key</span></span>
* <span data-ttu-id="63bb1-758">bölüm2:altbölüm0:anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-758">section2:subsection0:key</span></span>
* <span data-ttu-id="63bb1-759">bölüm2:altbölüm1:anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="63bb1-760">JSON Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-760">JSON Configuration Provider</span></span>

<span data-ttu-id="63bb1-761">Çalışma <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> süresi sırasında JSON dosya anahtar değeri çiftlerinden gelen yükler yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="63bb1-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="63bb1-762">JSON dosya yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örnekte arayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="63bb1-763">Aşırı yükleme izni belirterek:</span><span class="sxs-lookup"><span data-stu-id="63bb1-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="63bb1-764">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-764">Whether the file is optional.</span></span>
* <span data-ttu-id="63bb1-765">Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.</span><span class="sxs-lookup"><span data-stu-id="63bb1-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="63bb1-766">Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="63bb1-767">`AddJsonFile`yeni bir ana bilgisayar oluşturucu ile `CreateDefaultBuilder`baş harfe döndüğünde otomatik olarak iki kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="63bb1-768">Yöntem, yapılandırmayı yüklemek için aşağıdakilerden çağrılır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="63bb1-769">*appsettings.json* &ndash; Bu dosya ilk okunur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="63bb1-770">Dosyanın ortam sürümü *appsettings.json* dosyası tarafından sağlanan değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="63bb1-771">*ayarları. {Environment}.json* &ndash; Dosyanın ortam sürümü [IHostingEnvironment.EnvironmentName'e](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)göre yüklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="63bb1-772">Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="63bb1-773">`CreateDefaultBuilder`ayrıca yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="63bb1-774">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-774">Environment variables.</span></span>
* <span data-ttu-id="63bb1-775">Geliştirme ortamında [kullanıcı sırları (Gizli Yönetici).](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="63bb1-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="63bb1-776">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="63bb1-776">Command-line arguments.</span></span>

<span data-ttu-id="63bb1-777">Önce JSON Yapılandırma Sağlayıcısı kurulur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="63bb1-778">Bu nedenle, kullanıcı sırları, ortam değişkenleri ve komut satırı bağımsız *değişkenleri, uygulama ayarları* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="63bb1-779">`ConfigureAppConfiguration` *Appsettings.json* ve appsettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek için ana bilgisayarını oluştururken *arayın.{ Çevre}.json*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="63bb1-780">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="63bb1-780">**Example**</span></span>

<span data-ttu-id="63bb1-781">Örnek uygulama, iki çağrı içeren `CreateDefaultBuilder` ana bilgisayar oluşturmak için `AddJsonFile`statik kolaylık yönteminden yararlanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="63bb1-782">`AddJsonFile` *appsettings.json*gelen yükler yapılandırma için ilk çağrı :</span><span class="sxs-lookup"><span data-stu-id="63bb1-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="63bb1-783">Uygulama ayarlarından `AddJsonFile` yükler yapılandırmasına ikinci *çağrı.{ Çevre}.json*.</span><span class="sxs-lookup"><span data-stu-id="63bb1-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="63bb1-784">*Uygulama ayarları için. Development.json* örnek uygulamasında aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="63bb1-785">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-785">Run the sample app.</span></span> <span data-ttu-id="63bb1-786">Uygulamanın tarayıcısını ' `http://localhost:5000`dan aç.</span><span class="sxs-lookup"><span data-stu-id="63bb1-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="63bb1-787">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="63bb1-788">Anahtarın günlük `Logging:LogLevel:Default` düzeyi, `Debug` uygulamayı Geliştirme ortamında çalıştırırken olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="63bb1-789">Örnek uygulamayı Üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="63bb1-790">*Özellikler/launchSettings.json* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="63bb1-791">`ConfigurationSample` Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini ' den ' e `Production`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="63bb1-792">Dosyayı kaydedin ve `dotnet run` uygulamayı komut kabuğunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="63bb1-793">*Uygulama ayarlarındaki ayarlar. Development.json* artık *appsettings.json'daki*ayarları geçersiz kılamaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="63bb1-794">Anahtarın `Logging:LogLevel:Default` günlük düzeyi `Warning`.</span><span class="sxs-lookup"><span data-stu-id="63bb1-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="63bb1-795">XML Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-795">XML Configuration Provider</span></span>

<span data-ttu-id="63bb1-796">XML <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> dosya anahtar değeri çiftlerinden gelen yükler yapılandırması çalışma zamanında.</span><span class="sxs-lookup"><span data-stu-id="63bb1-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="63bb1-797">XML dosya yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örnekte çağırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="63bb1-798">Aşırı yükleme izni belirterek:</span><span class="sxs-lookup"><span data-stu-id="63bb1-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="63bb1-799">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="63bb1-799">Whether the file is optional.</span></span>
* <span data-ttu-id="63bb1-800">Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.</span><span class="sxs-lookup"><span data-stu-id="63bb1-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="63bb1-801">Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="63bb1-802">Yapılandırma anahtar değeri çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="63bb1-803">Dosyada Belge Türü Tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="63bb1-804">Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="63bb1-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="63bb1-805">XML yapılandırma dosyaları bölümleri yinelemek için farklı öğe adları kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="63bb1-805">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="63bb1-806">Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="63bb1-807">bölüm0:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-807">section0:key0</span></span>
* <span data-ttu-id="63bb1-808">bölüm0:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-808">section0:key1</span></span>
* <span data-ttu-id="63bb1-809">bölüm1:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-809">section1:key0</span></span>
* <span data-ttu-id="63bb1-810">bölüm1:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-810">section1:key1</span></span>

<span data-ttu-id="63bb1-811">`name` Öznitelik öğeleri ayırt etmek için kullanılırsa aynı öğe adı kullanan yinelenen öğeler çalışır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="63bb1-812">Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="63bb1-813">bölüm:bölüm0:anahtar:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-813">section:section0:key:key0</span></span>
* <span data-ttu-id="63bb1-814">bölüm:bölüm0:anahtar:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-814">section:section0:key:key1</span></span>
* <span data-ttu-id="63bb1-815">bölüm:bölüm1:key:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-815">section:section1:key:key0</span></span>
* <span data-ttu-id="63bb1-816">bölüm:bölüm1:key:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-816">section:section1:key:key1</span></span>

<span data-ttu-id="63bb1-817">Öznitelikler değerleri sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="63bb1-818">Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:</span><span class="sxs-lookup"><span data-stu-id="63bb1-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="63bb1-819">key:öznitelik</span><span class="sxs-lookup"><span data-stu-id="63bb1-819">key:attribute</span></span>
* <span data-ttu-id="63bb1-820">bölüm:key:öznitelik</span><span class="sxs-lookup"><span data-stu-id="63bb1-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="63bb1-821">Dosya başına anahtar Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="63bb1-822">Yapılandırma <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> anahtar değeri çiftleri olarak bir dizin dosyaları kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="63bb1-823">Anahtar dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-823">The key is the file name.</span></span> <span data-ttu-id="63bb1-824">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-824">The value contains the file's contents.</span></span> <span data-ttu-id="63bb1-825">Anahtar başına dosya Yapılandırma Sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="63bb1-826">Dosya başına anahtar yapılandırmasını etkinleştirmek <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> için, uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini bir .'</span><span class="sxs-lookup"><span data-stu-id="63bb1-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="63bb1-827">`directoryPath` Dosyalara mutlak bir yol olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="63bb1-828">Aşırı yükleme izni belirterek:</span><span class="sxs-lookup"><span data-stu-id="63bb1-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="63bb1-829">Kaynağı `Action<KeyPerFileConfigurationSource>` yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="63bb1-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="63bb1-830">Dizinin isteğe bağlı olup olmadığı ve dizine giden yol.</span><span class="sxs-lookup"><span data-stu-id="63bb1-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="63bb1-831">Çift alt çizgi`__`( ) dosya adlarında yapılandırma anahtarı delimiter olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="63bb1-832">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını `Logging:LogLevel:System`üretir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="63bb1-833">Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="63bb1-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="63bb1-834">Bellek Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-834">Memory Configuration Provider</span></span>

<span data-ttu-id="63bb1-835">Yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> anahtar değeri çiftleri olarak bellek içi bir koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="63bb1-836">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örnekte çağırın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="63bb1-837">Yapılandırma sağlayıcısı bir `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="63bb1-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="63bb1-838">Uygulamanın yapılandırmasını belirtmek için ana bilgisayarı kurarken arayın. `ConfigureAppConfiguration`</span><span class="sxs-lookup"><span data-stu-id="63bb1-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="63bb1-839">Aşağıdaki örnekte, bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="63bb1-840">Sözlük yapılandırmasağlamak için `AddInMemoryCollection` bir çağrı ile kullanılır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="63bb1-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="63bb1-841">GetValue</span></span>

<span data-ttu-id="63bb1-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirtilen bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen noncollection türüne dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="63bb1-843">Aşırı yükleme varsayılan bir değer kabul eder.</span><span class="sxs-lookup"><span data-stu-id="63bb1-843">An overload accepts a default value.</span></span>

<span data-ttu-id="63bb1-844">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="63bb1-844">The following example:</span></span>

* <span data-ttu-id="63bb1-845">Anahtarla `NumberKey`yapılandırmadan dize değerini ayıklar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="63bb1-846">Yapılandırma `NumberKey` anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="63bb1-847">Değeri bir `int`.</span><span class="sxs-lookup"><span data-stu-id="63bb1-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="63bb1-848">Sayfanın kullandığı `NumberConfig` değer deki değeri depolar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="63bb1-849">GetSection, GetChildren ve Var</span><span class="sxs-lookup"><span data-stu-id="63bb1-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="63bb1-850">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="63bb1-851">Biri bir çift alt bölüm içeren iki bölüm arasında dört anahtar bulunur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="63bb1-852">Dosya yapılandırmaya okunduğunda, yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="63bb1-853">bölüm0:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-853">section0:key0</span></span>
* <span data-ttu-id="63bb1-854">bölüm0:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-854">section0:key1</span></span>
* <span data-ttu-id="63bb1-855">bölüm1:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-855">section1:key0</span></span>
* <span data-ttu-id="63bb1-856">bölüm1:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-856">section1:key1</span></span>
* <span data-ttu-id="63bb1-857">bölüm2:altbölüm0:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="63bb1-858">bölüm2:altbölüm0:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="63bb1-859">bölüm2:altbölüm1:key0</span><span class="sxs-lookup"><span data-stu-id="63bb1-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="63bb1-860">bölüm2:altbölüm1:key1</span><span class="sxs-lookup"><span data-stu-id="63bb1-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="63bb1-861">GetSection</span><span class="sxs-lookup"><span data-stu-id="63bb1-861">GetSection</span></span>

<span data-ttu-id="63bb1-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) belirtilen alt bölüm tuşu ile bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="63bb1-863">Yalnızca anahtar <xref:Microsoft.Extensions.Configuration.IConfigurationSection> değeri çiftlerini `section1`içeren bir bölümü `GetSection` çağırmak ve tedarik etmek için:</span><span class="sxs-lookup"><span data-stu-id="63bb1-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="63bb1-864">Bir `configSection` değeri yok, sadece bir anahtar ve bir yol var.</span><span class="sxs-lookup"><span data-stu-id="63bb1-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="63bb1-865">Benzer şekilde, bölüm yolunu aramak `section2:subsection0` `GetSection` ve tedarik etmek için anahtarlar için değerleri elde etmek için:</span><span class="sxs-lookup"><span data-stu-id="63bb1-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="63bb1-866">`GetSection`asla `null`dönmez.</span><span class="sxs-lookup"><span data-stu-id="63bb1-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="63bb1-867">Eşleşen bir bölüm bulunamazsa, `IConfigurationSection` boş döndürülür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="63bb1-868">Eşleşen `GetSection` bir bölüm <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> döndürdüğünde doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="63bb1-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> ve bölüm olduğunda döndürülür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="63bb1-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="63bb1-870">GetChildren</span></span>

<span data-ttu-id="63bb1-871">[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) için `section2` bir çağrı `IEnumerable<IConfigurationSection>` içerir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="63bb1-872">Var</span><span class="sxs-lookup"><span data-stu-id="63bb1-872">Exists</span></span>

<span data-ttu-id="63bb1-873">Yapılandırma bölümünün var olup olmadığını belirlemek için [ConfigurationExtensions.Exists'ı](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="63bb1-874">Örnek veriler göz `sectionExists` `false` önüne alındığında, yapılandırma `section2:subsection2` verilerinde bir bölüm olmamasıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="63bb1-875">Nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="63bb1-875">Bind to an object graph</span></span>

<span data-ttu-id="63bb1-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>tüm POCO nesne grafiğini bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="63bb1-877">Basit bir nesneyi bağlamada olduğu gibi, yalnızca ortak okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="63bb1-878">Örnek, nesne `TvShow` grafiği ve `Metadata` `Actors` sınıfları içeren bir model içerir *(Modeller/TvShow.cs):*</span><span class="sxs-lookup"><span data-stu-id="63bb1-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="63bb1-879">Örnek uygulamanın yapılandırma verilerini içeren bir *tvshow.xml* dosyası vardır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="63bb1-880">Yapılandırma `Bind` yöntemi ile `TvShow` tüm nesne grafiğine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="63bb1-881">Bağlı örnek işleme için bir özelliğe atanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="63bb1-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)biner ve belirtilen türü döndürür.</span><span class="sxs-lookup"><span data-stu-id="63bb1-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="63bb1-883">`Get<T>`kullanmaktan `Bind`daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="63bb1-884">Aşağıdaki kod, önceki `Get<T>` örnekle nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="63bb1-885">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="63bb1-885">Bind an array to a class</span></span>

<span data-ttu-id="63bb1-886">*Örnek uygulama bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="63bb1-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="63bb1-887">Yapılandırma <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler.</span><span class="sxs-lookup"><span data-stu-id="63bb1-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="63bb1-888">Sayısal anahtar kesimini ortaya çıkaran herhangi`:0:`bir `:1:` &hellip; `:{n}:`dizi biçimi ( , , ) bir POCO sınıf dizisine dizi bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="63bb1-889">Bağlama sözleşme ile sağlanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-889">Binding is provided by convention.</span></span> <span data-ttu-id="63bb1-890">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcılarının gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="63bb1-891">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="63bb1-891">**In-memory array processing**</span></span>

<span data-ttu-id="63bb1-892">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="63bb1-893">Anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-893">Key</span></span>             | <span data-ttu-id="63bb1-894">Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="63bb1-895">dizi:girişler:0</span><span class="sxs-lookup"><span data-stu-id="63bb1-895">array:entries:0</span></span> | <span data-ttu-id="63bb1-896">değeri0</span><span class="sxs-lookup"><span data-stu-id="63bb1-896">value0</span></span> |
| <span data-ttu-id="63bb1-897">dizi:girişler:1</span><span class="sxs-lookup"><span data-stu-id="63bb1-897">array:entries:1</span></span> | <span data-ttu-id="63bb1-898">değer1</span><span class="sxs-lookup"><span data-stu-id="63bb1-898">value1</span></span> |
| <span data-ttu-id="63bb1-899">dizi:girişler:2</span><span class="sxs-lookup"><span data-stu-id="63bb1-899">array:entries:2</span></span> | <span data-ttu-id="63bb1-900">değeri2</span><span class="sxs-lookup"><span data-stu-id="63bb1-900">value2</span></span> |
| <span data-ttu-id="63bb1-901">dizi:girişler:4</span><span class="sxs-lookup"><span data-stu-id="63bb1-901">array:entries:4</span></span> | <span data-ttu-id="63bb1-902">değer4</span><span class="sxs-lookup"><span data-stu-id="63bb1-902">value4</span></span> |
| <span data-ttu-id="63bb1-903">dizi:girişler:5</span><span class="sxs-lookup"><span data-stu-id="63bb1-903">array:entries:5</span></span> | <span data-ttu-id="63bb1-904">değeri5</span><span class="sxs-lookup"><span data-stu-id="63bb1-904">value5</span></span> |

<span data-ttu-id="63bb1-905">Bu anahtarlar ve değerler, Bellek Yapılandırma Sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="63bb1-906">Dizi dizin &num;3 için bir değer atlar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="63bb1-907">Yapılandırma bağlayıcısı, bu diziyi bir nesneye bağlama sonucunun gösterildiği bir anda netleşen, bağlı nesnelerde null değerleri bağlama veya null girişleri oluşturma yeteneğine sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="63bb1-908">Örnek uygulamada, bağlı yapılandırma verilerini tutmak için bir POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="63bb1-909">Yapılandırma verileri nesneye bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="63bb1-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir, bu da daha kompakt kodla sonuçlanır:</span><span class="sxs-lookup"><span data-stu-id="63bb1-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="63bb1-911">Bağlı nesne, bir `ArrayExample`örnek , yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="63bb1-912">`ArrayExample.Entries`Dizin</span><span class="sxs-lookup"><span data-stu-id="63bb1-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="63bb1-913">`ArrayExample.Entries`Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="63bb1-914">0</span><span class="sxs-lookup"><span data-stu-id="63bb1-914">0</span></span>                            | <span data-ttu-id="63bb1-915">değeri0</span><span class="sxs-lookup"><span data-stu-id="63bb1-915">value0</span></span>                       |
| <span data-ttu-id="63bb1-916">1</span><span class="sxs-lookup"><span data-stu-id="63bb1-916">1</span></span>                            | <span data-ttu-id="63bb1-917">değer1</span><span class="sxs-lookup"><span data-stu-id="63bb1-917">value1</span></span>                       |
| <span data-ttu-id="63bb1-918">2</span><span class="sxs-lookup"><span data-stu-id="63bb1-918">2</span></span>                            | <span data-ttu-id="63bb1-919">değeri2</span><span class="sxs-lookup"><span data-stu-id="63bb1-919">value2</span></span>                       |
| <span data-ttu-id="63bb1-920">3</span><span class="sxs-lookup"><span data-stu-id="63bb1-920">3</span></span>                            | <span data-ttu-id="63bb1-921">değer4</span><span class="sxs-lookup"><span data-stu-id="63bb1-921">value4</span></span>                       |
| <span data-ttu-id="63bb1-922">4</span><span class="sxs-lookup"><span data-stu-id="63bb1-922">4</span></span>                            | <span data-ttu-id="63bb1-923">değeri5</span><span class="sxs-lookup"><span data-stu-id="63bb1-923">value5</span></span>                       |

<span data-ttu-id="63bb1-924">Bağlı &num;nesnedeki Dizin `array:4` 3, yapılandırma anahtarı ve değeri `value4`için yapılandırma verilerini tutar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="63bb1-925">Bir dizi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizileri yalnızca nesneyi oluştururken yapılandırma verilerini doğrulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="63bb1-926">Yapılandırma verilerinde null değeri tutulamaz ve yapılandırma anahtarlarındaki bir dizi bir veya daha fazla endeksi atladığında bağlı bir nesnede null değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="63bb1-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="63bb1-927">Dizin &num;3 için eksik yapılandırma öğesi, yapılandırmada doğru anahtar değeri çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından `ArrayExample` örneğine bağlanmadan önce sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="63bb1-928">Örnekte eksik anahtar değeri çifti olan ek bir JSON `ArrayExample.Entries` Yapılandırma Sağlayıcısı varsa, tam yapılandırma dizisi yle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="63bb1-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="63bb1-930">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="63bb1-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="63bb1-931">Tabloda gösterilen anahtar değeri çifti yapılandırmaya yüklenir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="63bb1-932">Anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-932">Key</span></span>             | <span data-ttu-id="63bb1-933">Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="63bb1-934">dizi:girişler:3</span><span class="sxs-lookup"><span data-stu-id="63bb1-934">array:entries:3</span></span> | <span data-ttu-id="63bb1-935">değeri3</span><span class="sxs-lookup"><span data-stu-id="63bb1-935">value3</span></span> |

<span data-ttu-id="63bb1-936">`ArrayExample` Sınıf örneği JSON Yapılandırma Sağlayıcısı dizin &num;3 için giriş içerir sonra bağlı ise, `ArrayExample.Entries` dizi değeri içerir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="63bb1-937">`ArrayExample.Entries`Dizin</span><span class="sxs-lookup"><span data-stu-id="63bb1-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="63bb1-938">`ArrayExample.Entries`Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="63bb1-939">0</span><span class="sxs-lookup"><span data-stu-id="63bb1-939">0</span></span>                            | <span data-ttu-id="63bb1-940">değeri0</span><span class="sxs-lookup"><span data-stu-id="63bb1-940">value0</span></span>                       |
| <span data-ttu-id="63bb1-941">1</span><span class="sxs-lookup"><span data-stu-id="63bb1-941">1</span></span>                            | <span data-ttu-id="63bb1-942">değer1</span><span class="sxs-lookup"><span data-stu-id="63bb1-942">value1</span></span>                       |
| <span data-ttu-id="63bb1-943">2</span><span class="sxs-lookup"><span data-stu-id="63bb1-943">2</span></span>                            | <span data-ttu-id="63bb1-944">değeri2</span><span class="sxs-lookup"><span data-stu-id="63bb1-944">value2</span></span>                       |
| <span data-ttu-id="63bb1-945">3</span><span class="sxs-lookup"><span data-stu-id="63bb1-945">3</span></span>                            | <span data-ttu-id="63bb1-946">değeri3</span><span class="sxs-lookup"><span data-stu-id="63bb1-946">value3</span></span>                       |
| <span data-ttu-id="63bb1-947">4</span><span class="sxs-lookup"><span data-stu-id="63bb1-947">4</span></span>                            | <span data-ttu-id="63bb1-948">değer4</span><span class="sxs-lookup"><span data-stu-id="63bb1-948">value4</span></span>                       |
| <span data-ttu-id="63bb1-949">5</span><span class="sxs-lookup"><span data-stu-id="63bb1-949">5</span></span>                            | <span data-ttu-id="63bb1-950">değeri5</span><span class="sxs-lookup"><span data-stu-id="63bb1-950">value5</span></span>                       |

<span data-ttu-id="63bb1-951">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="63bb1-951">**JSON array processing**</span></span>

<span data-ttu-id="63bb1-952">Bir JSON dosyası nda bir dizi varsa, sıfır tabanlı bölüm dizilimi olan dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="63bb1-953">Aşağıdaki yapılandırma dosyasında, `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="63bb1-954">JSON Yapılandırma Sağlayıcısı yapılandırma verilerini aşağıdaki anahtar değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="63bb1-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="63bb1-955">Anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-955">Key</span></span>                     | <span data-ttu-id="63bb1-956">Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="63bb1-957">json_array:anahtar</span><span class="sxs-lookup"><span data-stu-id="63bb1-957">json_array:key</span></span>          | <span data-ttu-id="63bb1-958">değerA</span><span class="sxs-lookup"><span data-stu-id="63bb1-958">valueA</span></span> |
| <span data-ttu-id="63bb1-959">json_array:alt bölüm:0</span><span class="sxs-lookup"><span data-stu-id="63bb1-959">json_array:subsection:0</span></span> | <span data-ttu-id="63bb1-960">değerB</span><span class="sxs-lookup"><span data-stu-id="63bb1-960">valueB</span></span> |
| <span data-ttu-id="63bb1-961">json_array:alt bölüm:1</span><span class="sxs-lookup"><span data-stu-id="63bb1-961">json_array:subsection:1</span></span> | <span data-ttu-id="63bb1-962">valueC</span><span class="sxs-lookup"><span data-stu-id="63bb1-962">valueC</span></span> |
| <span data-ttu-id="63bb1-963">json_array:alt bölüm:2</span><span class="sxs-lookup"><span data-stu-id="63bb1-963">json_array:subsection:2</span></span> | <span data-ttu-id="63bb1-964">Değerli</span><span class="sxs-lookup"><span data-stu-id="63bb1-964">valueD</span></span> |

<span data-ttu-id="63bb1-965">Örnek uygulamada, yapılandırma anahtar değeri çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="63bb1-966">Bağlama sonra, `JsonArrayExample.Key` değeri `valueA`tutar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="63bb1-967">Alt bölüm değerleri POCO dizi özelliğinde `Subsection`depolanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="63bb1-968">`JsonArrayExample.Subsection`Dizin</span><span class="sxs-lookup"><span data-stu-id="63bb1-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="63bb1-969">`JsonArrayExample.Subsection`Değer</span><span class="sxs-lookup"><span data-stu-id="63bb1-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="63bb1-970">0</span><span class="sxs-lookup"><span data-stu-id="63bb1-970">0</span></span>                                   | <span data-ttu-id="63bb1-971">değerB</span><span class="sxs-lookup"><span data-stu-id="63bb1-971">valueB</span></span>                              |
| <span data-ttu-id="63bb1-972">1</span><span class="sxs-lookup"><span data-stu-id="63bb1-972">1</span></span>                                   | <span data-ttu-id="63bb1-973">valueC</span><span class="sxs-lookup"><span data-stu-id="63bb1-973">valueC</span></span>                              |
| <span data-ttu-id="63bb1-974">2</span><span class="sxs-lookup"><span data-stu-id="63bb1-974">2</span></span>                                   | <span data-ttu-id="63bb1-975">Değerli</span><span class="sxs-lookup"><span data-stu-id="63bb1-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="63bb1-976">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="63bb1-976">Custom configuration provider</span></span>

<span data-ttu-id="63bb1-977">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar değeri çiftleri okuyan temel bir yapılandırma sağlayıcısının nasıl oluşturulturolduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="63bb1-978">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="63bb1-979">EF bellek veritabanı gösteri amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="63bb1-980">Bağlantı dizesi gerektiren bir veritabanı kullanmak `ConfigurationBuilder` için, bağlantı dizesini başka bir yapılandırma sağlayıcısından sağlamak için ikincil bir uygulama uygulayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="63bb1-981">Sağlayıcı başlangıçta yapılandırma içine bir veritabanı tablosu okur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="63bb1-982">Sağlayıcı, veritabanını anahtar başına sorgulamıyor.</span><span class="sxs-lookup"><span data-stu-id="63bb1-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="63bb1-983">Yeniden yükleme-on-change uygulanmaz, bu nedenle uygulama başladıktan sonra veritabanını güncelleştirmenin uygulamanın yapılandırması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="63bb1-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="63bb1-984">Yapılandırma `EFConfigurationValue` değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="63bb1-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="63bb1-985">*Modeller/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="63bb1-986">Yapılandırılan `EFConfigurationContext` değerleri depolamak ve erişmek için bir ekleyin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="63bb1-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="63bb1-988">Uygulayan bir sınıf <xref:Microsoft.Extensions.Configuration.IConfigurationSource>oluşturun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="63bb1-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="63bb1-990">'den <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>devralarak özel yapılandırma sağlayıcısı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="63bb1-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="63bb1-991">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="63bb1-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="63bb1-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="63bb1-993">Uzatma `AddEFConfiguration` yöntemi, yapılandırma kaynağını bir `ConfigurationBuilder`.'ye eklemeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="63bb1-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="63bb1-994">*Uzantılar/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="63bb1-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="63bb1-995">Aşağıdaki `EFConfigurationProvider` *kod, Program.cs*özel in nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="63bb1-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="63bb1-996">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="63bb1-996">Access configuration during startup</span></span>

<span data-ttu-id="63bb1-997">'deki `Startup` yapılandırma değerlerine erişmek için oluşturucuya enjekte edin. `IConfiguration` `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="63bb1-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="63bb1-998">Yapılandırmaya `Startup.Configure`erişmek için `IConfiguration` doğrudan yönteme enjekte edin veya oluşturucudan örneği kullanın:</span><span class="sxs-lookup"><span data-stu-id="63bb1-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="63bb1-999">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişim örneği için Bkz. [Uygulama başlangıç: Kolaylık yöntemleri.](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="63bb1-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="63bb1-1000">Razor Pages sayfasında veya MVC görünümünde yapılandırmaya erişin</span><span class="sxs-lookup"><span data-stu-id="63bb1-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="63bb1-1001">Razor Pages sayfasındaveya MVC görünümünde yapılandırma ayarlarına erişmek [için, Microsoft.Extensions.Configuration ad alanı](xref:Microsoft.Extensions.Configuration) için bir kullanma <xref:Microsoft.Extensions.Configuration.IConfiguration> [yönergesi](xref:mvc/views/razor#using) [(C# başvurusu: yönergeyi kullanma)](/dotnet/csharp/language-reference/keywords/using-directive)ekleyin ve sayfaya veya görünüme enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="63bb1-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="63bb1-1002">Jilet Sayfaları sayfasında:</span><span class="sxs-lookup"><span data-stu-id="63bb1-1002">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="63bb1-1003">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="63bb1-1003">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="63bb1-1004">Harici bir montajdan yapılandırma ekleme</span><span class="sxs-lookup"><span data-stu-id="63bb1-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="63bb1-1005">Uygulama, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> uygulamanın sınıfının dışındaki harici bir derlemeden başlangıçta bir `Startup` uygulamaya geliştirme eklemeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="63bb1-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="63bb1-1006">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="63bb1-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63bb1-1007">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="63bb1-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
