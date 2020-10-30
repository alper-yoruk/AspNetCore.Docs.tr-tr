---
title: ASP.NET Core yapılandırma
author: rick-anderson
description: ASP.NET Core uygulamasını yapılandırmak için yapılandırma API 'sini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/configuration/index
ms.openlocfilehash: 9e744ec6d0f0dd72bded8284e98fd9ce53056b84
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057979"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="94e53-103">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="94e53-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="94e53-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94e53-105">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="94e53-106">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="94e53-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="94e53-107">Gibi ayarlar dosyaları *:::no-loc(appsettings.json):::*</span><span class="sxs-lookup"><span data-stu-id="94e53-107">Settings files, such as *:::no-loc(appsettings.json):::*</span></span>
* <span data-ttu-id="94e53-108">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-108">Environment variables</span></span>
* <span data-ttu-id="94e53-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="94e53-109">Azure Key Vault</span></span>
* <span data-ttu-id="94e53-110">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-110">Azure App Configuration</span></span>
* <span data-ttu-id="94e53-111">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-111">Command-line arguments</span></span>
* <span data-ttu-id="94e53-112">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="94e53-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="94e53-113">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="94e53-113">Directory files</span></span>
* <span data-ttu-id="94e53-114">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="94e53-114">In-memory .NET objects</span></span>

<span data-ttu-id="94e53-115">Bu konuda ASP.NET Core yapılandırma hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="94e53-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="94e53-116">Konsol uygulamalarında yapılandırmayı kullanma hakkında daha fazla bilgi için bkz. [.NET yapılandırması](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="94e53-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="94e53-117">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="94e53-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="94e53-118">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-118">Default configuration</span></span>

<span data-ttu-id="94e53-119">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="94e53-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="94e53-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="94e53-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="94e53-121">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="94e53-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="94e53-122">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="94e53-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="94e53-123">[:::no-loc(appsettings.json):::](#appsettingsjson)[JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanma.</span><span class="sxs-lookup"><span data-stu-id="94e53-123">[:::no-loc(appsettings.json):::](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="94e53-124">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="94e53-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="94e53-125">Örneğin, *appSettings* . ***Üretim \* \* _._json* ve *appSettings* . \* \* \* geliştirme** _._json \*.</span><span class="sxs-lookup"><span data-stu-id="94e53-125">For example, *appsettings* . ***Production\*\*_._json* and *appsettings* .\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="94e53-126">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="94e53-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="94e53-127">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="94e53-128">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="94e53-129">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="94e53-130">Örneğin, hem hem de `MyKey` *:::no-loc(appsettings.json):::* ortamında ayarlandıysa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-130">For example, if `MyKey` is set in both *:::no-loc(appsettings.json):::* and the environment, the environment value is used.</span></span> <span data-ttu-id="94e53-131">Varsayılan yapılandırma sağlayıcılarını kullanarak,  [komut satırı yapılandırma sağlayıcısı](#clcp) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="94e53-132">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="94e53-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="94e53-133">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### :::no-loc(appsettings.json):::

<span data-ttu-id="94e53-134">Aşağıdaki dosyayı göz önünde bulundurun *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="94e53-134">Consider the following *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="94e53-135">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-136">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="94e53-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="94e53-137">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings* . ***Üretim \* \* _._json* ve *appSettings* . \* \* \* geliştirme** _._json \* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="94e53-137">*appsettings.*`Environment`*.json* : For example, the *appsettings* . ***Production\*\*_._json* and *appsettings* .\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="94e53-138">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="94e53-139">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="94e53-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="94e53-140">*appSettings* . `Environment` . *JSON* değerleri içindeki anahtarları geçersiz kılar *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94e53-140">*appsettings* .`Environment`. *json* values override keys in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="94e53-141">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="94e53-141">For example, by default:</span></span>

* <span data-ttu-id="94e53-142">Geliştirme aşamasında *appSettings* . \* **geliştirme** _._json \* yapılandırması, içinde bulunan değerlerin üzerine yazar *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94e53-142">In development, *appsettings* .\* **Development** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::* .</span></span>
* <span data-ttu-id="94e53-143">Üretimde, *appSettings* . \* **Üretim** _._json \* yapılandırması, içinde bulunan değerlerin üzerine yazar *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94e53-143">In production, *appsettings* .\* **Production** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="94e53-144">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="94e53-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="94e53-145">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="94e53-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="94e53-146">[Varsayılan](#default) yapılandırmayı kullanarak *:::no-loc(appsettings.json):::* ve *appSettings.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-146">Using the [default](#default) configuration, the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="94e53-147">Ve appSettings üzerinde yapılan *:::no-loc(appsettings.json):::* değişiklikler *.* `Environment` *. JSON* dosyası \* **After _ sonra** , uygulama başlatılır [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="94e53-147">Changes made to the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* file \* **after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="94e53-148">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="94e53-149">Hizmet toplamayı birleştirme</span><span class="sxs-lookup"><span data-stu-id="94e53-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="94e53-150">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="94e53-150">Security and secret manager</span></span>

<span data-ttu-id="94e53-151">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="94e53-151">Configuration data guidelines:</span></span>

<span data-ttu-id="94e53-152">_ Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="94e53-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="94e53-153">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="94e53-154">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="94e53-155">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="94e53-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="94e53-156">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) yapılandırma ayarlarını *:::no-loc(appsettings.json):::* ve appSettings sonrasında okur *.* `Environment` *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="94e53-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* .</span></span>

<span data-ttu-id="94e53-157">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="94e53-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="94e53-158"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="94e53-159">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="94e53-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="94e53-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="94e53-161">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="94e53-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="94e53-162">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-162">Environment variables</span></span>

<span data-ttu-id="94e53-163">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> yapılandırma, appSettings sonrasında anahtar-değer çiftleri ' nden yapılandırmayı yükler *:::no-loc(appsettings.json):::* *.* `Environment` *. JSON* ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="94e53-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="94e53-164">Bu nedenle, ortamdan okunan anahtar değerleri, öğesinden okunan değerleri geçersiz *:::no-loc(appsettings.json):::* kılar *.* `Environment` *. JSON* ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="94e53-164">Therefore, key values read from the environment override values read from *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="94e53-165">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="94e53-165">The following `set` commands:</span></span>

* <span data-ttu-id="94e53-166">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="94e53-167">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="94e53-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="94e53-168">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="94e53-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="94e53-169">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="94e53-169">The preceding environment settings:</span></span>

* <span data-ttu-id="94e53-170">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="94e53-171">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="94e53-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="94e53-172">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="94e53-173">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="94e53-174">`/M` değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="94e53-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="94e53-175">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="94e53-176">Önceki komutların geçersiz kılındığını ve appSettings 'i test etmek *:::no-loc(appsettings.json):::* *.* `Environment` *. JSON* :</span><span class="sxs-lookup"><span data-stu-id="94e53-176">To test that the preceding commands override *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* :</span></span>

* <span data-ttu-id="94e53-177">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="94e53-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="94e53-178">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="94e53-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="94e53-179"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="94e53-180">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="94e53-180">In the preceding code:</span></span>

* <span data-ttu-id="94e53-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="94e53-182">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="94e53-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="94e53-183">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="94e53-184">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="94e53-185">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="94e53-186">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="94e53-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="94e53-187">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="94e53-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="94e53-188">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="94e53-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="94e53-189">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="94e53-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="94e53-190">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="94e53-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="94e53-191">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="94e53-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="94e53-192">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="94e53-193">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="94e53-193">Exposed as environment variables.</span></span>

<span data-ttu-id="94e53-194">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="94e53-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="94e53-195">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="94e53-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="94e53-196">launchSettings.jsüzerinde ayarlanan ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="94e53-197">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="94e53-198">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="94e53-198">Command-line</span></span>

<span data-ttu-id="94e53-199">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="94e53-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="94e53-200">*:::no-loc(appsettings.json):::* ve *appSettings* . `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="94e53-200">*:::no-loc(appsettings.json):::* and *appsettings* .`Environment`. *json* files.</span></span>
* <span data-ttu-id="94e53-201">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="94e53-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="94e53-202">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-202">Environment variables.</span></span>

<span data-ttu-id="94e53-203">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="94e53-204">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-204">Command-line arguments</span></span>

<span data-ttu-id="94e53-205">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="94e53-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="94e53-206">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="94e53-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="94e53-207">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="94e53-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="94e53-208">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="94e53-208">The key value:</span></span>

* <span data-ttu-id="94e53-209">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="94e53-210">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="94e53-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="94e53-211">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="94e53-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="94e53-212">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="94e53-213">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="94e53-213">Switch mappings</span></span>

<span data-ttu-id="94e53-214">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="94e53-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="94e53-215">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="94e53-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="94e53-216">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="94e53-217">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="94e53-218">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="94e53-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="94e53-219">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="94e53-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="94e53-220">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="94e53-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="94e53-221">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="94e53-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="94e53-222">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="94e53-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="94e53-223">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="94e53-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-224">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="94e53-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="94e53-225">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="94e53-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="94e53-226">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="94e53-227">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="94e53-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="94e53-228">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="94e53-228">Hierarchical configuration data</span></span>

<span data-ttu-id="94e53-229">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="94e53-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="94e53-230">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *:::no-loc(appsettings.json):::* dosyayı içerir:</span><span class="sxs-lookup"><span data-stu-id="94e53-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="94e53-231">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-232">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="94e53-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="94e53-233">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="94e53-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="94e53-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="94e53-235">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94e53-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="94e53-236">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="94e53-236">Configuration keys and values</span></span>

<span data-ttu-id="94e53-237">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="94e53-237">Configuration keys:</span></span>

* <span data-ttu-id="94e53-238">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-238">Are case-insensitive.</span></span> <span data-ttu-id="94e53-239">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="94e53-240">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="94e53-241">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="94e53-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="94e53-242">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="94e53-242">Hierarchical keys</span></span>
  * <span data-ttu-id="94e53-243">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="94e53-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="94e53-244">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="94e53-245">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="94e53-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="94e53-246">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="94e53-247">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="94e53-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="94e53-248">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="94e53-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="94e53-249">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94e53-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="94e53-250">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="94e53-250">Configuration values:</span></span>

* <span data-ttu-id="94e53-251">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="94e53-251">Are strings.</span></span>
* <span data-ttu-id="94e53-252">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="94e53-253">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="94e53-253">Configuration providers</span></span>

<span data-ttu-id="94e53-254">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="94e53-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="94e53-255">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="94e53-255">Provider</span></span> | <span data-ttu-id="94e53-256">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="94e53-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="94e53-257">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="94e53-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="94e53-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="94e53-259">Azure uygulama yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="94e53-260">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="94e53-261">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="94e53-262">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="94e53-262">Command-line parameters</span></span> |
| [<span data-ttu-id="94e53-263">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="94e53-264">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="94e53-264">Custom source</span></span> |
| [<span data-ttu-id="94e53-265">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="94e53-266">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-266">Environment variables</span></span> |
| [<span data-ttu-id="94e53-267">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="94e53-268">INı, JSON ve XML dosyaları</span><span class="sxs-lookup"><span data-stu-id="94e53-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="94e53-269">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="94e53-270">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="94e53-270">Directory files</span></span> |
| [<span data-ttu-id="94e53-271">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="94e53-272">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="94e53-272">In-memory collections</span></span> |
| [<span data-ttu-id="94e53-273">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="94e53-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="94e53-274">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="94e53-274">File in the user profile directory</span></span> |

<span data-ttu-id="94e53-275">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="94e53-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="94e53-276">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="94e53-277">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="94e53-277">A typical sequence of configuration providers is:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="94e53-278">*appSettings* . `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="94e53-278">*appsettings* .`Environment`. *json*</span></span>
1. [<span data-ttu-id="94e53-279">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="94e53-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="94e53-280">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="94e53-281">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="94e53-282">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="94e53-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="94e53-283">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="94e53-284">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="94e53-284">Connection string prefixes</span></span>

<span data-ttu-id="94e53-285">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="94e53-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="94e53-286">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="94e53-287">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="94e53-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="94e53-288">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="94e53-288">Connection string prefix</span></span> | <span data-ttu-id="94e53-289">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="94e53-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="94e53-290">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="94e53-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="94e53-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="94e53-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="94e53-292">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="94e53-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="94e53-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="94e53-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="94e53-294">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="94e53-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="94e53-295">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="94e53-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="94e53-296">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="94e53-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="94e53-297">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="94e53-297">Environment variable key</span></span> | <span data-ttu-id="94e53-298">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="94e53-298">Converted configuration key</span></span> | <span data-ttu-id="94e53-299">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="94e53-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-300">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="94e53-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-301">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="94e53-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="94e53-302">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="94e53-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-303">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="94e53-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="94e53-304">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="94e53-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-305">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="94e53-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="94e53-306">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="94e53-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="94e53-307">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-307">File configuration provider</span></span>

<span data-ttu-id="94e53-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> , dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="94e53-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="94e53-309">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="94e53-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="94e53-310">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="94e53-311">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="94e53-312">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="94e53-313">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-313">INI configuration provider</span></span>

<span data-ttu-id="94e53-314">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="94e53-315">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="94e53-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="94e53-316">Yukarıdaki kodda, *MyIniConfig.ini* ve  *Myıniconfig* içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="94e53-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig* .`Environment`. *ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="94e53-317">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="94e53-318">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="94e53-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="94e53-319">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyIniConfig.ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="94e53-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="94e53-320">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="94e53-321">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-321">JSON configuration provider</span></span>

<span data-ttu-id="94e53-322">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="94e53-323">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-323">Overloads can specify:</span></span>

* <span data-ttu-id="94e53-324">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="94e53-324">Whether the file is optional.</span></span>
* <span data-ttu-id="94e53-325">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="94e53-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="94e53-326">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="94e53-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="94e53-327">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="94e53-327">The preceding code:</span></span>

* <span data-ttu-id="94e53-328">Dosyadaki *MyConfig.js* yüklemek için JSON yapılandırma sağlayıcısını aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="94e53-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="94e53-329">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="94e53-330">`reloadOnChange: true` : Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="94e53-331">Dosyadaki *MyConfig.js* önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="94e53-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="94e53-332">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig.js* dosya geçersiz kılma ayarı ayarları.</span><span class="sxs-lookup"><span data-stu-id="94e53-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="94e53-333">Genellikle \* **yok** _, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanan değerleri geçersiz kılan özel bir JSON dosyası istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="94e53-333">You typically \* **don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="94e53-334">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="94e53-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="94e53-335">Yukarıdaki kodda, \* ve  *MyConfig* üzerindeki _MyConfig.js`Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="94e53-335">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig* .`Environment`. *json* files:</span></span>

* <span data-ttu-id="94e53-336">Ve appSettings içindeki ayarları geçersiz kılın *:::no-loc(appsettings.json):::* *appsettings* . `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="94e53-336">Override settings in the *:::no-loc(appsettings.json):::* and *appsettings* .`Environment`. *json* files.</span></span>
* <span data-ttu-id="94e53-337">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="94e53-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="94e53-338">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , dosyasında aşağıdaki *MyConfig.js* içerir:</span><span class="sxs-lookup"><span data-stu-id="94e53-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="94e53-339">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="94e53-340">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-340">XML configuration provider</span></span>

<span data-ttu-id="94e53-341">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="94e53-342">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="94e53-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="94e53-343">Yukarıdaki kodda, *MyXMLFile.xml* ve  *myXMLfile* içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="94e53-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile* .`Environment`. *xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="94e53-344">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="94e53-345">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="94e53-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="94e53-346">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *MyXMLFile.xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="94e53-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="94e53-347">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-348">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="94e53-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="94e53-349">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-350">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="94e53-351">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="94e53-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="94e53-352">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="94e53-352">key:attribute</span></span>
* <span data-ttu-id="94e53-353">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="94e53-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="94e53-354">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="94e53-355">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="94e53-356">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-356">The key is the file name.</span></span> <span data-ttu-id="94e53-357">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-357">The value contains the file's contents.</span></span> <span data-ttu-id="94e53-358">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="94e53-359">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="94e53-360">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="94e53-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="94e53-361">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="94e53-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="94e53-362">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="94e53-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="94e53-363">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="94e53-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="94e53-364">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="94e53-365">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="94e53-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="94e53-366">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="94e53-367">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-367">Memory configuration provider</span></span>

<span data-ttu-id="94e53-368">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="94e53-369">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="94e53-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="94e53-370">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-371">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="94e53-372">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="94e53-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="94e53-373">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="94e53-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="94e53-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="94e53-374">GetValue</span></span>

<span data-ttu-id="94e53-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="94e53-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-376">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="94e53-377">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="94e53-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="94e53-378">İzleyen örnekler için, dosyasında aşağıdaki *MySubsection.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="94e53-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="94e53-379">Aşağıdaki kod yapılandırma sağlayıcılarına *MySubsection.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="94e53-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="94e53-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="94e53-380">GetSection</span></span>

<span data-ttu-id="94e53-381">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="94e53-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="94e53-382">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="94e53-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-383">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="94e53-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-384">`GetSection` hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="94e53-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="94e53-385">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="94e53-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="94e53-386">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="94e53-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="94e53-387">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="94e53-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="94e53-388">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="94e53-388">GetChildren and Exists</span></span>

<span data-ttu-id="94e53-389">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="94e53-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-390">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="94e53-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="94e53-391">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="94e53-391">Bind an array</span></span>

<span data-ttu-id="94e53-392">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="94e53-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="94e53-393">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="94e53-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="94e53-394">[Örnek karşıdan yükleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="94e53-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="94e53-395">Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.js* ekler:</span><span class="sxs-lookup"><span data-stu-id="94e53-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="94e53-396">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-397">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="94e53-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="94e53-398">Yukarıdaki çıktıda, Dizin 3 ' teMyArray.js' a karşılık gelen bir değer vardır `value40` `"4": "value40",` . *MyArray.json*</span><span class="sxs-lookup"><span data-stu-id="94e53-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json* .</span></span> <span data-ttu-id="94e53-399">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="94e53-400">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="94e53-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="94e53-401">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="94e53-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="94e53-402">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-403">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="94e53-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="94e53-404">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="94e53-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="94e53-405">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="94e53-406">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="94e53-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="94e53-407">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="94e53-408">Örnek indirmenin dosyasında aşağıdaki *Value3.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="94e53-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="94e53-409">Aşağıdaki kod, ve *üzerindeValue3.js* için yapılandırma içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="94e53-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="94e53-410">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-411">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="94e53-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="94e53-412">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="94e53-413">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-413">Custom configuration provider</span></span>

<span data-ttu-id="94e53-414">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="94e53-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="94e53-415">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="94e53-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="94e53-416">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="94e53-417">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="94e53-418">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="94e53-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="94e53-419">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="94e53-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="94e53-420">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="94e53-421">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="94e53-422">*Modeller/EFConfigurationValue. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-422">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="94e53-423">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="94e53-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="94e53-424">*Efconfigurationprovider/EFConfigurationContext. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="94e53-425">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="94e53-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="94e53-426">*Efconfigurationprovider/EFConfigurationSource. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="94e53-427">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="94e53-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="94e53-428">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="94e53-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="94e53-429">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="94e53-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="94e53-430">*Efconfigurationprovider/efconfigurationprovider. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="94e53-431">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="94e53-432">*Uzantılar/EntityFrameworkExtensions. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-432">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="94e53-433">Aşağıdaki kod, `EFConfigurationProvider` *program.cs* içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="94e53-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="94e53-434">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-434">Access configuration in Startup</span></span>

<span data-ttu-id="94e53-435">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="94e53-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="94e53-436">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="94e53-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="94e53-437">Sayfalarda erişim yapılandırması :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="94e53-437">Access configuration in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="94e53-438">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="94e53-438">The following code displays configuration data in a :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="94e53-439">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="94e53-440">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="94e53-441">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="94e53-442">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="94e53-443">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-443">Configure options with a delegate</span></span>

<span data-ttu-id="94e53-444">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="94e53-445">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="94e53-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="94e53-446">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="94e53-447">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="94e53-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="94e53-448">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="94e53-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="94e53-449">Önceki örnekte, `Option1` ve değerleri `Option2` içinde belirtilir *:::no-loc(appsettings.json):::* ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="94e53-449">In the preceding example, the values of `Option1` and `Option2` are specified in *:::no-loc(appsettings.json):::* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="94e53-450">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="94e53-450">Host versus app configuration</span></span>

<span data-ttu-id="94e53-451">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="94e53-452">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="94e53-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="94e53-453">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="94e53-454">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="94e53-455">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="94e53-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="94e53-456">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-456">Default host configuration</span></span>

<span data-ttu-id="94e53-457">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="94e53-458">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="94e53-459">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="94e53-460">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="94e53-461">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="94e53-462">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="94e53-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="94e53-463">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="94e53-464">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94e53-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="94e53-465">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="94e53-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="94e53-466">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="94e53-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="94e53-467">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-467">Other configuration</span></span>

<span data-ttu-id="94e53-468">Bu konu yalnızca *uygulama yapılandırması* ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="94e53-468">This topic only pertains to *app configuration* .</span></span> <span data-ttu-id="94e53-469">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="94e53-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="94e53-470">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="94e53-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="94e53-471">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="94e53-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="94e53-472">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="94e53-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="94e53-473">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="94e53-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="94e53-474">*launchSettings.js* ' de ayarlanan ortam değişkenleri, Sistem ortamındaki kümeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="94e53-475">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="94e53-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="94e53-476">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="94e53-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="94e53-477">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="94e53-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="94e53-478">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="94e53-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94e53-479">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94e53-479">Additional resources</span></span>

* [<span data-ttu-id="94e53-480">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="94e53-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94e53-481">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları* tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="94e53-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers* .</span></span> <span data-ttu-id="94e53-482">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="94e53-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="94e53-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="94e53-483">Azure Key Vault</span></span>
* <span data-ttu-id="94e53-484">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-484">Azure App Configuration</span></span>
* <span data-ttu-id="94e53-485">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-485">Command-line arguments</span></span>
* <span data-ttu-id="94e53-486">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="94e53-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="94e53-487">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="94e53-487">Directory files</span></span>
* <span data-ttu-id="94e53-488">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-488">Environment variables</span></span>
* <span data-ttu-id="94e53-489">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="94e53-489">In-memory .NET objects</span></span>
* <span data-ttu-id="94e53-490">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="94e53-490">Settings files</span></span>

<span data-ttu-id="94e53-491">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft.Extensions.Configurlama](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="94e53-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="94e53-492">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="94e53-493">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="94e53-494">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="94e53-495">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="94e53-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="94e53-496">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="94e53-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="94e53-497">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="94e53-497">Host versus app configuration</span></span>

<span data-ttu-id="94e53-498">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="94e53-499">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="94e53-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="94e53-500">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="94e53-501">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="94e53-502">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="94e53-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="94e53-503">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-503">Other configuration</span></span>

<span data-ttu-id="94e53-504">Bu konu yalnızca *uygulama yapılandırması* ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="94e53-504">This topic only pertains to *app configuration* .</span></span> <span data-ttu-id="94e53-505">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="94e53-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="94e53-506">*Üzerindelaunch.js* / *ÜzerindelaunchSettings.js* , geliştirme ortamı için açıklanan yapılandırma dosyalarıdır:</span><span class="sxs-lookup"><span data-stu-id="94e53-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="94e53-507">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="94e53-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="94e53-508">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="94e53-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="94e53-509">*web.config* , aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="94e53-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="94e53-510">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="94e53-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="94e53-511">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-511">Default configuration</span></span>

<span data-ttu-id="94e53-512">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="94e53-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="94e53-513">`CreateDefaultBuilder` uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="94e53-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="94e53-514">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="94e53-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="94e53-515">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="94e53-516">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="94e53-517">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="94e53-518">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="94e53-519">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="94e53-520">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="94e53-521">*:::no-loc(appsettings.json):::*[dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanma.</span><span class="sxs-lookup"><span data-stu-id="94e53-521">*:::no-loc(appsettings.json):::* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="94e53-522">*appSettings.* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="94e53-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="94e53-523">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="94e53-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="94e53-524">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="94e53-525">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="94e53-526">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="94e53-526">Security</span></span>

<span data-ttu-id="94e53-527">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="94e53-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="94e53-528">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="94e53-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="94e53-529">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="94e53-530">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="94e53-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="94e53-531">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="94e53-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="94e53-532"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="94e53-533">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="94e53-534">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94e53-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="94e53-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="94e53-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="94e53-536">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="94e53-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="94e53-537">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="94e53-537">Hierarchical configuration data</span></span>

<span data-ttu-id="94e53-538">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="94e53-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="94e53-539">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="94e53-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="94e53-540">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="94e53-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="94e53-541">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="94e53-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="94e53-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-542">section0:key0</span></span>
* <span data-ttu-id="94e53-543">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-543">section0:key1</span></span>
* <span data-ttu-id="94e53-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-544">section1:key0</span></span>
* <span data-ttu-id="94e53-545">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-545">section1:key1</span></span>

<span data-ttu-id="94e53-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="94e53-547">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94e53-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="94e53-548">Kurallar</span><span class="sxs-lookup"><span data-stu-id="94e53-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="94e53-549">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="94e53-549">Sources and providers</span></span>

<span data-ttu-id="94e53-550">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="94e53-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="94e53-551">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="94e53-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="94e53-552">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="94e53-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="94e53-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="94e53-554"><xref:Microsoft.Extensions.Configuration.IConfiguration>:::no-loc(Razor)::: <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="94e53-555">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="94e53-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="94e53-556">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="94e53-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="94e53-557">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="94e53-557">Keys</span></span>

<span data-ttu-id="94e53-558">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="94e53-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="94e53-559">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-559">Keys are case-insensitive.</span></span> <span data-ttu-id="94e53-560">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="94e53-561">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="94e53-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="94e53-562">Yinelenen JSON anahtarları hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/2381)bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="94e53-563">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="94e53-563">Hierarchical keys</span></span>
  * <span data-ttu-id="94e53-564">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="94e53-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="94e53-565">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="94e53-566">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="94e53-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="94e53-567">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="94e53-568">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="94e53-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="94e53-569">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="94e53-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="94e53-570">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94e53-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="94e53-571">Değerler</span><span class="sxs-lookup"><span data-stu-id="94e53-571">Values</span></span>

<span data-ttu-id="94e53-572">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="94e53-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="94e53-573">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="94e53-573">Values are strings.</span></span>
* <span data-ttu-id="94e53-574">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="94e53-575">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="94e53-575">Providers</span></span>

<span data-ttu-id="94e53-576">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="94e53-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="94e53-577">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="94e53-577">Provider</span></span> | <span data-ttu-id="94e53-578">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="94e53-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="94e53-579">[Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) ( *güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="94e53-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) ( *Security* topics)</span></span> | <span data-ttu-id="94e53-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="94e53-580">Azure Key Vault</span></span> |
| <span data-ttu-id="94e53-581">[Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="94e53-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="94e53-582">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="94e53-583">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="94e53-584">Komut satırı parametreleri</span><span class="sxs-lookup"><span data-stu-id="94e53-584">Command-line parameters</span></span> |
| [<span data-ttu-id="94e53-585">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="94e53-586">Özel kaynak</span><span class="sxs-lookup"><span data-stu-id="94e53-586">Custom source</span></span> |
| [<span data-ttu-id="94e53-587">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="94e53-588">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-588">Environment variables</span></span> |
| [<span data-ttu-id="94e53-589">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="94e53-590">Dosyalar (ıNı, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="94e53-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="94e53-591">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="94e53-592">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="94e53-592">Directory files</span></span> |
| [<span data-ttu-id="94e53-593">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="94e53-594">Bellek içi Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="94e53-594">In-memory collections</span></span> |
| <span data-ttu-id="94e53-595">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) ( *güvenlik* konuları)</span><span class="sxs-lookup"><span data-stu-id="94e53-595">[User secrets (Secret Manager)](xref:security/app-secrets) ( *Security* topics)</span></span> | <span data-ttu-id="94e53-596">Kullanıcı profili dizinindeki dosya</span><span class="sxs-lookup"><span data-stu-id="94e53-596">File in the user profile directory</span></span> |

<span data-ttu-id="94e53-597">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="94e53-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="94e53-598">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94e53-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="94e53-599">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="94e53-600">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="94e53-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="94e53-601">Dosyalar ( *:::no-loc(appsettings.json):::* , *appSettings. { Environment}. JSON* , `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="94e53-601">Files ( *:::no-loc(appsettings.json):::* , *appsettings.{Environment}.json* , where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="94e53-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="94e53-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="94e53-603">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="94e53-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="94e53-604">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-604">Environment variables</span></span>
1. <span data-ttu-id="94e53-605">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="94e53-605">Command-line arguments</span></span>

<span data-ttu-id="94e53-606">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="94e53-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="94e53-607">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="94e53-608">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="94e53-609">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94e53-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="94e53-610">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="94e53-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="94e53-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="94e53-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="94e53-612">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="94e53-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="94e53-613">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="94e53-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="94e53-614">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="94e53-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="94e53-615">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="94e53-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="94e53-616">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="94e53-617">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="94e53-617">Consume configuration during app startup</span></span>

<span data-ttu-id="94e53-618">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="94e53-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="94e53-619">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="94e53-620">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="94e53-621">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="94e53-622">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="94e53-623">`AddCommandLine` çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="94e53-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="94e53-624">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="94e53-625">`CreateDefaultBuilder` Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="94e53-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="94e53-626">Ve appSettings 'ten isteğe bağlı yapılandırma *:::no-loc(appsettings.json):::* *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="94e53-626">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="94e53-627">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="94e53-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="94e53-628">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-628">Environment variables.</span></span>

<span data-ttu-id="94e53-629">`CreateDefaultBuilder` Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="94e53-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="94e53-630">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="94e53-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="94e53-631">`CreateDefaultBuilder` Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="94e53-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="94e53-632">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="94e53-633">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="94e53-634">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="94e53-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="94e53-635">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="94e53-635">**Example**</span></span>

<span data-ttu-id="94e53-636">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="94e53-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="94e53-637">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="94e53-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="94e53-638">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="94e53-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="94e53-639">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="94e53-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="94e53-640">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="94e53-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="94e53-641">Arguments</span><span class="sxs-lookup"><span data-stu-id="94e53-641">Arguments</span></span>

<span data-ttu-id="94e53-642">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="94e53-643">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="94e53-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="94e53-644">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="94e53-644">Key prefix</span></span>               | <span data-ttu-id="94e53-645">Örnek</span><span class="sxs-lookup"><span data-stu-id="94e53-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="94e53-646">Ön ek yok</span><span class="sxs-lookup"><span data-stu-id="94e53-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="94e53-647">İki kısa çizgi ( `--` )</span><span class="sxs-lookup"><span data-stu-id="94e53-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="94e53-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="94e53-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="94e53-649">Eğik çizgi ( `/` )</span><span class="sxs-lookup"><span data-stu-id="94e53-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="94e53-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="94e53-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="94e53-651">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="94e53-652">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="94e53-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="94e53-653">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="94e53-653">Switch mappings</span></span>

<span data-ttu-id="94e53-654">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="94e53-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="94e53-655">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="94e53-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="94e53-656">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="94e53-657">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="94e53-658">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="94e53-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="94e53-659">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="94e53-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="94e53-660">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="94e53-661">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="94e53-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="94e53-662">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="94e53-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="94e53-663">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="94e53-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="94e53-664">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="94e53-665">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="94e53-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="94e53-666">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="94e53-667">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="94e53-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="94e53-668">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="94e53-669">Anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-669">Key</span></span>       | <span data-ttu-id="94e53-670">Değer</span><span class="sxs-lookup"><span data-stu-id="94e53-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="94e53-671">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="94e53-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="94e53-672">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="94e53-673">Anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-673">Key</span></span>               | <span data-ttu-id="94e53-674">Değer</span><span class="sxs-lookup"><span data-stu-id="94e53-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="94e53-675">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="94e53-676">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="94e53-677">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="94e53-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="94e53-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="94e53-679">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="94e53-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="94e53-680">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="94e53-681">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="94e53-682">`CreateDefaultBuilder` Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="94e53-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="94e53-683">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="94e53-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="94e53-684">Ve appSettings 'ten isteğe bağlı yapılandırma *:::no-loc(appsettings.json):::* *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="94e53-684">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="94e53-685">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="94e53-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="94e53-686">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-686">Command-line arguments.</span></span>

<span data-ttu-id="94e53-687">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="94e53-688">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="94e53-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="94e53-689">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="94e53-690">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="94e53-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="94e53-691">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="94e53-691">**Example**</span></span>

<span data-ttu-id="94e53-692">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="94e53-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="94e53-693">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="94e53-693">Run the sample app.</span></span> <span data-ttu-id="94e53-694">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="94e53-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="94e53-695">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="94e53-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="94e53-696">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="94e53-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="94e53-697">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="94e53-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="94e53-698">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="94e53-699">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="94e53-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="94e53-700">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="94e53-700">Prefixes</span></span>

<span data-ttu-id="94e53-701">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="94e53-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="94e53-702">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="94e53-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="94e53-703">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="94e53-704">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="94e53-705">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="94e53-706">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="94e53-706">**Connection string prefixes**</span></span>

<span data-ttu-id="94e53-707">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="94e53-708">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="94e53-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="94e53-709">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="94e53-709">Connection string prefix</span></span> | <span data-ttu-id="94e53-710">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="94e53-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="94e53-711">Özel sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="94e53-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="94e53-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="94e53-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="94e53-713">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="94e53-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="94e53-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="94e53-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="94e53-715">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="94e53-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="94e53-716">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="94e53-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="94e53-717">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="94e53-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="94e53-718">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="94e53-718">Environment variable key</span></span> | <span data-ttu-id="94e53-719">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="94e53-719">Converted configuration key</span></span> | <span data-ttu-id="94e53-720">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="94e53-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-721">Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="94e53-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-722">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="94e53-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="94e53-723">Değer: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="94e53-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-724">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="94e53-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="94e53-725">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="94e53-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="94e53-726">Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="94e53-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="94e53-727">Değer: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="94e53-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="94e53-728">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="94e53-728">**Example**</span></span>

<span data-ttu-id="94e53-729">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="94e53-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="94e53-730">Ad: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="94e53-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="94e53-731">Değer: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="94e53-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="94e53-732">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="94e53-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="94e53-733">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-733">File Configuration Provider</span></span>

<span data-ttu-id="94e53-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> , dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="94e53-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="94e53-735">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="94e53-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="94e53-736">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="94e53-737">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="94e53-738">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="94e53-739">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-739">INI Configuration Provider</span></span>

<span data-ttu-id="94e53-740">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="94e53-741">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="94e53-742">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="94e53-743">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="94e53-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="94e53-744">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="94e53-744">Whether the file is optional.</span></span>
* <span data-ttu-id="94e53-745">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="94e53-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="94e53-746"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="94e53-747">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="94e53-748">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="94e53-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="94e53-749">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="94e53-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="94e53-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-750">section0:key0</span></span>
* <span data-ttu-id="94e53-751">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-751">section0:key1</span></span>
* <span data-ttu-id="94e53-752">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-752">section1:subsection:key</span></span>
* <span data-ttu-id="94e53-753">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-753">section2:subsection0:key</span></span>
* <span data-ttu-id="94e53-754">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="94e53-755">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-755">JSON Configuration Provider</span></span>

<span data-ttu-id="94e53-756">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="94e53-757">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="94e53-758">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="94e53-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="94e53-759">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="94e53-759">Whether the file is optional.</span></span>
* <span data-ttu-id="94e53-760">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="94e53-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="94e53-761"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="94e53-762">`AddJsonFile` , ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="94e53-763">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="94e53-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="94e53-764">*:::no-loc(appsettings.json):::* : Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="94e53-764">*:::no-loc(appsettings.json):::* : This file is read first.</span></span> <span data-ttu-id="94e53-765">Dosyanın ortam sürümü, dosya tarafından belirtilen değerleri geçersiz kılabilir *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94e53-765">The environment version of the file can override the values provided by the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="94e53-766">*appSettings. {Environment}. JSON* : dosyanın ortam sürümü [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="94e53-766">*appsettings.{Environment}.json* : The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="94e53-767">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="94e53-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="94e53-768">`CreateDefaultBuilder` Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="94e53-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="94e53-769">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-769">Environment variables.</span></span>
* <span data-ttu-id="94e53-770">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="94e53-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="94e53-771">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="94e53-771">Command-line arguments.</span></span>

<span data-ttu-id="94e53-772">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="94e53-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="94e53-773">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94e53-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="94e53-774">`ConfigureAppConfiguration`Ana bilgisayarı derlerken, ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtecek çağrı *:::no-loc(appsettings.json):::* *. { Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="94e53-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* :</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="94e53-775">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="94e53-775">**Example**</span></span>

<span data-ttu-id="94e53-776">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="94e53-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="94e53-777">`AddJsonFile`Yapılandırmayı şuradan yükleyen ilk çağrı *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="94e53-777">The first call to `AddJsonFile` loads configuration from *:::no-loc(appsettings.json):::* :</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/:::no-loc(appsettings.json):::)]

* <span data-ttu-id="94e53-778">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="94e53-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json* .</span></span> <span data-ttu-id="94e53-779">Örnek uygulamada *appsettings.Development.js* için aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="94e53-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="94e53-780">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="94e53-780">Run the sample app.</span></span> <span data-ttu-id="94e53-781">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="94e53-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="94e53-782">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="94e53-783">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="94e53-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="94e53-784">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="94e53-785">Dosyadaki *Özellikler/launchSettings.js* açın.</span><span class="sxs-lookup"><span data-stu-id="94e53-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="94e53-786">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="94e53-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="94e53-787">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="94e53-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="94e53-788">*Üzerindeappsettings.Development.js* ayarları artık içindeki ayarları geçersiz kılmaz *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="94e53-788">The settings in the *appsettings.Development.json* no longer override the settings in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="94e53-789">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="94e53-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="94e53-790">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-790">XML Configuration Provider</span></span>

<span data-ttu-id="94e53-791">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="94e53-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="94e53-792">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="94e53-793">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="94e53-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="94e53-794">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="94e53-794">Whether the file is optional.</span></span>
* <span data-ttu-id="94e53-795">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="94e53-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="94e53-796"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="94e53-797">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="94e53-798">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="94e53-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="94e53-799">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="94e53-800">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="94e53-801">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="94e53-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="94e53-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-802">section0:key0</span></span>
* <span data-ttu-id="94e53-803">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-803">section0:key1</span></span>
* <span data-ttu-id="94e53-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-804">section1:key0</span></span>
* <span data-ttu-id="94e53-805">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-805">section1:key1</span></span>

<span data-ttu-id="94e53-806">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="94e53-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="94e53-807">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="94e53-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="94e53-808">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="94e53-808">section:section0:key:key0</span></span>
* <span data-ttu-id="94e53-809">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-809">section:section0:key:key1</span></span>
* <span data-ttu-id="94e53-810">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="94e53-810">section:section1:key:key0</span></span>
* <span data-ttu-id="94e53-811">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-811">section:section1:key:key1</span></span>

<span data-ttu-id="94e53-812">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="94e53-813">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="94e53-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="94e53-814">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="94e53-814">key:attribute</span></span>
* <span data-ttu-id="94e53-815">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="94e53-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="94e53-816">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="94e53-817">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="94e53-818">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="94e53-818">The key is the file name.</span></span> <span data-ttu-id="94e53-819">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-819">The value contains the file's contents.</span></span> <span data-ttu-id="94e53-820">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="94e53-821">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="94e53-822">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="94e53-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="94e53-823">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="94e53-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="94e53-824">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="94e53-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="94e53-825">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="94e53-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="94e53-826">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="94e53-827">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="94e53-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="94e53-828">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="94e53-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="94e53-829">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-829">Memory Configuration Provider</span></span>

<span data-ttu-id="94e53-830">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="94e53-831">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="94e53-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="94e53-832">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="94e53-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="94e53-833">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="94e53-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="94e53-834">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="94e53-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="94e53-835">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="94e53-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="94e53-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="94e53-836">GetValue</span></span>

<span data-ttu-id="94e53-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="94e53-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="94e53-838">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="94e53-838">An overload accepts a default value.</span></span>

<span data-ttu-id="94e53-839">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="94e53-839">The following example:</span></span>

* <span data-ttu-id="94e53-840">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="94e53-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="94e53-841">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="94e53-842">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="94e53-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="94e53-843">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="94e53-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="94e53-844">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="94e53-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="94e53-845">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="94e53-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="94e53-846">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="94e53-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="94e53-847">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="94e53-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="94e53-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-848">section0:key0</span></span>
* <span data-ttu-id="94e53-849">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-849">section0:key1</span></span>
* <span data-ttu-id="94e53-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-850">section1:key0</span></span>
* <span data-ttu-id="94e53-851">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-851">section1:key1</span></span>
* <span data-ttu-id="94e53-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="94e53-853">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="94e53-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="94e53-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="94e53-855">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="94e53-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="94e53-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="94e53-856">GetSection</span></span>

<span data-ttu-id="94e53-857">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="94e53-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="94e53-858"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="94e53-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="94e53-859">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="94e53-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="94e53-860">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="94e53-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="94e53-861">`GetSection` hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="94e53-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="94e53-862">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="94e53-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="94e53-863">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="94e53-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="94e53-864">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="94e53-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="94e53-865">GetChildren</span><span class="sxs-lookup"><span data-stu-id="94e53-865">GetChildren</span></span>

<span data-ttu-id="94e53-866">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="94e53-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="94e53-867">Var</span><span class="sxs-lookup"><span data-stu-id="94e53-867">Exists</span></span>

<span data-ttu-id="94e53-868">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="94e53-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="94e53-869">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="94e53-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="94e53-870">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="94e53-870">Bind to an object graph</span></span>

<span data-ttu-id="94e53-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> , bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="94e53-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="94e53-872">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="94e53-873">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` ( *modeller/tvshow. cs* ):</span><span class="sxs-lookup"><span data-stu-id="94e53-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes ( *Models/TvShow.cs* ):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="94e53-874">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow.xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="94e53-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="94e53-875">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="94e53-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="94e53-876">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="94e53-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="94e53-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="94e53-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="94e53-878">`Get<T>` , kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="94e53-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="94e53-879">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="94e53-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="94e53-880">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="94e53-880">Bind an array to a class</span></span>

<span data-ttu-id="94e53-881">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="94e53-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="94e53-882">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="94e53-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="94e53-883">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="94e53-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="94e53-884">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="94e53-884">Binding is provided by convention.</span></span> <span data-ttu-id="94e53-885">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="94e53-886">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="94e53-886">**In-memory array processing**</span></span>

<span data-ttu-id="94e53-887">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="94e53-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="94e53-888">Anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-888">Key</span></span>             | <span data-ttu-id="94e53-889">Değer</span><span class="sxs-lookup"><span data-stu-id="94e53-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="94e53-890">dizi: girdiler: 0</span><span class="sxs-lookup"><span data-stu-id="94e53-890">array:entries:0</span></span> | <span data-ttu-id="94e53-891">value0</span><span class="sxs-lookup"><span data-stu-id="94e53-891">value0</span></span> |
| <span data-ttu-id="94e53-892">dizi: girdiler: 1</span><span class="sxs-lookup"><span data-stu-id="94e53-892">array:entries:1</span></span> | <span data-ttu-id="94e53-893">value1</span><span class="sxs-lookup"><span data-stu-id="94e53-893">value1</span></span> |
| <span data-ttu-id="94e53-894">dizi: girdiler: 2</span><span class="sxs-lookup"><span data-stu-id="94e53-894">array:entries:2</span></span> | <span data-ttu-id="94e53-895">value2</span><span class="sxs-lookup"><span data-stu-id="94e53-895">value2</span></span> |
| <span data-ttu-id="94e53-896">dizi: girdiler: 4</span><span class="sxs-lookup"><span data-stu-id="94e53-896">array:entries:4</span></span> | <span data-ttu-id="94e53-897">value4</span><span class="sxs-lookup"><span data-stu-id="94e53-897">value4</span></span> |
| <span data-ttu-id="94e53-898">dizi: girdiler: 5</span><span class="sxs-lookup"><span data-stu-id="94e53-898">array:entries:5</span></span> | <span data-ttu-id="94e53-899">value5</span><span class="sxs-lookup"><span data-stu-id="94e53-899">value5</span></span> |

<span data-ttu-id="94e53-900">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="94e53-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="94e53-901">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="94e53-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="94e53-902">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="94e53-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="94e53-903">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="94e53-904">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="94e53-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="94e53-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="94e53-906">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="94e53-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="94e53-907">`ArrayExample.Entries` İndeks</span><span class="sxs-lookup"><span data-stu-id="94e53-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="94e53-908">`ArrayExample.Entries` Deeri</span><span class="sxs-lookup"><span data-stu-id="94e53-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="94e53-909">0</span><span class="sxs-lookup"><span data-stu-id="94e53-909">0</span></span>                            | <span data-ttu-id="94e53-910">value0</span><span class="sxs-lookup"><span data-stu-id="94e53-910">value0</span></span>                       |
| <span data-ttu-id="94e53-911">1</span><span class="sxs-lookup"><span data-stu-id="94e53-911">1</span></span>                            | <span data-ttu-id="94e53-912">value1</span><span class="sxs-lookup"><span data-stu-id="94e53-912">value1</span></span>                       |
| <span data-ttu-id="94e53-913">2</span><span class="sxs-lookup"><span data-stu-id="94e53-913">2</span></span>                            | <span data-ttu-id="94e53-914">value2</span><span class="sxs-lookup"><span data-stu-id="94e53-914">value2</span></span>                       |
| <span data-ttu-id="94e53-915">3</span><span class="sxs-lookup"><span data-stu-id="94e53-915">3</span></span>                            | <span data-ttu-id="94e53-916">value4</span><span class="sxs-lookup"><span data-stu-id="94e53-916">value4</span></span>                       |
| <span data-ttu-id="94e53-917">4</span><span class="sxs-lookup"><span data-stu-id="94e53-917">4</span></span>                            | <span data-ttu-id="94e53-918">value5</span><span class="sxs-lookup"><span data-stu-id="94e53-918">value5</span></span>                       |

<span data-ttu-id="94e53-919">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="94e53-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="94e53-920">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="94e53-921">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="94e53-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="94e53-922">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="94e53-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="94e53-923">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="94e53-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="94e53-924">*missing_value.js* :</span><span class="sxs-lookup"><span data-stu-id="94e53-924">*missing_value.json* :</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="94e53-925">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="94e53-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="94e53-926">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="94e53-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="94e53-927">Anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-927">Key</span></span>             | <span data-ttu-id="94e53-928">Değer</span><span class="sxs-lookup"><span data-stu-id="94e53-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="94e53-929">dizi: girdiler: 3</span><span class="sxs-lookup"><span data-stu-id="94e53-929">array:entries:3</span></span> | <span data-ttu-id="94e53-930">value3</span><span class="sxs-lookup"><span data-stu-id="94e53-930">value3</span></span> |

<span data-ttu-id="94e53-931">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="94e53-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="94e53-932">`ArrayExample.Entries` İndeks</span><span class="sxs-lookup"><span data-stu-id="94e53-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="94e53-933">`ArrayExample.Entries` Deeri</span><span class="sxs-lookup"><span data-stu-id="94e53-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="94e53-934">0</span><span class="sxs-lookup"><span data-stu-id="94e53-934">0</span></span>                            | <span data-ttu-id="94e53-935">value0</span><span class="sxs-lookup"><span data-stu-id="94e53-935">value0</span></span>                       |
| <span data-ttu-id="94e53-936">1</span><span class="sxs-lookup"><span data-stu-id="94e53-936">1</span></span>                            | <span data-ttu-id="94e53-937">value1</span><span class="sxs-lookup"><span data-stu-id="94e53-937">value1</span></span>                       |
| <span data-ttu-id="94e53-938">2</span><span class="sxs-lookup"><span data-stu-id="94e53-938">2</span></span>                            | <span data-ttu-id="94e53-939">value2</span><span class="sxs-lookup"><span data-stu-id="94e53-939">value2</span></span>                       |
| <span data-ttu-id="94e53-940">3</span><span class="sxs-lookup"><span data-stu-id="94e53-940">3</span></span>                            | <span data-ttu-id="94e53-941">value3</span><span class="sxs-lookup"><span data-stu-id="94e53-941">value3</span></span>                       |
| <span data-ttu-id="94e53-942">4</span><span class="sxs-lookup"><span data-stu-id="94e53-942">4</span></span>                            | <span data-ttu-id="94e53-943">value4</span><span class="sxs-lookup"><span data-stu-id="94e53-943">value4</span></span>                       |
| <span data-ttu-id="94e53-944">5</span><span class="sxs-lookup"><span data-stu-id="94e53-944">5</span></span>                            | <span data-ttu-id="94e53-945">value5</span><span class="sxs-lookup"><span data-stu-id="94e53-945">value5</span></span>                       |

<span data-ttu-id="94e53-946">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="94e53-946">**JSON array processing**</span></span>

<span data-ttu-id="94e53-947">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="94e53-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="94e53-948">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="94e53-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="94e53-949">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="94e53-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="94e53-950">Anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-950">Key</span></span>                     | <span data-ttu-id="94e53-951">Değer</span><span class="sxs-lookup"><span data-stu-id="94e53-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="94e53-952">json_array: anahtar</span><span class="sxs-lookup"><span data-stu-id="94e53-952">json_array:key</span></span>          | <span data-ttu-id="94e53-953">değer EA</span><span class="sxs-lookup"><span data-stu-id="94e53-953">valueA</span></span> |
| <span data-ttu-id="94e53-954">json_array: alt bölüm: 0</span><span class="sxs-lookup"><span data-stu-id="94e53-954">json_array:subsection:0</span></span> | <span data-ttu-id="94e53-955">valueB</span><span class="sxs-lookup"><span data-stu-id="94e53-955">valueB</span></span> |
| <span data-ttu-id="94e53-956">json_array: alt bölüm: 1</span><span class="sxs-lookup"><span data-stu-id="94e53-956">json_array:subsection:1</span></span> | <span data-ttu-id="94e53-957">değer EC</span><span class="sxs-lookup"><span data-stu-id="94e53-957">valueC</span></span> |
| <span data-ttu-id="94e53-958">json_array: alt bölüm: 2</span><span class="sxs-lookup"><span data-stu-id="94e53-958">json_array:subsection:2</span></span> | <span data-ttu-id="94e53-959">Değerler</span><span class="sxs-lookup"><span data-stu-id="94e53-959">valueD</span></span> |

<span data-ttu-id="94e53-960">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="94e53-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="94e53-961">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="94e53-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="94e53-962">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="94e53-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="94e53-963">`JsonArrayExample.Subsection` İndeks</span><span class="sxs-lookup"><span data-stu-id="94e53-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="94e53-964">`JsonArrayExample.Subsection` Deeri</span><span class="sxs-lookup"><span data-stu-id="94e53-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="94e53-965">0</span><span class="sxs-lookup"><span data-stu-id="94e53-965">0</span></span>                                   | <span data-ttu-id="94e53-966">valueB</span><span class="sxs-lookup"><span data-stu-id="94e53-966">valueB</span></span>                              |
| <span data-ttu-id="94e53-967">1</span><span class="sxs-lookup"><span data-stu-id="94e53-967">1</span></span>                                   | <span data-ttu-id="94e53-968">değer EC</span><span class="sxs-lookup"><span data-stu-id="94e53-968">valueC</span></span>                              |
| <span data-ttu-id="94e53-969">2</span><span class="sxs-lookup"><span data-stu-id="94e53-969">2</span></span>                                   | <span data-ttu-id="94e53-970">Değerler</span><span class="sxs-lookup"><span data-stu-id="94e53-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="94e53-971">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="94e53-971">Custom configuration provider</span></span>

<span data-ttu-id="94e53-972">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="94e53-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="94e53-973">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="94e53-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="94e53-974">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94e53-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="94e53-975">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="94e53-976">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="94e53-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="94e53-977">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="94e53-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="94e53-978">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="94e53-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="94e53-979">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="94e53-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="94e53-980">*Modeller/EFConfigurationValue. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-980">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="94e53-981">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="94e53-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="94e53-982">*Efconfigurationprovider/EFConfigurationContext. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="94e53-983">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="94e53-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="94e53-984">*Efconfigurationprovider/EFConfigurationSource. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="94e53-985">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="94e53-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="94e53-986">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="94e53-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="94e53-987">*Efconfigurationprovider/efconfigurationprovider. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="94e53-988">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="94e53-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="94e53-989">*Uzantılar/EntityFrameworkExtensions. cs* :</span><span class="sxs-lookup"><span data-stu-id="94e53-989">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="94e53-990">Aşağıdaki kod, `EFConfigurationProvider` *program.cs* içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="94e53-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="94e53-991">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="94e53-991">Access configuration during startup</span></span>

<span data-ttu-id="94e53-992">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="94e53-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="94e53-993">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="94e53-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="94e53-994">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="94e53-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="94e53-995">:::no-loc(Razor):::Sayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94e53-995">Access configuration in a :::no-loc(Razor)::: Pages page or MVC view</span></span>

<span data-ttu-id="94e53-996">Bir :::no-loc(Razor)::: Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft.Extensions.Configurlama ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94e53-996">To access configuration settings in a :::no-loc(Razor)::: Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="94e53-997">:::no-loc(Razor):::Sayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="94e53-997">In a :::no-loc(Razor)::: Pages page:</span></span>

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
    <h1>Access configuration in a :::no-loc(Razor)::: Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="94e53-998">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="94e53-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="94e53-999">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="94e53-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="94e53-1000">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="94e53-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="94e53-1001">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="94e53-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94e53-1002">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94e53-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end