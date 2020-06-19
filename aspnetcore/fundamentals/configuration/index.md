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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: 91200a7a4396d91bec3cfe6ce4b787839f30c72f
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102668"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="2caf3-103">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="2caf3-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2caf3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2caf3-105">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="2caf3-106">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="2caf3-107">*appsettings.js* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="2caf3-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-108">Environment variables</span></span>
* <span data-ttu-id="2caf3-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2caf3-109">Azure Key Vault</span></span>
* <span data-ttu-id="2caf3-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-110">Azure App Configuration</span></span>
* <span data-ttu-id="2caf3-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-111">Command-line arguments</span></span>
* <span data-ttu-id="2caf3-112">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="2caf3-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="2caf3-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-113">Directory files</span></span>
* <span data-ttu-id="2caf3-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-114">In-memory .NET objects</span></span>

<span data-ttu-id="2caf3-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2caf3-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="2caf3-116">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-116">Default configuration</span></span>

<span data-ttu-id="2caf3-117">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="2caf3-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="2caf3-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="2caf3-119">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="2caf3-120">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="2caf3-121">[JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak [appsettings.js](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="2caf3-122">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="2caf3-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="2caf3-123">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="2caf3-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="2caf3-124">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="2caf3-125">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2caf3-126">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="2caf3-127">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="2caf3-128">Örneğin, `MyKey` ve ortamında hem *appsettings.js* hem de ortamda ayarlandıysa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="2caf3-129">Varsayılan yapılandırma sağlayıcılarını kullanarak, [komut satırı yapılandırma sağlayıcısı](#clcp) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="2caf3-130">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="2caf3-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="2caf3-131">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="2caf3-132">Üzerinde appsettings.js</span><span class="sxs-lookup"><span data-stu-id="2caf3-132">appsettings.json</span></span>

<span data-ttu-id="2caf3-133">Dosyasında aşağıdaki *appsettings.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2caf3-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2caf3-134">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-135">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="2caf3-136">*Üzerindeappsettings.js*</span><span class="sxs-lookup"><span data-stu-id="2caf3-136">*appsettings.json*</span></span>
1. <span data-ttu-id="2caf3-137">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="2caf3-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="2caf3-138">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="2caf3-139">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2caf3-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2caf3-140">*appSettings*. `Environment` . *JSON* değerleri *appsettings.jsüzerindeki*anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="2caf3-141">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="2caf3-141">For example, by default:</span></span>

* <span data-ttu-id="2caf3-142">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması, *üzerindeappsettings.js*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="2caf3-143">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması, *üzerindeappsettings.js*bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="2caf3-144">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="2caf3-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="2caf3-145">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="2caf3-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="2caf3-146">[Varsayılan](#default) yapılandırmayı kullanarak *appsettings.js* ve appSettings ' i kullanın *.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="2caf3-147">Ve appSettings *üzerindeappsettings.js* yapılan değişiklikler *.* `Environment` uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="2caf3-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2caf3-148">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="2caf3-149">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="2caf3-149">Security and secret manager</span></span>

<span data-ttu-id="2caf3-150">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="2caf3-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="2caf3-151">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="2caf3-152">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="2caf3-153">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2caf3-154">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2caf3-155">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appsettings.json* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="2caf3-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="2caf3-156">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="2caf3-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2caf3-157"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2caf3-158">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="2caf3-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2caf3-160">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2caf3-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="2caf3-161">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-161">Environment variables</span></span>

<span data-ttu-id="2caf3-162">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> yapılandırma *appsettings.json*, appSettings sonrasında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2caf3-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2caf3-163">Bu nedenle, ortamdan okunan anahtar değerleri, *appsettings.json*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="2caf3-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2caf3-164">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="2caf3-164">The following `set` commands:</span></span>

* <span data-ttu-id="2caf3-165">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="2caf3-166">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="2caf3-167">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="2caf3-168">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="2caf3-168">The preceding environment settings:</span></span>

* <span data-ttu-id="2caf3-169">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="2caf3-170">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="2caf3-171">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="2caf3-172">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="2caf3-173">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="2caf3-174">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="2caf3-175">Yukarıdaki komutların ve appSettings *appsettings.js* geçersiz kılmasını test etmek *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="2caf3-176">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="2caf3-177">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="2caf3-178"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="2caf3-179">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="2caf3-179">In the preceding code:</span></span>

* <span data-ttu-id="2caf3-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2caf3-181">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2caf3-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="2caf3-182">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="2caf3-183">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="2caf3-184">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="2caf3-185">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="2caf3-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="2caf3-186">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="2caf3-187">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="2caf3-188">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2caf3-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="2caf3-189">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="2caf3-190">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="2caf3-191">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="2caf3-192">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-192">Exposed as environment variables.</span></span>

<span data-ttu-id="2caf3-193">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2caf3-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2caf3-194">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="2caf3-195">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="2caf3-195">Command-line</span></span>

<span data-ttu-id="2caf3-196">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-196">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="2caf3-197">Ve *appsettings* *appsettings.js* . `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="2caf3-197">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2caf3-198">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-198">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2caf3-199">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-199">Environment variables.</span></span>

<span data-ttu-id="2caf3-200">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-200">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="2caf3-201">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-201">Command-line arguments</span></span>

<span data-ttu-id="2caf3-202">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-202">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="2caf3-203">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-203">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="2caf3-204">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-204">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="2caf3-205">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="2caf3-205">The key value:</span></span>

* <span data-ttu-id="2caf3-206">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-206">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="2caf3-207">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-207">Isn't required if `=` is used.</span></span> <span data-ttu-id="2caf3-208">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="2caf3-208">For example, `MySetting=`.</span></span>

<span data-ttu-id="2caf3-209">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-209">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="2caf3-210">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="2caf3-210">Switch mappings</span></span>

<span data-ttu-id="2caf3-211">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-211">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="2caf3-212">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-212">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2caf3-213">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-213">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2caf3-214">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-214">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2caf3-215">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-215">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2caf3-216">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="2caf3-216">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2caf3-217">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-217">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="2caf3-218">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-218">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2caf3-219">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-219">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="2caf3-220">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-220">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-221">Anahtar değişimini test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-221">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2caf3-222">Note: Şu anda, `=` anahtar değiştirme değerlerini tek bir çizgiyle ayarlamak için kullanılamaz `-` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-222">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="2caf3-223">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-223">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="2caf3-224">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="2caf3-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2caf3-225">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="2caf3-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2caf3-226">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2caf3-227">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2caf3-228">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="2caf3-228">Hierarchical configuration data</span></span>

<span data-ttu-id="2caf3-229">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2caf3-230">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *appsettings.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2caf3-231">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-232">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="2caf3-233">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="2caf3-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2caf3-235">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="2caf3-236">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-236">Configuration keys and values</span></span>

<span data-ttu-id="2caf3-237">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="2caf3-237">Configuration keys:</span></span>

* <span data-ttu-id="2caf3-238">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-238">Are case-insensitive.</span></span> <span data-ttu-id="2caf3-239">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2caf3-240">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="2caf3-241">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="2caf3-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="2caf3-242">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="2caf3-242">Hierarchical keys</span></span>
  * <span data-ttu-id="2caf3-243">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2caf3-244">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2caf3-245">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="2caf3-246">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="2caf3-247">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2caf3-248">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2caf3-249">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="2caf3-250">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="2caf3-250">Configuration values:</span></span>

* <span data-ttu-id="2caf3-251">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-251">Are strings.</span></span>
* <span data-ttu-id="2caf3-252">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="2caf3-253">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="2caf3-253">Configuration providers</span></span>

<span data-ttu-id="2caf3-254">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2caf3-255">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="2caf3-255">Provider</span></span> | <span data-ttu-id="2caf3-256">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="2caf3-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="2caf3-257">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="2caf3-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2caf3-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="2caf3-259">Azure uygulama yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="2caf3-260">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="2caf3-261">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="2caf3-262">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-262">Command-line parameters</span></span> |
| [<span data-ttu-id="2caf3-263">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2caf3-264">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="2caf3-264">Custom source</span></span> |
| [<span data-ttu-id="2caf3-265">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="2caf3-266">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-266">Environment variables</span></span> |
| [<span data-ttu-id="2caf3-267">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2caf3-268">INı, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="2caf3-269">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2caf3-270">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-270">Directory files</span></span> |
| [<span data-ttu-id="2caf3-271">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2caf3-272">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="2caf3-272">In-memory collections</span></span> |
| [<span data-ttu-id="2caf3-273">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="2caf3-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="2caf3-274">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="2caf3-274">File in the user profile directory</span></span> |

<span data-ttu-id="2caf3-275">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="2caf3-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="2caf3-276">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2caf3-277">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2caf3-278">*Üzerindeappsettings.js*</span><span class="sxs-lookup"><span data-stu-id="2caf3-278">*appsettings.json*</span></span>
1. <span data-ttu-id="2caf3-279">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="2caf3-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="2caf3-280">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="2caf3-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="2caf3-281">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2caf3-282">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="2caf3-283">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2caf3-284">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="2caf3-285">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-285">Connection string prefixes</span></span>

<span data-ttu-id="2caf3-286">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="2caf3-287">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2caf3-288">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2caf3-289">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="2caf3-289">Connection string prefix</span></span> | <span data-ttu-id="2caf3-290">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="2caf3-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2caf3-291">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="2caf3-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2caf3-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="2caf3-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2caf3-293">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="2caf3-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2caf3-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2caf3-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2caf3-295">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="2caf3-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2caf3-296">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2caf3-297">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="2caf3-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2caf3-298">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="2caf3-298">Environment variable key</span></span> | <span data-ttu-id="2caf3-299">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="2caf3-299">Converted configuration key</span></span> | <span data-ttu-id="2caf3-300">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="2caf3-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-301">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="2caf3-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-302">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2caf3-303">Değer:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2caf3-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-304">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2caf3-305">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2caf3-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-306">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2caf3-307">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2caf3-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="2caf3-308">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-308">JSON configuration provider</span></span>

<span data-ttu-id="2caf3-309">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-309">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="2caf3-310">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-310">Overloads can specify:</span></span>

* <span data-ttu-id="2caf3-311">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="2caf3-311">Whether the file is optional.</span></span>
* <span data-ttu-id="2caf3-312">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-312">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="2caf3-313">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="2caf3-313">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="2caf3-314">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2caf3-314">The preceding code:</span></span>

* <span data-ttu-id="2caf3-315">Dosyadaki *MyConfig.js* yüklemek için JSON yapılandırma sağlayıcısını aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-315">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="2caf3-316">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-316">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="2caf3-317">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-317">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="2caf3-318">Dosyadaki *MyConfig.js* önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-318">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="2caf3-319">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig.js* dosya geçersiz kılma ayarı ayarları.</span><span class="sxs-lookup"><span data-stu-id="2caf3-319">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2caf3-320">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="2caf3-320">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2caf3-321">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="2caf3-322">Yukarıdaki kodda, ve *MyConfig* *MyConfig.js* `Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="2caf3-322">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="2caf3-323">*appsettings.json* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="2caf3-323">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2caf3-324">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-324">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2caf3-325">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *MyConfig.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="2caf3-326">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="2caf3-327">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-327">File configuration provider</span></span>

<span data-ttu-id="2caf3-328"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-328"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2caf3-329">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-329">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="2caf3-330">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-330">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2caf3-331">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-331">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="2caf3-332">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-332">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2caf3-333">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-333">INI configuration provider</span></span>

<span data-ttu-id="2caf3-334">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-334">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2caf3-335">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="2caf3-336">Yukarıdaki kodda, *MyIniConfig.ini* ve *Myıniconfig*içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-336">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2caf3-337">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-337">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2caf3-338">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2caf3-338">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2caf3-339">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyIniConfig.ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="2caf3-340">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="2caf3-341">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-341">XML configuration provider</span></span>

<span data-ttu-id="2caf3-342">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2caf3-343">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="2caf3-344">Yukarıdaki kodda, *MyXMLFile.xml* ve *myXMLfile*içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2caf3-345">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-345">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2caf3-346">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2caf3-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2caf3-347">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyXMLFile.xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="2caf3-348">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-349">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="2caf3-350">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-351">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2caf3-352">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2caf3-353">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="2caf3-353">key:attribute</span></span>
* <span data-ttu-id="2caf3-354">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="2caf3-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2caf3-355">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="2caf3-356">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2caf3-357">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-357">The key is the file name.</span></span> <span data-ttu-id="2caf3-358">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-358">The value contains the file's contents.</span></span> <span data-ttu-id="2caf3-359">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2caf3-360">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2caf3-361">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2caf3-362">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="2caf3-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="2caf3-363">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="2caf3-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2caf3-364">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2caf3-365">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2caf3-366">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2caf3-367">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="2caf3-368">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-368">Memory configuration provider</span></span>

<span data-ttu-id="2caf3-369">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2caf3-370">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="2caf3-371">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-372">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2caf3-373">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2caf3-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2caf3-374">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="2caf3-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="2caf3-375">GetValue</span></span>

<span data-ttu-id="2caf3-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="2caf3-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-377">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2caf3-378">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="2caf3-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2caf3-379">İzleyen örnekler için, dosyasında aşağıdaki *MySubsection.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2caf3-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="2caf3-380">Aşağıdaki kod yapılandırma sağlayıcılarına *MySubsection.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="2caf3-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="2caf3-381">GetSection</span></span>

<span data-ttu-id="2caf3-382">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="2caf3-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2caf3-383">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-384">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-385">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2caf3-386">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2caf3-387">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="2caf3-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2caf3-388">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="2caf3-389">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="2caf3-389">GetChildren and Exists</span></span>

<span data-ttu-id="2caf3-390">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-391">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="2caf3-392">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="2caf3-392">Bind an array</span></span>

<span data-ttu-id="2caf3-393">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2caf3-394">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="2caf3-395">[Örnek karşıdan yükleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2caf3-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="2caf3-396">Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="2caf3-397">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-398">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="2caf3-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="2caf3-399">Yukarıdaki çıktıda, Dizin 3 ' teMyArray.js' a karşılık gelen bir değer vardır `value40` `"4": "value40",` . *MyArray.json*</span><span class="sxs-lookup"><span data-stu-id="2caf3-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="2caf3-400">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="2caf3-401">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="2caf3-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="2caf3-402">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="2caf3-403">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-404">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="2caf3-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="2caf3-405">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2caf3-406">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2caf3-407">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2caf3-408">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="2caf3-409">Örnek indirmenin dosyasında aşağıdaki *Value3.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2caf3-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="2caf3-410">Aşağıdaki kod, ve *üzerindeValue3.js* için yapılandırma içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="2caf3-411">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-412">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="2caf3-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="2caf3-413">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="2caf3-414">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-414">Custom configuration provider</span></span>

<span data-ttu-id="2caf3-415">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2caf3-416">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2caf3-417">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2caf3-418">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2caf3-419">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2caf3-420">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2caf3-421">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2caf3-422">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2caf3-423">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2caf3-424">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2caf3-425">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2caf3-426">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2caf3-427">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2caf3-428">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2caf3-429">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="2caf3-430">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="2caf3-431">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2caf3-432">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2caf3-433">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2caf3-434">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="2caf3-435">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-435">Access configuration in Startup</span></span>

<span data-ttu-id="2caf3-436">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="2caf3-437">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2caf3-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="2caf3-438">Sayfalarda erişim yapılandırması Razor</span><span class="sxs-lookup"><span data-stu-id="2caf3-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="2caf3-439">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler Razor :</span><span class="sxs-lookup"><span data-stu-id="2caf3-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="2caf3-440">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="2caf3-441">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) Razor seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="2caf3-442">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="2caf3-443">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="2caf3-444">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-444">Configure options with a delegate</span></span>

<span data-ttu-id="2caf3-445">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="2caf3-446">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="2caf3-447">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="2caf3-448">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="2caf3-449">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2caf3-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="2caf3-450">Yukarıdaki örnekte, `Option1` ve değerleri `Option2` *üzerindeappsettings.js* belirtilmiştir ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2caf3-451">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="2caf3-451">Host versus app configuration</span></span>

<span data-ttu-id="2caf3-452">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2caf3-453">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2caf3-454">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2caf3-455">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2caf3-456">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="2caf3-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="2caf3-457">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-457">Default host configuration</span></span>

<span data-ttu-id="2caf3-458">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="2caf3-459">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2caf3-460">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="2caf3-461">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2caf3-462">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2caf3-463">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="2caf3-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="2caf3-464">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="2caf3-465">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="2caf3-466">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="2caf3-467">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2caf3-468">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-468">Other configuration</span></span>

<span data-ttu-id="2caf3-469">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2caf3-470">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2caf3-471">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2caf3-472">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2caf3-473">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="2caf3-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2caf3-474">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2caf3-475">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="2caf3-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2caf3-476">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="2caf3-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="2caf3-477">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2caf3-478">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2caf3-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2caf3-479">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2caf3-479">Additional resources</span></span>

* [<span data-ttu-id="2caf3-480">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="2caf3-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2caf3-481">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="2caf3-482">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="2caf3-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2caf3-483">Azure Key Vault</span></span>
* <span data-ttu-id="2caf3-484">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-484">Azure App Configuration</span></span>
* <span data-ttu-id="2caf3-485">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-485">Command-line arguments</span></span>
* <span data-ttu-id="2caf3-486">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="2caf3-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="2caf3-487">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-487">Directory files</span></span>
* <span data-ttu-id="2caf3-488">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-488">Environment variables</span></span>
* <span data-ttu-id="2caf3-489">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-489">In-memory .NET objects</span></span>
* <span data-ttu-id="2caf3-490">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-490">Settings files</span></span>

<span data-ttu-id="2caf3-491">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2caf3-492">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="2caf3-493">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="2caf3-494">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="2caf3-495">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="2caf3-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="2caf3-496">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2caf3-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2caf3-497">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="2caf3-497">Host versus app configuration</span></span>

<span data-ttu-id="2caf3-498">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2caf3-499">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2caf3-500">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2caf3-501">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2caf3-502">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="2caf3-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2caf3-503">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-503">Other configuration</span></span>

<span data-ttu-id="2caf3-504">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-504">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2caf3-505">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2caf3-506">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2caf3-507">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2caf3-508">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="2caf3-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2caf3-509">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2caf3-510">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="2caf3-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="2caf3-511">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-511">Default configuration</span></span>

<span data-ttu-id="2caf3-512">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="2caf3-513">`CreateDefaultBuilder`uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="2caf3-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="2caf3-514">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2caf3-515">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="2caf3-516">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2caf3-517">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2caf3-518">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2caf3-519">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2caf3-520">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="2caf3-521">[Dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="2caf3-521">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2caf3-522">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="2caf3-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2caf3-523">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="2caf3-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="2caf3-524">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="2caf3-525">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="2caf3-526">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="2caf3-526">Security</span></span>

<span data-ttu-id="2caf3-527">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="2caf3-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="2caf3-528">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="2caf3-529">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2caf3-530">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2caf3-531">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2caf3-532"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2caf3-533">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="2caf3-534">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="2caf3-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2caf3-536">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2caf3-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2caf3-537">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="2caf3-537">Hierarchical configuration data</span></span>

<span data-ttu-id="2caf3-538">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="2caf3-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2caf3-539">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="2caf3-540">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="2caf3-541">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="2caf3-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-542">section0:key0</span></span>
* <span data-ttu-id="2caf3-543">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-543">section0:key1</span></span>
* <span data-ttu-id="2caf3-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-544">section1:key0</span></span>
* <span data-ttu-id="2caf3-545">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-545">section1:key1</span></span>

<span data-ttu-id="2caf3-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2caf3-547">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="2caf3-548">Kurallar</span><span class="sxs-lookup"><span data-stu-id="2caf3-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="2caf3-549">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="2caf3-549">Sources and providers</span></span>

<span data-ttu-id="2caf3-550">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="2caf3-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="2caf3-551">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="2caf3-552">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="2caf3-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="2caf3-553"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2caf3-554"><xref:Microsoft.Extensions.Configuration.IConfiguration>Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="2caf3-555">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="2caf3-556">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="2caf3-557">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="2caf3-557">Keys</span></span>

<span data-ttu-id="2caf3-558">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="2caf3-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="2caf3-559">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-559">Keys are case-insensitive.</span></span> <span data-ttu-id="2caf3-560">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2caf3-561">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="2caf3-562">Yinelenen JSON anahtarları hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/2381)bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="2caf3-563">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="2caf3-563">Hierarchical keys</span></span>
  * <span data-ttu-id="2caf3-564">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2caf3-565">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2caf3-566">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="2caf3-567">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2caf3-568">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2caf3-569">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2caf3-570">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="2caf3-571">Değerler</span><span class="sxs-lookup"><span data-stu-id="2caf3-571">Values</span></span>

<span data-ttu-id="2caf3-572">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="2caf3-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="2caf3-573">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-573">Values are strings.</span></span>
* <span data-ttu-id="2caf3-574">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="2caf3-575">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="2caf3-575">Providers</span></span>

<span data-ttu-id="2caf3-576">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2caf3-577">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="2caf3-577">Provider</span></span> | <span data-ttu-id="2caf3-578">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="2caf3-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="2caf3-579">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="2caf3-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="2caf3-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2caf3-580">Azure Key Vault</span></span> |
| <span data-ttu-id="2caf3-581">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="2caf3-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="2caf3-582">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="2caf3-583">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="2caf3-584">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-584">Command-line parameters</span></span> |
| [<span data-ttu-id="2caf3-585">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2caf3-586">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="2caf3-586">Custom source</span></span> |
| [<span data-ttu-id="2caf3-587">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="2caf3-588">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-588">Environment variables</span></span> |
| [<span data-ttu-id="2caf3-589">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2caf3-590">Dosyalar (ıNı, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="2caf3-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="2caf3-591">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2caf3-592">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="2caf3-592">Directory files</span></span> |
| [<span data-ttu-id="2caf3-593">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2caf3-594">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="2caf3-594">In-memory collections</span></span> |
| <span data-ttu-id="2caf3-595">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="2caf3-595">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="2caf3-596">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="2caf3-596">File in the user profile directory</span></span> |

<span data-ttu-id="2caf3-597">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="2caf3-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="2caf3-598">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="2caf3-599">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2caf3-600">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2caf3-601">Dosyalar (*appsettings.json*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="2caf3-601">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="2caf3-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2caf3-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="2caf3-603">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="2caf3-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="2caf3-604">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-604">Environment variables</span></span>
1. <span data-ttu-id="2caf3-605">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2caf3-605">Command-line arguments</span></span>

<span data-ttu-id="2caf3-606">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2caf3-607">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2caf3-608">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="2caf3-609">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2caf3-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="2caf3-610">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="2caf3-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2caf3-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2caf3-612">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="2caf3-613">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="2caf3-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="2caf3-614">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="2caf3-615">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="2caf3-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="2caf3-616">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="2caf3-617">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="2caf3-617">Consume configuration during app startup</span></span>

<span data-ttu-id="2caf3-618">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2caf3-619">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="2caf3-620">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="2caf3-621">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="2caf3-622">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2caf3-623">`AddCommandLine`çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="2caf3-624">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2caf3-625">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2caf3-626">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="2caf3-626">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2caf3-627">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2caf3-628">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-628">Environment variables.</span></span>

<span data-ttu-id="2caf3-629">`CreateDefaultBuilder`Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="2caf3-630">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="2caf3-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="2caf3-631">`CreateDefaultBuilder`Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="2caf3-632">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="2caf3-633">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2caf3-634">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="2caf3-635">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="2caf3-635">**Example**</span></span>

<span data-ttu-id="2caf3-636">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="2caf3-637">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="2caf3-638">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="2caf3-639">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2caf3-640">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="2caf3-641">Arguments</span><span class="sxs-lookup"><span data-stu-id="2caf3-641">Arguments</span></span>

<span data-ttu-id="2caf3-642">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="2caf3-643">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="2caf3-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="2caf3-644">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="2caf3-644">Key prefix</span></span>               | <span data-ttu-id="2caf3-645">Örnek</span><span class="sxs-lookup"><span data-stu-id="2caf3-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="2caf3-646">Ön ek yok</span><span class="sxs-lookup"><span data-stu-id="2caf3-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="2caf3-647">İki kısa çizgi ( `--` )</span><span class="sxs-lookup"><span data-stu-id="2caf3-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="2caf3-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="2caf3-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="2caf3-649">Eğik çizgi ( `/` )</span><span class="sxs-lookup"><span data-stu-id="2caf3-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="2caf3-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="2caf3-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="2caf3-651">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="2caf3-652">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="2caf3-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="2caf3-653">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="2caf3-653">Switch mappings</span></span>

<span data-ttu-id="2caf3-654">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="2caf3-655">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2caf3-656">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2caf3-657">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2caf3-658">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2caf3-659">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="2caf3-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2caf3-660">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="2caf3-661">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2caf3-662">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2caf3-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="2caf3-663">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="2caf3-664">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="2caf3-665">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="2caf3-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2caf3-666">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2caf3-667">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="2caf3-668">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="2caf3-669">Anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-669">Key</span></span>       | <span data-ttu-id="2caf3-670">Değer</span><span class="sxs-lookup"><span data-stu-id="2caf3-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="2caf3-671">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="2caf3-672">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="2caf3-673">Anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-673">Key</span></span>               | <span data-ttu-id="2caf3-674">Değer</span><span class="sxs-lookup"><span data-stu-id="2caf3-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="2caf3-675">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="2caf3-676">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="2caf3-677">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2caf3-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="2caf3-679">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2caf3-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2caf3-680">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="2caf3-681">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2caf3-682">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2caf3-683">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="2caf3-684">*appsettings.json* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="2caf3-684">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2caf3-685">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2caf3-686">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-686">Command-line arguments.</span></span>

<span data-ttu-id="2caf3-687">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="2caf3-688">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="2caf3-689">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="2caf3-690">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="2caf3-691">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="2caf3-691">**Example**</span></span>

<span data-ttu-id="2caf3-692">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="2caf3-693">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-693">Run the sample app.</span></span> <span data-ttu-id="2caf3-694">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2caf3-695">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="2caf3-696">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="2caf3-697">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="2caf3-698">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="2caf3-699">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2caf3-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="2caf3-700">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="2caf3-700">Prefixes</span></span>

<span data-ttu-id="2caf3-701">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="2caf3-702">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="2caf3-703">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="2caf3-704">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="2caf3-705">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2caf3-706">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="2caf3-706">**Connection string prefixes**</span></span>

<span data-ttu-id="2caf3-707">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2caf3-708">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2caf3-709">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="2caf3-709">Connection string prefix</span></span> | <span data-ttu-id="2caf3-710">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="2caf3-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2caf3-711">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="2caf3-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2caf3-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="2caf3-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2caf3-713">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="2caf3-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2caf3-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2caf3-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2caf3-715">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="2caf3-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2caf3-716">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2caf3-717">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="2caf3-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2caf3-718">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="2caf3-718">Environment variable key</span></span> | <span data-ttu-id="2caf3-719">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="2caf3-719">Converted configuration key</span></span> | <span data-ttu-id="2caf3-720">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="2caf3-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-721">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="2caf3-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-722">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2caf3-723">Değer:`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2caf3-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-724">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2caf3-725">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2caf3-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2caf3-726">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2caf3-727">Değer:`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2caf3-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="2caf3-728">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="2caf3-728">**Example**</span></span>

<span data-ttu-id="2caf3-729">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="2caf3-730">Ada`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="2caf3-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="2caf3-731">Değer:`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="2caf3-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="2caf3-732">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="2caf3-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="2caf3-733">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-733">File Configuration Provider</span></span>

<span data-ttu-id="2caf3-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2caf3-735">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="2caf3-736">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2caf3-737">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="2caf3-738">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2caf3-739">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-739">INI Configuration Provider</span></span>

<span data-ttu-id="2caf3-740">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2caf3-741">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2caf3-742">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="2caf3-743">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="2caf3-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="2caf3-744">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="2caf3-744">Whether the file is optional.</span></span>
* <span data-ttu-id="2caf3-745">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2caf3-746"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2caf3-747">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2caf3-748">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="2caf3-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="2caf3-749">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2caf3-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-750">section0:key0</span></span>
* <span data-ttu-id="2caf3-751">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-751">section0:key1</span></span>
* <span data-ttu-id="2caf3-752">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-752">section1:subsection:key</span></span>
* <span data-ttu-id="2caf3-753">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-753">section2:subsection0:key</span></span>
* <span data-ttu-id="2caf3-754">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="2caf3-755">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-755">JSON Configuration Provider</span></span>

<span data-ttu-id="2caf3-756">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="2caf3-757">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2caf3-758">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="2caf3-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="2caf3-759">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="2caf3-759">Whether the file is optional.</span></span>
* <span data-ttu-id="2caf3-760">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2caf3-761"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2caf3-762">`AddJsonFile`, ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2caf3-763">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="2caf3-764">*appsettings.js*: Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="2caf3-764">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="2caf3-765">Dosyanın ortam sürümü, *appsettings.js* dosya tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-765">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="2caf3-766">*appSettings. {Environment}. JSON*: dosyanın ortam sürümü [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-766">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="2caf3-767">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2caf3-768">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2caf3-769">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-769">Environment variables.</span></span>
* <span data-ttu-id="2caf3-770">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="2caf3-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2caf3-771">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="2caf3-771">Command-line arguments.</span></span>

<span data-ttu-id="2caf3-772">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="2caf3-773">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="2caf3-774">`ConfigureAppConfiguration`Ana bilgisayarı oluştururken, *appsettings.json* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2caf3-775">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="2caf3-775">**Example**</span></span>

<span data-ttu-id="2caf3-776">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="2caf3-777">`AddJsonFile` *' Dekiappsettings.js*yapılandırmayı yükleyen ilk çağrı:</span><span class="sxs-lookup"><span data-stu-id="2caf3-777">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="2caf3-778">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="2caf3-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="2caf3-779">Örnek uygulamada *appsettings.Development.js* için aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="2caf3-780">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-780">Run the sample app.</span></span> <span data-ttu-id="2caf3-781">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2caf3-782">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="2caf3-783">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="2caf3-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="2caf3-784">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="2caf3-785">Dosyadaki *Özellikler/launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="2caf3-786">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="2caf3-787">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="2caf3-788">*appsettings.Development.jsüzerindeki* ayarlar artık *appsettings.jsüzerindeki*ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-788">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="2caf3-789">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="2caf3-790">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-790">XML Configuration Provider</span></span>

<span data-ttu-id="2caf3-791">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2caf3-792">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2caf3-793">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="2caf3-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="2caf3-794">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="2caf3-794">Whether the file is optional.</span></span>
* <span data-ttu-id="2caf3-795">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2caf3-796"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2caf3-797">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="2caf3-798">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="2caf3-799">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2caf3-800">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="2caf3-801">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2caf3-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-802">section0:key0</span></span>
* <span data-ttu-id="2caf3-803">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-803">section0:key1</span></span>
* <span data-ttu-id="2caf3-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-804">section1:key0</span></span>
* <span data-ttu-id="2caf3-805">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-805">section1:key1</span></span>

<span data-ttu-id="2caf3-806">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="2caf3-807">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2caf3-808">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-808">section:section0:key:key0</span></span>
* <span data-ttu-id="2caf3-809">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-809">section:section0:key:key1</span></span>
* <span data-ttu-id="2caf3-810">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-810">section:section1:key:key0</span></span>
* <span data-ttu-id="2caf3-811">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-811">section:section1:key:key1</span></span>

<span data-ttu-id="2caf3-812">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2caf3-813">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2caf3-814">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="2caf3-814">key:attribute</span></span>
* <span data-ttu-id="2caf3-815">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="2caf3-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2caf3-816">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="2caf3-817">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2caf3-818">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-818">The key is the file name.</span></span> <span data-ttu-id="2caf3-819">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-819">The value contains the file's contents.</span></span> <span data-ttu-id="2caf3-820">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2caf3-821">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2caf3-822">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2caf3-823">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="2caf3-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="2caf3-824">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="2caf3-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2caf3-825">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2caf3-826">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2caf3-827">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2caf3-828">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="2caf3-829">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-829">Memory Configuration Provider</span></span>

<span data-ttu-id="2caf3-830">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2caf3-831">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2caf3-832">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="2caf3-833">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="2caf3-834">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="2caf3-835">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="2caf3-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="2caf3-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="2caf3-836">GetValue</span></span>

<span data-ttu-id="2caf3-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="2caf3-838">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="2caf3-838">An overload accepts a default value.</span></span>

<span data-ttu-id="2caf3-839">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="2caf3-839">The following example:</span></span>

* <span data-ttu-id="2caf3-840">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="2caf3-841">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="2caf3-842">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="2caf3-843">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2caf3-844">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="2caf3-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2caf3-845">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="2caf3-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="2caf3-846">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="2caf3-847">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="2caf3-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-848">section0:key0</span></span>
* <span data-ttu-id="2caf3-849">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-849">section0:key1</span></span>
* <span data-ttu-id="2caf3-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-850">section1:key0</span></span>
* <span data-ttu-id="2caf3-851">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-851">section1:key1</span></span>
* <span data-ttu-id="2caf3-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="2caf3-853">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="2caf3-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="2caf3-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="2caf3-855">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="2caf3-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="2caf3-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="2caf3-856">GetSection</span></span>

<span data-ttu-id="2caf3-857">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="2caf3-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2caf3-858"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="2caf3-859">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="2caf3-860">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="2caf3-861">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2caf3-862">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2caf3-863">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="2caf3-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2caf3-864">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="2caf3-865">GetChildren</span><span class="sxs-lookup"><span data-stu-id="2caf3-865">GetChildren</span></span>

<span data-ttu-id="2caf3-866">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="2caf3-867">Var</span><span class="sxs-lookup"><span data-stu-id="2caf3-867">Exists</span></span>

<span data-ttu-id="2caf3-868">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="2caf3-869">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="2caf3-870">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="2caf3-870">Bind to an object graph</span></span>

<span data-ttu-id="2caf3-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>, bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="2caf3-872">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="2caf3-873">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="2caf3-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="2caf3-874">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow.xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="2caf3-875">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="2caf3-876">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="2caf3-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="2caf3-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="2caf3-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2caf3-878">`Get<T>`, kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="2caf3-879">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2caf3-880">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="2caf3-880">Bind an array to a class</span></span>

<span data-ttu-id="2caf3-881">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="2caf3-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="2caf3-882">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="2caf3-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2caf3-883">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="2caf3-884">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-884">Binding is provided by convention.</span></span> <span data-ttu-id="2caf3-885">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="2caf3-886">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="2caf3-886">**In-memory array processing**</span></span>

<span data-ttu-id="2caf3-887">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="2caf3-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="2caf3-888">Anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-888">Key</span></span>             | <span data-ttu-id="2caf3-889">Değer</span><span class="sxs-lookup"><span data-stu-id="2caf3-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2caf3-890">dizi: girdiler: 0</span><span class="sxs-lookup"><span data-stu-id="2caf3-890">array:entries:0</span></span> | <span data-ttu-id="2caf3-891">value0</span><span class="sxs-lookup"><span data-stu-id="2caf3-891">value0</span></span> |
| <span data-ttu-id="2caf3-892">dizi: girdiler: 1</span><span class="sxs-lookup"><span data-stu-id="2caf3-892">array:entries:1</span></span> | <span data-ttu-id="2caf3-893">value1</span><span class="sxs-lookup"><span data-stu-id="2caf3-893">value1</span></span> |
| <span data-ttu-id="2caf3-894">dizi: girdiler: 2</span><span class="sxs-lookup"><span data-stu-id="2caf3-894">array:entries:2</span></span> | <span data-ttu-id="2caf3-895">value2</span><span class="sxs-lookup"><span data-stu-id="2caf3-895">value2</span></span> |
| <span data-ttu-id="2caf3-896">dizi: girdiler: 4</span><span class="sxs-lookup"><span data-stu-id="2caf3-896">array:entries:4</span></span> | <span data-ttu-id="2caf3-897">value4</span><span class="sxs-lookup"><span data-stu-id="2caf3-897">value4</span></span> |
| <span data-ttu-id="2caf3-898">dizi: girdiler: 5</span><span class="sxs-lookup"><span data-stu-id="2caf3-898">array:entries:5</span></span> | <span data-ttu-id="2caf3-899">value5</span><span class="sxs-lookup"><span data-stu-id="2caf3-899">value5</span></span> |

<span data-ttu-id="2caf3-900">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="2caf3-901">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="2caf3-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="2caf3-902">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="2caf3-903">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="2caf3-904">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="2caf3-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="2caf3-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="2caf3-906">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="2caf3-907">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="2caf3-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2caf3-908">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="2caf3-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2caf3-909">0</span><span class="sxs-lookup"><span data-stu-id="2caf3-909">0</span></span>                            | <span data-ttu-id="2caf3-910">value0</span><span class="sxs-lookup"><span data-stu-id="2caf3-910">value0</span></span>                       |
| <span data-ttu-id="2caf3-911">1</span><span class="sxs-lookup"><span data-stu-id="2caf3-911">1</span></span>                            | <span data-ttu-id="2caf3-912">value1</span><span class="sxs-lookup"><span data-stu-id="2caf3-912">value1</span></span>                       |
| <span data-ttu-id="2caf3-913">2</span><span class="sxs-lookup"><span data-stu-id="2caf3-913">2</span></span>                            | <span data-ttu-id="2caf3-914">value2</span><span class="sxs-lookup"><span data-stu-id="2caf3-914">value2</span></span>                       |
| <span data-ttu-id="2caf3-915">3</span><span class="sxs-lookup"><span data-stu-id="2caf3-915">3</span></span>                            | <span data-ttu-id="2caf3-916">value4</span><span class="sxs-lookup"><span data-stu-id="2caf3-916">value4</span></span>                       |
| <span data-ttu-id="2caf3-917">4</span><span class="sxs-lookup"><span data-stu-id="2caf3-917">4</span></span>                            | <span data-ttu-id="2caf3-918">value5</span><span class="sxs-lookup"><span data-stu-id="2caf3-918">value5</span></span>                       |

<span data-ttu-id="2caf3-919">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2caf3-920">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2caf3-921">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2caf3-922">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="2caf3-923">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="2caf3-924">*missing_value.js*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-924">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="2caf3-925">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="2caf3-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="2caf3-926">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="2caf3-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="2caf3-927">Anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-927">Key</span></span>             | <span data-ttu-id="2caf3-928">Değer</span><span class="sxs-lookup"><span data-stu-id="2caf3-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2caf3-929">dizi: girdiler: 3</span><span class="sxs-lookup"><span data-stu-id="2caf3-929">array:entries:3</span></span> | <span data-ttu-id="2caf3-930">value3</span><span class="sxs-lookup"><span data-stu-id="2caf3-930">value3</span></span> |

<span data-ttu-id="2caf3-931">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="2caf3-932">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="2caf3-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2caf3-933">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="2caf3-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2caf3-934">0</span><span class="sxs-lookup"><span data-stu-id="2caf3-934">0</span></span>                            | <span data-ttu-id="2caf3-935">value0</span><span class="sxs-lookup"><span data-stu-id="2caf3-935">value0</span></span>                       |
| <span data-ttu-id="2caf3-936">1</span><span class="sxs-lookup"><span data-stu-id="2caf3-936">1</span></span>                            | <span data-ttu-id="2caf3-937">value1</span><span class="sxs-lookup"><span data-stu-id="2caf3-937">value1</span></span>                       |
| <span data-ttu-id="2caf3-938">2</span><span class="sxs-lookup"><span data-stu-id="2caf3-938">2</span></span>                            | <span data-ttu-id="2caf3-939">value2</span><span class="sxs-lookup"><span data-stu-id="2caf3-939">value2</span></span>                       |
| <span data-ttu-id="2caf3-940">3</span><span class="sxs-lookup"><span data-stu-id="2caf3-940">3</span></span>                            | <span data-ttu-id="2caf3-941">value3</span><span class="sxs-lookup"><span data-stu-id="2caf3-941">value3</span></span>                       |
| <span data-ttu-id="2caf3-942">4</span><span class="sxs-lookup"><span data-stu-id="2caf3-942">4</span></span>                            | <span data-ttu-id="2caf3-943">value4</span><span class="sxs-lookup"><span data-stu-id="2caf3-943">value4</span></span>                       |
| <span data-ttu-id="2caf3-944">5</span><span class="sxs-lookup"><span data-stu-id="2caf3-944">5</span></span>                            | <span data-ttu-id="2caf3-945">value5</span><span class="sxs-lookup"><span data-stu-id="2caf3-945">value5</span></span>                       |

<span data-ttu-id="2caf3-946">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="2caf3-946">**JSON array processing**</span></span>

<span data-ttu-id="2caf3-947">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="2caf3-948">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="2caf3-949">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="2caf3-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="2caf3-950">Anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-950">Key</span></span>                     | <span data-ttu-id="2caf3-951">Değer</span><span class="sxs-lookup"><span data-stu-id="2caf3-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="2caf3-952">json_array: anahtar</span><span class="sxs-lookup"><span data-stu-id="2caf3-952">json_array:key</span></span>          | <span data-ttu-id="2caf3-953">değer EA</span><span class="sxs-lookup"><span data-stu-id="2caf3-953">valueA</span></span> |
| <span data-ttu-id="2caf3-954">json_array: alt bölüm: 0</span><span class="sxs-lookup"><span data-stu-id="2caf3-954">json_array:subsection:0</span></span> | <span data-ttu-id="2caf3-955">valueB</span><span class="sxs-lookup"><span data-stu-id="2caf3-955">valueB</span></span> |
| <span data-ttu-id="2caf3-956">json_array: alt bölüm: 1</span><span class="sxs-lookup"><span data-stu-id="2caf3-956">json_array:subsection:1</span></span> | <span data-ttu-id="2caf3-957">değer EC</span><span class="sxs-lookup"><span data-stu-id="2caf3-957">valueC</span></span> |
| <span data-ttu-id="2caf3-958">json_array: alt bölüm: 2</span><span class="sxs-lookup"><span data-stu-id="2caf3-958">json_array:subsection:2</span></span> | <span data-ttu-id="2caf3-959">Değerler</span><span class="sxs-lookup"><span data-stu-id="2caf3-959">valueD</span></span> |

<span data-ttu-id="2caf3-960">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="2caf3-961">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="2caf3-962">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="2caf3-963">`JsonArrayExample.Subsection`İndeks</span><span class="sxs-lookup"><span data-stu-id="2caf3-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="2caf3-964">`JsonArrayExample.Subsection`Deeri</span><span class="sxs-lookup"><span data-stu-id="2caf3-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="2caf3-965">0</span><span class="sxs-lookup"><span data-stu-id="2caf3-965">0</span></span>                                   | <span data-ttu-id="2caf3-966">valueB</span><span class="sxs-lookup"><span data-stu-id="2caf3-966">valueB</span></span>                              |
| <span data-ttu-id="2caf3-967">1</span><span class="sxs-lookup"><span data-stu-id="2caf3-967">1</span></span>                                   | <span data-ttu-id="2caf3-968">değer EC</span><span class="sxs-lookup"><span data-stu-id="2caf3-968">valueC</span></span>                              |
| <span data-ttu-id="2caf3-969">2</span><span class="sxs-lookup"><span data-stu-id="2caf3-969">2</span></span>                                   | <span data-ttu-id="2caf3-970">Değerler</span><span class="sxs-lookup"><span data-stu-id="2caf3-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="2caf3-971">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="2caf3-971">Custom configuration provider</span></span>

<span data-ttu-id="2caf3-972">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2caf3-973">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2caf3-974">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2caf3-975">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2caf3-976">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="2caf3-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2caf3-977">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="2caf3-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2caf3-978">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="2caf3-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2caf3-979">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="2caf3-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2caf3-980">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-980">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2caf3-981">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2caf3-982">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-982">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2caf3-983">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2caf3-984">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-984">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2caf3-985">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="2caf3-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2caf3-986">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="2caf3-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="2caf3-987">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-987">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2caf3-988">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2caf3-989">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="2caf3-989">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2caf3-990">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2caf3-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="2caf3-991">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="2caf3-991">Access configuration during startup</span></span>

<span data-ttu-id="2caf3-992">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2caf3-993">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="2caf3-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="2caf3-994">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2caf3-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="2caf3-995">RazorSayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2caf3-995">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="2caf3-996">Bir Razor Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft.Extensions.Configurlama ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2caf3-996">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="2caf3-997">RazorSayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="2caf3-997">In a Razor Pages page:</span></span>

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

<span data-ttu-id="2caf3-998">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="2caf3-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2caf3-999">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="2caf3-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="2caf3-1000">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2caf3-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2caf3-1001">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2caf3-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2caf3-1002">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2caf3-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
