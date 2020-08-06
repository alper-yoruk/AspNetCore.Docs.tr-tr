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
ms.openlocfilehash: 9f143523a6d02ac018ad2a869cc9d768ee25681f
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819269"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="d983f-103">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="d983f-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d983f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d983f-105">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="d983f-106">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="d983f-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="d983f-107">*appsettings.js* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="d983f-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-108">Environment variables</span></span>
* <span data-ttu-id="d983f-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d983f-109">Azure Key Vault</span></span>
* <span data-ttu-id="d983f-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-110">Azure App Configuration</span></span>
* <span data-ttu-id="d983f-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-111">Command-line arguments</span></span>
* <span data-ttu-id="d983f-112">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="d983f-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="d983f-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-113">Directory files</span></span>
* <span data-ttu-id="d983f-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="d983f-114">In-memory .NET objects</span></span>

<span data-ttu-id="d983f-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d983f-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="d983f-116">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-116">Default configuration</span></span>

<span data-ttu-id="d983f-117">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d983f-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="d983f-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="d983f-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="d983f-119">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="d983f-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="d983f-120">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d983f-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="d983f-121">[JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak [appsettings.js](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="d983f-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="d983f-122">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d983f-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="d983f-123">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="d983f-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="d983f-124">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="d983f-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="d983f-125">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d983f-126">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="d983f-127">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="d983f-128">Örneğin, `MyKey` ve ortamında hem *appsettings.js* hem de ortamda ayarlandıysa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="d983f-129">Varsayılan yapılandırma sağlayıcılarını kullanarak, [komut satırı yapılandırma sağlayıcısı](#clcp) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="d983f-130">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="d983f-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="d983f-131">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="d983f-132">Üzerinde appsettings.js</span><span class="sxs-lookup"><span data-stu-id="d983f-132">appsettings.json</span></span>

<span data-ttu-id="d983f-133">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d983f-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d983f-134">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-135">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d983f-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="d983f-136">*Üzerindeappsettings.js*</span><span class="sxs-lookup"><span data-stu-id="d983f-136">*appsettings.json*</span></span>
1. <span data-ttu-id="d983f-137">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d983f-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="d983f-138">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="d983f-139">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d983f-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d983f-140">*appSettings*. `Environment` . *JSON* değerleri *appsettings.jsüzerindeki*anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="d983f-141">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="d983f-141">For example, by default:</span></span>

* <span data-ttu-id="d983f-142">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması, *üzerindeappsettings.js*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="d983f-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="d983f-143">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması, *üzerindeappsettings.js*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="d983f-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="d983f-144">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="d983f-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="d983f-145">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="d983f-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="d983f-146">[Varsayılan](#default) yapılandırmayı kullanarak *appsettings.js* ve appSettings ' i kullanın *.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="d983f-147">Ve appSettings *üzerindeappsettings.js* yapılan değişiklikler *.* `Environment` uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="d983f-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d983f-148">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="d983f-149">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="d983f-149">Security and secret manager</span></span>

<span data-ttu-id="d983f-150">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="d983f-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="d983f-151">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="d983f-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="d983f-152">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="d983f-153">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d983f-154">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="d983f-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d983f-155">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appsettings.json* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d983f-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="d983f-156">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="d983f-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d983f-157"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d983f-158">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="d983f-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="d983f-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d983f-160">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d983f-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="d983f-161">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-161">Environment variables</span></span>

<span data-ttu-id="d983f-162">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> yapılandırma *appsettings.json*, appSettings sonrasında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d983f-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d983f-163">Bu nedenle, ortamdan okunan anahtar değerleri, *appsettings.json*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="d983f-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d983f-164">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="d983f-164">The following `set` commands:</span></span>

* <span data-ttu-id="d983f-165">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="d983f-166">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="d983f-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="d983f-167">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d983f-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="d983f-168">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="d983f-168">The preceding environment settings:</span></span>

* <span data-ttu-id="d983f-169">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d983f-170">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="d983f-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d983f-171">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="d983f-172">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d983f-173">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d983f-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="d983f-174">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="d983f-175">Yukarıdaki komutların ve appSettings *appsettings.js* geçersiz kılmasını test etmek *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d983f-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="d983f-176">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="d983f-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="d983f-177">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d983f-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="d983f-178"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="d983f-179">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="d983f-179">In the preceding code:</span></span>

* <span data-ttu-id="d983f-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d983f-181">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d983f-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="d983f-182">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="d983f-183">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="d983f-184">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="d983f-185">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="d983f-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="d983f-186">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="d983f-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="d983f-187">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="d983f-188">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d983f-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="d983f-189">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="d983f-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d983f-190">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d983f-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d983f-191">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d983f-192">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="d983f-192">Exposed as environment variables.</span></span>

<span data-ttu-id="d983f-193">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d983f-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d983f-194">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="d983f-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="d983f-195">launchSettings.jsüzerinde ayarlanan ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="d983f-196">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="d983f-197">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="d983f-197">Command-line</span></span>

<span data-ttu-id="d983f-198">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="d983f-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="d983f-199">Ve *appsettings* *appsettings.js* . `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d983f-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d983f-200">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d983f-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d983f-201">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-201">Environment variables.</span></span>

<span data-ttu-id="d983f-202">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="d983f-203">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-203">Command-line arguments</span></span>

<span data-ttu-id="d983f-204">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="d983f-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="d983f-205">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="d983f-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="d983f-206">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="d983f-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="d983f-207">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="d983f-207">The key value:</span></span>

* <span data-ttu-id="d983f-208">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="d983f-209">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="d983f-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="d983f-210">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="d983f-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="d983f-211">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="d983f-212">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="d983f-212">Switch mappings</span></span>

<span data-ttu-id="d983f-213">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d983f-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="d983f-214">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="d983f-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d983f-215">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d983f-216">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d983f-217">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="d983f-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d983f-218">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="d983f-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d983f-219">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="d983f-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="d983f-220">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="d983f-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d983f-221">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="d983f-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="d983f-222">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="d983f-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-223">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="d983f-223">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<!-- Run the following command to test the key replacement: -->

<span data-ttu-id="d983f-224">Note: Şu anda, `=` anahtar değiştirme değerlerini tek bir çizgiyle ayarlamak için kullanılamaz `-` .</span><span class="sxs-lookup"><span data-stu-id="d983f-224">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="d983f-225">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-225">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d983f-226">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="d983f-226">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d983f-227">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-227">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d983f-228">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d983f-228">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d983f-229">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="d983f-229">Hierarchical configuration data</span></span>

<span data-ttu-id="d983f-230">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="d983f-230">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d983f-231">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *appsettings.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="d983f-231">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="d983f-232">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-232">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-233">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d983f-233">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="d983f-234">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="d983f-234">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="d983f-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d983f-236">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d983f-236">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="d983f-237">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="d983f-237">Configuration keys and values</span></span>

<span data-ttu-id="d983f-238">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="d983f-238">Configuration keys:</span></span>

* <span data-ttu-id="d983f-239">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-239">Are case-insensitive.</span></span> <span data-ttu-id="d983f-240">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-240">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d983f-241">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-241">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="d983f-242">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="d983f-242">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="d983f-243">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d983f-243">Hierarchical keys</span></span>
  * <span data-ttu-id="d983f-244">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d983f-244">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d983f-245">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-245">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d983f-246">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="d983f-246">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="d983f-247">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-247">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="d983f-248">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="d983f-248">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d983f-249">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d983f-249">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d983f-250">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d983f-250">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="d983f-251">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="d983f-251">Configuration values:</span></span>

* <span data-ttu-id="d983f-252">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="d983f-252">Are strings.</span></span>
* <span data-ttu-id="d983f-253">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-253">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="d983f-254">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d983f-254">Configuration providers</span></span>

<span data-ttu-id="d983f-255">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d983f-255">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d983f-256">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d983f-256">Provider</span></span> | <span data-ttu-id="d983f-257">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="d983f-257">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="d983f-258">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-258">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="d983f-259">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d983f-259">Azure Key Vault</span></span> |
| [<span data-ttu-id="d983f-260">Azure uygulama yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-260">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="d983f-261">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-261">Azure App Configuration</span></span> |
| [<span data-ttu-id="d983f-262">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-262">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="d983f-263">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="d983f-263">Command-line parameters</span></span> |
| [<span data-ttu-id="d983f-264">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-264">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d983f-265">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="d983f-265">Custom source</span></span> |
| [<span data-ttu-id="d983f-266">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-266">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="d983f-267">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-267">Environment variables</span></span> |
| [<span data-ttu-id="d983f-268">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-268">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d983f-269">INı, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-269">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="d983f-270">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-270">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d983f-271">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-271">Directory files</span></span> |
| [<span data-ttu-id="d983f-272">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-272">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d983f-273">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="d983f-273">In-memory collections</span></span> |
| [<span data-ttu-id="d983f-274">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="d983f-274">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="d983f-275">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="d983f-275">File in the user profile directory</span></span> |

<span data-ttu-id="d983f-276">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="d983f-276">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="d983f-277">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-277">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d983f-278">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d983f-278">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d983f-279">*Üzerindeappsettings.js*</span><span class="sxs-lookup"><span data-stu-id="d983f-279">*appsettings.json*</span></span>
1. <span data-ttu-id="d983f-280">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="d983f-280">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="d983f-281">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="d983f-281">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="d983f-282">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-282">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d983f-283">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-283">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="d983f-284">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="d983f-284">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d983f-285">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-285">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="d983f-286">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="d983f-286">Connection string prefixes</span></span>

<span data-ttu-id="d983f-287">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="d983f-287">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="d983f-288">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-288">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d983f-289">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="d983f-289">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d983f-290">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="d983f-290">Connection string prefix</span></span> | <span data-ttu-id="d983f-291">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d983f-291">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d983f-292">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d983f-292">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d983f-293">MySQL</span><span class="sxs-lookup"><span data-stu-id="d983f-293">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d983f-294">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="d983f-294">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d983f-295">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d983f-295">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d983f-296">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="d983f-296">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d983f-297">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="d983f-297">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d983f-298">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="d983f-298">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d983f-299">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="d983f-299">Environment variable key</span></span> | <span data-ttu-id="d983f-300">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d983f-300">Converted configuration key</span></span> | <span data-ttu-id="d983f-301">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="d983f-301">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-302">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="d983f-302">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-303">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d983f-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d983f-304">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d983f-304">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-305">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d983f-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d983f-306">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d983f-306">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-307">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d983f-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d983f-308">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d983f-308">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="d983f-309">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-309">File configuration provider</span></span>

<span data-ttu-id="d983f-310"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="d983f-310"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d983f-311">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="d983f-311">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="d983f-312">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-312">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d983f-313">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-313">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="d983f-314">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-314">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d983f-315">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-315">INI configuration provider</span></span>

<span data-ttu-id="d983f-316">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-316">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d983f-317">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="d983f-317">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="d983f-318">Yukarıdaki kodda, *MyIniConfig.ini* ve *Myıniconfig*içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="d983f-318">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d983f-319">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-319">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d983f-320">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d983f-320">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d983f-321">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyIniConfig.ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="d983f-321">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="d983f-322">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="d983f-323">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-323">JSON configuration provider</span></span>

<span data-ttu-id="d983f-324">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-324">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="d983f-325">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-325">Overloads can specify:</span></span>

* <span data-ttu-id="d983f-326">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d983f-326">Whether the file is optional.</span></span>
* <span data-ttu-id="d983f-327">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d983f-327">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="d983f-328">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d983f-328">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="d983f-329">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d983f-329">The preceding code:</span></span>

* <span data-ttu-id="d983f-330">Dosyadaki *MyConfig.js* yüklemek için JSON yapılandırma sağlayıcısını aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d983f-330">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="d983f-331">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-331">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="d983f-332">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-332">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="d983f-333">Dosyadaki *MyConfig.js* önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="d983f-333">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="d983f-334">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig.js* dosya geçersiz kılma ayarı ayarları.</span><span class="sxs-lookup"><span data-stu-id="d983f-334">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d983f-335">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="d983f-335">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d983f-336">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="d983f-336">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="d983f-337">Yukarıdaki kodda, ve *MyConfig* *MyConfig.js* `Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="d983f-337">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="d983f-338">*appsettings.json* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d983f-338">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="d983f-339">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="d983f-339">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d983f-340">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *MyConfig.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="d983f-340">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="d983f-341">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-341">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

### <a name="xml-configuration-provider"></a><span data-ttu-id="d983f-342">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-342">XML configuration provider</span></span>

<span data-ttu-id="d983f-343">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-343">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d983f-344">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="d983f-344">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="d983f-345">Yukarıdaki kodda, *MyXMLFile.xml* ve *myXMLfile*içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="d983f-345">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d983f-346">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-346">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d983f-347">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="d983f-347">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d983f-348">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyXMLFile.xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="d983f-348">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="d983f-349">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-349">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-350">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="d983f-350">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="d983f-351">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-351">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-352">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-352">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d983f-353">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="d983f-353">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d983f-354">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="d983f-354">key:attribute</span></span>
* <span data-ttu-id="d983f-355">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="d983f-355">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d983f-356">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-356">Key-per-file configuration provider</span></span>

<span data-ttu-id="d983f-357">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-357">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d983f-358">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-358">The key is the file name.</span></span> <span data-ttu-id="d983f-359">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-359">The value contains the file's contents.</span></span> <span data-ttu-id="d983f-360">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-360">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d983f-361">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-361">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d983f-362">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d983f-362">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d983f-363">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d983f-363">Overloads permit specifying:</span></span>

* <span data-ttu-id="d983f-364">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="d983f-364">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d983f-365">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d983f-365">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d983f-366">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-366">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d983f-367">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="d983f-367">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d983f-368">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-368">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="d983f-369">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-369">Memory configuration provider</span></span>

<span data-ttu-id="d983f-370">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-370">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d983f-371">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="d983f-371">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="d983f-372">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-372">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-373">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-373">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d983f-374">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="d983f-374">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d983f-375">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="d983f-375">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="d983f-376">GetValue</span><span class="sxs-lookup"><span data-stu-id="d983f-376">GetValue</span></span>

<span data-ttu-id="d983f-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="d983f-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-378">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-378">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d983f-379">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="d983f-379">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d983f-380">İzleyen örnekler için, dosyasında aşağıdaki *MySubsection.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d983f-380">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="d983f-381">Aşağıdaki kod yapılandırma sağlayıcılarına *MySubsection.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="d983f-381">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="d983f-382">GetSection</span><span class="sxs-lookup"><span data-stu-id="d983f-382">GetSection</span></span>

<span data-ttu-id="d983f-383">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="d983f-383">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d983f-384">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="d983f-384">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-385">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="d983f-385">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-386">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="d983f-386">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d983f-387">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d983f-387">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d983f-388">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="d983f-388">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d983f-389">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d983f-389">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="d983f-390">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="d983f-390">GetChildren and Exists</span></span>

<span data-ttu-id="d983f-391">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="d983f-391">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-392">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="d983f-392">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="d983f-393">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="d983f-393">Bind an array</span></span>

<span data-ttu-id="d983f-394">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d983f-394">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d983f-395">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d983f-395">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="d983f-396">[Örnek karşıdan yükleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d983f-396">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="d983f-397">Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="d983f-397">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="d983f-398">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-398">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-399">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="d983f-399">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="d983f-400">Yukarıdaki çıktıda, Dizin 3 ' teMyArray.js' a karşılık gelen bir değer vardır `value40` `"4": "value40",` . *MyArray.json*</span><span class="sxs-lookup"><span data-stu-id="d983f-400">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="d983f-401">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-401">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="d983f-402">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="d983f-402">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="d983f-403">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="d983f-403">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="d983f-404">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-404">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-405">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="d983f-405">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="d983f-406">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="d983f-406">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d983f-407">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-407">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d983f-408">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-408">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d983f-409">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-409">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="d983f-410">Örnek indirmenin dosyasında aşağıdaki *Value3.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d983f-410">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="d983f-411">Aşağıdaki kod, ve *üzerindeValue3.js* için yapılandırma içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="d983f-411">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="d983f-412">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-412">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-413">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="d983f-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="d983f-414">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-414">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="d983f-415">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-415">Custom configuration provider</span></span>

<span data-ttu-id="d983f-416">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="d983f-416">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d983f-417">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d983f-417">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d983f-418">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-418">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d983f-419">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-419">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d983f-420">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="d983f-420">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d983f-421">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-421">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d983f-422">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-422">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d983f-423">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-423">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d983f-424">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-424">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d983f-425">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="d983f-425">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d983f-426">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-426">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d983f-427">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="d983f-427">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d983f-428">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-428">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d983f-429">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="d983f-429">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d983f-430">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="d983f-430">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="d983f-431">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d983f-431">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="d983f-432">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-432">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d983f-433">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-433">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d983f-434">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-434">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d983f-435">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d983f-435">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="d983f-436">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-436">Access configuration in Startup</span></span>

<span data-ttu-id="d983f-437">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d983f-437">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="d983f-438">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="d983f-438">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="d983f-439">Sayfalarda erişim yapılandırması Razor</span><span class="sxs-lookup"><span data-stu-id="d983f-439">Access configuration in Razor Pages</span></span>

<span data-ttu-id="d983f-440">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler Razor :</span><span class="sxs-lookup"><span data-stu-id="d983f-440">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="d983f-441">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-441">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d983f-442">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) Razor seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-442">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="d983f-443">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-443">Access configuration in a MVC view file</span></span>

<span data-ttu-id="d983f-444">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-444">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="d983f-445">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-445">Configure options with a delegate</span></span>

<span data-ttu-id="d983f-446">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-446">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="d983f-447">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d983f-447">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d983f-448">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-448">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d983f-449">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="d983f-449">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="d983f-450">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d983f-450">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="d983f-451">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *üzerindeappsettings.js* belirtilmiştir ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="d983f-451">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d983f-452">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="d983f-452">Host versus app configuration</span></span>

<span data-ttu-id="d983f-453">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-453">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d983f-454">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d983f-454">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d983f-455">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-455">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d983f-456">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-456">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d983f-457">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="d983f-457">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="d983f-458">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-458">Default host configuration</span></span>

<span data-ttu-id="d983f-459">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-459">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="d983f-460">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-460">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d983f-461">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-461">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="d983f-462">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-462">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d983f-463">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-463">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d983f-464">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="d983f-464">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="d983f-465">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-465">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="d983f-466">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d983f-466">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="d983f-467">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="d983f-467">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="d983f-468">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d983f-468">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d983f-469">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-469">Other configuration</span></span>

<span data-ttu-id="d983f-470">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="d983f-470">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d983f-471">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="d983f-471">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d983f-472">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="d983f-472">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d983f-473">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="d983f-473">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d983f-474">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="d983f-474">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d983f-475">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="d983f-475">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d983f-476">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-476">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="d983f-477">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="d983f-477">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d983f-478">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="d983f-478">Add configuration from an external assembly</span></span>

<span data-ttu-id="d983f-479">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d983f-479">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d983f-480">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d983f-480">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d983f-481">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d983f-481">Additional resources</span></span>

* [<span data-ttu-id="d983f-482">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="d983f-482">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d983f-483">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="d983f-483">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="d983f-484">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="d983f-484">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="d983f-485">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d983f-485">Azure Key Vault</span></span>
* <span data-ttu-id="d983f-486">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-486">Azure App Configuration</span></span>
* <span data-ttu-id="d983f-487">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-487">Command-line arguments</span></span>
* <span data-ttu-id="d983f-488">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="d983f-488">Custom providers (installed or created)</span></span>
* <span data-ttu-id="d983f-489">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-489">Directory files</span></span>
* <span data-ttu-id="d983f-490">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-490">Environment variables</span></span>
* <span data-ttu-id="d983f-491">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="d983f-491">In-memory .NET objects</span></span>
* <span data-ttu-id="d983f-492">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-492">Settings files</span></span>

<span data-ttu-id="d983f-493">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="d983f-493">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d983f-494">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-494">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="d983f-495">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-495">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="d983f-496">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-496">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="d983f-497">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="d983f-497">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d983f-498">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d983f-498">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d983f-499">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="d983f-499">Host versus app configuration</span></span>

<span data-ttu-id="d983f-500">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-500">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d983f-501">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d983f-501">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d983f-502">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-502">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d983f-503">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-503">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d983f-504">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="d983f-504">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d983f-505">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-505">Other configuration</span></span>

<span data-ttu-id="d983f-506">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="d983f-506">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d983f-507">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="d983f-507">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d983f-508">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="d983f-508">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d983f-509">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="d983f-509">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d983f-510">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="d983f-510">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d983f-511">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="d983f-511">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d983f-512">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="d983f-512">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="d983f-513">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-513">Default configuration</span></span>

<span data-ttu-id="d983f-514">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="d983f-514">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="d983f-515">`CreateDefaultBuilder`uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="d983f-515">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="d983f-516">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d983f-516">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d983f-517">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-517">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="d983f-518">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-518">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d983f-519">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-519">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d983f-520">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-520">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d983f-521">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-521">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d983f-522">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-522">App configuration is provided from:</span></span>
  * <span data-ttu-id="d983f-523">[Dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="d983f-523">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d983f-524">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="d983f-524">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d983f-525">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="d983f-525">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d983f-526">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-526">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="d983f-527">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-527">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="d983f-528">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="d983f-528">Security</span></span>

<span data-ttu-id="d983f-529">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d983f-529">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="d983f-530">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="d983f-530">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="d983f-531">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-531">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d983f-532">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="d983f-532">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d983f-533">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="d983f-533">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d983f-534"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-534"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d983f-535">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-535">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="d983f-536">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d983f-536">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="d983f-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="d983f-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d983f-538">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d983f-538">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d983f-539">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="d983f-539">Hierarchical configuration data</span></span>

<span data-ttu-id="d983f-540">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="d983f-540">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d983f-541">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="d983f-541">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="d983f-542">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d983f-542">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="d983f-543">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="d983f-543">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="d983f-544">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-544">section0:key0</span></span>
* <span data-ttu-id="d983f-545">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-545">section0:key1</span></span>
* <span data-ttu-id="d983f-546">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-546">section1:key0</span></span>
* <span data-ttu-id="d983f-547">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-547">section1:key1</span></span>

<span data-ttu-id="d983f-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d983f-549">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d983f-549">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="d983f-550">Kurallar</span><span class="sxs-lookup"><span data-stu-id="d983f-550">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="d983f-551">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="d983f-551">Sources and providers</span></span>

<span data-ttu-id="d983f-552">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="d983f-552">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="d983f-553">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="d983f-553">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="d983f-554">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="d983f-554">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="d983f-555"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d983f-556"><xref:Microsoft.Extensions.Configuration.IConfiguration>Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="d983f-557">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d983f-557">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="d983f-558">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-558">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="d983f-559">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d983f-559">Keys</span></span>

<span data-ttu-id="d983f-560">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d983f-560">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="d983f-561">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-561">Keys are case-insensitive.</span></span> <span data-ttu-id="d983f-562">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-562">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d983f-563">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="d983f-563">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="d983f-564">Yinelenen JSON anahtarları hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/2381)bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-564">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="d983f-565">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d983f-565">Hierarchical keys</span></span>
  * <span data-ttu-id="d983f-566">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d983f-566">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d983f-567">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-567">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d983f-568">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="d983f-568">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="d983f-569">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-569">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d983f-570">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="d983f-570">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d983f-571">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d983f-571">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d983f-572">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d983f-572">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="d983f-573">Değerler</span><span class="sxs-lookup"><span data-stu-id="d983f-573">Values</span></span>

<span data-ttu-id="d983f-574">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="d983f-574">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="d983f-575">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="d983f-575">Values are strings.</span></span>
* <span data-ttu-id="d983f-576">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-576">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="d983f-577">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="d983f-577">Providers</span></span>

<span data-ttu-id="d983f-578">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d983f-578">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d983f-579">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d983f-579">Provider</span></span> | <span data-ttu-id="d983f-580">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="d983f-580">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="d983f-581">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="d983f-581">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="d983f-582">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d983f-582">Azure Key Vault</span></span> |
| <span data-ttu-id="d983f-583">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="d983f-583">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="d983f-584">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-584">Azure App Configuration</span></span> |
| [<span data-ttu-id="d983f-585">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-585">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="d983f-586">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="d983f-586">Command-line parameters</span></span> |
| [<span data-ttu-id="d983f-587">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-587">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d983f-588">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="d983f-588">Custom source</span></span> |
| [<span data-ttu-id="d983f-589">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-589">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="d983f-590">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-590">Environment variables</span></span> |
| [<span data-ttu-id="d983f-591">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-591">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d983f-592">Dosyalar (ıNı, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="d983f-592">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="d983f-593">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-593">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d983f-594">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="d983f-594">Directory files</span></span> |
| [<span data-ttu-id="d983f-595">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-595">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d983f-596">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="d983f-596">In-memory collections</span></span> |
| <span data-ttu-id="d983f-597">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="d983f-597">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="d983f-598">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="d983f-598">File in the user profile directory</span></span> |

<span data-ttu-id="d983f-599">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="d983f-599">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="d983f-600">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d983f-600">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="d983f-601">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-601">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d983f-602">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d983f-602">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d983f-603">Dosyalar (*appsettings.json*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="d983f-603">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="d983f-604">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d983f-604">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="d983f-605">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="d983f-605">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="d983f-606">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-606">Environment variables</span></span>
1. <span data-ttu-id="d983f-607">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="d983f-607">Command-line arguments</span></span>

<span data-ttu-id="d983f-608">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="d983f-608">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d983f-609">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-609">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d983f-610">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-610">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="d983f-611">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d983f-611">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="d983f-612">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="d983f-612">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="d983f-613">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d983f-613">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d983f-614">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d983f-614">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="d983f-615">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="d983f-615">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="d983f-616">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="d983f-616">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="d983f-617">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="d983f-617">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="d983f-618">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-618">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="d983f-619">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="d983f-619">Consume configuration during app startup</span></span>

<span data-ttu-id="d983f-620">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d983f-620">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d983f-621">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-621">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="d983f-622">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-622">Command-line Configuration Provider</span></span>

<span data-ttu-id="d983f-623">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-623">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="d983f-624">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-624">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d983f-625">`AddCommandLine`çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="d983f-625">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="d983f-626">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-626">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d983f-627">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d983f-627">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d983f-628">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d983f-628">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d983f-629">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d983f-629">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d983f-630">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-630">Environment variables.</span></span>

<span data-ttu-id="d983f-631">`CreateDefaultBuilder`Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="d983f-631">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="d983f-632">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="d983f-632">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="d983f-633">`CreateDefaultBuilder`Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="d983f-633">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="d983f-634">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-634">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="d983f-635">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-635">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d983f-636">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="d983f-636">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="d983f-637">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="d983f-637">**Example**</span></span>

<span data-ttu-id="d983f-638">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="d983f-638">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="d983f-639">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="d983f-639">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="d983f-640">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="d983f-640">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="d983f-641">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d983f-641">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d983f-642">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d983f-642">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="d983f-643">Arguments</span><span class="sxs-lookup"><span data-stu-id="d983f-643">Arguments</span></span>

<span data-ttu-id="d983f-644">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-644">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="d983f-645">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="d983f-645">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="d983f-646">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="d983f-646">Key prefix</span></span>               | <span data-ttu-id="d983f-647">Örnek</span><span class="sxs-lookup"><span data-stu-id="d983f-647">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="d983f-648">Ön ek yok</span><span class="sxs-lookup"><span data-stu-id="d983f-648">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="d983f-649">İki kısa çizgi ( `--` )</span><span class="sxs-lookup"><span data-stu-id="d983f-649">Two dashes (`--`)</span></span>        | <span data-ttu-id="d983f-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="d983f-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="d983f-651">Eğik çizgi ( `/` )</span><span class="sxs-lookup"><span data-stu-id="d983f-651">Forward slash (`/`)</span></span>      | <span data-ttu-id="d983f-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="d983f-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="d983f-653">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-653">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="d983f-654">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="d983f-654">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="d983f-655">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="d983f-655">Switch mappings</span></span>

<span data-ttu-id="d983f-656">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d983f-656">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="d983f-657">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="d983f-657">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d983f-658">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-658">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d983f-659">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-659">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d983f-660">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="d983f-660">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d983f-661">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="d983f-661">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d983f-662">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-662">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="d983f-663">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="d983f-663">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d983f-664">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d983f-664">Create a switch mappings dictionary.</span></span> <span data-ttu-id="d983f-665">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d983f-665">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="d983f-666">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-666">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="d983f-667">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="d983f-667">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d983f-668">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-668">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d983f-669">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d983f-669">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="d983f-670">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-670">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="d983f-671">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-671">Key</span></span>       | <span data-ttu-id="d983f-672">Değer</span><span class="sxs-lookup"><span data-stu-id="d983f-672">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="d983f-673">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="d983f-673">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="d983f-674">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-674">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="d983f-675">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-675">Key</span></span>               | <span data-ttu-id="d983f-676">Değer</span><span class="sxs-lookup"><span data-stu-id="d983f-676">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="d983f-677">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-677">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="d983f-678">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-678">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="d983f-679">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-679">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d983f-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="d983f-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="d983f-681">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="d983f-681">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d983f-682">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-682">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="d983f-683">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-683">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d983f-684">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d983f-684">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d983f-685">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="d983f-685">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="d983f-686">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="d983f-686">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d983f-687">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d983f-687">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d983f-688">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-688">Command-line arguments.</span></span>

<span data-ttu-id="d983f-689">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-689">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="d983f-690">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d983f-690">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="d983f-691">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-691">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="d983f-692">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="d983f-692">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="d983f-693">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="d983f-693">**Example**</span></span>

<span data-ttu-id="d983f-694">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="d983f-694">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="d983f-695">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d983f-695">Run the sample app.</span></span> <span data-ttu-id="d983f-696">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d983f-696">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d983f-697">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="d983f-697">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="d983f-698">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="d983f-698">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="d983f-699">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="d983f-699">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="d983f-700">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-700">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="d983f-701">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d983f-701">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="d983f-702">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="d983f-702">Prefixes</span></span>

<span data-ttu-id="d983f-703">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="d983f-703">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="d983f-704">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="d983f-704">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="d983f-705">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-705">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="d983f-706">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-706">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="d983f-707">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-707">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d983f-708">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="d983f-708">**Connection string prefixes**</span></span>

<span data-ttu-id="d983f-709">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-709">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d983f-710">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="d983f-710">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d983f-711">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="d983f-711">Connection string prefix</span></span> | <span data-ttu-id="d983f-712">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d983f-712">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d983f-713">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="d983f-713">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d983f-714">MySQL</span><span class="sxs-lookup"><span data-stu-id="d983f-714">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d983f-715">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="d983f-715">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d983f-716">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d983f-716">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d983f-717">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="d983f-717">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d983f-718">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="d983f-718">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d983f-719">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="d983f-719">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d983f-720">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="d983f-720">Environment variable key</span></span> | <span data-ttu-id="d983f-721">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d983f-721">Converted configuration key</span></span> | <span data-ttu-id="d983f-722">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="d983f-722">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-723">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="d983f-723">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-724">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d983f-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d983f-725">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d983f-725">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-726">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d983f-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d983f-727">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d983f-727">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d983f-728">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="d983f-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d983f-729">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d983f-729">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="d983f-730">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="d983f-730">**Example**</span></span>

<span data-ttu-id="d983f-731">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d983f-731">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="d983f-732">Ad: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="d983f-732">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="d983f-733">Değer: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="d983f-733">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="d983f-734">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="d983f-734">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="d983f-735">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-735">File Configuration Provider</span></span>

<span data-ttu-id="d983f-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="d983f-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d983f-737">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="d983f-737">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="d983f-738">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-738">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d983f-739">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-739">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="d983f-740">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-740">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d983f-741">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-741">INI Configuration Provider</span></span>

<span data-ttu-id="d983f-742">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-742">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d983f-743">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-743">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d983f-744">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-744">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="d983f-745">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d983f-745">Overloads permit specifying:</span></span>

* <span data-ttu-id="d983f-746">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d983f-746">Whether the file is optional.</span></span>
* <span data-ttu-id="d983f-747">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d983f-747">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d983f-748"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-748">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d983f-749">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-749">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d983f-750">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="d983f-750">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="d983f-751">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="d983f-751">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d983f-752">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-752">section0:key0</span></span>
* <span data-ttu-id="d983f-753">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-753">section0:key1</span></span>
* <span data-ttu-id="d983f-754">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-754">section1:subsection:key</span></span>
* <span data-ttu-id="d983f-755">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-755">section2:subsection0:key</span></span>
* <span data-ttu-id="d983f-756">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-756">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="d983f-757">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-757">JSON Configuration Provider</span></span>

<span data-ttu-id="d983f-758">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-758">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="d983f-759">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-759">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d983f-760">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d983f-760">Overloads permit specifying:</span></span>

* <span data-ttu-id="d983f-761">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d983f-761">Whether the file is optional.</span></span>
* <span data-ttu-id="d983f-762">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d983f-762">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d983f-763"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-763">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d983f-764">`AddJsonFile`, ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-764">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d983f-765">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="d983f-765">The method is called to load configuration from:</span></span>

* <span data-ttu-id="d983f-766">*appsettings.js*: Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="d983f-766">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="d983f-767">Dosyanın ortam sürümü, *appsettings.js* dosya tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-767">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="d983f-768">*appSettings. {Environment}. JSON*: dosyanın ortam sürümü [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d983f-768">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="d983f-769">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d983f-769">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d983f-770">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d983f-770">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d983f-771">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-771">Environment variables.</span></span>
* <span data-ttu-id="d983f-772">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="d983f-772">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d983f-773">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="d983f-773">Command-line arguments.</span></span>

<span data-ttu-id="d983f-774">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d983f-774">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="d983f-775">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d983f-775">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="d983f-776">`ConfigureAppConfiguration`Ana bilgisayarı oluştururken, *appsettings.json* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d983f-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d983f-777">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="d983f-777">**Example**</span></span>

<span data-ttu-id="d983f-778">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="d983f-778">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="d983f-779">`AddJsonFile` *' Dekiappsettings.js*yapılandırmayı yükleyen ilk çağrı:</span><span class="sxs-lookup"><span data-stu-id="d983f-779">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="d983f-780">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d983f-780">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="d983f-781">Örnek uygulamada *appsettings.Development.js* için aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d983f-781">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="d983f-782">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d983f-782">Run the sample app.</span></span> <span data-ttu-id="d983f-783">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="d983f-783">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d983f-784">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-784">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="d983f-785">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="d983f-785">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="d983f-786">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-786">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="d983f-787">Dosyadaki *Özellikler/launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="d983f-787">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="d983f-788">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="d983f-788">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="d983f-789">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d983f-789">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="d983f-790">*appsettings.Development.jsüzerindeki* ayarlar artık *appsettings.jsüzerindeki*ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-790">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="d983f-791">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="d983f-791">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="d983f-792">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-792">XML Configuration Provider</span></span>

<span data-ttu-id="d983f-793">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="d983f-793">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d983f-794">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-794">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d983f-795">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d983f-795">Overloads permit specifying:</span></span>

* <span data-ttu-id="d983f-796">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="d983f-796">Whether the file is optional.</span></span>
* <span data-ttu-id="d983f-797">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d983f-797">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d983f-798"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-798">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d983f-799">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-799">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="d983f-800">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="d983f-800">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="d983f-801">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d983f-802">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-802">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="d983f-803">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="d983f-803">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d983f-804">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-804">section0:key0</span></span>
* <span data-ttu-id="d983f-805">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-805">section0:key1</span></span>
* <span data-ttu-id="d983f-806">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-806">section1:key0</span></span>
* <span data-ttu-id="d983f-807">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-807">section1:key1</span></span>

<span data-ttu-id="d983f-808">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="d983f-808">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="d983f-809">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="d983f-809">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d983f-810">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="d983f-810">section:section0:key:key0</span></span>
* <span data-ttu-id="d983f-811">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-811">section:section0:key:key1</span></span>
* <span data-ttu-id="d983f-812">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="d983f-812">section:section1:key:key0</span></span>
* <span data-ttu-id="d983f-813">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-813">section:section1:key:key1</span></span>

<span data-ttu-id="d983f-814">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-814">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d983f-815">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="d983f-815">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d983f-816">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="d983f-816">key:attribute</span></span>
* <span data-ttu-id="d983f-817">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="d983f-817">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d983f-818">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-818">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="d983f-819">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-819">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d983f-820">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d983f-820">The key is the file name.</span></span> <span data-ttu-id="d983f-821">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-821">The value contains the file's contents.</span></span> <span data-ttu-id="d983f-822">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-822">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d983f-823">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-823">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d983f-824">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d983f-824">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d983f-825">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="d983f-825">Overloads permit specifying:</span></span>

* <span data-ttu-id="d983f-826">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="d983f-826">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d983f-827">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d983f-827">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d983f-828">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-828">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d983f-829">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="d983f-829">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d983f-830">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="d983f-830">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="d983f-831">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-831">Memory Configuration Provider</span></span>

<span data-ttu-id="d983f-832">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-832">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d983f-833">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d983f-833">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d983f-834">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="d983f-834">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="d983f-835">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="d983f-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="d983f-836">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d983f-836">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="d983f-837">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="d983f-837">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="d983f-838">GetValue</span><span class="sxs-lookup"><span data-stu-id="d983f-838">GetValue</span></span>

<span data-ttu-id="d983f-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="d983f-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="d983f-840">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d983f-840">An overload accepts a default value.</span></span>

<span data-ttu-id="d983f-841">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="d983f-841">The following example:</span></span>

* <span data-ttu-id="d983f-842">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="d983f-842">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="d983f-843">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-843">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="d983f-844">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="d983f-844">Types the value as an `int`.</span></span>
* <span data-ttu-id="d983f-845">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="d983f-845">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d983f-846">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="d983f-846">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d983f-847">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="d983f-847">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="d983f-848">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="d983f-848">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="d983f-849">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="d983f-849">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="d983f-850">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-850">section0:key0</span></span>
* <span data-ttu-id="d983f-851">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-851">section0:key1</span></span>
* <span data-ttu-id="d983f-852">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-852">section1:key0</span></span>
* <span data-ttu-id="d983f-853">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-853">section1:key1</span></span>
* <span data-ttu-id="d983f-854">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-854">section2:subsection0:key0</span></span>
* <span data-ttu-id="d983f-855">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-855">section2:subsection0:key1</span></span>
* <span data-ttu-id="d983f-856">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="d983f-856">section2:subsection1:key0</span></span>
* <span data-ttu-id="d983f-857">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="d983f-857">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="d983f-858">GetSection</span><span class="sxs-lookup"><span data-stu-id="d983f-858">GetSection</span></span>

<span data-ttu-id="d983f-859">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="d983f-859">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d983f-860"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="d983f-860">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="d983f-861">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="d983f-861">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="d983f-862">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="d983f-862">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="d983f-863">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="d983f-863">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d983f-864">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d983f-864">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d983f-865">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="d983f-865">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d983f-866">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d983f-866">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="d983f-867">GetChildren</span><span class="sxs-lookup"><span data-stu-id="d983f-867">GetChildren</span></span>

<span data-ttu-id="d983f-868">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d983f-868">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="d983f-869">Var</span><span class="sxs-lookup"><span data-stu-id="d983f-869">Exists</span></span>

<span data-ttu-id="d983f-870">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="d983f-870">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="d983f-871">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="d983f-871">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="d983f-872">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="d983f-872">Bind to an object graph</span></span>

<span data-ttu-id="d983f-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>, bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d983f-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="d983f-874">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-874">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="d983f-875">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="d983f-875">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="d983f-876">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow.xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d983f-876">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="d983f-877">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="d983f-877">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="d983f-878">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="d983f-878">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="d983f-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="d983f-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="d983f-880">`Get<T>`, kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="d983f-880">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="d983f-881">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="d983f-881">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d983f-882">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="d983f-882">Bind an array to a class</span></span>

<span data-ttu-id="d983f-883">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="d983f-883">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="d983f-884">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="d983f-884">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d983f-885">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d983f-885">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="d983f-886">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d983f-886">Binding is provided by convention.</span></span> <span data-ttu-id="d983f-887">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-887">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="d983f-888">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="d983f-888">**In-memory array processing**</span></span>

<span data-ttu-id="d983f-889">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="d983f-889">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="d983f-890">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-890">Key</span></span>             | <span data-ttu-id="d983f-891">Değer</span><span class="sxs-lookup"><span data-stu-id="d983f-891">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d983f-892">dizi: girdiler: 0</span><span class="sxs-lookup"><span data-stu-id="d983f-892">array:entries:0</span></span> | <span data-ttu-id="d983f-893">value0</span><span class="sxs-lookup"><span data-stu-id="d983f-893">value0</span></span> |
| <span data-ttu-id="d983f-894">dizi: girdiler: 1</span><span class="sxs-lookup"><span data-stu-id="d983f-894">array:entries:1</span></span> | <span data-ttu-id="d983f-895">value1</span><span class="sxs-lookup"><span data-stu-id="d983f-895">value1</span></span> |
| <span data-ttu-id="d983f-896">dizi: girdiler: 2</span><span class="sxs-lookup"><span data-stu-id="d983f-896">array:entries:2</span></span> | <span data-ttu-id="d983f-897">value2</span><span class="sxs-lookup"><span data-stu-id="d983f-897">value2</span></span> |
| <span data-ttu-id="d983f-898">dizi: girdiler: 4</span><span class="sxs-lookup"><span data-stu-id="d983f-898">array:entries:4</span></span> | <span data-ttu-id="d983f-899">value4</span><span class="sxs-lookup"><span data-stu-id="d983f-899">value4</span></span> |
| <span data-ttu-id="d983f-900">dizi: girdiler: 5</span><span class="sxs-lookup"><span data-stu-id="d983f-900">array:entries:5</span></span> | <span data-ttu-id="d983f-901">value5</span><span class="sxs-lookup"><span data-stu-id="d983f-901">value5</span></span> |

<span data-ttu-id="d983f-902">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="d983f-902">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="d983f-903">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="d983f-903">The array skips a value for index &num;3.</span></span> <span data-ttu-id="d983f-904">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="d983f-904">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="d983f-905">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-905">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="d983f-906">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="d983f-906">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="d983f-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="d983f-908">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="d983f-908">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="d983f-909">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="d983f-909">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d983f-910">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="d983f-910">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d983f-911">0</span><span class="sxs-lookup"><span data-stu-id="d983f-911">0</span></span>                            | <span data-ttu-id="d983f-912">value0</span><span class="sxs-lookup"><span data-stu-id="d983f-912">value0</span></span>                       |
| <span data-ttu-id="d983f-913">1</span><span class="sxs-lookup"><span data-stu-id="d983f-913">1</span></span>                            | <span data-ttu-id="d983f-914">value1</span><span class="sxs-lookup"><span data-stu-id="d983f-914">value1</span></span>                       |
| <span data-ttu-id="d983f-915">2</span><span class="sxs-lookup"><span data-stu-id="d983f-915">2</span></span>                            | <span data-ttu-id="d983f-916">value2</span><span class="sxs-lookup"><span data-stu-id="d983f-916">value2</span></span>                       |
| <span data-ttu-id="d983f-917">3</span><span class="sxs-lookup"><span data-stu-id="d983f-917">3</span></span>                            | <span data-ttu-id="d983f-918">value4</span><span class="sxs-lookup"><span data-stu-id="d983f-918">value4</span></span>                       |
| <span data-ttu-id="d983f-919">4</span><span class="sxs-lookup"><span data-stu-id="d983f-919">4</span></span>                            | <span data-ttu-id="d983f-920">value5</span><span class="sxs-lookup"><span data-stu-id="d983f-920">value5</span></span>                       |

<span data-ttu-id="d983f-921">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="d983f-921">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d983f-922">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-922">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d983f-923">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-923">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d983f-924">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d983f-924">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="d983f-925">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="d983f-925">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="d983f-926">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="d983f-926">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="d983f-927">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="d983f-927">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="d983f-928">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="d983f-928">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="d983f-929">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-929">Key</span></span>             | <span data-ttu-id="d983f-930">Değer</span><span class="sxs-lookup"><span data-stu-id="d983f-930">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d983f-931">dizi: girdiler: 3</span><span class="sxs-lookup"><span data-stu-id="d983f-931">array:entries:3</span></span> | <span data-ttu-id="d983f-932">value3</span><span class="sxs-lookup"><span data-stu-id="d983f-932">value3</span></span> |

<span data-ttu-id="d983f-933">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d983f-933">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="d983f-934">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="d983f-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d983f-935">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="d983f-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d983f-936">0</span><span class="sxs-lookup"><span data-stu-id="d983f-936">0</span></span>                            | <span data-ttu-id="d983f-937">value0</span><span class="sxs-lookup"><span data-stu-id="d983f-937">value0</span></span>                       |
| <span data-ttu-id="d983f-938">1</span><span class="sxs-lookup"><span data-stu-id="d983f-938">1</span></span>                            | <span data-ttu-id="d983f-939">value1</span><span class="sxs-lookup"><span data-stu-id="d983f-939">value1</span></span>                       |
| <span data-ttu-id="d983f-940">2</span><span class="sxs-lookup"><span data-stu-id="d983f-940">2</span></span>                            | <span data-ttu-id="d983f-941">value2</span><span class="sxs-lookup"><span data-stu-id="d983f-941">value2</span></span>                       |
| <span data-ttu-id="d983f-942">3</span><span class="sxs-lookup"><span data-stu-id="d983f-942">3</span></span>                            | <span data-ttu-id="d983f-943">value3</span><span class="sxs-lookup"><span data-stu-id="d983f-943">value3</span></span>                       |
| <span data-ttu-id="d983f-944">4</span><span class="sxs-lookup"><span data-stu-id="d983f-944">4</span></span>                            | <span data-ttu-id="d983f-945">value4</span><span class="sxs-lookup"><span data-stu-id="d983f-945">value4</span></span>                       |
| <span data-ttu-id="d983f-946">5</span><span class="sxs-lookup"><span data-stu-id="d983f-946">5</span></span>                            | <span data-ttu-id="d983f-947">value5</span><span class="sxs-lookup"><span data-stu-id="d983f-947">value5</span></span>                       |

<span data-ttu-id="d983f-948">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="d983f-948">**JSON array processing**</span></span>

<span data-ttu-id="d983f-949">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d983f-949">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="d983f-950">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="d983f-950">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="d983f-951">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="d983f-951">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="d983f-952">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-952">Key</span></span>                     | <span data-ttu-id="d983f-953">Değer</span><span class="sxs-lookup"><span data-stu-id="d983f-953">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="d983f-954">json_array: anahtar</span><span class="sxs-lookup"><span data-stu-id="d983f-954">json_array:key</span></span>          | <span data-ttu-id="d983f-955">değer EA</span><span class="sxs-lookup"><span data-stu-id="d983f-955">valueA</span></span> |
| <span data-ttu-id="d983f-956">json_array: alt bölüm: 0</span><span class="sxs-lookup"><span data-stu-id="d983f-956">json_array:subsection:0</span></span> | <span data-ttu-id="d983f-957">valueB</span><span class="sxs-lookup"><span data-stu-id="d983f-957">valueB</span></span> |
| <span data-ttu-id="d983f-958">json_array: alt bölüm: 1</span><span class="sxs-lookup"><span data-stu-id="d983f-958">json_array:subsection:1</span></span> | <span data-ttu-id="d983f-959">değer EC</span><span class="sxs-lookup"><span data-stu-id="d983f-959">valueC</span></span> |
| <span data-ttu-id="d983f-960">json_array: alt bölüm: 2</span><span class="sxs-lookup"><span data-stu-id="d983f-960">json_array:subsection:2</span></span> | <span data-ttu-id="d983f-961">Değerler</span><span class="sxs-lookup"><span data-stu-id="d983f-961">valueD</span></span> |

<span data-ttu-id="d983f-962">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d983f-962">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="d983f-963">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="d983f-963">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="d983f-964">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="d983f-964">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="d983f-965">`JsonArrayExample.Subsection`İndeks</span><span class="sxs-lookup"><span data-stu-id="d983f-965">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="d983f-966">`JsonArrayExample.Subsection`Deeri</span><span class="sxs-lookup"><span data-stu-id="d983f-966">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="d983f-967">0</span><span class="sxs-lookup"><span data-stu-id="d983f-967">0</span></span>                                   | <span data-ttu-id="d983f-968">valueB</span><span class="sxs-lookup"><span data-stu-id="d983f-968">valueB</span></span>                              |
| <span data-ttu-id="d983f-969">1</span><span class="sxs-lookup"><span data-stu-id="d983f-969">1</span></span>                                   | <span data-ttu-id="d983f-970">değer EC</span><span class="sxs-lookup"><span data-stu-id="d983f-970">valueC</span></span>                              |
| <span data-ttu-id="d983f-971">2</span><span class="sxs-lookup"><span data-stu-id="d983f-971">2</span></span>                                   | <span data-ttu-id="d983f-972">Değerler</span><span class="sxs-lookup"><span data-stu-id="d983f-972">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="d983f-973">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="d983f-973">Custom configuration provider</span></span>

<span data-ttu-id="d983f-974">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="d983f-974">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d983f-975">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d983f-975">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d983f-976">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d983f-976">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d983f-977">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-977">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d983f-978">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="d983f-978">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d983f-979">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="d983f-979">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d983f-980">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="d983f-980">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d983f-981">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="d983f-981">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d983f-982">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-982">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d983f-983">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="d983f-983">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d983f-984">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-984">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d983f-985">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="d983f-985">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d983f-986">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-986">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d983f-987">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="d983f-987">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d983f-988">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="d983f-988">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="d983f-989">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-989">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d983f-990">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d983f-990">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d983f-991">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="d983f-991">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d983f-992">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d983f-992">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="d983f-993">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="d983f-993">Access configuration during startup</span></span>

<span data-ttu-id="d983f-994">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d983f-994">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d983f-995">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="d983f-995">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="d983f-996">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="d983f-996">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="d983f-997">RazorSayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d983f-997">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="d983f-998">Bir Razor Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft.Extensions.Configurlama ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d983f-998">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="d983f-999">RazorSayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="d983f-999">In a Razor Pages page:</span></span>

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

<span data-ttu-id="d983f-1000">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="d983f-1000">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d983f-1001">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="d983f-1001">Add configuration from an external assembly</span></span>

<span data-ttu-id="d983f-1002">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d983f-1002">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d983f-1003">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d983f-1003">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d983f-1004">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d983f-1004">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
