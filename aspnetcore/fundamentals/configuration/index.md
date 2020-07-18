---
title: ASP.NET Core yapılandırma
author: rick-anderson
description: ASP.NET Core uygulamasını yapılandırmak için yapılandırma API 'sini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: 6e47e627915bd8988d161f7d5af4a89f3671c0a7
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445456"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="e6c37-103">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="e6c37-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e6c37-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6c37-105">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="e6c37-106">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="e6c37-107">*appsettings.js* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="e6c37-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-108">Environment variables</span></span>
* <span data-ttu-id="e6c37-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e6c37-109">Azure Key Vault</span></span>
* <span data-ttu-id="e6c37-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-110">Azure App Configuration</span></span>
* <span data-ttu-id="e6c37-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-111">Command-line arguments</span></span>
* <span data-ttu-id="e6c37-112">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="e6c37-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="e6c37-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-113">Directory files</span></span>
* <span data-ttu-id="e6c37-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-114">In-memory .NET objects</span></span>

<span data-ttu-id="e6c37-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6c37-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="e6c37-116">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-116">Default configuration</span></span>

<span data-ttu-id="e6c37-117">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="e6c37-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="e6c37-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="e6c37-119">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="e6c37-120">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="e6c37-121">[JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak [appsettings.js](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="e6c37-122">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e6c37-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="e6c37-123">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="e6c37-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="e6c37-124">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="e6c37-125">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="e6c37-126">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="e6c37-127">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="e6c37-128">Örneğin, `MyKey` ve ortamında hem *appsettings.js* hem de ortamda ayarlandıysa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="e6c37-129">Varsayılan yapılandırma sağlayıcılarını kullanarak, [komut satırı yapılandırma sağlayıcısı](#clcp) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="e6c37-130">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="e6c37-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="e6c37-131">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="e6c37-132">Üzerinde appsettings.js</span><span class="sxs-lookup"><span data-stu-id="e6c37-132">appsettings.json</span></span>

<span data-ttu-id="e6c37-133">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e6c37-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="e6c37-134">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-135">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="e6c37-136">*Üzerindeappsettings.js*</span><span class="sxs-lookup"><span data-stu-id="e6c37-136">*appsettings.json*</span></span>
1. <span data-ttu-id="e6c37-137">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="e6c37-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="e6c37-138">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="e6c37-139">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e6c37-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e6c37-140">*appSettings*. `Environment` . *JSON* değerleri *appsettings.jsüzerindeki*anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="e6c37-141">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="e6c37-141">For example, by default:</span></span>

* <span data-ttu-id="e6c37-142">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması, *üzerindeappsettings.js*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="e6c37-143">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması, *üzerindeappsettings.js*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="e6c37-144">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="e6c37-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="e6c37-145">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="e6c37-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="e6c37-146">[Varsayılan](#default) yapılandırmayı kullanarak *appsettings.js* ve appSettings ' i kullanın *.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="e6c37-147">Ve appSettings *üzerindeappsettings.js* yapılan değişiklikler *.* `Environment` uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="e6c37-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="e6c37-148">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="e6c37-149">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="e6c37-149">Security and secret manager</span></span>

<span data-ttu-id="e6c37-150">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="e6c37-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="e6c37-151">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="e6c37-152">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="e6c37-153">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="e6c37-154">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="e6c37-155">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appsettings.json* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e6c37-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="e6c37-156">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="e6c37-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="e6c37-157"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="e6c37-158">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="e6c37-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="e6c37-160">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e6c37-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="e6c37-161">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-161">Environment variables</span></span>

<span data-ttu-id="e6c37-162">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> yapılandırma *appsettings.json*, appSettings sonrasında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e6c37-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e6c37-163">Bu nedenle, ortamdan okunan anahtar değerleri, *appsettings.json*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="e6c37-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e6c37-164">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="e6c37-164">The following `set` commands:</span></span>

* <span data-ttu-id="e6c37-165">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="e6c37-166">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="e6c37-167">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="e6c37-168">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="e6c37-168">The preceding environment settings:</span></span>

* <span data-ttu-id="e6c37-169">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="e6c37-170">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="e6c37-171">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="e6c37-172">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="e6c37-173">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="e6c37-174">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="e6c37-175">Yukarıdaki komutların ve appSettings *appsettings.js* geçersiz kılmasını test etmek *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="e6c37-176">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="e6c37-177">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="e6c37-178"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="e6c37-179">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="e6c37-179">In the preceding code:</span></span>

* <span data-ttu-id="e6c37-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="e6c37-181">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e6c37-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="e6c37-182">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="e6c37-183">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="e6c37-184">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="e6c37-185">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="e6c37-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="e6c37-186">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="e6c37-187">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="e6c37-188">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e6c37-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="e6c37-189">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="e6c37-190">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="e6c37-191">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="e6c37-192">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-192">Exposed as environment variables.</span></span>

<span data-ttu-id="e6c37-193">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="e6c37-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="e6c37-194">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="e6c37-195">launchSettings.jsüzerinde ayarlanan ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="e6c37-196">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="e6c37-197">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="e6c37-197">Command-line</span></span>

<span data-ttu-id="e6c37-198">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="e6c37-199">Ve *appsettings* *appsettings.js* . `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="e6c37-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="e6c37-200">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e6c37-201">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-201">Environment variables.</span></span>

<span data-ttu-id="e6c37-202">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="e6c37-203">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-203">Command-line arguments</span></span>

<span data-ttu-id="e6c37-204">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="e6c37-205">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="e6c37-206">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="e6c37-207">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="e6c37-207">The key value:</span></span>

* <span data-ttu-id="e6c37-208">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="e6c37-209">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="e6c37-210">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="e6c37-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="e6c37-211">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="e6c37-212">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="e6c37-212">Switch mappings</span></span>

<span data-ttu-id="e6c37-213">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="e6c37-214">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="e6c37-215">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="e6c37-216">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="e6c37-217">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="e6c37-218">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="e6c37-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="e6c37-219">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="e6c37-220">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="e6c37-221">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="e6c37-222">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-223">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="e6c37-223">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<!-- Run the following command to test the key replacement: -->

<span data-ttu-id="e6c37-224">Note: Şu anda, `=` anahtar değiştirme değerlerini tek bir çizgiyle ayarlamak için kullanılamaz `-` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-224">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="e6c37-225">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-225">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="e6c37-226">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="e6c37-226">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="e6c37-227">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-227">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e6c37-228">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-228">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="e6c37-229">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="e6c37-229">Hierarchical configuration data</span></span>

<span data-ttu-id="e6c37-230">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-230">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="e6c37-231">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *appsettings.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-231">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="e6c37-232">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-232">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-233">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-233">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="e6c37-234">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-234">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="e6c37-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="e6c37-236">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-236">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="e6c37-237">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-237">Configuration keys and values</span></span>

<span data-ttu-id="e6c37-238">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="e6c37-238">Configuration keys:</span></span>

* <span data-ttu-id="e6c37-239">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-239">Are case-insensitive.</span></span> <span data-ttu-id="e6c37-240">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-240">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="e6c37-241">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-241">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="e6c37-242">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="e6c37-242">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="e6c37-243">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="e6c37-243">Hierarchical keys</span></span>
  * <span data-ttu-id="e6c37-244">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-244">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="e6c37-245">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-245">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="e6c37-246">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-246">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="e6c37-247">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-247">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="e6c37-248">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-248">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="e6c37-249">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-249">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e6c37-250">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-250">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="e6c37-251">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="e6c37-251">Configuration values:</span></span>

* <span data-ttu-id="e6c37-252">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-252">Are strings.</span></span>
* <span data-ttu-id="e6c37-253">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-253">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="e6c37-254">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="e6c37-254">Configuration providers</span></span>

<span data-ttu-id="e6c37-255">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-255">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="e6c37-256">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="e6c37-256">Provider</span></span> | <span data-ttu-id="e6c37-257">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="e6c37-257">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="e6c37-258">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-258">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="e6c37-259">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e6c37-259">Azure Key Vault</span></span> |
| [<span data-ttu-id="e6c37-260">Azure uygulama yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-260">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="e6c37-261">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-261">Azure App Configuration</span></span> |
| [<span data-ttu-id="e6c37-262">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-262">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="e6c37-263">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-263">Command-line parameters</span></span> |
| [<span data-ttu-id="e6c37-264">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-264">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="e6c37-265">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="e6c37-265">Custom source</span></span> |
| [<span data-ttu-id="e6c37-266">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-266">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="e6c37-267">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-267">Environment variables</span></span> |
| [<span data-ttu-id="e6c37-268">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-268">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="e6c37-269">INı, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-269">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="e6c37-270">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-270">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="e6c37-271">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-271">Directory files</span></span> |
| [<span data-ttu-id="e6c37-272">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-272">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="e6c37-273">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="e6c37-273">In-memory collections</span></span> |
| [<span data-ttu-id="e6c37-274">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="e6c37-274">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="e6c37-275">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="e6c37-275">File in the user profile directory</span></span> |

<span data-ttu-id="e6c37-276">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="e6c37-276">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="e6c37-277">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-277">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="e6c37-278">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-278">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="e6c37-279">*Üzerindeappsettings.js*</span><span class="sxs-lookup"><span data-stu-id="e6c37-279">*appsettings.json*</span></span>
1. <span data-ttu-id="e6c37-280">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="e6c37-280">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="e6c37-281">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="e6c37-281">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="e6c37-282">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-282">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="e6c37-283">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-283">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="e6c37-284">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-284">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="e6c37-285">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-285">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="e6c37-286">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-286">Connection string prefixes</span></span>

<span data-ttu-id="e6c37-287">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-287">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="e6c37-288">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-288">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="e6c37-289">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-289">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="e6c37-290">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="e6c37-290">Connection string prefix</span></span> | <span data-ttu-id="e6c37-291">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="e6c37-291">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="e6c37-292">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="e6c37-292">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="e6c37-293">MySQL</span><span class="sxs-lookup"><span data-stu-id="e6c37-293">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="e6c37-294">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="e6c37-294">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="e6c37-295">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e6c37-295">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="e6c37-296">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="e6c37-296">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="e6c37-297">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-297">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="e6c37-298">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="e6c37-298">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="e6c37-299">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="e6c37-299">Environment variable key</span></span> | <span data-ttu-id="e6c37-300">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="e6c37-300">Converted configuration key</span></span> | <span data-ttu-id="e6c37-301">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="e6c37-301">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-302">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="e6c37-302">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-303">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e6c37-304">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="e6c37-304">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-305">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e6c37-306">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e6c37-306">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-307">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e6c37-308">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e6c37-308">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="e6c37-309">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-309">JSON configuration provider</span></span>

<span data-ttu-id="e6c37-310">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-310">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="e6c37-311">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-311">Overloads can specify:</span></span>

* <span data-ttu-id="e6c37-312">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="e6c37-312">Whether the file is optional.</span></span>
* <span data-ttu-id="e6c37-313">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-313">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="e6c37-314">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="e6c37-314">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="e6c37-315">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="e6c37-315">The preceding code:</span></span>

* <span data-ttu-id="e6c37-316">Dosyadaki *MyConfig.js* yüklemek için JSON yapılandırma sağlayıcısını aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-316">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="e6c37-317">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-317">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="e6c37-318">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-318">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="e6c37-319">Dosyadaki *MyConfig.js* önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-319">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="e6c37-320">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig.js* dosya geçersiz kılma ayarı ayarları.</span><span class="sxs-lookup"><span data-stu-id="e6c37-320">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e6c37-321">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="e6c37-321">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e6c37-322">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="e6c37-323">Yukarıdaki kodda, ve *MyConfig* *MyConfig.js* `Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="e6c37-323">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="e6c37-324">*appsettings.json* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="e6c37-324">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="e6c37-325">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-325">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e6c37-326">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *MyConfig.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="e6c37-327">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="e6c37-328">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-328">File configuration provider</span></span>

<span data-ttu-id="e6c37-329"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-329"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="e6c37-330">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-330">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="e6c37-331">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-331">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="e6c37-332">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-332">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="e6c37-333">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-333">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="e6c37-334">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-334">INI configuration provider</span></span>

<span data-ttu-id="e6c37-335">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-335">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="e6c37-336">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-336">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="e6c37-337">Yukarıdaki kodda, *MyIniConfig.ini* ve *Myıniconfig*içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-337">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="e6c37-338">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-338">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="e6c37-339">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e6c37-339">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e6c37-340">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyIniConfig.ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-340">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="e6c37-341">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-341">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="e6c37-342">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-342">XML configuration provider</span></span>

<span data-ttu-id="e6c37-343">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-343">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="e6c37-344">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-344">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="e6c37-345">Yukarıdaki kodda, *MyXMLFile.xml* ve *myXMLfile*içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-345">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="e6c37-346">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-346">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="e6c37-347">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="e6c37-347">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="e6c37-348">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyXMLFile.xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-348">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="e6c37-349">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-349">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-350">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-350">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="e6c37-351">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-351">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-352">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-352">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="e6c37-353">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-353">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e6c37-354">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="e6c37-354">key:attribute</span></span>
* <span data-ttu-id="e6c37-355">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="e6c37-355">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="e6c37-356">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-356">Key-per-file configuration provider</span></span>

<span data-ttu-id="e6c37-357">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-357">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="e6c37-358">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-358">The key is the file name.</span></span> <span data-ttu-id="e6c37-359">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-359">The value contains the file's contents.</span></span> <span data-ttu-id="e6c37-360">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-360">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="e6c37-361">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-361">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="e6c37-362">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-362">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="e6c37-363">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="e6c37-363">Overloads permit specifying:</span></span>

* <span data-ttu-id="e6c37-364">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="e6c37-364">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="e6c37-365">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-365">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="e6c37-366">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-366">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="e6c37-367">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-367">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="e6c37-368">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-368">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="e6c37-369">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-369">Memory configuration provider</span></span>

<span data-ttu-id="e6c37-370">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-370">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="e6c37-371">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-371">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="e6c37-372">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-372">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-373">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-373">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="e6c37-374">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="e6c37-374">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="e6c37-375">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-375">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="e6c37-376">GetValue</span><span class="sxs-lookup"><span data-stu-id="e6c37-376">GetValue</span></span>

<span data-ttu-id="e6c37-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="e6c37-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-378">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-378">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="e6c37-379">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="e6c37-379">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="e6c37-380">İzleyen örnekler için, dosyasında aşağıdaki *MySubsection.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e6c37-380">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="e6c37-381">Aşağıdaki kod yapılandırma sağlayıcılarına *MySubsection.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-381">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="e6c37-382">GetSection</span><span class="sxs-lookup"><span data-stu-id="e6c37-382">GetSection</span></span>

<span data-ttu-id="e6c37-383">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="e6c37-383">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="e6c37-384">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-384">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-385">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-385">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-386">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-386">`GetSection` never returns `null`.</span></span> <span data-ttu-id="e6c37-387">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-387">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="e6c37-388">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="e6c37-388">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="e6c37-389">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-389">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="e6c37-390">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="e6c37-390">GetChildren and Exists</span></span>

<span data-ttu-id="e6c37-391">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-391">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-392">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-392">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="e6c37-393">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="e6c37-393">Bind an array</span></span>

<span data-ttu-id="e6c37-394">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-394">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e6c37-395">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-395">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="e6c37-396">[Örnek karşıdan yükleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e6c37-396">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="e6c37-397">Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-397">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="e6c37-398">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-398">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-399">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="e6c37-399">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="e6c37-400">Yukarıdaki çıktıda, Dizin 3 ' teMyArray.js' a karşılık gelen bir değer vardır `value40` `"4": "value40",` . *MyArray.json*</span><span class="sxs-lookup"><span data-stu-id="e6c37-400">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="e6c37-401">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-401">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="e6c37-402">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="e6c37-402">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="e6c37-403">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-403">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="e6c37-404">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-404">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-405">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="e6c37-405">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="e6c37-406">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-406">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="e6c37-407">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-407">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="e6c37-408">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-408">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="e6c37-409">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-409">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="e6c37-410">Örnek indirmenin dosyasında aşağıdaki *Value3.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e6c37-410">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="e6c37-411">Aşağıdaki kod, ve *üzerindeValue3.js* için yapılandırma içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-411">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="e6c37-412">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-412">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-413">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="e6c37-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="e6c37-414">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-414">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="e6c37-415">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-415">Custom configuration provider</span></span>

<span data-ttu-id="e6c37-416">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-416">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="e6c37-417">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-417">The provider has the following characteristics:</span></span>

* <span data-ttu-id="e6c37-418">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-418">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="e6c37-419">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-419">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="e6c37-420">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-420">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="e6c37-421">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-421">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="e6c37-422">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-422">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="e6c37-423">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-423">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="e6c37-424">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-424">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="e6c37-425">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-425">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="e6c37-426">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-426">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="e6c37-427">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-427">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="e6c37-428">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-428">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="e6c37-429">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-429">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="e6c37-430">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-430">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="e6c37-431">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-431">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="e6c37-432">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-432">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="e6c37-433">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-433">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="e6c37-434">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-434">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="e6c37-435">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-435">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="e6c37-436">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-436">Access configuration in Startup</span></span>

<span data-ttu-id="e6c37-437">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-437">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="e6c37-438">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="e6c37-438">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="e6c37-439">Sayfalarda erişim yapılandırması Razor</span><span class="sxs-lookup"><span data-stu-id="e6c37-439">Access configuration in Razor Pages</span></span>

<span data-ttu-id="e6c37-440">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler Razor :</span><span class="sxs-lookup"><span data-stu-id="e6c37-440">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="e6c37-441">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-441">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="e6c37-442">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) Razor seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-442">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="e6c37-443">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-443">Access configuration in a MVC view file</span></span>

<span data-ttu-id="e6c37-444">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-444">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="e6c37-445">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-445">Configure options with a delegate</span></span>

<span data-ttu-id="e6c37-446">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-446">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="e6c37-447">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-447">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="e6c37-448">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-448">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e6c37-449">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-449">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="e6c37-450">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e6c37-450">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="e6c37-451">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *üzerindeappsettings.js* belirtilmiştir ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-451">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="e6c37-452">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="e6c37-452">Host versus app configuration</span></span>

<span data-ttu-id="e6c37-453">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-453">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="e6c37-454">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-454">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e6c37-455">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-455">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="e6c37-456">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-456">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="e6c37-457">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="e6c37-457">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="e6c37-458">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-458">Default host configuration</span></span>

<span data-ttu-id="e6c37-459">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-459">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="e6c37-460">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-460">Host configuration is provided from:</span></span>
  * <span data-ttu-id="e6c37-461">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-461">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="e6c37-462">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-462">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="e6c37-463">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-463">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="e6c37-464">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="e6c37-464">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="e6c37-465">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-465">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="e6c37-466">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-466">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="e6c37-467">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-467">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="e6c37-468">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-468">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="e6c37-469">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-469">Other configuration</span></span>

<span data-ttu-id="e6c37-470">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-470">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="e6c37-471">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-471">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="e6c37-472">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-472">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="e6c37-473">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-473">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="e6c37-474">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="e6c37-474">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="e6c37-475">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-475">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="e6c37-476">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-476">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="e6c37-477">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="e6c37-477">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="e6c37-478">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="e6c37-478">Add configuration from an external assembly</span></span>

<span data-ttu-id="e6c37-479">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-479">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="e6c37-480">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e6c37-480">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6c37-481">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e6c37-481">Additional resources</span></span>

* [<span data-ttu-id="e6c37-482">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="e6c37-482">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6c37-483">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-483">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="e6c37-484">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-484">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="e6c37-485">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e6c37-485">Azure Key Vault</span></span>
* <span data-ttu-id="e6c37-486">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-486">Azure App Configuration</span></span>
* <span data-ttu-id="e6c37-487">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-487">Command-line arguments</span></span>
* <span data-ttu-id="e6c37-488">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="e6c37-488">Custom providers (installed or created)</span></span>
* <span data-ttu-id="e6c37-489">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-489">Directory files</span></span>
* <span data-ttu-id="e6c37-490">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-490">Environment variables</span></span>
* <span data-ttu-id="e6c37-491">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-491">In-memory .NET objects</span></span>
* <span data-ttu-id="e6c37-492">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-492">Settings files</span></span>

<span data-ttu-id="e6c37-493">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-493">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="e6c37-494">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-494">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="e6c37-495">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-495">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="e6c37-496">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-496">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="e6c37-497">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="e6c37-497">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="e6c37-498">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6c37-498">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="e6c37-499">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="e6c37-499">Host versus app configuration</span></span>

<span data-ttu-id="e6c37-500">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-500">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="e6c37-501">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-501">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="e6c37-502">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-502">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="e6c37-503">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-503">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="e6c37-504">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="e6c37-504">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="e6c37-505">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-505">Other configuration</span></span>

<span data-ttu-id="e6c37-506">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-506">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="e6c37-507">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-507">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="e6c37-508">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-508">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="e6c37-509">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-509">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="e6c37-510">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="e6c37-510">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="e6c37-511">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-511">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="e6c37-512">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="e6c37-512">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="e6c37-513">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-513">Default configuration</span></span>

<span data-ttu-id="e6c37-514">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-514">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="e6c37-515">`CreateDefaultBuilder`uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="e6c37-515">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="e6c37-516">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-516">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e6c37-517">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-517">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="e6c37-518">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-518">Host configuration is provided from:</span></span>
  * <span data-ttu-id="e6c37-519">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-519">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="e6c37-520">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-520">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="e6c37-521">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-521">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="e6c37-522">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-522">App configuration is provided from:</span></span>
  * <span data-ttu-id="e6c37-523">[Dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="e6c37-523">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="e6c37-524">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="e6c37-524">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="e6c37-525">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="e6c37-525">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="e6c37-526">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-526">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="e6c37-527">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-527">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="e6c37-528">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="e6c37-528">Security</span></span>

<span data-ttu-id="e6c37-529">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="e6c37-529">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="e6c37-530">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-530">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="e6c37-531">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-531">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="e6c37-532">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-532">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="e6c37-533">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-533">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="e6c37-534"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-534"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="e6c37-535">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-535">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="e6c37-536">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-536">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="e6c37-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="e6c37-538">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e6c37-538">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="e6c37-539">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="e6c37-539">Hierarchical configuration data</span></span>

<span data-ttu-id="e6c37-540">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="e6c37-540">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="e6c37-541">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-541">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="e6c37-542">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-542">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="e6c37-543">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-543">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="e6c37-544">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-544">section0:key0</span></span>
* <span data-ttu-id="e6c37-545">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-545">section0:key1</span></span>
* <span data-ttu-id="e6c37-546">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-546">section1:key0</span></span>
* <span data-ttu-id="e6c37-547">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-547">section1:key1</span></span>

<span data-ttu-id="e6c37-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="e6c37-549">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-549">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="e6c37-550">Kurallar</span><span class="sxs-lookup"><span data-stu-id="e6c37-550">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="e6c37-551">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="e6c37-551">Sources and providers</span></span>

<span data-ttu-id="e6c37-552">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="e6c37-552">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="e6c37-553">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-553">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="e6c37-554">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="e6c37-554">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="e6c37-555"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e6c37-556"><xref:Microsoft.Extensions.Configuration.IConfiguration>Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="e6c37-557">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-557">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="e6c37-558">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-558">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="e6c37-559">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="e6c37-559">Keys</span></span>

<span data-ttu-id="e6c37-560">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="e6c37-560">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="e6c37-561">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-561">Keys are case-insensitive.</span></span> <span data-ttu-id="e6c37-562">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-562">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="e6c37-563">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-563">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="e6c37-564">Yinelenen JSON anahtarları hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/2381)bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-564">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="e6c37-565">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="e6c37-565">Hierarchical keys</span></span>
  * <span data-ttu-id="e6c37-566">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-566">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="e6c37-567">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-567">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="e6c37-568">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-568">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="e6c37-569">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-569">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="e6c37-570">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-570">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="e6c37-571">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-571">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e6c37-572">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-572">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="e6c37-573">Değerler</span><span class="sxs-lookup"><span data-stu-id="e6c37-573">Values</span></span>

<span data-ttu-id="e6c37-574">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="e6c37-574">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="e6c37-575">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-575">Values are strings.</span></span>
* <span data-ttu-id="e6c37-576">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-576">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="e6c37-577">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="e6c37-577">Providers</span></span>

<span data-ttu-id="e6c37-578">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-578">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="e6c37-579">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="e6c37-579">Provider</span></span> | <span data-ttu-id="e6c37-580">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="e6c37-580">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="e6c37-581">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="e6c37-581">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="e6c37-582">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e6c37-582">Azure Key Vault</span></span> |
| <span data-ttu-id="e6c37-583">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="e6c37-583">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="e6c37-584">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-584">Azure App Configuration</span></span> |
| [<span data-ttu-id="e6c37-585">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-585">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="e6c37-586">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-586">Command-line parameters</span></span> |
| [<span data-ttu-id="e6c37-587">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-587">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="e6c37-588">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="e6c37-588">Custom source</span></span> |
| [<span data-ttu-id="e6c37-589">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-589">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="e6c37-590">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-590">Environment variables</span></span> |
| [<span data-ttu-id="e6c37-591">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-591">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="e6c37-592">Dosyalar (ıNı, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="e6c37-592">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="e6c37-593">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-593">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="e6c37-594">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="e6c37-594">Directory files</span></span> |
| [<span data-ttu-id="e6c37-595">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-595">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="e6c37-596">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="e6c37-596">In-memory collections</span></span> |
| <span data-ttu-id="e6c37-597">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="e6c37-597">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="e6c37-598">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="e6c37-598">File in the user profile directory</span></span> |

<span data-ttu-id="e6c37-599">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="e6c37-599">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="e6c37-600">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-600">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="e6c37-601">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-601">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="e6c37-602">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-602">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="e6c37-603">Dosyalar (*appsettings.json*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="e6c37-603">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="e6c37-604">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e6c37-604">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="e6c37-605">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="e6c37-605">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="e6c37-606">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-606">Environment variables</span></span>
1. <span data-ttu-id="e6c37-607">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e6c37-607">Command-line arguments</span></span>

<span data-ttu-id="e6c37-608">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-608">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="e6c37-609">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-609">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e6c37-610">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-610">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="e6c37-611">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e6c37-611">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="e6c37-612">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-612">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="e6c37-613">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e6c37-613">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e6c37-614">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-614">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="e6c37-615">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="e6c37-615">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="e6c37-616">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-616">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="e6c37-617">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="e6c37-617">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="e6c37-618">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-618">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="e6c37-619">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="e6c37-619">Consume configuration during app startup</span></span>

<span data-ttu-id="e6c37-620">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-620">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e6c37-621">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-621">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="e6c37-622">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-622">Command-line Configuration Provider</span></span>

<span data-ttu-id="e6c37-623">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-623">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="e6c37-624">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-624">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e6c37-625">`AddCommandLine`çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-625">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="e6c37-626">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-626">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e6c37-627">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-627">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e6c37-628">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="e6c37-628">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="e6c37-629">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-629">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e6c37-630">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-630">Environment variables.</span></span>

<span data-ttu-id="e6c37-631">`CreateDefaultBuilder`Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-631">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="e6c37-632">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="e6c37-632">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="e6c37-633">`CreateDefaultBuilder`Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-633">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="e6c37-634">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-634">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="e6c37-635">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-635">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e6c37-636">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-636">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="e6c37-637">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="e6c37-637">**Example**</span></span>

<span data-ttu-id="e6c37-638">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-638">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="e6c37-639">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-639">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="e6c37-640">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-640">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="e6c37-641">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-641">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e6c37-642">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-642">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="e6c37-643">Arguments</span><span class="sxs-lookup"><span data-stu-id="e6c37-643">Arguments</span></span>

<span data-ttu-id="e6c37-644">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-644">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="e6c37-645">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="e6c37-645">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="e6c37-646">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="e6c37-646">Key prefix</span></span>               | <span data-ttu-id="e6c37-647">Örnek</span><span class="sxs-lookup"><span data-stu-id="e6c37-647">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="e6c37-648">Ön ek yok</span><span class="sxs-lookup"><span data-stu-id="e6c37-648">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="e6c37-649">İki kısa çizgi ( `--` )</span><span class="sxs-lookup"><span data-stu-id="e6c37-649">Two dashes (`--`)</span></span>        | <span data-ttu-id="e6c37-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="e6c37-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="e6c37-651">Eğik çizgi ( `/` )</span><span class="sxs-lookup"><span data-stu-id="e6c37-651">Forward slash (`/`)</span></span>      | <span data-ttu-id="e6c37-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="e6c37-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="e6c37-653">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-653">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="e6c37-654">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="e6c37-654">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="e6c37-655">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="e6c37-655">Switch mappings</span></span>

<span data-ttu-id="e6c37-656">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-656">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="e6c37-657">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-657">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="e6c37-658">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-658">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="e6c37-659">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-659">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="e6c37-660">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-660">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="e6c37-661">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="e6c37-661">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="e6c37-662">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-662">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="e6c37-663">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-663">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="e6c37-664">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e6c37-664">Create a switch mappings dictionary.</span></span> <span data-ttu-id="e6c37-665">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-665">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="e6c37-666">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-666">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="e6c37-667">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="e6c37-667">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="e6c37-668">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-668">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e6c37-669">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-669">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="e6c37-670">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-670">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="e6c37-671">Anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-671">Key</span></span>       | <span data-ttu-id="e6c37-672">Değer</span><span class="sxs-lookup"><span data-stu-id="e6c37-672">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="e6c37-673">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-673">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="e6c37-674">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-674">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="e6c37-675">Anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-675">Key</span></span>               | <span data-ttu-id="e6c37-676">Değer</span><span class="sxs-lookup"><span data-stu-id="e6c37-676">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="e6c37-677">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-677">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="e6c37-678">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-678">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="e6c37-679">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-679">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e6c37-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="e6c37-681">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="e6c37-681">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="e6c37-682">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-682">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="e6c37-683">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-683">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e6c37-684">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-684">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e6c37-685">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-685">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="e6c37-686">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="e6c37-686">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="e6c37-687">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-687">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e6c37-688">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-688">Command-line arguments.</span></span>

<span data-ttu-id="e6c37-689">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-689">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="e6c37-690">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-690">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="e6c37-691">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-691">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="e6c37-692">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-692">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="e6c37-693">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="e6c37-693">**Example**</span></span>

<span data-ttu-id="e6c37-694">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-694">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="e6c37-695">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-695">Run the sample app.</span></span> <span data-ttu-id="e6c37-696">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-696">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e6c37-697">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-697">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="e6c37-698">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-698">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="e6c37-699">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-699">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="e6c37-700">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-700">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="e6c37-701">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e6c37-701">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="e6c37-702">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="e6c37-702">Prefixes</span></span>

<span data-ttu-id="e6c37-703">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-703">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="e6c37-704">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-704">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="e6c37-705">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-705">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="e6c37-706">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-706">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="e6c37-707">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-707">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e6c37-708">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="e6c37-708">**Connection string prefixes**</span></span>

<span data-ttu-id="e6c37-709">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-709">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="e6c37-710">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-710">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="e6c37-711">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="e6c37-711">Connection string prefix</span></span> | <span data-ttu-id="e6c37-712">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="e6c37-712">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="e6c37-713">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="e6c37-713">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="e6c37-714">MySQL</span><span class="sxs-lookup"><span data-stu-id="e6c37-714">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="e6c37-715">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="e6c37-715">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="e6c37-716">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e6c37-716">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="e6c37-717">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="e6c37-717">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="e6c37-718">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-718">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="e6c37-719">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="e6c37-719">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="e6c37-720">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="e6c37-720">Environment variable key</span></span> | <span data-ttu-id="e6c37-721">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="e6c37-721">Converted configuration key</span></span> | <span data-ttu-id="e6c37-722">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="e6c37-722">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-723">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="e6c37-723">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-724">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e6c37-725">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="e6c37-725">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-726">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e6c37-727">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e6c37-727">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="e6c37-728">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="e6c37-729">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="e6c37-729">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="e6c37-730">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="e6c37-730">**Example**</span></span>

<span data-ttu-id="e6c37-731">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-731">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="e6c37-732">Ad: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="e6c37-732">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="e6c37-733">Değer: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="e6c37-733">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="e6c37-734">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="e6c37-734">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="e6c37-735">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-735">File Configuration Provider</span></span>

<span data-ttu-id="e6c37-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="e6c37-737">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-737">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="e6c37-738">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-738">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="e6c37-739">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-739">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="e6c37-740">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-740">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="e6c37-741">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-741">INI Configuration Provider</span></span>

<span data-ttu-id="e6c37-742">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-742">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="e6c37-743">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-743">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e6c37-744">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-744">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="e6c37-745">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="e6c37-745">Overloads permit specifying:</span></span>

* <span data-ttu-id="e6c37-746">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="e6c37-746">Whether the file is optional.</span></span>
* <span data-ttu-id="e6c37-747">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-747">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e6c37-748"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-748">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e6c37-749">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-749">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e6c37-750">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="e6c37-750">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="e6c37-751">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-751">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e6c37-752">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-752">section0:key0</span></span>
* <span data-ttu-id="e6c37-753">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-753">section0:key1</span></span>
* <span data-ttu-id="e6c37-754">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-754">section1:subsection:key</span></span>
* <span data-ttu-id="e6c37-755">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-755">section2:subsection0:key</span></span>
* <span data-ttu-id="e6c37-756">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-756">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="e6c37-757">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-757">JSON Configuration Provider</span></span>

<span data-ttu-id="e6c37-758">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-758">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="e6c37-759">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-759">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e6c37-760">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="e6c37-760">Overloads permit specifying:</span></span>

* <span data-ttu-id="e6c37-761">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="e6c37-761">Whether the file is optional.</span></span>
* <span data-ttu-id="e6c37-762">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-762">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e6c37-763"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-763">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e6c37-764">`AddJsonFile`, ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-764">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e6c37-765">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-765">The method is called to load configuration from:</span></span>

* <span data-ttu-id="e6c37-766">*appsettings.js*: Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="e6c37-766">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="e6c37-767">Dosyanın ortam sürümü, *appsettings.js* dosya tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-767">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="e6c37-768">*appSettings. {Environment}. JSON*: dosyanın ortam sürümü [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-768">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="e6c37-769">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-769">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="e6c37-770">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-770">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="e6c37-771">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-771">Environment variables.</span></span>
* <span data-ttu-id="e6c37-772">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="e6c37-772">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="e6c37-773">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="e6c37-773">Command-line arguments.</span></span>

<span data-ttu-id="e6c37-774">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-774">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="e6c37-775">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-775">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="e6c37-776">`ConfigureAppConfiguration`Ana bilgisayarı oluştururken, *appsettings.json* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e6c37-777">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="e6c37-777">**Example**</span></span>

<span data-ttu-id="e6c37-778">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-778">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="e6c37-779">`AddJsonFile` *' Dekiappsettings.js*yapılandırmayı yükleyen ilk çağrı:</span><span class="sxs-lookup"><span data-stu-id="e6c37-779">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="e6c37-780">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e6c37-780">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="e6c37-781">Örnek uygulamada *appsettings.Development.js* için aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-781">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="e6c37-782">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-782">Run the sample app.</span></span> <span data-ttu-id="e6c37-783">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-783">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="e6c37-784">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-784">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="e6c37-785">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="e6c37-785">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="e6c37-786">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-786">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="e6c37-787">Dosyadaki *Özellikler/launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-787">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="e6c37-788">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-788">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="e6c37-789">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-789">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="e6c37-790">*appsettings.Development.jsüzerindeki* ayarlar artık *appsettings.jsüzerindeki*ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-790">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="e6c37-791">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-791">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="e6c37-792">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-792">XML Configuration Provider</span></span>

<span data-ttu-id="e6c37-793">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-793">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="e6c37-794">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-794">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e6c37-795">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="e6c37-795">Overloads permit specifying:</span></span>

* <span data-ttu-id="e6c37-796">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="e6c37-796">Whether the file is optional.</span></span>
* <span data-ttu-id="e6c37-797">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-797">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="e6c37-798"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-798">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="e6c37-799">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-799">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="e6c37-800">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-800">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="e6c37-801">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="e6c37-802">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-802">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="e6c37-803">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-803">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e6c37-804">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-804">section0:key0</span></span>
* <span data-ttu-id="e6c37-805">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-805">section0:key1</span></span>
* <span data-ttu-id="e6c37-806">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-806">section1:key0</span></span>
* <span data-ttu-id="e6c37-807">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-807">section1:key1</span></span>

<span data-ttu-id="e6c37-808">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-808">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="e6c37-809">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-809">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e6c37-810">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-810">section:section0:key:key0</span></span>
* <span data-ttu-id="e6c37-811">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-811">section:section0:key:key1</span></span>
* <span data-ttu-id="e6c37-812">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-812">section:section1:key:key0</span></span>
* <span data-ttu-id="e6c37-813">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-813">section:section1:key:key1</span></span>

<span data-ttu-id="e6c37-814">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-814">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="e6c37-815">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-815">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="e6c37-816">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="e6c37-816">key:attribute</span></span>
* <span data-ttu-id="e6c37-817">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="e6c37-817">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="e6c37-818">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-818">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="e6c37-819">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-819">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="e6c37-820">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-820">The key is the file name.</span></span> <span data-ttu-id="e6c37-821">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-821">The value contains the file's contents.</span></span> <span data-ttu-id="e6c37-822">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-822">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="e6c37-823">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-823">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="e6c37-824">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-824">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="e6c37-825">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="e6c37-825">Overloads permit specifying:</span></span>

* <span data-ttu-id="e6c37-826">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="e6c37-826">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="e6c37-827">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-827">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="e6c37-828">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-828">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="e6c37-829">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-829">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="e6c37-830">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-830">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="e6c37-831">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-831">Memory Configuration Provider</span></span>

<span data-ttu-id="e6c37-832">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-832">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="e6c37-833">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-833">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="e6c37-834">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-834">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="e6c37-835">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="e6c37-836">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-836">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="e6c37-837">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="e6c37-837">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="e6c37-838">GetValue</span><span class="sxs-lookup"><span data-stu-id="e6c37-838">GetValue</span></span>

<span data-ttu-id="e6c37-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="e6c37-840">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="e6c37-840">An overload accepts a default value.</span></span>

<span data-ttu-id="e6c37-841">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="e6c37-841">The following example:</span></span>

* <span data-ttu-id="e6c37-842">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-842">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="e6c37-843">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-843">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="e6c37-844">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-844">Types the value as an `int`.</span></span>
* <span data-ttu-id="e6c37-845">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-845">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="e6c37-846">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="e6c37-846">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="e6c37-847">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="e6c37-847">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="e6c37-848">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-848">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="e6c37-849">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-849">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="e6c37-850">section0:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-850">section0:key0</span></span>
* <span data-ttu-id="e6c37-851">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-851">section0:key1</span></span>
* <span data-ttu-id="e6c37-852">section1:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-852">section1:key0</span></span>
* <span data-ttu-id="e6c37-853">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-853">section1:key1</span></span>
* <span data-ttu-id="e6c37-854">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-854">section2:subsection0:key0</span></span>
* <span data-ttu-id="e6c37-855">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-855">section2:subsection0:key1</span></span>
* <span data-ttu-id="e6c37-856">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="e6c37-856">section2:subsection1:key0</span></span>
* <span data-ttu-id="e6c37-857">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="e6c37-857">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="e6c37-858">GetSection</span><span class="sxs-lookup"><span data-stu-id="e6c37-858">GetSection</span></span>

<span data-ttu-id="e6c37-859">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="e6c37-859">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="e6c37-860"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-860">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="e6c37-861">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-861">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="e6c37-862">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-862">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="e6c37-863">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-863">`GetSection` never returns `null`.</span></span> <span data-ttu-id="e6c37-864">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-864">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="e6c37-865">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="e6c37-865">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="e6c37-866">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-866">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="e6c37-867">GetChildren</span><span class="sxs-lookup"><span data-stu-id="e6c37-867">GetChildren</span></span>

<span data-ttu-id="e6c37-868">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-868">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="e6c37-869">Var</span><span class="sxs-lookup"><span data-stu-id="e6c37-869">Exists</span></span>

<span data-ttu-id="e6c37-870">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-870">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="e6c37-871">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-871">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="e6c37-872">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="e6c37-872">Bind to an object graph</span></span>

<span data-ttu-id="e6c37-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>, bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="e6c37-874">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-874">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="e6c37-875">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="e6c37-875">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="e6c37-876">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow.xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-876">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="e6c37-877">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-877">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="e6c37-878">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="e6c37-878">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="e6c37-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="e6c37-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="e6c37-880">`Get<T>`, kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-880">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="e6c37-881">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-881">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="e6c37-882">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="e6c37-882">Bind an array to a class</span></span>

<span data-ttu-id="e6c37-883">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="e6c37-883">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="e6c37-884">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="e6c37-884">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="e6c37-885">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-885">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="e6c37-886">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-886">Binding is provided by convention.</span></span> <span data-ttu-id="e6c37-887">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-887">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="e6c37-888">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="e6c37-888">**In-memory array processing**</span></span>

<span data-ttu-id="e6c37-889">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="e6c37-889">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="e6c37-890">Anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-890">Key</span></span>             | <span data-ttu-id="e6c37-891">Değer</span><span class="sxs-lookup"><span data-stu-id="e6c37-891">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="e6c37-892">dizi: girdiler: 0</span><span class="sxs-lookup"><span data-stu-id="e6c37-892">array:entries:0</span></span> | <span data-ttu-id="e6c37-893">value0</span><span class="sxs-lookup"><span data-stu-id="e6c37-893">value0</span></span> |
| <span data-ttu-id="e6c37-894">dizi: girdiler: 1</span><span class="sxs-lookup"><span data-stu-id="e6c37-894">array:entries:1</span></span> | <span data-ttu-id="e6c37-895">value1</span><span class="sxs-lookup"><span data-stu-id="e6c37-895">value1</span></span> |
| <span data-ttu-id="e6c37-896">dizi: girdiler: 2</span><span class="sxs-lookup"><span data-stu-id="e6c37-896">array:entries:2</span></span> | <span data-ttu-id="e6c37-897">value2</span><span class="sxs-lookup"><span data-stu-id="e6c37-897">value2</span></span> |
| <span data-ttu-id="e6c37-898">dizi: girdiler: 4</span><span class="sxs-lookup"><span data-stu-id="e6c37-898">array:entries:4</span></span> | <span data-ttu-id="e6c37-899">value4</span><span class="sxs-lookup"><span data-stu-id="e6c37-899">value4</span></span> |
| <span data-ttu-id="e6c37-900">dizi: girdiler: 5</span><span class="sxs-lookup"><span data-stu-id="e6c37-900">array:entries:5</span></span> | <span data-ttu-id="e6c37-901">value5</span><span class="sxs-lookup"><span data-stu-id="e6c37-901">value5</span></span> |

<span data-ttu-id="e6c37-902">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-902">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="e6c37-903">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="e6c37-903">The array skips a value for index &num;3.</span></span> <span data-ttu-id="e6c37-904">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-904">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="e6c37-905">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-905">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="e6c37-906">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="e6c37-906">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="e6c37-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="e6c37-908">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-908">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="e6c37-909">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="e6c37-909">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="e6c37-910">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="e6c37-910">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="e6c37-911">0</span><span class="sxs-lookup"><span data-stu-id="e6c37-911">0</span></span>                            | <span data-ttu-id="e6c37-912">value0</span><span class="sxs-lookup"><span data-stu-id="e6c37-912">value0</span></span>                       |
| <span data-ttu-id="e6c37-913">1</span><span class="sxs-lookup"><span data-stu-id="e6c37-913">1</span></span>                            | <span data-ttu-id="e6c37-914">value1</span><span class="sxs-lookup"><span data-stu-id="e6c37-914">value1</span></span>                       |
| <span data-ttu-id="e6c37-915">2</span><span class="sxs-lookup"><span data-stu-id="e6c37-915">2</span></span>                            | <span data-ttu-id="e6c37-916">value2</span><span class="sxs-lookup"><span data-stu-id="e6c37-916">value2</span></span>                       |
| <span data-ttu-id="e6c37-917">3</span><span class="sxs-lookup"><span data-stu-id="e6c37-917">3</span></span>                            | <span data-ttu-id="e6c37-918">value4</span><span class="sxs-lookup"><span data-stu-id="e6c37-918">value4</span></span>                       |
| <span data-ttu-id="e6c37-919">4</span><span class="sxs-lookup"><span data-stu-id="e6c37-919">4</span></span>                            | <span data-ttu-id="e6c37-920">value5</span><span class="sxs-lookup"><span data-stu-id="e6c37-920">value5</span></span>                       |

<span data-ttu-id="e6c37-921">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-921">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="e6c37-922">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-922">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="e6c37-923">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-923">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="e6c37-924">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-924">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="e6c37-925">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-925">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="e6c37-926">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-926">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="e6c37-927">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="e6c37-927">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="e6c37-928">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="e6c37-928">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="e6c37-929">Anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-929">Key</span></span>             | <span data-ttu-id="e6c37-930">Değer</span><span class="sxs-lookup"><span data-stu-id="e6c37-930">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="e6c37-931">dizi: girdiler: 3</span><span class="sxs-lookup"><span data-stu-id="e6c37-931">array:entries:3</span></span> | <span data-ttu-id="e6c37-932">value3</span><span class="sxs-lookup"><span data-stu-id="e6c37-932">value3</span></span> |

<span data-ttu-id="e6c37-933">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-933">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="e6c37-934">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="e6c37-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="e6c37-935">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="e6c37-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="e6c37-936">0</span><span class="sxs-lookup"><span data-stu-id="e6c37-936">0</span></span>                            | <span data-ttu-id="e6c37-937">value0</span><span class="sxs-lookup"><span data-stu-id="e6c37-937">value0</span></span>                       |
| <span data-ttu-id="e6c37-938">1</span><span class="sxs-lookup"><span data-stu-id="e6c37-938">1</span></span>                            | <span data-ttu-id="e6c37-939">value1</span><span class="sxs-lookup"><span data-stu-id="e6c37-939">value1</span></span>                       |
| <span data-ttu-id="e6c37-940">2</span><span class="sxs-lookup"><span data-stu-id="e6c37-940">2</span></span>                            | <span data-ttu-id="e6c37-941">value2</span><span class="sxs-lookup"><span data-stu-id="e6c37-941">value2</span></span>                       |
| <span data-ttu-id="e6c37-942">3</span><span class="sxs-lookup"><span data-stu-id="e6c37-942">3</span></span>                            | <span data-ttu-id="e6c37-943">value3</span><span class="sxs-lookup"><span data-stu-id="e6c37-943">value3</span></span>                       |
| <span data-ttu-id="e6c37-944">4</span><span class="sxs-lookup"><span data-stu-id="e6c37-944">4</span></span>                            | <span data-ttu-id="e6c37-945">value4</span><span class="sxs-lookup"><span data-stu-id="e6c37-945">value4</span></span>                       |
| <span data-ttu-id="e6c37-946">5</span><span class="sxs-lookup"><span data-stu-id="e6c37-946">5</span></span>                            | <span data-ttu-id="e6c37-947">value5</span><span class="sxs-lookup"><span data-stu-id="e6c37-947">value5</span></span>                       |

<span data-ttu-id="e6c37-948">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="e6c37-948">**JSON array processing**</span></span>

<span data-ttu-id="e6c37-949">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-949">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="e6c37-950">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-950">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="e6c37-951">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="e6c37-951">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="e6c37-952">Anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-952">Key</span></span>                     | <span data-ttu-id="e6c37-953">Değer</span><span class="sxs-lookup"><span data-stu-id="e6c37-953">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="e6c37-954">json_array: anahtar</span><span class="sxs-lookup"><span data-stu-id="e6c37-954">json_array:key</span></span>          | <span data-ttu-id="e6c37-955">değer EA</span><span class="sxs-lookup"><span data-stu-id="e6c37-955">valueA</span></span> |
| <span data-ttu-id="e6c37-956">json_array: alt bölüm: 0</span><span class="sxs-lookup"><span data-stu-id="e6c37-956">json_array:subsection:0</span></span> | <span data-ttu-id="e6c37-957">valueB</span><span class="sxs-lookup"><span data-stu-id="e6c37-957">valueB</span></span> |
| <span data-ttu-id="e6c37-958">json_array: alt bölüm: 1</span><span class="sxs-lookup"><span data-stu-id="e6c37-958">json_array:subsection:1</span></span> | <span data-ttu-id="e6c37-959">değer EC</span><span class="sxs-lookup"><span data-stu-id="e6c37-959">valueC</span></span> |
| <span data-ttu-id="e6c37-960">json_array: alt bölüm: 2</span><span class="sxs-lookup"><span data-stu-id="e6c37-960">json_array:subsection:2</span></span> | <span data-ttu-id="e6c37-961">Değerler</span><span class="sxs-lookup"><span data-stu-id="e6c37-961">valueD</span></span> |

<span data-ttu-id="e6c37-962">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-962">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="e6c37-963">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-963">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="e6c37-964">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-964">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="e6c37-965">`JsonArrayExample.Subsection`İndeks</span><span class="sxs-lookup"><span data-stu-id="e6c37-965">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="e6c37-966">`JsonArrayExample.Subsection`Deeri</span><span class="sxs-lookup"><span data-stu-id="e6c37-966">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="e6c37-967">0</span><span class="sxs-lookup"><span data-stu-id="e6c37-967">0</span></span>                                   | <span data-ttu-id="e6c37-968">valueB</span><span class="sxs-lookup"><span data-stu-id="e6c37-968">valueB</span></span>                              |
| <span data-ttu-id="e6c37-969">1</span><span class="sxs-lookup"><span data-stu-id="e6c37-969">1</span></span>                                   | <span data-ttu-id="e6c37-970">değer EC</span><span class="sxs-lookup"><span data-stu-id="e6c37-970">valueC</span></span>                              |
| <span data-ttu-id="e6c37-971">2</span><span class="sxs-lookup"><span data-stu-id="e6c37-971">2</span></span>                                   | <span data-ttu-id="e6c37-972">Değerler</span><span class="sxs-lookup"><span data-stu-id="e6c37-972">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="e6c37-973">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="e6c37-973">Custom configuration provider</span></span>

<span data-ttu-id="e6c37-974">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-974">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="e6c37-975">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-975">The provider has the following characteristics:</span></span>

* <span data-ttu-id="e6c37-976">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-976">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="e6c37-977">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-977">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="e6c37-978">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="e6c37-978">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="e6c37-979">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="e6c37-979">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="e6c37-980">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="e6c37-980">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="e6c37-981">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="e6c37-981">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="e6c37-982">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-982">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="e6c37-983">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-983">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="e6c37-984">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-984">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="e6c37-985">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-985">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="e6c37-986">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-986">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="e6c37-987">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="e6c37-987">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="e6c37-988">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="e6c37-988">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="e6c37-989">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-989">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="e6c37-990">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-990">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="e6c37-991">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="e6c37-991">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="e6c37-992">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="e6c37-992">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="e6c37-993">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="e6c37-993">Access configuration during startup</span></span>

<span data-ttu-id="e6c37-994">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-994">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e6c37-995">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="e6c37-995">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="e6c37-996">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="e6c37-996">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="e6c37-997">RazorSayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e6c37-997">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="e6c37-998">Bir Razor Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft.Extensions.Configurlama ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e6c37-998">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="e6c37-999">RazorSayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="e6c37-999">In a Razor Pages page:</span></span>

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

<span data-ttu-id="e6c37-1000">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="e6c37-1000">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="e6c37-1001">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="e6c37-1001">Add configuration from an external assembly</span></span>

<span data-ttu-id="e6c37-1002">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e6c37-1002">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="e6c37-1003">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e6c37-1003">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6c37-1004">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e6c37-1004">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
