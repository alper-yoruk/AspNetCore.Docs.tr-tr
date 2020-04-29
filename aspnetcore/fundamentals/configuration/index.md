---
title: ASP.NET Core yapılandırma
author: rick-anderson
description: ASP.NET Core uygulamasını yapılandırmak için yapılandırma API 'sini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 7715adc9b39edd4f8a5882b2e60a1b5513fe400b
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206001"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="d0c99-103">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d0c99-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="d0c99-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d0c99-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d0c99-105">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="d0c99-106">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="d0c99-107">*AppSettings. JSON* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="d0c99-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-108">Environment variables</span></span>
* <span data-ttu-id="d0c99-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0c99-109">Azure Key Vault</span></span>
* <span data-ttu-id="d0c99-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-110">Azure App Configuration</span></span>
* <span data-ttu-id="d0c99-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-111">Command-line arguments</span></span>
* <span data-ttu-id="d0c99-112">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="d0c99-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="d0c99-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-113">Directory files</span></span>
* <span data-ttu-id="d0c99-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-114">In-memory .NET objects</span></span>

<span data-ttu-id="d0c99-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0c99-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="d0c99-116">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d0c99-116">Default configuration</span></span>

<span data-ttu-id="d0c99-117">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="d0c99-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="d0c99-119">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan `IConfiguration` bir kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="d0c99-120">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="d0c99-121">[JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak [appSettings. JSON](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="d0c99-122">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d0c99-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="d0c99-123">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="d0c99-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="d0c99-124">Uygulama `Development` ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="d0c99-125">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d0c99-126">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="d0c99-127">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="d0c99-128">Örneğin, `MyKey` *appSettings. JSON* ve ortamda ayarlanırsa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="d0c99-129">Varsayılan yapılandırma sağlayıcılarını kullanarak, [komut satırı yapılandırma sağlayıcısı](#command-line-configuration-provider) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="d0c99-130">Hakkında `CreateDefaultBuilder`daha fazla bilgi için bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="d0c99-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="d0c99-131">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="d0c99-132">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="d0c99-132">appsettings.json</span></span>

<span data-ttu-id="d0c99-133">Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d0c99-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d0c99-134">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-135">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="d0c99-136">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="d0c99-136">*appsettings.json*</span></span>
1. <span data-ttu-id="d0c99-137">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d0c99-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="d0c99-138">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="d0c99-139">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d0c99-140">*appSettings*. `Environment`. *JSON* değerleri *appSettings. JSON*içindeki anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="d0c99-141">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="d0c99-141">For example, by default:</span></span>

* <span data-ttu-id="d0c99-142">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması *appSettings. JSON*içinde bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="d0c99-143">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması *appSettings. JSON*içinde bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="d0c99-144">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="d0c99-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="d0c99-145">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="d0c99-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="d0c99-146">İlgili yapılandırma değerlerini okumak için tercih edilen yol, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d0c99-147">Örneğin, aşağıdaki yapılandırma değerlerini okumak için:</span><span class="sxs-lookup"><span data-stu-id="d0c99-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="d0c99-148">Aşağıdaki `PositionOptions` sınıfı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d0c99-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="d0c99-149">Türün tüm genel okuma-yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="d0c99-150">Alanlar bağlanmadı ***.***</span><span class="sxs-lookup"><span data-stu-id="d0c99-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="d0c99-151">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d0c99-151">The following code:</span></span>

* <span data-ttu-id="d0c99-152">Sınıfı bölümüne bağlamak için [Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) `Position` ' i `PositionOptions` çağırır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="d0c99-153">`Position` Yapılandırma verilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="d0c99-155">`ConfigurationBinder.Get<T>`, kullanmaktan `ConfigurationBinder.Bind`daha uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="d0c99-156">Aşağıdaki kod, `PositionOptions` sınıfıyla birlikte nasıl kullanılacağını `ConfigurationBinder.Get<T>` gösterir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-157">***Seçenekler deseninin*** kullanılmasına yönelik alternatif bir yaklaşım, `Position` bölümü bağlamak ve [bağımlılık ekleme hizmeti kapsayıcısına](xref:fundamentals/dependency-injection)eklemektir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d0c99-158">Aşağıdaki kodda, `PositionOptions` ile <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hizmet kapsayıcısına eklenir ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="d0c99-159">Önceki kodu kullanarak, aşağıdaki kod konum seçeneklerini okur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-160">[Varsayılan](#default) yapılandırmayı kullanarak *appSettings. JSON* ve *appSettings.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="d0c99-161">*AppSettings. JSON* ve appSettings üzerinde yapılan değişiklikler *.* `Environment`uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="d0c99-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d0c99-162">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="d0c99-163">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="d0c99-163">Security and secret manager</span></span>

<span data-ttu-id="d0c99-164">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="d0c99-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="d0c99-165">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="d0c99-166">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="d0c99-167">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d0c99-168">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d0c99-169">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appSettings. JSON* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d0c99-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="d0c99-170">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="d0c99-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d0c99-171"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d0c99-172">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="d0c99-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d0c99-174">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="d0c99-175">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-175">Environment variables</span></span>

<span data-ttu-id="d0c99-176">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> *appSettings. JSON*, appSettings 'i okuduktan sonra anahtar-değer çiftlerinden yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d0c99-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d0c99-177">Bu nedenle, ortamdan okunan anahtar değerleri *appSettings. JSON*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="d0c99-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d0c99-178">Aşağıdaki `set` komutlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-178">The following `set` commands:</span></span>

* <span data-ttu-id="d0c99-179">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="d0c99-180">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="d0c99-181">`dotnet run` Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="d0c99-182">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="d0c99-182">The preceding environment settings:</span></span>

* <span data-ttu-id="d0c99-183">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d0c99-184">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d0c99-185">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="d0c99-186">Farklı `set`olarak `setx` , ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d0c99-187">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="d0c99-188">`/M` Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="d0c99-189">Yukarıdaki komutların *appSettings. JSON* ve appSettings 'i geçersiz kılmasını test etmek için *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="d0c99-190">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="d0c99-191">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="d0c99-192">Ortam <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="d0c99-193">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="d0c99-193">In the preceding code:</span></span>

* <span data-ttu-id="d0c99-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d0c99-195">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d0c99-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="d0c99-196">`MyCustomPrefix_` Önek ile ayarlanan ortam değişkenleri [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="d0c99-197">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="d0c99-198">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="d0c99-199">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="d0c99-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="d0c99-200">[Varsayılan yapılandırma](#default) , `DOTNET_` ve `ASPNETCORE_`önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="d0c99-201">`DOTNET_` Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="d0c99-202">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d0c99-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="d0c99-203">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d0c99-204">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d0c99-205">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d0c99-206">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-206">Exposed as environment variables.</span></span>

<span data-ttu-id="d0c99-207">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d0c99-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d0c99-208">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="d0c99-209">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="d0c99-209">Command-line</span></span>

<span data-ttu-id="d0c99-210">[Varsayılan](#default) yapılandırmayı kullanarak, aşağıdaki yapılandırma <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="d0c99-211">*appSettings. JSON* ve *appSettings*. `Environment`. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d0c99-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d0c99-212">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d0c99-213">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-213">Environment variables.</span></span>

<span data-ttu-id="d0c99-214">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="d0c99-215">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-215">Command-line arguments</span></span>

<span data-ttu-id="d0c99-216">Aşağıdaki komut kullanarak `=`anahtarları ve değerleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="d0c99-217">Aşağıdaki komut kullanarak `/`anahtarları ve değerleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="d0c99-218">Aşağıdaki komut kullanarak `--`anahtarları ve değerleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="d0c99-219">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="d0c99-219">The key value:</span></span>

* <span data-ttu-id="d0c99-220">' İ `=`izlemelidir, ya da anahtarın bir boşluk izleyen değeri `--` veya `/` öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="d0c99-221">Kullanılıyorsa gerekli `=` değildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="d0c99-222">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="d0c99-223">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan `=` komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="d0c99-224">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="d0c99-224">Switch mappings</span></span>

<span data-ttu-id="d0c99-225">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="d0c99-226"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Metoda anahtar değiştirme sözlüğü sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d0c99-227">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d0c99-228">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d0c99-229">Tek tireyle (`-`) ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d0c99-230">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="d0c99-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d0c99-231">Anahtarlar veya `-` `--`ile başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="d0c99-232">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d0c99-233">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="d0c99-234">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-235">Anahtar değişimini test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d0c99-236">Note: Şu anda `=` , anahtar değiştirme değerlerini tek bir çizgiyle `-`ayarlamak için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="d0c99-237">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="d0c99-238">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d0c99-239">Anahtar eşlemeleri kullanan uygulamalar için, ' ye `CreateDefaultBuilder` yapılan çağrı bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="d0c99-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d0c99-240">Yöntemin çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine `CreateDefaultBuilder`geçirmenin bir yolu yoktur. `AddCommandLine` `CreateDefaultBuilder`</span><span class="sxs-lookup"><span data-stu-id="d0c99-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d0c99-241">Çözüm, bağımsız değişkenleri öğesine `CreateDefaultBuilder` geçirmektir, bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d0c99-242">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="d0c99-242">Hierarchical configuration data</span></span>

<span data-ttu-id="d0c99-243">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d0c99-244">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *appSettings. JSON* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d0c99-245">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-246">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="d0c99-247">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="d0c99-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d0c99-249">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="d0c99-250">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-250">Configuration keys and values</span></span>

<span data-ttu-id="d0c99-251">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="d0c99-251">Configuration keys:</span></span>

* <span data-ttu-id="d0c99-252">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-252">Are case-insensitive.</span></span> <span data-ttu-id="d0c99-253">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d0c99-254">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="d0c99-255">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="d0c99-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="d0c99-256">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d0c99-256">Hierarchical keys</span></span>
  * <span data-ttu-id="d0c99-257">Yapılandırma API 'SI içinde, iki nokta üst üste`:`ayırıcı () tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d0c99-258">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d0c99-259">Çift alt çizgi, `__`tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta `:`olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="d0c99-260">Azure Key Vault, hiyerarşik anahtarlar ayırıcı olarak `--` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="d0c99-261">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak bir `--` `:` ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d0c99-262">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d0c99-263">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="d0c99-264">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="d0c99-264">Configuration values:</span></span>

* <span data-ttu-id="d0c99-265">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-265">Are strings.</span></span>
* <span data-ttu-id="d0c99-266">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="d0c99-267">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d0c99-267">Configuration providers</span></span>

<span data-ttu-id="d0c99-268">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d0c99-269">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d0c99-269">Provider</span></span> | <span data-ttu-id="d0c99-270">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="d0c99-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="d0c99-271">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="d0c99-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0c99-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="d0c99-273">Azure uygulama yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="d0c99-274">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="d0c99-275">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="d0c99-276">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-276">Command-line parameters</span></span> |
| [<span data-ttu-id="d0c99-277">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d0c99-278">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="d0c99-278">Custom source</span></span> |
| [<span data-ttu-id="d0c99-279">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="d0c99-280">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-280">Environment variables</span></span> |
| [<span data-ttu-id="d0c99-281">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d0c99-282">INı, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="d0c99-283">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d0c99-284">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-284">Directory files</span></span> |
| [<span data-ttu-id="d0c99-285">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d0c99-286">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="d0c99-286">In-memory collections</span></span> |
| [<span data-ttu-id="d0c99-287">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="d0c99-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="d0c99-288">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="d0c99-288">File in the user profile directory</span></span> |

<span data-ttu-id="d0c99-289">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="d0c99-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="d0c99-290">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d0c99-291">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d0c99-292">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="d0c99-292">*appsettings.json*</span></span>
1. <span data-ttu-id="d0c99-293">*appSettings*. `Environment`. *JSON*</span><span class="sxs-lookup"><span data-stu-id="d0c99-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="d0c99-294">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="d0c99-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="d0c99-295">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d0c99-296">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="d0c99-297">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d0c99-298">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="d0c99-299">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-299">Connection string prefixes</span></span>

<span data-ttu-id="d0c99-300">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="d0c99-301">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d0c99-302">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına `AddEnvironmentVariables`hiçbir önek sağlanmadığında uygulamaya yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d0c99-303">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="d0c99-303">Connection string prefix</span></span> | <span data-ttu-id="d0c99-304">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d0c99-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d0c99-305">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d0c99-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d0c99-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="d0c99-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d0c99-307">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="d0c99-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d0c99-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d0c99-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d0c99-309">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="d0c99-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d0c99-310">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü (`ConnectionStrings`) eklenerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d0c99-311">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_`, belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="d0c99-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d0c99-312">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="d0c99-312">Environment variable key</span></span> | <span data-ttu-id="d0c99-313">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d0c99-313">Converted configuration key</span></span> | <span data-ttu-id="d0c99-314">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="d0c99-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-315">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="d0c99-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-316">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d0c99-317">Değer:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d0c99-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-318">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d0c99-319">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d0c99-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-320">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d0c99-321">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d0c99-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="d0c99-322">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-322">JSON configuration provider</span></span>

<span data-ttu-id="d0c99-323">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Yapılandırmayı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="d0c99-324">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-324">Overloads can specify:</span></span>

* <span data-ttu-id="d0c99-325">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d0c99-325">Whether the file is optional.</span></span>
* <span data-ttu-id="d0c99-326">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="d0c99-327">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d0c99-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="d0c99-328">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d0c99-328">The preceding code:</span></span>

* <span data-ttu-id="d0c99-329">JSON yapılandırma sağlayıcısını, *MyConfig. JSON* dosyasını yüklemek için aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="d0c99-330">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="d0c99-331">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="d0c99-332">*MyConfig. JSON* dosyasından önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="d0c99-333">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig. JSON* dosyası geçersiz kılma ayarındaki ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d0c99-334">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="d0c99-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d0c99-335">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="d0c99-336">Yukarıdaki kodda, *MyConfig. JSON* ve *MyConfig*içindeki ayarlar. `Environment`. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="d0c99-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="d0c99-337">*AppSettings. JSON* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment`. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d0c99-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d0c99-338">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d0c99-339">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , şu *MyConfig. JSON* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="d0c99-340">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="d0c99-341">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-341">File configuration provider</span></span>

<span data-ttu-id="d0c99-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d0c99-343">Aşağıdaki yapılandırma sağlayıcıları öğesinden `FileConfigurationProvider`türetilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="d0c99-344">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d0c99-345">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="d0c99-346">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d0c99-347">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-347">INI configuration provider</span></span>

<span data-ttu-id="d0c99-348">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma zamanında INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d0c99-349">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="d0c99-350">Yukarıdaki kodda, *myıniconfig. ini* ve *myıniconfig*içindeki ayarlar. `Environment`. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d0c99-351">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d0c99-352">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d0c99-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d0c99-353">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *Myıniconfig. ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="d0c99-354">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="d0c99-355">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-355">XML configuration provider</span></span>

<span data-ttu-id="d0c99-356">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma zamanında XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d0c99-357">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="d0c99-358">Yukarıdaki kodda, *myXMLfile. xml* ve *myXMLfile*içindeki ayarlar. `Environment`. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d0c99-359">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d0c99-360">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d0c99-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d0c99-361">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , şu *myXMLfile. xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="d0c99-362">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-363">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="d0c99-364">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-365">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d0c99-366">Önceki yapılandırma dosyası aşağıdaki anahtarları ile `value`yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d0c99-367">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="d0c99-367">key:attribute</span></span>
* <span data-ttu-id="d0c99-368">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="d0c99-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d0c99-369">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="d0c99-370">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d0c99-371">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-371">The key is the file name.</span></span> <span data-ttu-id="d0c99-372">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-372">The value contains the file's contents.</span></span> <span data-ttu-id="d0c99-373">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d0c99-374">Dosya başına anahtar yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> genişletme yöntemini çağırın. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="d0c99-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d0c99-375">`directoryPath` Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d0c99-376">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d0c99-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="d0c99-377">Kaynağı `Action<KeyPerFileConfigurationSource>` yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="d0c99-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d0c99-378">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d0c99-379">Çift alt çizgi (`__`), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d0c99-380">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını `Logging:LogLevel:System`üretir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d0c99-381">Uygulamanın `ConfigureAppConfiguration` yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="d0c99-382">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-382">Memory configuration provider</span></span>

<span data-ttu-id="d0c99-383">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d0c99-384">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="d0c99-385">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-386">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d0c99-387">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d0c99-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d0c99-388">Bkz [Bind an array](#boa) . kullanarak `MemoryConfigurationProvider`bir diziyi başka bir örnek için bağlama.</span><span class="sxs-lookup"><span data-stu-id="d0c99-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="d0c99-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="d0c99-389">GetValue</span></span>

<span data-ttu-id="d0c99-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="d0c99-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-391">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d0c99-392">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="d0c99-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d0c99-393">İzleyen örnekler için aşağıdaki *Myalt bölüm. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d0c99-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="d0c99-394">Aşağıdaki kod, *Myalt bölüm. JSON* öğesini yapılandırma sağlayıcılarına ekler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="d0c99-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="d0c99-395">GetSection</span></span>

<span data-ttu-id="d0c99-396">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="d0c99-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d0c99-397">Aşağıdaki kod şu değerleri döndürür `section1`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-398">Aşağıdaki kod şu değerleri döndürür `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-399">`GetSection`hiçbir süre `null`geri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="d0c99-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d0c99-400">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d0c99-401">Eşleşen `GetSection` bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="d0c99-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d0c99-402">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="d0c99-403">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="d0c99-403">GetChildren and Exists</span></span>

<span data-ttu-id="d0c99-404">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-405">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="d0c99-406">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="d0c99-406">Bind an array</span></span>

<span data-ttu-id="d0c99-407">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d0c99-408">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="d0c99-409">[Örnek indirmenizde](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *myArray. JSON* ' i göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d0c99-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="d0c99-410">Aşağıdaki kod, yapılandırma sağlayıcılarına *myArray. JSON* ekler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="d0c99-411">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-412">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="d0c99-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="d0c99-413">Önceki çıktıda, Dizin 3 ' te, `value40` *myArray. JSON*içinde öğesine `"4": "value40",` karşılık gelen bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="d0c99-414">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="d0c99-415">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="d0c99-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="d0c99-416">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="d0c99-417">Aşağıdaki kod, `arrayDict` `Dictionary` içindeki yapılandırmayı okur ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-418">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="d0c99-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="d0c99-419">İlişkili &num;nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d0c99-420">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d0c99-421">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d0c99-422">Dizin 3 anahtar/değer çiftini &num;okuyan `ArrayExample` &num;herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="d0c99-423">Örnek indirmenin aşağıdaki *Value3. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d0c99-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="d0c99-424">Aşağıdaki kod *Value3. JSON* için yapılandırmayı ve `arrayDict` `Dictionary`şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="d0c99-425">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d0c99-426">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="d0c99-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="d0c99-427">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="d0c99-428">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-428">Custom configuration provider</span></span>

<span data-ttu-id="d0c99-429">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d0c99-430">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d0c99-431">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d0c99-432">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, başka bir yapılandırma sağlayıcısından bağlantı `ConfigurationBuilder` dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d0c99-433">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d0c99-434">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d0c99-435">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d0c99-436">Yapılandırma değerlerini `EFConfigurationValue` veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d0c99-437">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d0c99-438">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d0c99-439">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d0c99-440">Uygulayan <xref:Microsoft.Extensions.Configuration.IConfigurationSource>bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d0c99-441">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d0c99-442">Öğesinden <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>devralarak özel yapılandırma sağlayıcısını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d0c99-443">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="d0c99-444">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="d0c99-445">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d0c99-446">`AddEFConfiguration` Genişletme yöntemi, yapılandırma kaynağını bir `ConfigurationBuilder`öğesine eklemeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d0c99-447">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d0c99-448">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="d0c99-449">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-449">Access configuration in Startup</span></span>

<span data-ttu-id="d0c99-450">Aşağıdaki kod, `Startup` yöntemlerde yapılandırma verilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="d0c99-451">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="d0c99-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="d0c99-452">Razor Pages 'de erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="d0c99-453">Aşağıdaki kod, bir Razor sayfasında yapılandırma verilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="d0c99-454">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="d0c99-455">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d0c99-456">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="d0c99-456">Host versus app configuration</span></span>

<span data-ttu-id="d0c99-457">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d0c99-458">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d0c99-459">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d0c99-460">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d0c99-461">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host>..</span><span class="sxs-lookup"><span data-stu-id="d0c99-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="d0c99-462">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-462">Default host configuration</span></span>

<span data-ttu-id="d0c99-463">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="d0c99-464">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d0c99-465">Ortam değişkenleri `DOTNET_` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin, `DOTNET_ENVIRONMENT`) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d0c99-466">Yapılandırma anahtar-`DOTNET_`değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d0c99-467">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d0c99-468">Web ana bilgisayar varsayılan yapılandırması oluşturuldu (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="d0c99-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="d0c99-469">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="d0c99-470">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="d0c99-471">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` Ortam değişkeni olarak `true`ayarlanmışsa iletilen üstbilgiler ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="d0c99-472">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d0c99-473">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d0c99-473">Other configuration</span></span>

<span data-ttu-id="d0c99-474">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d0c99-475">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d0c99-476">*Launch. JSON*/*launchsettings. JSON* , geliştirme ortamı için yapılandırma dosyalarını araçlar, aşağıda açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d0c99-477">İçinde <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d0c99-478">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="d0c99-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d0c99-479">*Web. config* aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d0c99-480">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations>..</span><span class="sxs-lookup"><span data-stu-id="d0c99-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d0c99-481">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="d0c99-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="d0c99-482"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Uygulama, uygulamanın `Startup` sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d0c99-483">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0c99-484">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d0c99-484">Additional resources</span></span>

* [<span data-ttu-id="d0c99-485">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="d0c99-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d0c99-486">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="d0c99-487">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="d0c99-488">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0c99-488">Azure Key Vault</span></span>
* <span data-ttu-id="d0c99-489">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-489">Azure App Configuration</span></span>
* <span data-ttu-id="d0c99-490">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-490">Command-line arguments</span></span>
* <span data-ttu-id="d0c99-491">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="d0c99-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="d0c99-492">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-492">Directory files</span></span>
* <span data-ttu-id="d0c99-493">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-493">Environment variables</span></span>
* <span data-ttu-id="d0c99-494">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-494">In-memory .NET objects</span></span>
* <span data-ttu-id="d0c99-495">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-495">Settings files</span></span>

<span data-ttu-id="d0c99-496">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d0c99-497">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="d0c99-498">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="d0c99-499">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="d0c99-500">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d0c99-501">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0c99-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d0c99-502">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="d0c99-502">Host versus app configuration</span></span>

<span data-ttu-id="d0c99-503">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d0c99-504">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d0c99-505">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d0c99-506">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d0c99-507">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host>..</span><span class="sxs-lookup"><span data-stu-id="d0c99-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d0c99-508">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d0c99-508">Other configuration</span></span>

<span data-ttu-id="d0c99-509">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d0c99-510">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d0c99-511">*Launch. JSON*/*launchsettings. JSON* , geliştirme ortamı için yapılandırma dosyalarını araçlar, aşağıda açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d0c99-512">İçinde <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d0c99-513">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="d0c99-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d0c99-514">*Web. config* aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d0c99-515">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations>..</span><span class="sxs-lookup"><span data-stu-id="d0c99-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="d0c99-516">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d0c99-516">Default configuration</span></span>

<span data-ttu-id="d0c99-517">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> tabanlı Web Apps.</span><span class="sxs-lookup"><span data-stu-id="d0c99-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="d0c99-518">`CreateDefaultBuilder`uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="d0c99-519">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d0c99-520">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="d0c99-521">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d0c99-522">Ortam değişkenleri `ASPNETCORE_` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin, `ASPNETCORE_ENVIRONMENT`) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d0c99-523">Yapılandırma anahtar-`ASPNETCORE_`değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d0c99-524">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d0c99-525">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="d0c99-526">[dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d0c99-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d0c99-527">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="d0c99-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d0c99-528">Uygulama, giriş derlemesini kullanarak `Development` ortamda çalıştırıldığında [gizli Yöneticisi](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d0c99-529">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="d0c99-530">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="d0c99-531">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="d0c99-531">Security</span></span>

<span data-ttu-id="d0c99-532">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d0c99-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="d0c99-533">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="d0c99-534">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d0c99-535">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d0c99-536">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d0c99-537"><xref:security/app-secrets>&ndash; Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d0c99-538">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="d0c99-539">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="d0c99-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d0c99-541">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d0c99-542">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="d0c99-542">Hierarchical configuration data</span></span>

<span data-ttu-id="d0c99-543">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="d0c99-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d0c99-544">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="d0c99-545">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="d0c99-546">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta (`:`) kullanımıyla düzleştirilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="d0c99-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-547">section0:key0</span></span>
* <span data-ttu-id="d0c99-548">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-548">section0:key1</span></span>
* <span data-ttu-id="d0c99-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-549">section1:key0</span></span>
* <span data-ttu-id="d0c99-550">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-550">section1:key1</span></span>

<span data-ttu-id="d0c99-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d0c99-552">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="d0c99-553">Kurallar</span><span class="sxs-lookup"><span data-stu-id="d0c99-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="d0c99-554">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="d0c99-554">Sources and providers</span></span>

<span data-ttu-id="d0c99-555">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="d0c99-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="d0c99-556">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="d0c99-557">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="d0c99-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="d0c99-558"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d0c99-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>, sınıfının yapılandırmasını elde etmek için <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> bir Razor Pages <xref:Microsoft.AspNetCore.Mvc.Controller> veya MVC içine eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="d0c99-560">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="d0c99-561">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="d0c99-562">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d0c99-562">Keys</span></span>

<span data-ttu-id="d0c99-563">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d0c99-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="d0c99-564">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-564">Keys are case-insensitive.</span></span> <span data-ttu-id="d0c99-565">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d0c99-566">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="d0c99-567">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d0c99-567">Hierarchical keys</span></span>
  * <span data-ttu-id="d0c99-568">Yapılandırma API 'SI içinde, iki nokta üst üste`:`ayırıcı () tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d0c99-569">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d0c99-570">Çift alt çizgi (`__`) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="d0c99-571">Azure Key Vault, hiyerarşik anahtarlar ayırıcı olarak `--` (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d0c99-572">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d0c99-573">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d0c99-574">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="d0c99-575">Değerler</span><span class="sxs-lookup"><span data-stu-id="d0c99-575">Values</span></span>

<span data-ttu-id="d0c99-576">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d0c99-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="d0c99-577">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-577">Values are strings.</span></span>
* <span data-ttu-id="d0c99-578">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="d0c99-579">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="d0c99-579">Providers</span></span>

<span data-ttu-id="d0c99-580">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d0c99-581">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d0c99-581">Provider</span></span> | <span data-ttu-id="d0c99-582">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="d0c99-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="d0c99-583">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="d0c99-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="d0c99-584">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0c99-584">Azure Key Vault</span></span> |
| <span data-ttu-id="d0c99-585">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="d0c99-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="d0c99-586">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="d0c99-587">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="d0c99-588">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-588">Command-line parameters</span></span> |
| [<span data-ttu-id="d0c99-589">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d0c99-590">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="d0c99-590">Custom source</span></span> |
| [<span data-ttu-id="d0c99-591">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="d0c99-592">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-592">Environment variables</span></span> |
| [<span data-ttu-id="d0c99-593">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d0c99-594">Dosyalar (ıNı, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="d0c99-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="d0c99-595">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d0c99-596">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d0c99-596">Directory files</span></span> |
| [<span data-ttu-id="d0c99-597">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d0c99-598">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="d0c99-598">In-memory collections</span></span> |
| <span data-ttu-id="d0c99-599">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="d0c99-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="d0c99-600">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="d0c99-600">File in the user profile directory</span></span> |

<span data-ttu-id="d0c99-601">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="d0c99-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="d0c99-602">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="d0c99-603">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d0c99-604">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d0c99-605">Dosyalar (*appSettings. JSON*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="d0c99-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="d0c99-606">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0c99-606">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="d0c99-607">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="d0c99-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="d0c99-608">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-608">Environment variables</span></span>
1. <span data-ttu-id="d0c99-609">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d0c99-609">Command-line arguments</span></span>

<span data-ttu-id="d0c99-610">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d0c99-611">Yeni bir ana bilgisayar Oluşturucu ile `CreateDefaultBuilder`başlatıldığında önceki sağlayıcı dizisi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d0c99-612">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="d0c99-613">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d0c99-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="d0c99-614">Konak oluşturucuyu yapılandırmak için, yapılandırma ile <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="d0c99-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d0c99-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d0c99-616">Ana `ConfigureAppConfiguration` bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="d0c99-617">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="d0c99-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="d0c99-618">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' `AddCommandLine` u çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="d0c99-619">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="d0c99-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="d0c99-620">Tarafından `CreateDefaultBuilder`eklenen sağlayıcıları kaldırmak için, önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="d0c99-621">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="d0c99-621">Consume configuration during app startup</span></span>

<span data-ttu-id="d0c99-622">Uygulamasında `ConfigureAppConfiguration` uygulamaya sağlanan yapılandırma, uygulamanın başlangıcında, dahil olmak üzere `Startup.ConfigureServices`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0c99-623">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="d0c99-624">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="d0c99-625">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="d0c99-626">Komut satırı yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d0c99-627">`AddCommandLine`çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="d0c99-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="d0c99-628">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d0c99-629">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d0c99-630">*AppSettings. JSON* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d0c99-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d0c99-631">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d0c99-632">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-632">Environment variables.</span></span>

<span data-ttu-id="d0c99-633">`CreateDefaultBuilder`Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="d0c99-634">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="d0c99-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="d0c99-635">`CreateDefaultBuilder`Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="d0c99-636">Bu nedenle, tarafından `CreateDefaultBuilder` etkinleştirilen komut satırı yapılandırması konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="d0c99-637">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından `CreateDefaultBuilder`zaten çağırılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d0c99-638">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve en son çağrısı `AddCommandLine` yapın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="d0c99-639">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="d0c99-639">**Example**</span></span>

<span data-ttu-id="d0c99-640">Örnek uygulama, ' a çağrı içeren konağı oluşturmak için `CreateDefaultBuilder` statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="d0c99-641">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="d0c99-642">`dotnet run` Komutuna bir komut satırı bağımsız değişkeni sağlayın, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="d0c99-643">Uygulama çalıştırıldıktan sonra, konumundaki `http://localhost:5000`uygulamaya bir tarayıcı açın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d0c99-644">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="d0c99-645">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="d0c99-645">Arguments</span></span>

<span data-ttu-id="d0c99-646">Değer bir eşittir işaretini (`=`) izlemelidir veya değer bir boşluk izleyen anahtar bir ön eke (`--` ya da `/`) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="d0c99-647">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="d0c99-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="d0c99-648">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="d0c99-648">Key prefix</span></span>               | <span data-ttu-id="d0c99-649">Örnek</span><span class="sxs-lookup"><span data-stu-id="d0c99-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="d0c99-650">Ön ek yok</span><span class="sxs-lookup"><span data-stu-id="d0c99-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="d0c99-651">İki kısa çizgi`--`()</span><span class="sxs-lookup"><span data-stu-id="d0c99-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="d0c99-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="d0c99-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="d0c99-653">Eğik çizgi (`/`)</span><span class="sxs-lookup"><span data-stu-id="d0c99-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="d0c99-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="d0c99-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="d0c99-655">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="d0c99-656">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="d0c99-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="d0c99-657">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="d0c99-657">Switch mappings</span></span>

<span data-ttu-id="d0c99-658">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="d0c99-659">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> yöntemine anahtar değiştirme sözlüğü sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d0c99-660">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d0c99-661">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d0c99-662">Tek tireyle (`-`) ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d0c99-663">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="d0c99-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d0c99-664">Anahtarlar bir tire (`-`) veya çift tireyle (`--`) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="d0c99-665">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d0c99-666">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="d0c99-667">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="d0c99-668">Konak oluşturulduğunda, anahtar eşlemeleri sözlüğüne çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="d0c99-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="d0c99-669">Anahtar eşlemeleri kullanan uygulamalar için, ' ye `CreateDefaultBuilder` yapılan çağrı bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="d0c99-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d0c99-670">Yöntemin çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine `CreateDefaultBuilder`geçirmenin bir yolu yoktur. `AddCommandLine` `CreateDefaultBuilder`</span><span class="sxs-lookup"><span data-stu-id="d0c99-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d0c99-671">Çözüm, bağımsız değişkenleri öğesine `CreateDefaultBuilder` geçirmektir, bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="d0c99-672">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="d0c99-673">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-673">Key</span></span>       | <span data-ttu-id="d0c99-674">Değer</span><span class="sxs-lookup"><span data-stu-id="d0c99-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="d0c99-675">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="d0c99-676">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="d0c99-677">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-677">Key</span></span>               | <span data-ttu-id="d0c99-678">Değer</span><span class="sxs-lookup"><span data-stu-id="d0c99-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="d0c99-679">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="d0c99-680">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="d0c99-681">Ortam değişkenleri yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d0c99-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="d0c99-683">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d0c99-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d0c99-684">`AddEnvironmentVariables`, [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu `ASPNETCORE_` başlatıldığında ve `CreateDefaultBuilder` çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="d0c99-685">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d0c99-686">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d0c99-687">Önek olmadan çağırarak `AddEnvironmentVariables` ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="d0c99-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="d0c99-688">*AppSettings. JSON* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d0c99-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d0c99-689">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d0c99-690">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-690">Command-line arguments.</span></span>

<span data-ttu-id="d0c99-691">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="d0c99-692">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="d0c99-693">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki `ConfigureAppConfiguration` ek sağlayıcılarını çağırın ve önekiyle birlikte çağırın: `AddEnvironmentVariables`</span><span class="sxs-lookup"><span data-stu-id="d0c99-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="d0c99-694">Diğer `AddEnvironmentVariables` sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="d0c99-695">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="d0c99-695">**Example**</span></span>

<span data-ttu-id="d0c99-696">Örnek uygulama, ' a çağrı içeren konağı oluşturmak için `CreateDefaultBuilder` statik kolaylık yönteminden yararlanır `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="d0c99-697">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-697">Run the sample app.</span></span> <span data-ttu-id="d0c99-698">Üzerinde `http://localhost:5000`uygulama için bir tarayıcı açın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d0c99-699">Çıktının ortam değişkeni `ENVIRONMENT`için anahtar-değer çiftini içerdiğini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="d0c99-700">Değer, genellikle `Development` yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="d0c99-701">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="d0c99-702">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="d0c99-703">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d0c99-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="d0c99-704">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="d0c99-704">Prefixes</span></span>

<span data-ttu-id="d0c99-705">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, `AddEnvironmentVariables` yöntemine bir ön ek sağlanırken filtrelenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="d0c99-706">Örneğin, önekte `CUSTOM_`ortam değişkenlerini filtrelemek için, yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="d0c99-707">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="d0c99-708">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="d0c99-709">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d0c99-710">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="d0c99-710">**Connection string prefixes**</span></span>

<span data-ttu-id="d0c99-711">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d0c99-712">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d0c99-713">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="d0c99-713">Connection string prefix</span></span> | <span data-ttu-id="d0c99-714">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d0c99-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d0c99-715">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d0c99-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d0c99-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="d0c99-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d0c99-717">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="d0c99-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d0c99-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d0c99-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d0c99-719">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="d0c99-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d0c99-720">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü (`ConnectionStrings`) eklenerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d0c99-721">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_`, belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="d0c99-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d0c99-722">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="d0c99-722">Environment variable key</span></span> | <span data-ttu-id="d0c99-723">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d0c99-723">Converted configuration key</span></span> | <span data-ttu-id="d0c99-724">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="d0c99-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-725">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="d0c99-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-726">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d0c99-727">Değer:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d0c99-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-728">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d0c99-729">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d0c99-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d0c99-730">Anahtar: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="d0c99-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d0c99-731">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d0c99-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="d0c99-732">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="d0c99-732">**Example**</span></span>

<span data-ttu-id="d0c99-733">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="d0c99-734">Ad &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="d0c99-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="d0c99-735">Değer &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="d0c99-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="d0c99-736">Eklenmiş `IConfiguration` ve adlı `_config`bir alana atanmışsa, şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="d0c99-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="d0c99-737">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-737">File Configuration Provider</span></span>

<span data-ttu-id="d0c99-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d0c99-739">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="d0c99-740">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d0c99-741">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="d0c99-742">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d0c99-743">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-743">INI Configuration Provider</span></span>

<span data-ttu-id="d0c99-744">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma zamanında INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d0c99-745">INI dosya yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d0c99-746">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="d0c99-747">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d0c99-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="d0c99-748">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d0c99-748">Whether the file is optional.</span></span>
* <span data-ttu-id="d0c99-749">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d0c99-750">Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d0c99-751">Uygulamanın `ConfigureAppConfiguration` yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d0c99-752">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="d0c99-752">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="d0c99-753">Önceki yapılandırma dosyası aşağıdaki anahtarları ile `value`yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d0c99-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-754">section0:key0</span></span>
* <span data-ttu-id="d0c99-755">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-755">section0:key1</span></span>
* <span data-ttu-id="d0c99-756">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-756">section1:subsection:key</span></span>
* <span data-ttu-id="d0c99-757">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-757">section2:subsection0:key</span></span>
* <span data-ttu-id="d0c99-758">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="d0c99-759">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-759">JSON Configuration Provider</span></span>

<span data-ttu-id="d0c99-760">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı sırasında JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="d0c99-761">JSON dosya yapılandırmasını etkinleştirmek için bir örneğinde <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> genişletme yöntemini çağırın. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="d0c99-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d0c99-762">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d0c99-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="d0c99-763">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d0c99-763">Whether the file is optional.</span></span>
* <span data-ttu-id="d0c99-764">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d0c99-765">Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d0c99-766">`AddJsonFile`, ile `CreateDefaultBuilder`yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d0c99-767">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="d0c99-768">*appSettings. JSON* &ndash; bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="d0c99-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="d0c99-769">Dosyanın ortam sürümü, *appSettings. JSON* dosyası tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="d0c99-770">*appSettings. {Environment}. JSON* &ndash; dosyanın ortam sürümü [ıhostingenvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="d0c99-771">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d0c99-772">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d0c99-773">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-773">Environment variables.</span></span>
* <span data-ttu-id="d0c99-774">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d0c99-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d0c99-775">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d0c99-775">Command-line arguments.</span></span>

<span data-ttu-id="d0c99-776">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="d0c99-777">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="d0c99-778">Ana `ConfigureAppConfiguration` bilgisayarı, *appSettings. JSON* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere oluştururken çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d0c99-779">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="d0c99-779">**Example**</span></span>

<span data-ttu-id="d0c99-780">Örnek uygulama, için `CreateDefaultBuilder` `AddJsonFile`iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="d0c99-781">İlk çağrı, `AddJsonFile` *appSettings. JSON*' dan yapılandırmayı yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="d0c99-782">Yapılandırma appSettings 'ten `AddJsonFile` yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d0c99-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="d0c99-783">*AppSettings için. Geliştirme. JSON* örnek uygulamada aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="d0c99-784">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-784">Run the sample app.</span></span> <span data-ttu-id="d0c99-785">Üzerinde `http://localhost:5000`uygulama için bir tarayıcı açın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d0c99-786">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="d0c99-787">Anahtarın `Logging:LogLevel:Default` günlük düzeyi, uygulamanın geliştirme ortamında `Debug` çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="d0c99-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="d0c99-788">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="d0c99-789">*Properties/launchSettings. JSON* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="d0c99-790">`ConfigurationSample` Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak `Production`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="d0c99-791">Dosyayı kaydedin ve uygulamayı bir komut kabuğu `dotnet run` içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="d0c99-792">AppSettings içindeki ayarlar *. Development. JSON* artık *appSettings. JSON*içindeki ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="d0c99-793">Anahtarın `Logging:LogLevel:Default` günlük düzeyi `Warning`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="d0c99-794">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-794">XML Configuration Provider</span></span>

<span data-ttu-id="d0c99-795">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma zamanında XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d0c99-796">XML dosya yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d0c99-797">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d0c99-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="d0c99-798">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d0c99-798">Whether the file is optional.</span></span>
* <span data-ttu-id="d0c99-799">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d0c99-800">Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d0c99-801">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="d0c99-802">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="d0c99-803">Uygulamanın `ConfigureAppConfiguration` yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d0c99-804">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-804">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="d0c99-805">Önceki yapılandırma dosyası aşağıdaki anahtarları ile `value`yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d0c99-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-806">section0:key0</span></span>
* <span data-ttu-id="d0c99-807">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-807">section0:key1</span></span>
* <span data-ttu-id="d0c99-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-808">section1:key0</span></span>
* <span data-ttu-id="d0c99-809">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-809">section1:key1</span></span>

<span data-ttu-id="d0c99-810">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="d0c99-811">Önceki yapılandırma dosyası aşağıdaki anahtarları ile `value`yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d0c99-812">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-812">section:section0:key:key0</span></span>
* <span data-ttu-id="d0c99-813">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-813">section:section0:key:key1</span></span>
* <span data-ttu-id="d0c99-814">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-814">section:section1:key:key0</span></span>
* <span data-ttu-id="d0c99-815">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-815">section:section1:key:key1</span></span>

<span data-ttu-id="d0c99-816">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d0c99-817">Önceki yapılandırma dosyası aşağıdaki anahtarları ile `value`yükler:</span><span class="sxs-lookup"><span data-stu-id="d0c99-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d0c99-818">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="d0c99-818">key:attribute</span></span>
* <span data-ttu-id="d0c99-819">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="d0c99-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d0c99-820">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="d0c99-821">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d0c99-822">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-822">The key is the file name.</span></span> <span data-ttu-id="d0c99-823">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-823">The value contains the file's contents.</span></span> <span data-ttu-id="d0c99-824">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d0c99-825">Dosya başına anahtar yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> genişletme yöntemini çağırın. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="d0c99-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d0c99-826">`directoryPath` Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d0c99-827">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d0c99-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="d0c99-828">Kaynağı `Action<KeyPerFileConfigurationSource>` yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="d0c99-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d0c99-829">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d0c99-830">Çift alt çizgi (`__`), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d0c99-831">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını `Logging:LogLevel:System`üretir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d0c99-832">Uygulamanın `ConfigureAppConfiguration` yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="d0c99-833">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-833">Memory Configuration Provider</span></span>

<span data-ttu-id="d0c99-834">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d0c99-835">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, bir örneğinde <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemini çağırın. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder></span><span class="sxs-lookup"><span data-stu-id="d0c99-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d0c99-836">Yapılandırma sağlayıcısı bir `IEnumerable<KeyValuePair<String,String>>`ile başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="d0c99-837">Uygulamanın `ConfigureAppConfiguration` yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="d0c99-838">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="d0c99-839">Sözlük, yapılandırmayı sağlamak `AddInMemoryCollection` için çağrısı ile kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="d0c99-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="d0c99-840">GetValue</span></span>

<span data-ttu-id="d0c99-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="d0c99-842">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d0c99-842">An overload accepts a default value.</span></span>

<span data-ttu-id="d0c99-843">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="d0c99-843">The following example:</span></span>

* <span data-ttu-id="d0c99-844">Yapılandırmadaki dize değerini anahtarla `NumberKey`ayıklar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="d0c99-845">`NumberKey` Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="d0c99-846">Değeri olarak bir `int`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="d0c99-847">Değeri, sayfanın kullanımı için `NumberConfig` özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d0c99-848">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="d0c99-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d0c99-849">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="d0c99-850">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="d0c99-851">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="d0c99-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-852">section0:key0</span></span>
* <span data-ttu-id="d0c99-853">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-853">section0:key1</span></span>
* <span data-ttu-id="d0c99-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-854">section1:key0</span></span>
* <span data-ttu-id="d0c99-855">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-855">section1:key1</span></span>
* <span data-ttu-id="d0c99-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="d0c99-857">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="d0c99-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="d0c99-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="d0c99-859">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d0c99-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="d0c99-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="d0c99-860">GetSection</span></span>

<span data-ttu-id="d0c99-861">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d0c99-862">Yalnızca içindeki <xref:Microsoft.Extensions.Configuration.IConfigurationSection> `section1`anahtar-değer çiftlerini içeren bir öğesini döndürmek için, bölüm `GetSection` adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="d0c99-863">Bir `configSection` değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="d0c99-864">Benzer şekilde, içindeki `section2:subsection0`anahtarların değerlerini almak için, Bölüm yolunu `GetSection` çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="d0c99-865">`GetSection`hiçbir süre `null`geri döndürmez.</span><span class="sxs-lookup"><span data-stu-id="d0c99-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d0c99-866">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d0c99-867">Eşleşen `GetSection` bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="d0c99-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d0c99-868">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="d0c99-869">GetChildren</span><span class="sxs-lookup"><span data-stu-id="d0c99-869">GetChildren</span></span>

<span data-ttu-id="d0c99-870">Üzerinde `section2` bir `IEnumerable<IConfigurationSection>` [iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı, şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="d0c99-871">Var</span><span class="sxs-lookup"><span data-stu-id="d0c99-871">Exists</span></span>

<span data-ttu-id="d0c99-872">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="d0c99-873">Örnek veriler `sectionExists` `false` verildiğinde, yapılandırma verilerinde bir `section2:subsection2` bölüm olmadığı için.</span><span class="sxs-lookup"><span data-stu-id="d0c99-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="d0c99-874">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="d0c99-874">Bind to an object graph</span></span>

<span data-ttu-id="d0c99-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>, bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="d0c99-876">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="d0c99-877">Örnek, nesne grafı `TvShow` içeren `Metadata` ve `Actors` sınıfları olan bir model içerir (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="d0c99-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="d0c99-878">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow. xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="d0c99-879">Yapılandırma, `Bind` yöntemiyle nesne grafiğinin tamamına `TvShow` bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="d0c99-880">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="d0c99-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="d0c99-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="d0c99-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="d0c99-882">`Get<T>`, kullanmaktan `Bind`daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="d0c99-883">Aşağıdaki kod, önceki örnekle birlikte nasıl `Get<T>` kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d0c99-884">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="d0c99-884">Bind an array to a class</span></span>

<span data-ttu-id="d0c99-885">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="d0c99-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="d0c99-886">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d0c99-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d0c99-887">Bir sayısal anahtar segmenti`:0:`(, `:1:`, &hellip; `:{n}:`) sunan herhangi bir dizi biçimi, bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="d0c99-888">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-888">Binding is provided by convention.</span></span> <span data-ttu-id="d0c99-889">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="d0c99-890">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="d0c99-890">**In-memory array processing**</span></span>

<span data-ttu-id="d0c99-891">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="d0c99-892">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-892">Key</span></span>             | <span data-ttu-id="d0c99-893">Değer</span><span class="sxs-lookup"><span data-stu-id="d0c99-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d0c99-894">dizi: girdiler: 0</span><span class="sxs-lookup"><span data-stu-id="d0c99-894">array:entries:0</span></span> | <span data-ttu-id="d0c99-895">value0</span><span class="sxs-lookup"><span data-stu-id="d0c99-895">value0</span></span> |
| <span data-ttu-id="d0c99-896">dizi: girdiler: 1</span><span class="sxs-lookup"><span data-stu-id="d0c99-896">array:entries:1</span></span> | <span data-ttu-id="d0c99-897">value1</span><span class="sxs-lookup"><span data-stu-id="d0c99-897">value1</span></span> |
| <span data-ttu-id="d0c99-898">dizi: girdiler: 2</span><span class="sxs-lookup"><span data-stu-id="d0c99-898">array:entries:2</span></span> | <span data-ttu-id="d0c99-899">value2</span><span class="sxs-lookup"><span data-stu-id="d0c99-899">value2</span></span> |
| <span data-ttu-id="d0c99-900">dizi: girdiler: 4</span><span class="sxs-lookup"><span data-stu-id="d0c99-900">array:entries:4</span></span> | <span data-ttu-id="d0c99-901">value4</span><span class="sxs-lookup"><span data-stu-id="d0c99-901">value4</span></span> |
| <span data-ttu-id="d0c99-902">dizi: girdiler: 5</span><span class="sxs-lookup"><span data-stu-id="d0c99-902">array:entries:5</span></span> | <span data-ttu-id="d0c99-903">value5</span><span class="sxs-lookup"><span data-stu-id="d0c99-903">value5</span></span> |

<span data-ttu-id="d0c99-904">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="d0c99-905">Dizi, Dizin &num;3 için bir değeri atlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="d0c99-906">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="d0c99-907">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="d0c99-908">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="d0c99-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="d0c99-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="d0c99-910">Bir örneği olan `ArrayExample`bağlantılı nesne, yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="d0c99-911">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="d0c99-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d0c99-912">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="d0c99-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d0c99-913">0</span><span class="sxs-lookup"><span data-stu-id="d0c99-913">0</span></span>                            | <span data-ttu-id="d0c99-914">value0</span><span class="sxs-lookup"><span data-stu-id="d0c99-914">value0</span></span>                       |
| <span data-ttu-id="d0c99-915">1</span><span class="sxs-lookup"><span data-stu-id="d0c99-915">1</span></span>                            | <span data-ttu-id="d0c99-916">value1</span><span class="sxs-lookup"><span data-stu-id="d0c99-916">value1</span></span>                       |
| <span data-ttu-id="d0c99-917">2</span><span class="sxs-lookup"><span data-stu-id="d0c99-917">2</span></span>                            | <span data-ttu-id="d0c99-918">value2</span><span class="sxs-lookup"><span data-stu-id="d0c99-918">value2</span></span>                       |
| <span data-ttu-id="d0c99-919">3</span><span class="sxs-lookup"><span data-stu-id="d0c99-919">3</span></span>                            | <span data-ttu-id="d0c99-920">value4</span><span class="sxs-lookup"><span data-stu-id="d0c99-920">value4</span></span>                       |
| <span data-ttu-id="d0c99-921">4</span><span class="sxs-lookup"><span data-stu-id="d0c99-921">4</span></span>                            | <span data-ttu-id="d0c99-922">value5</span><span class="sxs-lookup"><span data-stu-id="d0c99-922">value5</span></span>                       |

<span data-ttu-id="d0c99-923">İlişkili &num;nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d0c99-924">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d0c99-925">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d0c99-926">Yapılandırmada doğru anahtar-değer çiftini &num;üreten herhangi bir yapılandırma sağlayıcısı tarafından `ArrayExample` örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="d0c99-927">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, tam yapılandırma dizisiyle `ArrayExample.Entries` eşleşir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="d0c99-928">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="d0c99-929">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="d0c99-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="d0c99-930">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="d0c99-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="d0c99-931">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-931">Key</span></span>             | <span data-ttu-id="d0c99-932">Değer</span><span class="sxs-lookup"><span data-stu-id="d0c99-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d0c99-933">dizi: girdiler: 3</span><span class="sxs-lookup"><span data-stu-id="d0c99-933">array:entries:3</span></span> | <span data-ttu-id="d0c99-934">value3</span><span class="sxs-lookup"><span data-stu-id="d0c99-934">value3</span></span> |

<span data-ttu-id="d0c99-935">`ArrayExample` Sınıf ÖRNEĞI, JSON yapılandırma sağlayıcısı Dizin &num;3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="d0c99-936">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="d0c99-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d0c99-937">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="d0c99-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d0c99-938">0</span><span class="sxs-lookup"><span data-stu-id="d0c99-938">0</span></span>                            | <span data-ttu-id="d0c99-939">value0</span><span class="sxs-lookup"><span data-stu-id="d0c99-939">value0</span></span>                       |
| <span data-ttu-id="d0c99-940">1</span><span class="sxs-lookup"><span data-stu-id="d0c99-940">1</span></span>                            | <span data-ttu-id="d0c99-941">value1</span><span class="sxs-lookup"><span data-stu-id="d0c99-941">value1</span></span>                       |
| <span data-ttu-id="d0c99-942">2</span><span class="sxs-lookup"><span data-stu-id="d0c99-942">2</span></span>                            | <span data-ttu-id="d0c99-943">value2</span><span class="sxs-lookup"><span data-stu-id="d0c99-943">value2</span></span>                       |
| <span data-ttu-id="d0c99-944">3</span><span class="sxs-lookup"><span data-stu-id="d0c99-944">3</span></span>                            | <span data-ttu-id="d0c99-945">value3</span><span class="sxs-lookup"><span data-stu-id="d0c99-945">value3</span></span>                       |
| <span data-ttu-id="d0c99-946">4</span><span class="sxs-lookup"><span data-stu-id="d0c99-946">4</span></span>                            | <span data-ttu-id="d0c99-947">value4</span><span class="sxs-lookup"><span data-stu-id="d0c99-947">value4</span></span>                       |
| <span data-ttu-id="d0c99-948">5</span><span class="sxs-lookup"><span data-stu-id="d0c99-948">5</span></span>                            | <span data-ttu-id="d0c99-949">value5</span><span class="sxs-lookup"><span data-stu-id="d0c99-949">value5</span></span>                       |

<span data-ttu-id="d0c99-950">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="d0c99-950">**JSON array processing**</span></span>

<span data-ttu-id="d0c99-951">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="d0c99-952">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="d0c99-953">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="d0c99-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="d0c99-954">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-954">Key</span></span>                     | <span data-ttu-id="d0c99-955">Değer</span><span class="sxs-lookup"><span data-stu-id="d0c99-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="d0c99-956">json_array: anahtar</span><span class="sxs-lookup"><span data-stu-id="d0c99-956">json_array:key</span></span>          | <span data-ttu-id="d0c99-957">değer EA</span><span class="sxs-lookup"><span data-stu-id="d0c99-957">valueA</span></span> |
| <span data-ttu-id="d0c99-958">json_array: alt bölüm: 0</span><span class="sxs-lookup"><span data-stu-id="d0c99-958">json_array:subsection:0</span></span> | <span data-ttu-id="d0c99-959">valueB</span><span class="sxs-lookup"><span data-stu-id="d0c99-959">valueB</span></span> |
| <span data-ttu-id="d0c99-960">json_array: alt bölüm: 1</span><span class="sxs-lookup"><span data-stu-id="d0c99-960">json_array:subsection:1</span></span> | <span data-ttu-id="d0c99-961">değer EC</span><span class="sxs-lookup"><span data-stu-id="d0c99-961">valueC</span></span> |
| <span data-ttu-id="d0c99-962">json_array: alt bölüm: 2</span><span class="sxs-lookup"><span data-stu-id="d0c99-962">json_array:subsection:2</span></span> | <span data-ttu-id="d0c99-963">Değerler</span><span class="sxs-lookup"><span data-stu-id="d0c99-963">valueD</span></span> |

<span data-ttu-id="d0c99-964">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="d0c99-965">Bağlama işleminden sonra `JsonArrayExample.Key` değeri `valueA`barındırır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="d0c99-966">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="d0c99-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="d0c99-967">`JsonArrayExample.Subsection`İndeks</span><span class="sxs-lookup"><span data-stu-id="d0c99-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="d0c99-968">`JsonArrayExample.Subsection`Deeri</span><span class="sxs-lookup"><span data-stu-id="d0c99-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="d0c99-969">0</span><span class="sxs-lookup"><span data-stu-id="d0c99-969">0</span></span>                                   | <span data-ttu-id="d0c99-970">valueB</span><span class="sxs-lookup"><span data-stu-id="d0c99-970">valueB</span></span>                              |
| <span data-ttu-id="d0c99-971">1</span><span class="sxs-lookup"><span data-stu-id="d0c99-971">1</span></span>                                   | <span data-ttu-id="d0c99-972">değer EC</span><span class="sxs-lookup"><span data-stu-id="d0c99-972">valueC</span></span>                              |
| <span data-ttu-id="d0c99-973">2</span><span class="sxs-lookup"><span data-stu-id="d0c99-973">2</span></span>                                   | <span data-ttu-id="d0c99-974">Değerler</span><span class="sxs-lookup"><span data-stu-id="d0c99-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="d0c99-975">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d0c99-975">Custom configuration provider</span></span>

<span data-ttu-id="d0c99-976">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d0c99-977">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d0c99-978">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d0c99-979">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, başka bir yapılandırma sağlayıcısından bağlantı `ConfigurationBuilder` dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d0c99-980">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="d0c99-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d0c99-981">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="d0c99-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d0c99-982">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d0c99-983">Yapılandırma değerlerini `EFConfigurationValue` veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="d0c99-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d0c99-984">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d0c99-985">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d0c99-986">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d0c99-987">Uygulayan <xref:Microsoft.Extensions.Configuration.IConfigurationSource>bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d0c99-988">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d0c99-989">Öğesinden <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>devralarak özel yapılandırma sağlayıcısını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d0c99-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d0c99-990">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="d0c99-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="d0c99-991">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d0c99-992">`AddEFConfiguration` Genişletme yöntemi, yapılandırma kaynağını bir `ConfigurationBuilder`öğesine eklemeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0c99-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d0c99-993">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="d0c99-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d0c99-994">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d0c99-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="d0c99-995">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="d0c99-995">Access configuration during startup</span></span>

<span data-ttu-id="d0c99-996">İçindeki `IConfiguration` `Startup.ConfigureServices`yapılandırma değerlerine `Startup` erişmek için oluşturucuya ekleme.</span><span class="sxs-lookup"><span data-stu-id="d0c99-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0c99-997">' Da yapılandırmaya erişmek `Startup.Configure`için, `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="d0c99-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="d0c99-998">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="d0c99-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="d0c99-999">Razor Pages sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d0c99-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="d0c99-1000">Razor Pages sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft. Extensions. Configuration ad alanı](xref:Microsoft.Extensions.Configuration) için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d0c99-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="d0c99-1001">Razor Pages sayfasında:</span><span class="sxs-lookup"><span data-stu-id="d0c99-1001">In a Razor Pages page:</span></span>

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

<span data-ttu-id="d0c99-1002">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="d0c99-1002">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d0c99-1003">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="d0c99-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="d0c99-1004"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> Uygulama, uygulamanın `Startup` sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0c99-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d0c99-1005">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d0c99-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0c99-1006">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d0c99-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
