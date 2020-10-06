---
title: ASP.NET Core yapılandırma
author: rick-anderson
description: ASP.NET Core uygulamasını yapılandırmak için yapılandırma API 'sini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: 7565ede55acd936072fc1930918d46808548f287
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762353"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="72d77-103">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="72d77-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="72d77-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="72d77-105">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="72d77-106">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="72d77-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="72d77-107">*appsettings.js* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="72d77-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-108">Environment variables</span></span>
* <span data-ttu-id="72d77-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d77-109">Azure Key Vault</span></span>
* <span data-ttu-id="72d77-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-110">Azure App Configuration</span></span>
* <span data-ttu-id="72d77-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-111">Command-line arguments</span></span>
* <span data-ttu-id="72d77-112">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="72d77-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="72d77-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-113">Directory files</span></span>
* <span data-ttu-id="72d77-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="72d77-114">In-memory .NET objects</span></span>

<span data-ttu-id="72d77-115">Bu konuda ASP.NET Core yapılandırma hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="72d77-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="72d77-116">Konsol uygulamalarında yapılandırmayı kullanma hakkında daha fazla bilgi için bkz. [.NET yapılandırması](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="72d77-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="72d77-117">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72d77-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="72d77-118">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-118">Default configuration</span></span>

<span data-ttu-id="72d77-119">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="72d77-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="72d77-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="72d77-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="72d77-121">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="72d77-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="72d77-122">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="72d77-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="72d77-123">[JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak [appsettings.js](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="72d77-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="72d77-124">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="72d77-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="72d77-125">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d77-125">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="72d77-126">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="72d77-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="72d77-127">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="72d77-128">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="72d77-129">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="72d77-130">Örneğin, `MyKey` ve ortamında hem *appsettings.js* hem de ortamda ayarlandıysa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="72d77-131">Varsayılan yapılandırma sağlayıcılarını kullanarak,  [komut satırı yapılandırma sağlayıcısı](#clcp) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="72d77-132">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="72d77-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="72d77-133">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="72d77-134">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="72d77-134">appsettings.json</span></span>

<span data-ttu-id="72d77-135">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="72d77-135">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="72d77-136">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-136">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-137">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="72d77-137">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="72d77-138">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="72d77-138">*appsettings.json*</span></span>
1. <span data-ttu-id="72d77-139">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="72d77-139">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="72d77-140">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-140">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="72d77-141">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="72d77-141">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="72d77-142">*appSettings*. `Environment` . *JSON* değerleri *appsettings.jsüzerindeki*anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-142">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="72d77-143">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="72d77-143">For example, by default:</span></span>

* <span data-ttu-id="72d77-144">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması, \* üzerindeappsettings.js\*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="72d77-144">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="72d77-145">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması, \* üzerindeappsettings.js\*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="72d77-145">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="72d77-146">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="72d77-146">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="72d77-147">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="72d77-147">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="72d77-148">[Varsayılan](#default) yapılandırmayı kullanarak *appsettings.js* ve appSettings ' i kullanın *.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-148">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="72d77-149">Ve appSettings *üzerindeappsettings.js* yapılan değişiklikler *.* `Environment` uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="72d77-149">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="72d77-150">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-150">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="72d77-151">Hizmet toplamayı birleştirme</span><span class="sxs-lookup"><span data-stu-id="72d77-151">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="72d77-152">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="72d77-152">Security and secret manager</span></span>

<span data-ttu-id="72d77-153">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="72d77-153">Configuration data guidelines:</span></span>

* <span data-ttu-id="72d77-154">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="72d77-154">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="72d77-155">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-155">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="72d77-156">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-156">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="72d77-157">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="72d77-157">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="72d77-158">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appsettings.json* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d77-158">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="72d77-159">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="72d77-159">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="72d77-160"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-160"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="72d77-161">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-161">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="72d77-162">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="72d77-162">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="72d77-163">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d77-163">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="72d77-164">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-164">Environment variables</span></span>

<span data-ttu-id="72d77-165">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> yapılandırma *appsettings.json*, appSettings sonrasında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="72d77-165">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="72d77-166">Bu nedenle, ortamdan okunan anahtar değerleri, *appsettings.json*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="72d77-166">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="72d77-167">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="72d77-167">The following `set` commands:</span></span>

* <span data-ttu-id="72d77-168">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-168">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="72d77-169">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="72d77-169">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="72d77-170">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="72d77-170">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="72d77-171">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="72d77-171">The preceding environment settings:</span></span>

* <span data-ttu-id="72d77-172">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-172">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="72d77-173">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="72d77-173">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="72d77-174">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-174">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="72d77-175">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-175">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="72d77-176">`/M` değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="72d77-176">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="72d77-177">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-177">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="72d77-178">Yukarıdaki komutların ve appSettings *appsettings.js* geçersiz kılmasını test etmek *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72d77-178">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="72d77-179">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="72d77-179">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="72d77-180">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="72d77-180">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="72d77-181"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-181">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="72d77-182">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="72d77-182">In the preceding code:</span></span>

* <span data-ttu-id="72d77-183">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-183">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="72d77-184">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="72d77-184">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="72d77-185">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-185">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="72d77-186">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-186">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="72d77-187">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-187">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="72d77-188">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="72d77-188">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="72d77-189">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="72d77-189">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="72d77-190">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-190">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="72d77-191">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="72d77-191">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="72d77-192">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="72d77-192">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="72d77-193">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="72d77-193">Azure App Service application settings are:</span></span>

* <span data-ttu-id="72d77-194">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-194">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="72d77-195">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="72d77-195">Exposed as environment variables.</span></span>

<span data-ttu-id="72d77-196">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="72d77-196">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="72d77-197">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="72d77-197">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="72d77-198">launchSettings.jsüzerinde ayarlanan ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-198">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="72d77-199">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-199">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="72d77-200">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="72d77-200">Command-line</span></span>

<span data-ttu-id="72d77-201">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="72d77-201">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="72d77-202">Ve *appsettings* *appsettings.js* . `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="72d77-202">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="72d77-203">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="72d77-203">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d77-204">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-204">Environment variables.</span></span>

<span data-ttu-id="72d77-205">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-205">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="72d77-206">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-206">Command-line arguments</span></span>

<span data-ttu-id="72d77-207">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="72d77-207">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="72d77-208">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="72d77-208">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="72d77-209">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="72d77-209">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="72d77-210">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="72d77-210">The key value:</span></span>

* <span data-ttu-id="72d77-211">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-211">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="72d77-212">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="72d77-212">Isn't required if `=` is used.</span></span> <span data-ttu-id="72d77-213">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="72d77-213">For example, `MySetting=`.</span></span>

<span data-ttu-id="72d77-214">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-214">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="72d77-215">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="72d77-215">Switch mappings</span></span>

<span data-ttu-id="72d77-216">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="72d77-216">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="72d77-217">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="72d77-217">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="72d77-218">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-218">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="72d77-219">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-219">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="72d77-220">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="72d77-220">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="72d77-221">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="72d77-221">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="72d77-222">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="72d77-222">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="72d77-223">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="72d77-223">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="72d77-224">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="72d77-224">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="72d77-225">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="72d77-225">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-226">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="72d77-226">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="72d77-227">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="72d77-227">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="72d77-228">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-228">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d77-229">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="72d77-229">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="72d77-230">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="72d77-230">Hierarchical configuration data</span></span>

<span data-ttu-id="72d77-231">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="72d77-231">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="72d77-232">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *appsettings.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="72d77-232">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="72d77-233">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-233">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-234">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="72d77-234">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="72d77-235">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="72d77-235">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="72d77-236"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-236"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="72d77-237">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="72d77-237">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="72d77-238">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="72d77-238">Configuration keys and values</span></span>

<span data-ttu-id="72d77-239">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="72d77-239">Configuration keys:</span></span>

* <span data-ttu-id="72d77-240">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-240">Are case-insensitive.</span></span> <span data-ttu-id="72d77-241">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-241">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="72d77-242">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-242">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="72d77-243">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="72d77-243">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="72d77-244">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="72d77-244">Hierarchical keys</span></span>
  * <span data-ttu-id="72d77-245">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="72d77-245">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="72d77-246">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-246">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="72d77-247">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="72d77-247">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="72d77-248">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-248">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="72d77-249">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="72d77-249">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="72d77-250">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="72d77-250">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d77-251">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="72d77-251">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="72d77-252">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="72d77-252">Configuration values:</span></span>

* <span data-ttu-id="72d77-253">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="72d77-253">Are strings.</span></span>
* <span data-ttu-id="72d77-254">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-254">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="72d77-255">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="72d77-255">Configuration providers</span></span>

<span data-ttu-id="72d77-256">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="72d77-256">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="72d77-257">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="72d77-257">Provider</span></span> | <span data-ttu-id="72d77-258">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="72d77-258">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="72d77-259">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-259">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="72d77-260">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d77-260">Azure Key Vault</span></span> |
| [<span data-ttu-id="72d77-261">Azure uygulama yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-261">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="72d77-262">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-262">Azure App Configuration</span></span> |
| [<span data-ttu-id="72d77-263">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-263">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="72d77-264">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="72d77-264">Command-line parameters</span></span> |
| [<span data-ttu-id="72d77-265">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-265">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="72d77-266">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="72d77-266">Custom source</span></span> |
| [<span data-ttu-id="72d77-267">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-267">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="72d77-268">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-268">Environment variables</span></span> |
| [<span data-ttu-id="72d77-269">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-269">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="72d77-270">INı, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-270">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="72d77-271">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-271">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="72d77-272">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-272">Directory files</span></span> |
| [<span data-ttu-id="72d77-273">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-273">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="72d77-274">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="72d77-274">In-memory collections</span></span> |
| [<span data-ttu-id="72d77-275">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="72d77-275">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="72d77-276">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="72d77-276">File in the user profile directory</span></span> |

<span data-ttu-id="72d77-277">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="72d77-277">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="72d77-278">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-278">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="72d77-279">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="72d77-279">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="72d77-280">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="72d77-280">*appsettings.json*</span></span>
1. <span data-ttu-id="72d77-281">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="72d77-281">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="72d77-282">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="72d77-282">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="72d77-283">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-283">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="72d77-284">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-284">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="72d77-285">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="72d77-285">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="72d77-286">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-286">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="72d77-287">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="72d77-287">Connection string prefixes</span></span>

<span data-ttu-id="72d77-288">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="72d77-288">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="72d77-289">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-289">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="72d77-290">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="72d77-290">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="72d77-291">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="72d77-291">Connection string prefix</span></span> | <span data-ttu-id="72d77-292">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="72d77-292">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="72d77-293">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="72d77-293">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="72d77-294">MySQL</span><span class="sxs-lookup"><span data-stu-id="72d77-294">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="72d77-295">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="72d77-295">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="72d77-296">SQL Server</span><span class="sxs-lookup"><span data-stu-id="72d77-296">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="72d77-297">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="72d77-297">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="72d77-298">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="72d77-298">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="72d77-299">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="72d77-299">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="72d77-300">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="72d77-300">Environment variable key</span></span> | <span data-ttu-id="72d77-301">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="72d77-301">Converted configuration key</span></span> | <span data-ttu-id="72d77-302">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="72d77-302">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-303">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="72d77-303">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-304">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="72d77-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d77-305">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d77-305">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-306">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="72d77-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d77-307">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d77-307">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-308">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="72d77-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d77-309">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d77-309">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="72d77-310">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-310">File configuration provider</span></span>

<span data-ttu-id="72d77-311"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> , dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="72d77-311"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="72d77-312">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="72d77-312">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="72d77-313">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-313">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="72d77-314">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-314">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="72d77-315">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-315">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="72d77-316">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-316">INI configuration provider</span></span>

<span data-ttu-id="72d77-317">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-317">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d77-318">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="72d77-318">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="72d77-319">Yukarıdaki kodda, *MyIniConfig.ini* ve  *Myıniconfig*içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="72d77-319">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="72d77-320">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-320">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="72d77-321">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d77-321">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d77-322">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyIniConfig.ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="72d77-322">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="72d77-323">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-323">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="72d77-324">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-324">JSON configuration provider</span></span>

<span data-ttu-id="72d77-325">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-325">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="72d77-326">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-326">Overloads can specify:</span></span>

* <span data-ttu-id="72d77-327">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="72d77-327">Whether the file is optional.</span></span>
* <span data-ttu-id="72d77-328">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="72d77-328">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="72d77-329">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="72d77-329">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="72d77-330">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="72d77-330">The preceding code:</span></span>

* <span data-ttu-id="72d77-331">Dosyadaki *MyConfig.js* yüklemek için JSON yapılandırma sağlayıcısını aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="72d77-331">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="72d77-332">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-332">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="72d77-333">`reloadOnChange: true` : Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-333">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="72d77-334">Dosyadaki *MyConfig.js* önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="72d77-334">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="72d77-335">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig.js* dosya geçersiz kılma ayarı ayarları.</span><span class="sxs-lookup"><span data-stu-id="72d77-335">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d77-336">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="72d77-336">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d77-337">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="72d77-337">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="72d77-338">Yukarıdaki kodda, ve *MyConfig* *MyConfig.js* `Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="72d77-338">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="72d77-339">*appsettings.json* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="72d77-339">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="72d77-340">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="72d77-340">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d77-341">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *MyConfig.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="72d77-341">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="72d77-342">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-342">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="72d77-343">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-343">XML configuration provider</span></span>

<span data-ttu-id="72d77-344">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-344">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d77-345">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="72d77-345">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="72d77-346">Yukarıdaki kodda, *MyXMLFile.xml* ve  *myXMLfile*içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="72d77-346">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="72d77-347">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-347">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="72d77-348">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="72d77-348">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="72d77-349">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyXMLFile.xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="72d77-349">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="72d77-350">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-350">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-351">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="72d77-351">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="72d77-352">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-352">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-353">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-353">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="72d77-354">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="72d77-354">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d77-355">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="72d77-355">key:attribute</span></span>
* <span data-ttu-id="72d77-356">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="72d77-356">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="72d77-357">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-357">Key-per-file configuration provider</span></span>

<span data-ttu-id="72d77-358">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-358">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="72d77-359">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-359">The key is the file name.</span></span> <span data-ttu-id="72d77-360">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-360">The value contains the file's contents.</span></span> <span data-ttu-id="72d77-361">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-361">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="72d77-362">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-362">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="72d77-363">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="72d77-363">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="72d77-364">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="72d77-364">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d77-365">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="72d77-365">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="72d77-366">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="72d77-366">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="72d77-367">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-367">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="72d77-368">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="72d77-368">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="72d77-369">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-369">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="72d77-370">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-370">Memory configuration provider</span></span>

<span data-ttu-id="72d77-371">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-371">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="72d77-372">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="72d77-372">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="72d77-373">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-373">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-374">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-374">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="72d77-375">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="72d77-375">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="72d77-376">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="72d77-376">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="72d77-377">GetValue</span><span class="sxs-lookup"><span data-stu-id="72d77-377">GetValue</span></span>

<span data-ttu-id="72d77-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="72d77-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-379">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-379">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="72d77-380">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="72d77-380">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="72d77-381">İzleyen örnekler için, dosyasında aşağıdaki *MySubsection.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="72d77-381">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="72d77-382">Aşağıdaki kod yapılandırma sağlayıcılarına *MySubsection.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="72d77-382">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="72d77-383">GetSection</span><span class="sxs-lookup"><span data-stu-id="72d77-383">GetSection</span></span>

<span data-ttu-id="72d77-384">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="72d77-384">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="72d77-385">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="72d77-385">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-386">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="72d77-386">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-387">`GetSection` hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="72d77-387">`GetSection` never returns `null`.</span></span> <span data-ttu-id="72d77-388">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="72d77-388">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="72d77-389">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="72d77-389">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="72d77-390">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="72d77-390">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="72d77-391">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="72d77-391">GetChildren and Exists</span></span>

<span data-ttu-id="72d77-392">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="72d77-392">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-393">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="72d77-393">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="72d77-394">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="72d77-394">Bind an array</span></span>

<span data-ttu-id="72d77-395">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="72d77-395">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d77-396">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="72d77-396">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="72d77-397">[Örnek karşıdan yükleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="72d77-397">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="72d77-398">Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="72d77-398">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="72d77-399">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-399">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-400">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="72d77-400">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="72d77-401">Yukarıdaki çıktıda, Dizin 3 ' teMyArray.js' a karşılık gelen bir değer vardır `value40` `"4": "value40",` . *MyArray.json*</span><span class="sxs-lookup"><span data-stu-id="72d77-401">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="72d77-402">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-402">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="72d77-403">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="72d77-403">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="72d77-404">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="72d77-404">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="72d77-405">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-405">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-406">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="72d77-406">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="72d77-407">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="72d77-407">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="72d77-408">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-408">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="72d77-409">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-409">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="72d77-410">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-410">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="72d77-411">Örnek indirmenin dosyasında aşağıdaki *Value3.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="72d77-411">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="72d77-412">Aşağıdaki kod, ve \* üzerindeValue3.js\* için yapılandırma içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="72d77-412">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="72d77-413">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-413">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-414">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="72d77-414">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="72d77-415">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-415">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="72d77-416">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-416">Custom configuration provider</span></span>

<span data-ttu-id="72d77-417">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="72d77-417">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="72d77-418">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="72d77-418">The provider has the following characteristics:</span></span>

* <span data-ttu-id="72d77-419">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-419">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="72d77-420">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-420">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="72d77-421">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="72d77-421">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="72d77-422">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-422">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="72d77-423">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-423">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="72d77-424">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-424">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="72d77-425">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-425">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="72d77-426">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="72d77-426">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="72d77-427">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-427">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="72d77-428">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="72d77-428">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="72d77-429">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-429">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="72d77-430">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="72d77-430">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="72d77-431">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="72d77-431">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="72d77-432">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="72d77-432">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="72d77-433">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-433">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="72d77-434">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-434">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="72d77-435">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-435">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="72d77-436">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="72d77-436">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="72d77-437">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-437">Access configuration in Startup</span></span>

<span data-ttu-id="72d77-438">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="72d77-438">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="72d77-439">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="72d77-439">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="72d77-440">Sayfalarda erişim yapılandırması Razor</span><span class="sxs-lookup"><span data-stu-id="72d77-440">Access configuration in Razor Pages</span></span>

<span data-ttu-id="72d77-441">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler Razor :</span><span class="sxs-lookup"><span data-stu-id="72d77-441">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="72d77-442">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-442">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="72d77-443">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) Razor seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-443">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="72d77-444">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-444">Access configuration in a MVC view file</span></span>

<span data-ttu-id="72d77-445">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-445">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="72d77-446">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-446">Configure options with a delegate</span></span>

<span data-ttu-id="72d77-447">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-447">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="72d77-448">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="72d77-448">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="72d77-449">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-449">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="72d77-450">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="72d77-450">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="72d77-451">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72d77-451">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="72d77-452">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` \* üzerindeappsettings.js\* belirtilmiştir ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="72d77-452">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="72d77-453">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="72d77-453">Host versus app configuration</span></span>

<span data-ttu-id="72d77-454">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-454">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="72d77-455">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="72d77-455">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="72d77-456">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-456">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="72d77-457">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-457">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="72d77-458">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="72d77-458">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="72d77-459">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-459">Default host configuration</span></span>

<span data-ttu-id="72d77-460">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-460">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="72d77-461">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-461">Host configuration is provided from:</span></span>
  * <span data-ttu-id="72d77-462">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-462">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="72d77-463">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-463">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="72d77-464">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-464">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="72d77-465">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="72d77-465">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="72d77-466">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-466">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="72d77-467">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="72d77-467">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="72d77-468">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="72d77-468">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="72d77-469">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="72d77-469">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="72d77-470">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-470">Other configuration</span></span>

<span data-ttu-id="72d77-471">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="72d77-471">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="72d77-472">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="72d77-472">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="72d77-473">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="72d77-473">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="72d77-474">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="72d77-474">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="72d77-475">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="72d77-475">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="72d77-476">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="72d77-476">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="72d77-477">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-477">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="72d77-478">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="72d77-478">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="72d77-479">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="72d77-479">Add configuration from an external assembly</span></span>

<span data-ttu-id="72d77-480">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="72d77-480">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="72d77-481">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d77-481">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72d77-482">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="72d77-482">Additional resources</span></span>

* [<span data-ttu-id="72d77-483">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="72d77-483">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="72d77-484">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="72d77-484">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="72d77-485">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="72d77-485">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="72d77-486">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d77-486">Azure Key Vault</span></span>
* <span data-ttu-id="72d77-487">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-487">Azure App Configuration</span></span>
* <span data-ttu-id="72d77-488">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-488">Command-line arguments</span></span>
* <span data-ttu-id="72d77-489">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="72d77-489">Custom providers (installed or created)</span></span>
* <span data-ttu-id="72d77-490">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-490">Directory files</span></span>
* <span data-ttu-id="72d77-491">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-491">Environment variables</span></span>
* <span data-ttu-id="72d77-492">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="72d77-492">In-memory .NET objects</span></span>
* <span data-ttu-id="72d77-493">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-493">Settings files</span></span>

<span data-ttu-id="72d77-494">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="72d77-494">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="72d77-495">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-495">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="72d77-496">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-496">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="72d77-497">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-497">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="72d77-498">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="72d77-498">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="72d77-499">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72d77-499">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="72d77-500">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="72d77-500">Host versus app configuration</span></span>

<span data-ttu-id="72d77-501">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-501">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="72d77-502">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="72d77-502">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="72d77-503">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-503">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="72d77-504">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-504">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="72d77-505">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="72d77-505">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="72d77-506">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-506">Other configuration</span></span>

<span data-ttu-id="72d77-507">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="72d77-507">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="72d77-508">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="72d77-508">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="72d77-509">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="72d77-509">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="72d77-510">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="72d77-510">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="72d77-511">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="72d77-511">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="72d77-512">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="72d77-512">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="72d77-513">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="72d77-513">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="72d77-514">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-514">Default configuration</span></span>

<span data-ttu-id="72d77-515">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="72d77-515">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="72d77-516">`CreateDefaultBuilder` uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="72d77-516">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="72d77-517">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="72d77-517">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="72d77-518">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-518">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="72d77-519">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-519">Host configuration is provided from:</span></span>
  * <span data-ttu-id="72d77-520">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-520">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="72d77-521">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-521">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="72d77-522">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-522">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="72d77-523">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-523">App configuration is provided from:</span></span>
  * <span data-ttu-id="72d77-524">[Dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="72d77-524">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="72d77-525">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="72d77-525">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="72d77-526">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="72d77-526">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="72d77-527">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-527">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="72d77-528">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-528">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="72d77-529">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="72d77-529">Security</span></span>

<span data-ttu-id="72d77-530">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="72d77-530">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="72d77-531">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="72d77-531">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="72d77-532">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-532">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="72d77-533">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="72d77-533">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="72d77-534">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="72d77-534">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="72d77-535"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-535"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="72d77-536">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-536">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="72d77-537">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="72d77-537">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="72d77-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="72d77-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="72d77-539">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d77-539">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="72d77-540">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="72d77-540">Hierarchical configuration data</span></span>

<span data-ttu-id="72d77-541">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="72d77-541">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="72d77-542">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="72d77-542">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="72d77-543">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="72d77-543">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="72d77-544">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="72d77-544">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="72d77-545">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-545">section0:key0</span></span>
* <span data-ttu-id="72d77-546">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-546">section0:key1</span></span>
* <span data-ttu-id="72d77-547">section1:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-547">section1:key0</span></span>
* <span data-ttu-id="72d77-548">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-548">section1:key1</span></span>

<span data-ttu-id="72d77-549"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-549"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="72d77-550">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="72d77-550">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="72d77-551">Kurallar</span><span class="sxs-lookup"><span data-stu-id="72d77-551">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="72d77-552">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="72d77-552">Sources and providers</span></span>

<span data-ttu-id="72d77-553">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="72d77-553">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="72d77-554">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="72d77-554">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="72d77-555">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="72d77-555">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="72d77-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="72d77-557"><xref:Microsoft.Extensions.Configuration.IConfiguration>Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="72d77-558">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="72d77-558">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="72d77-559">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-559">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="72d77-560">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="72d77-560">Keys</span></span>

<span data-ttu-id="72d77-561">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="72d77-561">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="72d77-562">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-562">Keys are case-insensitive.</span></span> <span data-ttu-id="72d77-563">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-563">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="72d77-564">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="72d77-564">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="72d77-565">Yinelenen JSON anahtarları hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/2381)bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-565">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="72d77-566">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="72d77-566">Hierarchical keys</span></span>
  * <span data-ttu-id="72d77-567">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="72d77-567">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="72d77-568">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-568">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="72d77-569">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="72d77-569">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="72d77-570">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-570">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="72d77-571">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="72d77-571">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="72d77-572">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="72d77-572">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d77-573">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="72d77-573">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="72d77-574">Değerler</span><span class="sxs-lookup"><span data-stu-id="72d77-574">Values</span></span>

<span data-ttu-id="72d77-575">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="72d77-575">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="72d77-576">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="72d77-576">Values are strings.</span></span>
* <span data-ttu-id="72d77-577">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-577">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="72d77-578">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="72d77-578">Providers</span></span>

<span data-ttu-id="72d77-579">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="72d77-579">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="72d77-580">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="72d77-580">Provider</span></span> | <span data-ttu-id="72d77-581">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="72d77-581">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="72d77-582">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="72d77-582">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="72d77-583">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d77-583">Azure Key Vault</span></span> |
| <span data-ttu-id="72d77-584">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="72d77-584">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="72d77-585">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-585">Azure App Configuration</span></span> |
| [<span data-ttu-id="72d77-586">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-586">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="72d77-587">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="72d77-587">Command-line parameters</span></span> |
| [<span data-ttu-id="72d77-588">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-588">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="72d77-589">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="72d77-589">Custom source</span></span> |
| [<span data-ttu-id="72d77-590">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-590">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="72d77-591">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-591">Environment variables</span></span> |
| [<span data-ttu-id="72d77-592">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-592">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="72d77-593">Dosyalar (ıNı, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="72d77-593">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="72d77-594">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-594">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="72d77-595">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="72d77-595">Directory files</span></span> |
| [<span data-ttu-id="72d77-596">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-596">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="72d77-597">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="72d77-597">In-memory collections</span></span> |
| <span data-ttu-id="72d77-598">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="72d77-598">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="72d77-599">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="72d77-599">File in the user profile directory</span></span> |

<span data-ttu-id="72d77-600">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="72d77-600">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="72d77-601">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="72d77-601">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="72d77-602">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-602">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="72d77-603">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="72d77-603">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="72d77-604">Dosyalar (*appsettings.json*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="72d77-604">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="72d77-605">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="72d77-605">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="72d77-606">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="72d77-606">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="72d77-607">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-607">Environment variables</span></span>
1. <span data-ttu-id="72d77-608">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="72d77-608">Command-line arguments</span></span>

<span data-ttu-id="72d77-609">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="72d77-609">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="72d77-610">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-610">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d77-611">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-611">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="72d77-612">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="72d77-612">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="72d77-613">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="72d77-613">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="72d77-614">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="72d77-614">ConfigureAppConfiguration</span></span>

<span data-ttu-id="72d77-615">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="72d77-615">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="72d77-616">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="72d77-616">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="72d77-617">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="72d77-617">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="72d77-618">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="72d77-618">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="72d77-619">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-619">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="72d77-620">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="72d77-620">Consume configuration during app startup</span></span>

<span data-ttu-id="72d77-621">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="72d77-621">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="72d77-622">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-622">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="72d77-623">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-623">Command-line Configuration Provider</span></span>

<span data-ttu-id="72d77-624">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-624">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="72d77-625">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-625">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d77-626">`AddCommandLine` çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="72d77-626">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="72d77-627">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-627">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d77-628">`CreateDefaultBuilder` Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="72d77-628">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="72d77-629">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="72d77-629">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="72d77-630">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="72d77-630">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d77-631">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-631">Environment variables.</span></span>

<span data-ttu-id="72d77-632">`CreateDefaultBuilder` Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="72d77-632">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="72d77-633">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="72d77-633">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="72d77-634">`CreateDefaultBuilder` Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="72d77-634">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="72d77-635">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-635">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="72d77-636">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-636">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d77-637">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="72d77-637">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="72d77-638">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="72d77-638">**Example**</span></span>

<span data-ttu-id="72d77-639">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="72d77-639">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="72d77-640">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="72d77-640">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="72d77-641">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="72d77-641">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="72d77-642">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="72d77-642">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="72d77-643">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="72d77-643">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="72d77-644">Bağımsız değişkenler</span><span class="sxs-lookup"><span data-stu-id="72d77-644">Arguments</span></span>

<span data-ttu-id="72d77-645">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-645">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="72d77-646">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="72d77-646">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="72d77-647">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="72d77-647">Key prefix</span></span>               | <span data-ttu-id="72d77-648">Örnek</span><span class="sxs-lookup"><span data-stu-id="72d77-648">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="72d77-649">Ön ek yok</span><span class="sxs-lookup"><span data-stu-id="72d77-649">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="72d77-650">İki kısa çizgi ( `--` )</span><span class="sxs-lookup"><span data-stu-id="72d77-650">Two dashes (`--`)</span></span>        | <span data-ttu-id="72d77-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="72d77-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="72d77-652">Eğik çizgi ( `/` )</span><span class="sxs-lookup"><span data-stu-id="72d77-652">Forward slash (`/`)</span></span>      | <span data-ttu-id="72d77-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="72d77-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="72d77-654">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-654">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="72d77-655">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="72d77-655">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="72d77-656">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="72d77-656">Switch mappings</span></span>

<span data-ttu-id="72d77-657">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="72d77-657">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="72d77-658">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="72d77-658">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="72d77-659">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-659">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="72d77-660">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-660">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="72d77-661">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="72d77-661">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="72d77-662">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="72d77-662">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="72d77-663">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-663">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="72d77-664">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="72d77-664">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="72d77-665">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="72d77-665">Create a switch mappings dictionary.</span></span> <span data-ttu-id="72d77-666">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="72d77-666">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="72d77-667">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-667">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="72d77-668">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="72d77-668">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="72d77-669">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-669">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d77-670">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="72d77-670">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="72d77-671">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-671">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="72d77-672">Anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-672">Key</span></span>       | <span data-ttu-id="72d77-673">Değer</span><span class="sxs-lookup"><span data-stu-id="72d77-673">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="72d77-674">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="72d77-674">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="72d77-675">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-675">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="72d77-676">Anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-676">Key</span></span>               | <span data-ttu-id="72d77-677">Değer</span><span class="sxs-lookup"><span data-stu-id="72d77-677">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="72d77-678">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-678">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="72d77-679">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-679">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="72d77-680">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-680">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="72d77-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="72d77-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="72d77-682">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="72d77-682">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="72d77-683">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-683">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="72d77-684">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-684">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d77-685">`CreateDefaultBuilder` Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="72d77-685">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="72d77-686">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="72d77-686">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="72d77-687">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="72d77-687">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="72d77-688">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="72d77-688">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d77-689">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-689">Command-line arguments.</span></span>

<span data-ttu-id="72d77-690">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-690">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="72d77-691">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="72d77-691">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="72d77-692">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-692">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="72d77-693">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="72d77-693">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="72d77-694">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="72d77-694">**Example**</span></span>

<span data-ttu-id="72d77-695">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="72d77-695">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="72d77-696">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="72d77-696">Run the sample app.</span></span> <span data-ttu-id="72d77-697">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="72d77-697">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="72d77-698">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="72d77-698">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="72d77-699">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="72d77-699">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="72d77-700">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="72d77-700">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="72d77-701">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-701">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="72d77-702">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="72d77-702">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="72d77-703">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="72d77-703">Prefixes</span></span>

<span data-ttu-id="72d77-704">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="72d77-704">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="72d77-705">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="72d77-705">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="72d77-706">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-706">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="72d77-707">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-707">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="72d77-708">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-708">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d77-709">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="72d77-709">**Connection string prefixes**</span></span>

<span data-ttu-id="72d77-710">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-710">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="72d77-711">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="72d77-711">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="72d77-712">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="72d77-712">Connection string prefix</span></span> | <span data-ttu-id="72d77-713">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="72d77-713">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="72d77-714">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="72d77-714">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="72d77-715">MySQL</span><span class="sxs-lookup"><span data-stu-id="72d77-715">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="72d77-716">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="72d77-716">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="72d77-717">SQL Server</span><span class="sxs-lookup"><span data-stu-id="72d77-717">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="72d77-718">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="72d77-718">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="72d77-719">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="72d77-719">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="72d77-720">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="72d77-720">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="72d77-721">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="72d77-721">Environment variable key</span></span> | <span data-ttu-id="72d77-722">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="72d77-722">Converted configuration key</span></span> | <span data-ttu-id="72d77-723">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="72d77-723">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-724">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="72d77-724">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-725">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="72d77-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d77-726">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d77-726">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-727">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="72d77-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d77-728">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d77-728">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="72d77-729">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="72d77-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="72d77-730">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="72d77-730">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="72d77-731">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="72d77-731">**Example**</span></span>

<span data-ttu-id="72d77-732">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="72d77-732">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="72d77-733">Ad: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="72d77-733">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="72d77-734">Değer: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="72d77-734">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="72d77-735">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="72d77-735">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="72d77-736">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-736">File Configuration Provider</span></span>

<span data-ttu-id="72d77-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> , dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="72d77-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="72d77-738">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="72d77-738">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="72d77-739">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-739">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="72d77-740">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-740">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="72d77-741">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-741">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="72d77-742">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-742">INI Configuration Provider</span></span>

<span data-ttu-id="72d77-743">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-743">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d77-744">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-744">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d77-745">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-745">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="72d77-746">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="72d77-746">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d77-747">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="72d77-747">Whether the file is optional.</span></span>
* <span data-ttu-id="72d77-748">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="72d77-748">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="72d77-749"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-749">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="72d77-750">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-750">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="72d77-751">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="72d77-751">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="72d77-752">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="72d77-752">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d77-753">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-753">section0:key0</span></span>
* <span data-ttu-id="72d77-754">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-754">section0:key1</span></span>
* <span data-ttu-id="72d77-755">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-755">section1:subsection:key</span></span>
* <span data-ttu-id="72d77-756">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-756">section2:subsection0:key</span></span>
* <span data-ttu-id="72d77-757">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-757">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="72d77-758">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-758">JSON Configuration Provider</span></span>

<span data-ttu-id="72d77-759">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-759">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="72d77-760">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-760">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d77-761">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="72d77-761">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d77-762">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="72d77-762">Whether the file is optional.</span></span>
* <span data-ttu-id="72d77-763">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="72d77-763">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="72d77-764"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-764">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="72d77-765">`AddJsonFile` , ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-765">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="72d77-766">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="72d77-766">The method is called to load configuration from:</span></span>

* <span data-ttu-id="72d77-767">*appsettings.js*: Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="72d77-767">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="72d77-768">Dosyanın ortam sürümü, *appsettings.js* dosya tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-768">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="72d77-769">*appSettings. {Environment}. JSON*: dosyanın ortam sürümü [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="72d77-769">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="72d77-770">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="72d77-770">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="72d77-771">`CreateDefaultBuilder` Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="72d77-771">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="72d77-772">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-772">Environment variables.</span></span>
* <span data-ttu-id="72d77-773">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="72d77-773">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="72d77-774">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="72d77-774">Command-line arguments.</span></span>

<span data-ttu-id="72d77-775">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="72d77-775">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="72d77-776">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="72d77-776">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="72d77-777">`ConfigureAppConfiguration`Ana bilgisayarı oluştururken, *appsettings.json* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="72d77-777">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="72d77-778">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="72d77-778">**Example**</span></span>

<span data-ttu-id="72d77-779">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="72d77-779">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="72d77-780">`AddJsonFile` *' Dekiappsettings.js*yapılandırmayı yükleyen ilk çağrı:</span><span class="sxs-lookup"><span data-stu-id="72d77-780">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="72d77-781">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="72d77-781">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="72d77-782">Örnek uygulamada *appsettings.Development.js* için aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="72d77-782">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="72d77-783">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="72d77-783">Run the sample app.</span></span> <span data-ttu-id="72d77-784">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="72d77-784">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="72d77-785">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-785">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="72d77-786">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="72d77-786">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="72d77-787">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-787">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="72d77-788">Dosyadaki *Özellikler/launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="72d77-788">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="72d77-789">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="72d77-789">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="72d77-790">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="72d77-790">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="72d77-791">*appsettings.Development.jsüzerindeki* ayarlar artık *appsettings.jsüzerindeki*ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-791">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="72d77-792">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="72d77-792">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="72d77-793">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-793">XML Configuration Provider</span></span>

<span data-ttu-id="72d77-794">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="72d77-794">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="72d77-795">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-795">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d77-796">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="72d77-796">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d77-797">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="72d77-797">Whether the file is optional.</span></span>
* <span data-ttu-id="72d77-798">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="72d77-798">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="72d77-799"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-799">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="72d77-800">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-800">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="72d77-801">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="72d77-801">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="72d77-802">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-802">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="72d77-803">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-803">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="72d77-804">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="72d77-804">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d77-805">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-805">section0:key0</span></span>
* <span data-ttu-id="72d77-806">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-806">section0:key1</span></span>
* <span data-ttu-id="72d77-807">section1:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-807">section1:key0</span></span>
* <span data-ttu-id="72d77-808">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-808">section1:key1</span></span>

<span data-ttu-id="72d77-809">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="72d77-809">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="72d77-810">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="72d77-810">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d77-811">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="72d77-811">section:section0:key:key0</span></span>
* <span data-ttu-id="72d77-812">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-812">section:section0:key:key1</span></span>
* <span data-ttu-id="72d77-813">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="72d77-813">section:section1:key:key0</span></span>
* <span data-ttu-id="72d77-814">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-814">section:section1:key:key1</span></span>

<span data-ttu-id="72d77-815">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-815">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="72d77-816">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="72d77-816">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="72d77-817">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="72d77-817">key:attribute</span></span>
* <span data-ttu-id="72d77-818">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="72d77-818">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="72d77-819">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-819">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="72d77-820">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-820">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="72d77-821">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="72d77-821">The key is the file name.</span></span> <span data-ttu-id="72d77-822">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-822">The value contains the file's contents.</span></span> <span data-ttu-id="72d77-823">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-823">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="72d77-824">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-824">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="72d77-825">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="72d77-825">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="72d77-826">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="72d77-826">Overloads permit specifying:</span></span>

* <span data-ttu-id="72d77-827">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="72d77-827">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="72d77-828">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="72d77-828">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="72d77-829">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-829">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="72d77-830">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="72d77-830">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="72d77-831">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="72d77-831">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="72d77-832">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-832">Memory Configuration Provider</span></span>

<span data-ttu-id="72d77-833">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-833">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="72d77-834">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="72d77-834">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="72d77-835">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="72d77-835">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="72d77-836">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="72d77-836">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="72d77-837">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="72d77-837">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="72d77-838">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="72d77-838">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="72d77-839">GetValue</span><span class="sxs-lookup"><span data-stu-id="72d77-839">GetValue</span></span>

<span data-ttu-id="72d77-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="72d77-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="72d77-841">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="72d77-841">An overload accepts a default value.</span></span>

<span data-ttu-id="72d77-842">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="72d77-842">The following example:</span></span>

* <span data-ttu-id="72d77-843">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="72d77-843">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="72d77-844">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-844">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="72d77-845">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="72d77-845">Types the value as an `int`.</span></span>
* <span data-ttu-id="72d77-846">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="72d77-846">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="72d77-847">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="72d77-847">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="72d77-848">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="72d77-848">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="72d77-849">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="72d77-849">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="72d77-850">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="72d77-850">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="72d77-851">section0:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-851">section0:key0</span></span>
* <span data-ttu-id="72d77-852">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-852">section0:key1</span></span>
* <span data-ttu-id="72d77-853">section1:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-853">section1:key0</span></span>
* <span data-ttu-id="72d77-854">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-854">section1:key1</span></span>
* <span data-ttu-id="72d77-855">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-855">section2:subsection0:key0</span></span>
* <span data-ttu-id="72d77-856">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-856">section2:subsection0:key1</span></span>
* <span data-ttu-id="72d77-857">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="72d77-857">section2:subsection1:key0</span></span>
* <span data-ttu-id="72d77-858">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="72d77-858">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="72d77-859">GetSection</span><span class="sxs-lookup"><span data-stu-id="72d77-859">GetSection</span></span>

<span data-ttu-id="72d77-860">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="72d77-860">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="72d77-861"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="72d77-861">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="72d77-862">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="72d77-862">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="72d77-863">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="72d77-863">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="72d77-864">`GetSection` hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="72d77-864">`GetSection` never returns `null`.</span></span> <span data-ttu-id="72d77-865">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="72d77-865">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="72d77-866">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="72d77-866">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="72d77-867">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="72d77-867">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="72d77-868">GetChildren</span><span class="sxs-lookup"><span data-stu-id="72d77-868">GetChildren</span></span>

<span data-ttu-id="72d77-869">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="72d77-869">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="72d77-870">Var</span><span class="sxs-lookup"><span data-stu-id="72d77-870">Exists</span></span>

<span data-ttu-id="72d77-871">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="72d77-871">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="72d77-872">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="72d77-872">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="72d77-873">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="72d77-873">Bind to an object graph</span></span>

<span data-ttu-id="72d77-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> , bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="72d77-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="72d77-875">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-875">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="72d77-876">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="72d77-876">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="72d77-877">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow.xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="72d77-877">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="72d77-878">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="72d77-878">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="72d77-879">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="72d77-879">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="72d77-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="72d77-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="72d77-881">`Get<T>` , kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="72d77-881">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="72d77-882">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="72d77-882">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="72d77-883">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="72d77-883">Bind an array to a class</span></span>

<span data-ttu-id="72d77-884">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="72d77-884">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="72d77-885">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="72d77-885">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="72d77-886">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="72d77-886">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="72d77-887">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="72d77-887">Binding is provided by convention.</span></span> <span data-ttu-id="72d77-888">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-888">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="72d77-889">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="72d77-889">**In-memory array processing**</span></span>

<span data-ttu-id="72d77-890">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="72d77-890">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="72d77-891">Anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-891">Key</span></span>             | <span data-ttu-id="72d77-892">Değer</span><span class="sxs-lookup"><span data-stu-id="72d77-892">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="72d77-893">dizi: girdiler: 0</span><span class="sxs-lookup"><span data-stu-id="72d77-893">array:entries:0</span></span> | <span data-ttu-id="72d77-894">value0</span><span class="sxs-lookup"><span data-stu-id="72d77-894">value0</span></span> |
| <span data-ttu-id="72d77-895">dizi: girdiler: 1</span><span class="sxs-lookup"><span data-stu-id="72d77-895">array:entries:1</span></span> | <span data-ttu-id="72d77-896">value1</span><span class="sxs-lookup"><span data-stu-id="72d77-896">value1</span></span> |
| <span data-ttu-id="72d77-897">dizi: girdiler: 2</span><span class="sxs-lookup"><span data-stu-id="72d77-897">array:entries:2</span></span> | <span data-ttu-id="72d77-898">value2</span><span class="sxs-lookup"><span data-stu-id="72d77-898">value2</span></span> |
| <span data-ttu-id="72d77-899">dizi: girdiler: 4</span><span class="sxs-lookup"><span data-stu-id="72d77-899">array:entries:4</span></span> | <span data-ttu-id="72d77-900">value4</span><span class="sxs-lookup"><span data-stu-id="72d77-900">value4</span></span> |
| <span data-ttu-id="72d77-901">dizi: girdiler: 5</span><span class="sxs-lookup"><span data-stu-id="72d77-901">array:entries:5</span></span> | <span data-ttu-id="72d77-902">value5</span><span class="sxs-lookup"><span data-stu-id="72d77-902">value5</span></span> |

<span data-ttu-id="72d77-903">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="72d77-903">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="72d77-904">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="72d77-904">The array skips a value for index &num;3.</span></span> <span data-ttu-id="72d77-905">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="72d77-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="72d77-906">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-906">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="72d77-907">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="72d77-907">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="72d77-908">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-908">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="72d77-909">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="72d77-909">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="72d77-910">`ArrayExample.Entries` İndeks</span><span class="sxs-lookup"><span data-stu-id="72d77-910">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="72d77-911">`ArrayExample.Entries` Deeri</span><span class="sxs-lookup"><span data-stu-id="72d77-911">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="72d77-912">0</span><span class="sxs-lookup"><span data-stu-id="72d77-912">0</span></span>                            | <span data-ttu-id="72d77-913">value0</span><span class="sxs-lookup"><span data-stu-id="72d77-913">value0</span></span>                       |
| <span data-ttu-id="72d77-914">1</span><span class="sxs-lookup"><span data-stu-id="72d77-914">1</span></span>                            | <span data-ttu-id="72d77-915">value1</span><span class="sxs-lookup"><span data-stu-id="72d77-915">value1</span></span>                       |
| <span data-ttu-id="72d77-916">2</span><span class="sxs-lookup"><span data-stu-id="72d77-916">2</span></span>                            | <span data-ttu-id="72d77-917">value2</span><span class="sxs-lookup"><span data-stu-id="72d77-917">value2</span></span>                       |
| <span data-ttu-id="72d77-918">3</span><span class="sxs-lookup"><span data-stu-id="72d77-918">3</span></span>                            | <span data-ttu-id="72d77-919">value4</span><span class="sxs-lookup"><span data-stu-id="72d77-919">value4</span></span>                       |
| <span data-ttu-id="72d77-920">4</span><span class="sxs-lookup"><span data-stu-id="72d77-920">4</span></span>                            | <span data-ttu-id="72d77-921">value5</span><span class="sxs-lookup"><span data-stu-id="72d77-921">value5</span></span>                       |

<span data-ttu-id="72d77-922">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="72d77-922">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="72d77-923">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-923">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="72d77-924">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-924">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="72d77-925">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="72d77-925">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="72d77-926">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="72d77-926">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="72d77-927">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="72d77-927">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="72d77-928">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="72d77-928">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="72d77-929">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="72d77-929">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="72d77-930">Anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-930">Key</span></span>             | <span data-ttu-id="72d77-931">Değer</span><span class="sxs-lookup"><span data-stu-id="72d77-931">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="72d77-932">dizi: girdiler: 3</span><span class="sxs-lookup"><span data-stu-id="72d77-932">array:entries:3</span></span> | <span data-ttu-id="72d77-933">value3</span><span class="sxs-lookup"><span data-stu-id="72d77-933">value3</span></span> |

<span data-ttu-id="72d77-934">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="72d77-934">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="72d77-935">`ArrayExample.Entries` İndeks</span><span class="sxs-lookup"><span data-stu-id="72d77-935">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="72d77-936">`ArrayExample.Entries` Deeri</span><span class="sxs-lookup"><span data-stu-id="72d77-936">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="72d77-937">0</span><span class="sxs-lookup"><span data-stu-id="72d77-937">0</span></span>                            | <span data-ttu-id="72d77-938">value0</span><span class="sxs-lookup"><span data-stu-id="72d77-938">value0</span></span>                       |
| <span data-ttu-id="72d77-939">1</span><span class="sxs-lookup"><span data-stu-id="72d77-939">1</span></span>                            | <span data-ttu-id="72d77-940">value1</span><span class="sxs-lookup"><span data-stu-id="72d77-940">value1</span></span>                       |
| <span data-ttu-id="72d77-941">2</span><span class="sxs-lookup"><span data-stu-id="72d77-941">2</span></span>                            | <span data-ttu-id="72d77-942">value2</span><span class="sxs-lookup"><span data-stu-id="72d77-942">value2</span></span>                       |
| <span data-ttu-id="72d77-943">3</span><span class="sxs-lookup"><span data-stu-id="72d77-943">3</span></span>                            | <span data-ttu-id="72d77-944">value3</span><span class="sxs-lookup"><span data-stu-id="72d77-944">value3</span></span>                       |
| <span data-ttu-id="72d77-945">4</span><span class="sxs-lookup"><span data-stu-id="72d77-945">4</span></span>                            | <span data-ttu-id="72d77-946">value4</span><span class="sxs-lookup"><span data-stu-id="72d77-946">value4</span></span>                       |
| <span data-ttu-id="72d77-947">5</span><span class="sxs-lookup"><span data-stu-id="72d77-947">5</span></span>                            | <span data-ttu-id="72d77-948">value5</span><span class="sxs-lookup"><span data-stu-id="72d77-948">value5</span></span>                       |

<span data-ttu-id="72d77-949">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="72d77-949">**JSON array processing**</span></span>

<span data-ttu-id="72d77-950">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="72d77-950">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="72d77-951">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="72d77-951">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="72d77-952">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="72d77-952">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="72d77-953">Anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-953">Key</span></span>                     | <span data-ttu-id="72d77-954">Değer</span><span class="sxs-lookup"><span data-stu-id="72d77-954">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="72d77-955">json_array: anahtar</span><span class="sxs-lookup"><span data-stu-id="72d77-955">json_array:key</span></span>          | <span data-ttu-id="72d77-956">değer EA</span><span class="sxs-lookup"><span data-stu-id="72d77-956">valueA</span></span> |
| <span data-ttu-id="72d77-957">json_array: alt bölüm: 0</span><span class="sxs-lookup"><span data-stu-id="72d77-957">json_array:subsection:0</span></span> | <span data-ttu-id="72d77-958">valueB</span><span class="sxs-lookup"><span data-stu-id="72d77-958">valueB</span></span> |
| <span data-ttu-id="72d77-959">json_array: alt bölüm: 1</span><span class="sxs-lookup"><span data-stu-id="72d77-959">json_array:subsection:1</span></span> | <span data-ttu-id="72d77-960">değer EC</span><span class="sxs-lookup"><span data-stu-id="72d77-960">valueC</span></span> |
| <span data-ttu-id="72d77-961">json_array: alt bölüm: 2</span><span class="sxs-lookup"><span data-stu-id="72d77-961">json_array:subsection:2</span></span> | <span data-ttu-id="72d77-962">Değerler</span><span class="sxs-lookup"><span data-stu-id="72d77-962">valueD</span></span> |

<span data-ttu-id="72d77-963">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="72d77-963">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="72d77-964">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="72d77-964">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="72d77-965">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="72d77-965">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="72d77-966">`JsonArrayExample.Subsection` İndeks</span><span class="sxs-lookup"><span data-stu-id="72d77-966">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="72d77-967">`JsonArrayExample.Subsection` Deeri</span><span class="sxs-lookup"><span data-stu-id="72d77-967">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="72d77-968">0</span><span class="sxs-lookup"><span data-stu-id="72d77-968">0</span></span>                                   | <span data-ttu-id="72d77-969">valueB</span><span class="sxs-lookup"><span data-stu-id="72d77-969">valueB</span></span>                              |
| <span data-ttu-id="72d77-970">1</span><span class="sxs-lookup"><span data-stu-id="72d77-970">1</span></span>                                   | <span data-ttu-id="72d77-971">değer EC</span><span class="sxs-lookup"><span data-stu-id="72d77-971">valueC</span></span>                              |
| <span data-ttu-id="72d77-972">2</span><span class="sxs-lookup"><span data-stu-id="72d77-972">2</span></span>                                   | <span data-ttu-id="72d77-973">Değerler</span><span class="sxs-lookup"><span data-stu-id="72d77-973">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="72d77-974">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="72d77-974">Custom configuration provider</span></span>

<span data-ttu-id="72d77-975">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="72d77-975">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="72d77-976">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="72d77-976">The provider has the following characteristics:</span></span>

* <span data-ttu-id="72d77-977">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="72d77-977">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="72d77-978">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-978">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="72d77-979">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="72d77-979">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="72d77-980">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="72d77-980">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="72d77-981">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="72d77-981">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="72d77-982">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="72d77-982">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="72d77-983">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-983">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="72d77-984">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="72d77-984">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="72d77-985">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-985">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="72d77-986">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="72d77-986">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="72d77-987">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-987">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="72d77-988">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="72d77-988">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="72d77-989">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="72d77-989">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="72d77-990">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-990">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="72d77-991">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="72d77-991">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="72d77-992">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="72d77-992">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="72d77-993">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="72d77-993">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="72d77-994">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="72d77-994">Access configuration during startup</span></span>

<span data-ttu-id="72d77-995">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="72d77-995">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="72d77-996">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="72d77-996">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="72d77-997">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="72d77-997">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="72d77-998">RazorSayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="72d77-998">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="72d77-999">Bir Razor Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft.Extensions.Configurlama ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="72d77-999">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="72d77-1000">RazorSayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="72d77-1000">In a Razor Pages page:</span></span>

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

<span data-ttu-id="72d77-1001">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="72d77-1001">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="72d77-1002">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="72d77-1002">Add configuration from an external assembly</span></span>

<span data-ttu-id="72d77-1003">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="72d77-1003">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="72d77-1004">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="72d77-1004">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72d77-1005">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="72d77-1005">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end