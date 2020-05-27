---
<span data-ttu-id="3037b-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-102">'Blazor'</span></span>
- <span data-ttu-id="3037b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-103">'Identity'</span></span>
- <span data-ttu-id="3037b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-105">'Razor'</span></span>
- <span data-ttu-id="3037b-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="3037b-107">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="3037b-108">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3037b-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3037b-109">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="3037b-110">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="3037b-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="3037b-111">*AppSettings. JSON* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="3037b-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="3037b-112">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-112">Environment variables</span></span>
* <span data-ttu-id="3037b-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="3037b-113">Azure Key Vault</span></span>
* <span data-ttu-id="3037b-114">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3037b-114">Azure App Configuration</span></span>
* <span data-ttu-id="3037b-115">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-115">Command-line arguments</span></span>
* <span data-ttu-id="3037b-116">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="3037b-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="3037b-117">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="3037b-117">Directory files</span></span>
* <span data-ttu-id="3037b-118">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="3037b-118">In-memory .NET objects</span></span>

<span data-ttu-id="3037b-119">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3037b-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="3037b-120">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-120">Default configuration</span></span>

<span data-ttu-id="3037b-121">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3037b-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="3037b-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="3037b-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="3037b-123">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="3037b-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="3037b-124">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3037b-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="3037b-125">[JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak [appSettings. JSON](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="3037b-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="3037b-126">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3037b-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="3037b-127">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="3037b-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="3037b-128">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="3037b-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="3037b-129">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="3037b-130">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="3037b-131">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3037b-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="3037b-132">Örneğin, `MyKey` *appSettings. JSON* ve ortamda ayarlanırsa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="3037b-133">Varsayılan yapılandırma sağlayıcılarını kullanarak, [komut satırı yapılandırma sağlayıcısı](#command-line-configuration-provider) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3037b-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="3037b-134">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="3037b-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="3037b-135">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="3037b-136">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="3037b-136">appsettings.json</span></span>

<span data-ttu-id="3037b-137">Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3037b-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="3037b-138">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-139">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="3037b-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="3037b-140">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="3037b-140">*appsettings.json*</span></span>
1. <span data-ttu-id="3037b-141">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="3037b-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="3037b-142">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="3037b-143">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3037b-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3037b-144">*appSettings*. `Environment` . *JSON* değerleri *appSettings. JSON*içindeki anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3037b-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="3037b-145">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="3037b-145">For example, by default:</span></span>

* <span data-ttu-id="3037b-146">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması *appSettings. JSON*içinde bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="3037b-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="3037b-147">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması *appSettings. JSON*içinde bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="3037b-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="3037b-148">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="3037b-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="3037b-149">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="3037b-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="3037b-150">[Varsayılan](#default) yapılandırmayı kullanarak *appSettings. JSON* ve *appSettings.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="3037b-151">*AppSettings. JSON* ve appSettings üzerinde yapılan değişiklikler *.* `Environment` uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="3037b-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="3037b-152">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="3037b-153">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="3037b-153">Security and secret manager</span></span>

<span data-ttu-id="3037b-154">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="3037b-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="3037b-155">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="3037b-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="3037b-156">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="3037b-157">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="3037b-158">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="3037b-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="3037b-159">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appSettings. JSON* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3037b-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="3037b-160">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="3037b-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="3037b-161"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="3037b-162">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="3037b-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="3037b-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="3037b-164">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3037b-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="3037b-165">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-165">Environment variables</span></span>

<span data-ttu-id="3037b-166">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> *appSettings. JSON*, appSettings 'i okuduktan sonra anahtar-değer çiftlerinden yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="3037b-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="3037b-167">Bu nedenle, ortamdan okunan anahtar değerleri *appSettings. JSON*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="3037b-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="3037b-168">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="3037b-168">The following `set` commands:</span></span>

* <span data-ttu-id="3037b-169">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="3037b-170">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="3037b-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="3037b-171">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3037b-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="3037b-172">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="3037b-172">The preceding environment settings:</span></span>

* <span data-ttu-id="3037b-173">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="3037b-174">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="3037b-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="3037b-175">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="3037b-176">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="3037b-177">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3037b-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="3037b-178">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="3037b-179">Yukarıdaki komutların *appSettings. JSON* ve appSettings 'i geçersiz kılmasını test etmek için *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3037b-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="3037b-180">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="3037b-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="3037b-181">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="3037b-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="3037b-182"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="3037b-183">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="3037b-183">In the preceding code:</span></span>

* <span data-ttu-id="3037b-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="3037b-185">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="3037b-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="3037b-186">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3037b-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="3037b-187">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="3037b-188">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="3037b-189">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="3037b-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="3037b-190">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="3037b-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="3037b-191">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="3037b-192">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3037b-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="3037b-193">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="3037b-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="3037b-194">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3037b-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="3037b-195">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="3037b-196">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="3037b-196">Exposed as environment variables.</span></span>

<span data-ttu-id="3037b-197">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="3037b-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="3037b-198">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="3037b-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="3037b-199">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="3037b-199">Command-line</span></span>

<span data-ttu-id="3037b-200">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="3037b-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="3037b-201">*appSettings. JSON* ve *appSettings*. `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="3037b-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="3037b-202">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="3037b-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3037b-203">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-203">Environment variables.</span></span>

<span data-ttu-id="3037b-204">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="3037b-205">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-205">Command-line arguments</span></span>

<span data-ttu-id="3037b-206">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="3037b-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="3037b-207">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="3037b-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="3037b-208">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="3037b-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="3037b-209">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="3037b-209">The key value:</span></span>

* <span data-ttu-id="3037b-210">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="3037b-211">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="3037b-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="3037b-212">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="3037b-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="3037b-213">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="3037b-214">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="3037b-214">Switch mappings</span></span>

<span data-ttu-id="3037b-215">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="3037b-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="3037b-216">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="3037b-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="3037b-217">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="3037b-218">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="3037b-219">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="3037b-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="3037b-220">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="3037b-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="3037b-221">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="3037b-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="3037b-222">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="3037b-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="3037b-223">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="3037b-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="3037b-224">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="3037b-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-225">Anahtar değişimini test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="3037b-226">Note: Şu anda, `=` anahtar değiştirme değerlerini tek bir çizgiyle ayarlamak için kullanılamaz `-` .</span><span class="sxs-lookup"><span data-stu-id="3037b-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="3037b-227">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="3037b-228">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="3037b-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="3037b-229">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="3037b-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="3037b-230">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3037b-231">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="3037b-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="3037b-232">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="3037b-232">Hierarchical configuration data</span></span>

<span data-ttu-id="3037b-233">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="3037b-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="3037b-234">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *appSettings. JSON* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="3037b-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="3037b-235">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-236">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3037b-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="3037b-237">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="3037b-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="3037b-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="3037b-239">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3037b-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="3037b-240">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="3037b-240">Configuration keys and values</span></span>

<span data-ttu-id="3037b-241">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="3037b-241">Configuration keys:</span></span>

* <span data-ttu-id="3037b-242">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-242">Are case-insensitive.</span></span> <span data-ttu-id="3037b-243">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="3037b-244">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="3037b-245">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="3037b-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="3037b-246">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="3037b-246">Hierarchical keys</span></span>
  * <span data-ttu-id="3037b-247">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3037b-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="3037b-248">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="3037b-249">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="3037b-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="3037b-250">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="3037b-251">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="3037b-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="3037b-252">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="3037b-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3037b-253">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3037b-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="3037b-254">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="3037b-254">Configuration values:</span></span>

* <span data-ttu-id="3037b-255">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="3037b-255">Are strings.</span></span>
* <span data-ttu-id="3037b-256">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="3037b-257">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="3037b-257">Configuration providers</span></span>

<span data-ttu-id="3037b-258">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3037b-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="3037b-259">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="3037b-259">Provider</span></span> | <span data-ttu-id="3037b-260">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="3037b-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="3037b-261">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-262">'Blazor'</span></span>
- <span data-ttu-id="3037b-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-263">'Identity'</span></span>
- <span data-ttu-id="3037b-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-265">'Razor'</span></span>
- <span data-ttu-id="3037b-266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-267">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-268">'Blazor'</span></span>
- <span data-ttu-id="3037b-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-269">'Identity'</span></span>
- <span data-ttu-id="3037b-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-271">'Razor'</span></span>
- <span data-ttu-id="3037b-272">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-272">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-273">---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-274">'Blazor'</span></span>
- <span data-ttu-id="3037b-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-275">'Identity'</span></span>
- <span data-ttu-id="3037b-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-277">'Razor'</span></span>
- <span data-ttu-id="3037b-278">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-279">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-280">'Blazor'</span></span>
- <span data-ttu-id="3037b-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-281">'Identity'</span></span>
- <span data-ttu-id="3037b-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-283">'Razor'</span></span>
- <span data-ttu-id="3037b-284">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-285">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-286">'Blazor'</span></span>
- <span data-ttu-id="3037b-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-287">'Identity'</span></span>
- <span data-ttu-id="3037b-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-289">'Razor'</span></span>
- <span data-ttu-id="3037b-290">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-291">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-292">'Blazor'</span></span>
- <span data-ttu-id="3037b-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-293">'Identity'</span></span>
- <span data-ttu-id="3037b-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-295">'Razor'</span></span>
- <span data-ttu-id="3037b-296">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-297">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-298">'Blazor'</span></span>
- <span data-ttu-id="3037b-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-299">'Identity'</span></span>
- <span data-ttu-id="3037b-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-301">'Razor'</span></span>
- <span data-ttu-id="3037b-302">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-303">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-304">'Blazor'</span></span>
- <span data-ttu-id="3037b-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-305">'Identity'</span></span>
- <span data-ttu-id="3037b-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-307">'Razor'</span></span>
- <span data-ttu-id="3037b-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-309">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-310">'Blazor'</span></span>
- <span data-ttu-id="3037b-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-311">'Identity'</span></span>
- <span data-ttu-id="3037b-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-313">'Razor'</span></span>
- <span data-ttu-id="3037b-314">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-315">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-316">'Blazor'</span></span>
- <span data-ttu-id="3037b-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-317">'Identity'</span></span>
- <span data-ttu-id="3037b-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-319">'Razor'</span></span>
- <span data-ttu-id="3037b-320">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-321">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-322">'Blazor'</span></span>
- <span data-ttu-id="3037b-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-323">'Identity'</span></span>
- <span data-ttu-id="3037b-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-325">'Razor'</span></span>
- <span data-ttu-id="3037b-326">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-327">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-328">'Blazor'</span></span>
- <span data-ttu-id="3037b-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-329">'Identity'</span></span>
- <span data-ttu-id="3037b-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-331">'Razor'</span></span>
- <span data-ttu-id="3037b-332">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-334">'Blazor'</span></span>
- <span data-ttu-id="3037b-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-335">'Identity'</span></span>
- <span data-ttu-id="3037b-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-337">'Razor'</span></span>
- <span data-ttu-id="3037b-338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-340">'Blazor'</span></span>
- <span data-ttu-id="3037b-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-341">'Identity'</span></span>
- <span data-ttu-id="3037b-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-343">'Razor'</span></span>
- <span data-ttu-id="3037b-344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-346">'Blazor'</span></span>
- <span data-ttu-id="3037b-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-347">'Identity'</span></span>
- <span data-ttu-id="3037b-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-349">'Razor'</span></span>
- <span data-ttu-id="3037b-350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-352">'Blazor'</span></span>
- <span data-ttu-id="3037b-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-353">'Identity'</span></span>
- <span data-ttu-id="3037b-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-355">'Razor'</span></span>
- <span data-ttu-id="3037b-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-358">'Blazor'</span></span>
- <span data-ttu-id="3037b-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-359">'Identity'</span></span>
- <span data-ttu-id="3037b-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-361">'Razor'</span></span>
- <span data-ttu-id="3037b-362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-362">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-363">------------------ | | [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure Uygulama yapılandırması | | [Komut satırı yapılandırma sağlayıcısı](#clcp) | Komut satırı parametreleri | | [Özel yapılandırma sağlayıcısı](#custom-configuration-provider) | Özel kaynak | | [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) | Ortam değişkenleri | | [Dosya yapılandırma sağlayıcısı](#file-configuration-provider) | INı, JSON ve XML dosyaları | | [Dosya başına anahtar yapılandırma sağlayıcısı](#key-per-file-configuration-provider) | Dizin dosyaları | | [Bellek yapılandırma sağlayıcısı](#memory-configuration-provider) | Bellek içi Koleksiyonlar | | [Gizli dizi Yöneticisi](xref:security/app-secrets) | Kullanıcı profili dizinindeki dosya |</span><span class="sxs-lookup"><span data-stu-id="3037b-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="3037b-364">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="3037b-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="3037b-365">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="3037b-366">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3037b-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="3037b-367">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="3037b-367">*appsettings.json*</span></span>
1. <span data-ttu-id="3037b-368">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="3037b-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="3037b-369">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="3037b-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="3037b-370">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="3037b-371">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="3037b-372">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="3037b-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="3037b-373">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="3037b-374">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="3037b-374">Connection string prefixes</span></span>

<span data-ttu-id="3037b-375">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="3037b-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="3037b-376">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="3037b-377">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="3037b-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="3037b-378">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="3037b-378">Connection string prefix</span></span> | <span data-ttu-id="3037b-379">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="3037b-379">Provider</span></span> |
| ---
<span data-ttu-id="3037b-380">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-381">'Blazor'</span></span>
- <span data-ttu-id="3037b-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-382">'Identity'</span></span>
- <span data-ttu-id="3037b-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-384">'Razor'</span></span>
- <span data-ttu-id="3037b-385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-386">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-387">'Blazor'</span></span>
- <span data-ttu-id="3037b-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-388">'Identity'</span></span>
- <span data-ttu-id="3037b-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-390">'Razor'</span></span>
- <span data-ttu-id="3037b-391">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-392">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-393">'Blazor'</span></span>
- <span data-ttu-id="3037b-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-394">'Identity'</span></span>
- <span data-ttu-id="3037b-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-396">'Razor'</span></span>
- <span data-ttu-id="3037b-397">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-398">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-399">'Blazor'</span></span>
- <span data-ttu-id="3037b-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-400">'Identity'</span></span>
- <span data-ttu-id="3037b-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-402">'Razor'</span></span>
- <span data-ttu-id="3037b-403">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-404">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-405">'Blazor'</span></span>
- <span data-ttu-id="3037b-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-406">'Identity'</span></span>
- <span data-ttu-id="3037b-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-408">'Razor'</span></span>
- <span data-ttu-id="3037b-409">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-410">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-411">'Blazor'</span></span>
- <span data-ttu-id="3037b-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-412">'Identity'</span></span>
- <span data-ttu-id="3037b-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-414">'Razor'</span></span>
- <span data-ttu-id="3037b-415">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-416">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-417">'Blazor'</span></span>
- <span data-ttu-id="3037b-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-418">'Identity'</span></span>
- <span data-ttu-id="3037b-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-420">'Razor'</span></span>
- <span data-ttu-id="3037b-421">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-422">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-423">'Blazor'</span></span>
- <span data-ttu-id="3037b-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-424">'Identity'</span></span>
- <span data-ttu-id="3037b-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-426">'Razor'</span></span>
- <span data-ttu-id="3037b-427">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-428">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-429">'Blazor'</span></span>
- <span data-ttu-id="3037b-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-430">'Identity'</span></span>
- <span data-ttu-id="3037b-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-432">'Razor'</span></span>
- <span data-ttu-id="3037b-433">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-434">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-435">'Blazor'</span></span>
- <span data-ttu-id="3037b-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-436">'Identity'</span></span>
- <span data-ttu-id="3037b-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-438">'Razor'</span></span>
- <span data-ttu-id="3037b-439">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-439">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-440">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-441">'Blazor'</span></span>
- <span data-ttu-id="3037b-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-442">'Identity'</span></span>
- <span data-ttu-id="3037b-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-444">'Razor'</span></span>
- <span data-ttu-id="3037b-445">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-446">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-447">'Blazor'</span></span>
- <span data-ttu-id="3037b-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-448">'Identity'</span></span>
- <span data-ttu-id="3037b-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-450">'Razor'</span></span>
- <span data-ttu-id="3037b-451">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-451">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-452">---- | | `CUSTOMCONNSTR_` | Özel sağlayıcı | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="3037b-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="3037b-453">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="3037b-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="3037b-454">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="3037b-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="3037b-455">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="3037b-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="3037b-456">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="3037b-456">Environment variable key</span></span> | <span data-ttu-id="3037b-457">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="3037b-457">Converted configuration key</span></span> | <span data-ttu-id="3037b-458">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="3037b-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="3037b-459">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-460">'Blazor'</span></span>
- <span data-ttu-id="3037b-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-461">'Identity'</span></span>
- <span data-ttu-id="3037b-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-463">'Razor'</span></span>
- <span data-ttu-id="3037b-464">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-465">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-466">'Blazor'</span></span>
- <span data-ttu-id="3037b-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-467">'Identity'</span></span>
- <span data-ttu-id="3037b-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-469">'Razor'</span></span>
- <span data-ttu-id="3037b-470">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-471">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-472">'Blazor'</span></span>
- <span data-ttu-id="3037b-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-473">'Identity'</span></span>
- <span data-ttu-id="3037b-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-475">'Razor'</span></span>
- <span data-ttu-id="3037b-476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-477">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-478">'Blazor'</span></span>
- <span data-ttu-id="3037b-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-479">'Identity'</span></span>
- <span data-ttu-id="3037b-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-481">'Razor'</span></span>
- <span data-ttu-id="3037b-482">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-483">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-484">'Blazor'</span></span>
- <span data-ttu-id="3037b-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-485">'Identity'</span></span>
- <span data-ttu-id="3037b-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-487">'Razor'</span></span>
- <span data-ttu-id="3037b-488">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-489">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-490">'Blazor'</span></span>
- <span data-ttu-id="3037b-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-491">'Identity'</span></span>
- <span data-ttu-id="3037b-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-493">'Razor'</span></span>
- <span data-ttu-id="3037b-494">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-495">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-496">'Blazor'</span></span>
- <span data-ttu-id="3037b-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-497">'Identity'</span></span>
- <span data-ttu-id="3037b-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-499">'Razor'</span></span>
- <span data-ttu-id="3037b-500">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-501">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-502">'Blazor'</span></span>
- <span data-ttu-id="3037b-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-503">'Identity'</span></span>
- <span data-ttu-id="3037b-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-505">'Razor'</span></span>
- <span data-ttu-id="3037b-506">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-507">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-508">'Blazor'</span></span>
- <span data-ttu-id="3037b-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-509">'Identity'</span></span>
- <span data-ttu-id="3037b-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-511">'Razor'</span></span>
- <span data-ttu-id="3037b-512">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-513">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-514">'Blazor'</span></span>
- <span data-ttu-id="3037b-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-515">'Identity'</span></span>
- <span data-ttu-id="3037b-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-517">'Razor'</span></span>
- <span data-ttu-id="3037b-518">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-518">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-519">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-520">'Blazor'</span></span>
- <span data-ttu-id="3037b-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-521">'Identity'</span></span>
- <span data-ttu-id="3037b-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-523">'Razor'</span></span>
- <span data-ttu-id="3037b-524">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-525">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-526">'Blazor'</span></span>
- <span data-ttu-id="3037b-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-527">'Identity'</span></span>
- <span data-ttu-id="3037b-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-529">'Razor'</span></span>
- <span data-ttu-id="3037b-530">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-531">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-532">'Blazor'</span></span>
- <span data-ttu-id="3037b-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-533">'Identity'</span></span>
- <span data-ttu-id="3037b-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-535">'Razor'</span></span>
- <span data-ttu-id="3037b-536">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-537">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-538">'Blazor'</span></span>
- <span data-ttu-id="3037b-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-539">'Identity'</span></span>
- <span data-ttu-id="3037b-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-541">'Razor'</span></span>
- <span data-ttu-id="3037b-542">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-543">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-544">'Blazor'</span></span>
- <span data-ttu-id="3037b-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-545">'Identity'</span></span>
- <span data-ttu-id="3037b-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-547">'Razor'</span></span>
- <span data-ttu-id="3037b-548">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-549">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-550">'Blazor'</span></span>
- <span data-ttu-id="3037b-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-551">'Identity'</span></span>
- <span data-ttu-id="3037b-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-553">'Razor'</span></span>
- <span data-ttu-id="3037b-554">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-555">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-556">'Blazor'</span></span>
- <span data-ttu-id="3037b-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-557">'Identity'</span></span>
- <span data-ttu-id="3037b-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-559">'Razor'</span></span>
- <span data-ttu-id="3037b-560">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-561">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-562">'Blazor'</span></span>
- <span data-ttu-id="3037b-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-563">'Identity'</span></span>
- <span data-ttu-id="3037b-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-565">'Razor'</span></span>
- <span data-ttu-id="3037b-566">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-567">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-568">'Blazor'</span></span>
- <span data-ttu-id="3037b-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-569">'Identity'</span></span>
- <span data-ttu-id="3037b-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-571">'Razor'</span></span>
- <span data-ttu-id="3037b-572">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-573">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-574">'Blazor'</span></span>
- <span data-ttu-id="3037b-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-575">'Identity'</span></span>
- <span data-ttu-id="3037b-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-577">'Razor'</span></span>
- <span data-ttu-id="3037b-578">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-579">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-580">'Blazor'</span></span>
- <span data-ttu-id="3037b-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-581">'Identity'</span></span>
- <span data-ttu-id="3037b-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-583">'Razor'</span></span>
- <span data-ttu-id="3037b-584">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-584">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-585">-------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-586">'Blazor'</span></span>
- <span data-ttu-id="3037b-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-587">'Identity'</span></span>
- <span data-ttu-id="3037b-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-589">'Razor'</span></span>
- <span data-ttu-id="3037b-590">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-591">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-592">'Blazor'</span></span>
- <span data-ttu-id="3037b-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-593">'Identity'</span></span>
- <span data-ttu-id="3037b-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-595">'Razor'</span></span>
- <span data-ttu-id="3037b-596">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-597">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-598">'Blazor'</span></span>
- <span data-ttu-id="3037b-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-599">'Identity'</span></span>
- <span data-ttu-id="3037b-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-601">'Razor'</span></span>
- <span data-ttu-id="3037b-602">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-603">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-604">'Blazor'</span></span>
- <span data-ttu-id="3037b-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-605">'Identity'</span></span>
- <span data-ttu-id="3037b-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-607">'Razor'</span></span>
- <span data-ttu-id="3037b-608">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-609">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-610">'Blazor'</span></span>
- <span data-ttu-id="3037b-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-611">'Identity'</span></span>
- <span data-ttu-id="3037b-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-613">'Razor'</span></span>
- <span data-ttu-id="3037b-614">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-615">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-616">'Blazor'</span></span>
- <span data-ttu-id="3037b-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-617">'Identity'</span></span>
- <span data-ttu-id="3037b-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-619">'Razor'</span></span>
- <span data-ttu-id="3037b-620">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-621">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-622">'Blazor'</span></span>
- <span data-ttu-id="3037b-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-623">'Identity'</span></span>
- <span data-ttu-id="3037b-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-625">'Razor'</span></span>
- <span data-ttu-id="3037b-626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-627">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-628">'Blazor'</span></span>
- <span data-ttu-id="3037b-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-629">'Identity'</span></span>
- <span data-ttu-id="3037b-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-631">'Razor'</span></span>
- <span data-ttu-id="3037b-632">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-633">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-634">'Blazor'</span></span>
- <span data-ttu-id="3037b-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-635">'Identity'</span></span>
- <span data-ttu-id="3037b-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-637">'Razor'</span></span>
- <span data-ttu-id="3037b-638">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-639">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-640">'Blazor'</span></span>
- <span data-ttu-id="3037b-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-641">'Identity'</span></span>
- <span data-ttu-id="3037b-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-643">'Razor'</span></span>
- <span data-ttu-id="3037b-644">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-645">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-646">'Blazor'</span></span>
- <span data-ttu-id="3037b-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-647">'Identity'</span></span>
- <span data-ttu-id="3037b-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-649">'Razor'</span></span>
- <span data-ttu-id="3037b-650">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-651">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-652">'Blazor'</span></span>
- <span data-ttu-id="3037b-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-653">'Identity'</span></span>
- <span data-ttu-id="3037b-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-655">'Razor'</span></span>
- <span data-ttu-id="3037b-656">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-657">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-658">'Blazor'</span></span>
- <span data-ttu-id="3037b-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-659">'Identity'</span></span>
- <span data-ttu-id="3037b-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-661">'Razor'</span></span>
- <span data-ttu-id="3037b-662">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-663">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-664">'Blazor'</span></span>
- <span data-ttu-id="3037b-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-665">'Identity'</span></span>
- <span data-ttu-id="3037b-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-667">'Razor'</span></span>
- <span data-ttu-id="3037b-668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-669">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-670">'Blazor'</span></span>
- <span data-ttu-id="3037b-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-671">'Identity'</span></span>
- <span data-ttu-id="3037b-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-673">'Razor'</span></span>
- <span data-ttu-id="3037b-674">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-675">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-676">'Blazor'</span></span>
- <span data-ttu-id="3037b-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-677">'Identity'</span></span>
- <span data-ttu-id="3037b-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-679">'Razor'</span></span>
- <span data-ttu-id="3037b-680">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-681">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-682">'Blazor'</span></span>
- <span data-ttu-id="3037b-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-683">'Identity'</span></span>
- <span data-ttu-id="3037b-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-685">'Razor'</span></span>
- <span data-ttu-id="3037b-686">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-687">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-688">'Blazor'</span></span>
- <span data-ttu-id="3037b-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-689">'Identity'</span></span>
- <span data-ttu-id="3037b-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-691">'Razor'</span></span>
- <span data-ttu-id="3037b-692">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-693">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-694">'Blazor'</span></span>
- <span data-ttu-id="3037b-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-695">'Identity'</span></span>
- <span data-ttu-id="3037b-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-697">'Razor'</span></span>
- <span data-ttu-id="3037b-698">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-699">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-700">'Blazor'</span></span>
- <span data-ttu-id="3037b-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-701">'Identity'</span></span>
- <span data-ttu-id="3037b-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-703">'Razor'</span></span>
- <span data-ttu-id="3037b-704">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-705">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-706">'Blazor'</span></span>
- <span data-ttu-id="3037b-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-707">'Identity'</span></span>
- <span data-ttu-id="3037b-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-709">'Razor'</span></span>
- <span data-ttu-id="3037b-710">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-711">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-712">'Blazor'</span></span>
- <span data-ttu-id="3037b-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-713">'Identity'</span></span>
- <span data-ttu-id="3037b-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-715">'Razor'</span></span>
- <span data-ttu-id="3037b-716">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-717">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-718">'Blazor'</span></span>
- <span data-ttu-id="3037b-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-719">'Identity'</span></span>
- <span data-ttu-id="3037b-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-721">'Razor'</span></span>
- <span data-ttu-id="3037b-722">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-723">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-724">'Blazor'</span></span>
- <span data-ttu-id="3037b-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-725">'Identity'</span></span>
- <span data-ttu-id="3037b-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-727">'Razor'</span></span>
- <span data-ttu-id="3037b-728">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-729">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-730">'Blazor'</span></span>
- <span data-ttu-id="3037b-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-731">'Identity'</span></span>
- <span data-ttu-id="3037b-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-733">'Razor'</span></span>
- <span data-ttu-id="3037b-734">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-735">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-736">'Blazor'</span></span>
- <span data-ttu-id="3037b-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-737">'Identity'</span></span>
- <span data-ttu-id="3037b-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-739">'Razor'</span></span>
- <span data-ttu-id="3037b-740">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-741">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-742">'Blazor'</span></span>
- <span data-ttu-id="3037b-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-743">'Identity'</span></span>
- <span data-ttu-id="3037b-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-745">'Razor'</span></span>
- <span data-ttu-id="3037b-746">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-747">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-748">'Blazor'</span></span>
- <span data-ttu-id="3037b-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-749">'Identity'</span></span>
- <span data-ttu-id="3037b-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-751">'Razor'</span></span>
- <span data-ttu-id="3037b-752">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-753">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-754">'Blazor'</span></span>
- <span data-ttu-id="3037b-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-755">'Identity'</span></span>
- <span data-ttu-id="3037b-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-757">'Razor'</span></span>
- <span data-ttu-id="3037b-758">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-759">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-760">'Blazor'</span></span>
- <span data-ttu-id="3037b-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-761">'Identity'</span></span>
- <span data-ttu-id="3037b-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-763">'Razor'</span></span>
- <span data-ttu-id="3037b-764">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-765">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-766">'Blazor'</span></span>
- <span data-ttu-id="3037b-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-767">'Identity'</span></span>
- <span data-ttu-id="3037b-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-769">'Razor'</span></span>
- <span data-ttu-id="3037b-770">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-771">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-772">'Blazor'</span></span>
- <span data-ttu-id="3037b-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-773">'Identity'</span></span>
- <span data-ttu-id="3037b-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-775">'Razor'</span></span>
- <span data-ttu-id="3037b-776">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-777">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-778">'Blazor'</span></span>
- <span data-ttu-id="3037b-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-779">'Identity'</span></span>
- <span data-ttu-id="3037b-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-781">'Razor'</span></span>
- <span data-ttu-id="3037b-782">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-783">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-784">'Blazor'</span></span>
- <span data-ttu-id="3037b-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-785">'Identity'</span></span>
- <span data-ttu-id="3037b-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-787">'Razor'</span></span>
- <span data-ttu-id="3037b-788">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-789">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-790">'Blazor'</span></span>
- <span data-ttu-id="3037b-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-791">'Identity'</span></span>
- <span data-ttu-id="3037b-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-793">'Razor'</span></span>
- <span data-ttu-id="3037b-794">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-795">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-796">'Blazor'</span></span>
- <span data-ttu-id="3037b-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-797">'Identity'</span></span>
- <span data-ttu-id="3037b-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-799">'Razor'</span></span>
- <span data-ttu-id="3037b-800">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-801">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-802">'Blazor'</span></span>
- <span data-ttu-id="3037b-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-803">'Identity'</span></span>
- <span data-ttu-id="3037b-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-805">'Razor'</span></span>
- <span data-ttu-id="3037b-806">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-806">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="3037b-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="3037b-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="3037b-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3037b-809">Değer: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="3037b-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3037b-810">Değer: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="3037b-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3037b-811">Deeri`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="3037b-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="3037b-812">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-812">JSON configuration provider</span></span>

<span data-ttu-id="3037b-813">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="3037b-814">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-814">Overloads can specify:</span></span>

* <span data-ttu-id="3037b-815">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="3037b-815">Whether the file is optional.</span></span>
* <span data-ttu-id="3037b-816">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3037b-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="3037b-817">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3037b-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="3037b-818">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="3037b-818">The preceding code:</span></span>

* <span data-ttu-id="3037b-819">JSON yapılandırma sağlayıcısını, *MyConfig. JSON* dosyasını yüklemek için aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="3037b-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="3037b-820">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="3037b-821">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="3037b-822">*MyConfig. JSON* dosyasından önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="3037b-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="3037b-823">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig. JSON* dosyası geçersiz kılma ayarındaki ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3037b-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3037b-824">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="3037b-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3037b-825">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="3037b-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="3037b-826">Yukarıdaki kodda, *MyConfig. JSON* ve *MyConfig*içindeki `Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="3037b-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="3037b-827">*AppSettings. JSON* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="3037b-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="3037b-828">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="3037b-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3037b-829">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , şu *MyConfig. JSON* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="3037b-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="3037b-830">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="3037b-831">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-831">File configuration provider</span></span>

<span data-ttu-id="3037b-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="3037b-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="3037b-833">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="3037b-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="3037b-834">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="3037b-835">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="3037b-836">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="3037b-837">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-837">INI configuration provider</span></span>

<span data-ttu-id="3037b-838">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="3037b-839">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="3037b-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="3037b-840">Yukarıdaki kodda, *myıniconfig. ini* ve *myıniconfig*içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="3037b-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="3037b-841">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="3037b-842">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3037b-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3037b-843">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *Myıniconfig. ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="3037b-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="3037b-844">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="3037b-845">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-845">XML configuration provider</span></span>

<span data-ttu-id="3037b-846">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="3037b-847">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="3037b-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="3037b-848">Yukarıdaki kodda, *myXMLfile. xml* ve *myXMLfile*içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="3037b-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="3037b-849">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="3037b-850">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="3037b-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="3037b-851">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , şu *myXMLfile. xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="3037b-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="3037b-852">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-853">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="3037b-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="3037b-854">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-855">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="3037b-856">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="3037b-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3037b-857">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="3037b-857">key:attribute</span></span>
* <span data-ttu-id="3037b-858">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="3037b-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="3037b-859">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="3037b-860">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="3037b-861">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-861">The key is the file name.</span></span> <span data-ttu-id="3037b-862">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-862">The value contains the file's contents.</span></span> <span data-ttu-id="3037b-863">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="3037b-864">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="3037b-865">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3037b-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="3037b-866">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="3037b-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="3037b-867">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="3037b-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="3037b-868">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="3037b-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="3037b-869">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="3037b-870">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="3037b-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="3037b-871">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="3037b-872">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-872">Memory configuration provider</span></span>

<span data-ttu-id="3037b-873">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="3037b-874">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="3037b-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="3037b-875">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-876">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="3037b-877">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="3037b-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="3037b-878">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="3037b-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="3037b-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="3037b-879">GetValue</span></span>

<span data-ttu-id="3037b-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="3037b-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-881">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="3037b-882">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="3037b-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="3037b-883">İzleyen örnekler için aşağıdaki *Myalt bölüm. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3037b-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="3037b-884">Aşağıdaki kod, *Myalt bölüm. JSON* öğesini yapılandırma sağlayıcılarına ekler:</span><span class="sxs-lookup"><span data-stu-id="3037b-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="3037b-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="3037b-885">GetSection</span></span>

<span data-ttu-id="3037b-886">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="3037b-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="3037b-887">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="3037b-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-888">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="3037b-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-889">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="3037b-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="3037b-890">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3037b-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="3037b-891">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="3037b-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="3037b-892">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3037b-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="3037b-893">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="3037b-893">GetChildren and Exists</span></span>

<span data-ttu-id="3037b-894">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="3037b-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-895">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="3037b-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="3037b-896">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="3037b-896">Bind an array</span></span>

<span data-ttu-id="3037b-897">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="3037b-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3037b-898">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3037b-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="3037b-899">[Örnek indirmenizde](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *myArray. JSON* ' i göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3037b-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="3037b-900">Aşağıdaki kod, yapılandırma sağlayıcılarına *myArray. JSON* ekler:</span><span class="sxs-lookup"><span data-stu-id="3037b-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="3037b-901">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-902">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="3037b-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="3037b-903">Önceki çıktıda, Dizin 3 ' `value40` `"4": "value40",` te, *myArray. JSON*içinde öğesine karşılık gelen bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="3037b-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="3037b-904">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="3037b-905">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="3037b-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="3037b-906">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="3037b-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="3037b-907">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-908">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="3037b-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="3037b-909">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="3037b-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="3037b-910">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="3037b-911">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="3037b-912">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="3037b-913">Örnek indirmenin aşağıdaki *Value3. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3037b-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="3037b-914">Aşağıdaki kod *Value3. JSON* için yapılandırmayı ve şunları içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="3037b-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="3037b-915">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-916">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="3037b-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="3037b-917">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="3037b-918">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-918">Custom configuration provider</span></span>

<span data-ttu-id="3037b-919">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="3037b-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="3037b-920">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="3037b-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="3037b-921">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="3037b-922">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="3037b-923">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="3037b-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="3037b-924">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="3037b-925">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="3037b-926">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="3037b-927">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="3037b-928">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="3037b-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="3037b-929">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="3037b-930">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="3037b-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="3037b-931">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="3037b-932">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="3037b-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="3037b-933">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="3037b-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="3037b-934">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3037b-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="3037b-935">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="3037b-936">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="3037b-937">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="3037b-938">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="3037b-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="3037b-939">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3037b-939">Access configuration in Startup</span></span>

<span data-ttu-id="3037b-940">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="3037b-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="3037b-941">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="3037b-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="3037b-942">Sayfalarda erişim yapılandırması Razor</span><span class="sxs-lookup"><span data-stu-id="3037b-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="3037b-943">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler Razor :</span><span class="sxs-lookup"><span data-stu-id="3037b-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="3037b-944">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="3037b-945">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) Razor seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="3037b-946">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3037b-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="3037b-947">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="3037b-948">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-948">Configure options with a delegate</span></span>

<span data-ttu-id="3037b-949">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3037b-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="3037b-950">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3037b-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="3037b-951">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3037b-952">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="3037b-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="3037b-953">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3037b-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="3037b-954">Önceki örnekte, `Option1` ve değerleri `Option2` *appSettings. JSON* içinde belirtilmiştir ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="3037b-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="3037b-955">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="3037b-955">Host versus app configuration</span></span>

<span data-ttu-id="3037b-956">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="3037b-957">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3037b-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="3037b-958">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="3037b-959">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="3037b-960">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="3037b-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="3037b-961">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3037b-961">Default host configuration</span></span>

<span data-ttu-id="3037b-962">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="3037b-963">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="3037b-964">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="3037b-965">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="3037b-966">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="3037b-967">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="3037b-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="3037b-968">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="3037b-969">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3037b-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="3037b-970">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="3037b-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="3037b-971">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="3037b-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="3037b-972">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-972">Other configuration</span></span>

<span data-ttu-id="3037b-973">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="3037b-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="3037b-974">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="3037b-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="3037b-975">*Launch. JSON* / *Launchsettings. JSON* , geliştirme ortamı için yapılandırma dosyalarını araçlar, aşağıda açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="3037b-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="3037b-976">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="3037b-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="3037b-977">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="3037b-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="3037b-978">*Web. config* aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="3037b-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="3037b-979">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="3037b-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="3037b-980">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="3037b-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="3037b-981">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="3037b-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="3037b-982">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3037b-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3037b-983">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3037b-983">Additional resources</span></span>

* [<span data-ttu-id="3037b-984">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="3037b-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3037b-985">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="3037b-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="3037b-986">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="3037b-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="3037b-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="3037b-987">Azure Key Vault</span></span>
* <span data-ttu-id="3037b-988">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3037b-988">Azure App Configuration</span></span>
* <span data-ttu-id="3037b-989">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-989">Command-line arguments</span></span>
* <span data-ttu-id="3037b-990">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="3037b-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="3037b-991">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="3037b-991">Directory files</span></span>
* <span data-ttu-id="3037b-992">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-992">Environment variables</span></span>
* <span data-ttu-id="3037b-993">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="3037b-993">In-memory .NET objects</span></span>
* <span data-ttu-id="3037b-994">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="3037b-994">Settings files</span></span>

<span data-ttu-id="3037b-995">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="3037b-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3037b-996">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="3037b-997">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="3037b-998">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="3037b-999">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="3037b-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="3037b-1000">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3037b-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="3037b-1001">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="3037b-1001">Host versus app configuration</span></span>

<span data-ttu-id="3037b-1002">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="3037b-1003">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3037b-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="3037b-1004">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="3037b-1005">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="3037b-1006">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="3037b-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="3037b-1007">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-1007">Other configuration</span></span>

<span data-ttu-id="3037b-1008">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="3037b-1009">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="3037b-1010">*Launch. JSON* / *Launchsettings. JSON* , geliştirme ortamı için yapılandırma dosyalarını araçlar, aşağıda açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="3037b-1011">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="3037b-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="3037b-1012">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="3037b-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="3037b-1013">*Web. config* aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="3037b-1014">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="3037b-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="3037b-1015">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-1015">Default configuration</span></span>

<span data-ttu-id="3037b-1016">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="3037b-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="3037b-1017">`CreateDefaultBuilder`uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="3037b-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="3037b-1018">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="3037b-1019">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="3037b-1020">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="3037b-1021">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="3037b-1022">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="3037b-1023">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="3037b-1024">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="3037b-1025">[dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="3037b-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="3037b-1026">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="3037b-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="3037b-1027">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="3037b-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="3037b-1028">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="3037b-1029">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="3037b-1030">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="3037b-1030">Security</span></span>

<span data-ttu-id="3037b-1031">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="3037b-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="3037b-1032">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="3037b-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="3037b-1033">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="3037b-1034">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="3037b-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="3037b-1035">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="3037b-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="3037b-1036"><xref:security/app-secrets>&ndash;Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="3037b-1037">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="3037b-1038">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="3037b-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="3037b-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="3037b-1040">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3037b-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="3037b-1041">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="3037b-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="3037b-1042">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="3037b-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="3037b-1043">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="3037b-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="3037b-1044">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3037b-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="3037b-1045">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="3037b-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="3037b-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-1046">section0:key0</span></span>
* <span data-ttu-id="3037b-1047">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-1047">section0:key1</span></span>
* <span data-ttu-id="3037b-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-1048">section1:key0</span></span>
* <span data-ttu-id="3037b-1049">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-1049">section1:key1</span></span>

<span data-ttu-id="3037b-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="3037b-1051">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="3037b-1052">Kurallar</span><span class="sxs-lookup"><span data-stu-id="3037b-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="3037b-1053">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="3037b-1053">Sources and providers</span></span>

<span data-ttu-id="3037b-1054">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="3037b-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="3037b-1055">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="3037b-1056">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="3037b-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="3037b-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3037b-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="3037b-1059">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="3037b-1060">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="3037b-1061">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="3037b-1061">Keys</span></span>

<span data-ttu-id="3037b-1062">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="3037b-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="3037b-1063">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="3037b-1064">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="3037b-1065">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="3037b-1066">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="3037b-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="3037b-1067">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="3037b-1068">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="3037b-1069">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="3037b-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="3037b-1070">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="3037b-1071">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="3037b-1072">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="3037b-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3037b-1073">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="3037b-1074">Değerler</span><span class="sxs-lookup"><span data-stu-id="3037b-1074">Values</span></span>

<span data-ttu-id="3037b-1075">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="3037b-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="3037b-1076">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1076">Values are strings.</span></span>
* <span data-ttu-id="3037b-1077">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="3037b-1078">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="3037b-1078">Providers</span></span>

<span data-ttu-id="3037b-1079">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="3037b-1080">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="3037b-1080">Provider</span></span> | <span data-ttu-id="3037b-1081">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="3037b-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="3037b-1082">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1083">'Blazor'</span></span>
- <span data-ttu-id="3037b-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1084">'Identity'</span></span>
- <span data-ttu-id="3037b-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1086">'Razor'</span></span>
- <span data-ttu-id="3037b-1087">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1088">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1089">'Blazor'</span></span>
- <span data-ttu-id="3037b-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1090">'Identity'</span></span>
- <span data-ttu-id="3037b-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1092">'Razor'</span></span>
- <span data-ttu-id="3037b-1093">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1094">---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1095">'Blazor'</span></span>
- <span data-ttu-id="3037b-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1096">'Identity'</span></span>
- <span data-ttu-id="3037b-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1098">'Razor'</span></span>
- <span data-ttu-id="3037b-1099">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1100">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1101">'Blazor'</span></span>
- <span data-ttu-id="3037b-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1102">'Identity'</span></span>
- <span data-ttu-id="3037b-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1104">'Razor'</span></span>
- <span data-ttu-id="3037b-1105">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1106">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1107">'Blazor'</span></span>
- <span data-ttu-id="3037b-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1108">'Identity'</span></span>
- <span data-ttu-id="3037b-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1110">'Razor'</span></span>
- <span data-ttu-id="3037b-1111">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1112">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1113">'Blazor'</span></span>
- <span data-ttu-id="3037b-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1114">'Identity'</span></span>
- <span data-ttu-id="3037b-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1116">'Razor'</span></span>
- <span data-ttu-id="3037b-1117">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1118">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1119">'Blazor'</span></span>
- <span data-ttu-id="3037b-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1120">'Identity'</span></span>
- <span data-ttu-id="3037b-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1122">'Razor'</span></span>
- <span data-ttu-id="3037b-1123">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1124">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1125">'Blazor'</span></span>
- <span data-ttu-id="3037b-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1126">'Identity'</span></span>
- <span data-ttu-id="3037b-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1128">'Razor'</span></span>
- <span data-ttu-id="3037b-1129">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1130">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1131">'Blazor'</span></span>
- <span data-ttu-id="3037b-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1132">'Identity'</span></span>
- <span data-ttu-id="3037b-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1134">'Razor'</span></span>
- <span data-ttu-id="3037b-1135">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1136">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1137">'Blazor'</span></span>
- <span data-ttu-id="3037b-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1138">'Identity'</span></span>
- <span data-ttu-id="3037b-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1140">'Razor'</span></span>
- <span data-ttu-id="3037b-1141">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1142">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1143">'Blazor'</span></span>
- <span data-ttu-id="3037b-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1144">'Identity'</span></span>
- <span data-ttu-id="3037b-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1146">'Razor'</span></span>
- <span data-ttu-id="3037b-1147">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1148">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1149">'Blazor'</span></span>
- <span data-ttu-id="3037b-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1150">'Identity'</span></span>
- <span data-ttu-id="3037b-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1152">'Razor'</span></span>
- <span data-ttu-id="3037b-1153">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1154">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1155">'Blazor'</span></span>
- <span data-ttu-id="3037b-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1156">'Identity'</span></span>
- <span data-ttu-id="3037b-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1158">'Razor'</span></span>
- <span data-ttu-id="3037b-1159">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1160">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1161">'Blazor'</span></span>
- <span data-ttu-id="3037b-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1162">'Identity'</span></span>
- <span data-ttu-id="3037b-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1164">'Razor'</span></span>
- <span data-ttu-id="3037b-1165">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1166">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1167">'Blazor'</span></span>
- <span data-ttu-id="3037b-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1168">'Identity'</span></span>
- <span data-ttu-id="3037b-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1170">'Razor'</span></span>
- <span data-ttu-id="3037b-1171">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1172">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1173">'Blazor'</span></span>
- <span data-ttu-id="3037b-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1174">'Identity'</span></span>
- <span data-ttu-id="3037b-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1176">'Razor'</span></span>
- <span data-ttu-id="3037b-1177">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1178">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1179">'Blazor'</span></span>
- <span data-ttu-id="3037b-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1180">'Identity'</span></span>
- <span data-ttu-id="3037b-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1182">'Razor'</span></span>
- <span data-ttu-id="3037b-1183">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1184">------------------ | | [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları) | Azure Key Vault | | [Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri) | Azure Uygulama yapılandırması | | [Komut satırı yapılandırma sağlayıcısı](#command-line-configuration-provider) | Komut satırı parametreleri | | [Özel yapılandırma sağlayıcısı](#custom-configuration-provider) | Özel kaynak | | [Ortam değişkenleri yapılandırma sağlayıcısı](#environment-variables-configuration-provider) | Ortam değişkenleri | | [Dosya yapılandırma sağlayıcısı](#file-configuration-provider) | Dosyalar (ıNı, JSON, XML) | | [Dosya başına anahtar yapılandırma sağlayıcısı](#key-per-file-configuration-provider) | Dizin dosyaları | | [Bellek yapılandırma sağlayıcısı](#memory-configuration-provider) | Bellek içi Koleksiyonlar | | [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları) | Kullanıcı profili dizinindeki dosya |</span><span class="sxs-lookup"><span data-stu-id="3037b-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="3037b-1185">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="3037b-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="3037b-1186">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="3037b-1187">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="3037b-1188">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="3037b-1189">Dosyalar (*appSettings. JSON*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="3037b-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="3037b-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="3037b-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="3037b-1191">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="3037b-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="3037b-1192">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-1192">Environment variables</span></span>
1. <span data-ttu-id="3037b-1193">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3037b-1193">Command-line arguments</span></span>

<span data-ttu-id="3037b-1194">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="3037b-1195">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3037b-1196">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="3037b-1197">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="3037b-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="3037b-1198">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="3037b-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="3037b-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="3037b-1200">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="3037b-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="3037b-1201">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="3037b-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="3037b-1202">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="3037b-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="3037b-1203">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="3037b-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="3037b-1204">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="3037b-1205">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="3037b-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="3037b-1206">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3037b-1207">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="3037b-1208">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="3037b-1209">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="3037b-1210">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3037b-1211">`AddCommandLine`çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="3037b-1212">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3037b-1213">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="3037b-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="3037b-1214">*AppSettings. JSON* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="3037b-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="3037b-1215">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="3037b-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3037b-1216">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-1216">Environment variables.</span></span>

<span data-ttu-id="3037b-1217">`CreateDefaultBuilder`Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="3037b-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="3037b-1218">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="3037b-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="3037b-1219">`CreateDefaultBuilder`Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="3037b-1220">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="3037b-1221">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3037b-1222">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="3037b-1223">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="3037b-1223">**Example**</span></span>

<span data-ttu-id="3037b-1224">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="3037b-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="3037b-1225">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="3037b-1226">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="3037b-1227">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="3037b-1228">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="3037b-1229">Bağımsız değişkenler</span><span class="sxs-lookup"><span data-stu-id="3037b-1229">Arguments</span></span>

<span data-ttu-id="3037b-1230">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="3037b-1231">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="3037b-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="3037b-1232">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="3037b-1232">Key prefix</span></span>               | <span data-ttu-id="3037b-1233">Örnek</span><span class="sxs-lookup"><span data-stu-id="3037b-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="3037b-1234">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1235">'Blazor'</span></span>
- <span data-ttu-id="3037b-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1236">'Identity'</span></span>
- <span data-ttu-id="3037b-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1238">'Razor'</span></span>
- <span data-ttu-id="3037b-1239">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1240">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1241">'Blazor'</span></span>
- <span data-ttu-id="3037b-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1242">'Identity'</span></span>
- <span data-ttu-id="3037b-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1244">'Razor'</span></span>
- <span data-ttu-id="3037b-1245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1246">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1247">'Blazor'</span></span>
- <span data-ttu-id="3037b-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1248">'Identity'</span></span>
- <span data-ttu-id="3037b-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1250">'Razor'</span></span>
- <span data-ttu-id="3037b-1251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1252">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1253">'Blazor'</span></span>
- <span data-ttu-id="3037b-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1254">'Identity'</span></span>
- <span data-ttu-id="3037b-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1256">'Razor'</span></span>
- <span data-ttu-id="3037b-1257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1258">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1259">'Blazor'</span></span>
- <span data-ttu-id="3037b-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1260">'Identity'</span></span>
- <span data-ttu-id="3037b-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1262">'Razor'</span></span>
- <span data-ttu-id="3037b-1263">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1264">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1265">'Blazor'</span></span>
- <span data-ttu-id="3037b-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1266">'Identity'</span></span>
- <span data-ttu-id="3037b-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1268">'Razor'</span></span>
- <span data-ttu-id="3037b-1269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1270">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1271">'Blazor'</span></span>
- <span data-ttu-id="3037b-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1272">'Identity'</span></span>
- <span data-ttu-id="3037b-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1274">'Razor'</span></span>
- <span data-ttu-id="3037b-1275">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1276">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1277">'Blazor'</span></span>
- <span data-ttu-id="3037b-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1278">'Identity'</span></span>
- <span data-ttu-id="3037b-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1280">'Razor'</span></span>
- <span data-ttu-id="3037b-1281">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1282">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1283">'Blazor'</span></span>
- <span data-ttu-id="3037b-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1284">'Identity'</span></span>
- <span data-ttu-id="3037b-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1286">'Razor'</span></span>
- <span data-ttu-id="3037b-1287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1288">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1289">'Blazor'</span></span>
- <span data-ttu-id="3037b-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1290">'Identity'</span></span>
- <span data-ttu-id="3037b-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1292">'Razor'</span></span>
- <span data-ttu-id="3037b-1293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1294">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1295">'Blazor'</span></span>
- <span data-ttu-id="3037b-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1296">'Identity'</span></span>
- <span data-ttu-id="3037b-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1298">'Razor'</span></span>
- <span data-ttu-id="3037b-1299">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1300">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1301">'Blazor'</span></span>
- <span data-ttu-id="3037b-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1302">'Identity'</span></span>
- <span data-ttu-id="3037b-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1304">'Razor'</span></span>
- <span data-ttu-id="3037b-1305">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1306">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1307">'Blazor'</span></span>
- <span data-ttu-id="3037b-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1308">'Identity'</span></span>
- <span data-ttu-id="3037b-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1310">'Razor'</span></span>
- <span data-ttu-id="3037b-1311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1312">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1313">'Blazor'</span></span>
- <span data-ttu-id="3037b-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1314">'Identity'</span></span>
- <span data-ttu-id="3037b-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1316">'Razor'</span></span>
- <span data-ttu-id="3037b-1317">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1318">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1319">'Blazor'</span></span>
- <span data-ttu-id="3037b-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1320">'Identity'</span></span>
- <span data-ttu-id="3037b-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1322">'Razor'</span></span>
- <span data-ttu-id="3037b-1323">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1324">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1325">'Blazor'</span></span>
- <span data-ttu-id="3037b-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1326">'Identity'</span></span>
- <span data-ttu-id="3037b-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1328">'Razor'</span></span>
- <span data-ttu-id="3037b-1329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1330">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1331">'Blazor'</span></span>
- <span data-ttu-id="3037b-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1332">'Identity'</span></span>
- <span data-ttu-id="3037b-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1334">'Razor'</span></span>
- <span data-ttu-id="3037b-1335">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1336">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1337">'Blazor'</span></span>
- <span data-ttu-id="3037b-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1338">'Identity'</span></span>
- <span data-ttu-id="3037b-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1340">'Razor'</span></span>
- <span data-ttu-id="3037b-1341">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1342">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1343">'Blazor'</span></span>
- <span data-ttu-id="3037b-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1344">'Identity'</span></span>
- <span data-ttu-id="3037b-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1346">'Razor'</span></span>
- <span data-ttu-id="3037b-1347">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1348">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1349">'Blazor'</span></span>
- <span data-ttu-id="3037b-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1350">'Identity'</span></span>
- <span data-ttu-id="3037b-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1352">'Razor'</span></span>
- <span data-ttu-id="3037b-1353">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1354">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1355">'Blazor'</span></span>
- <span data-ttu-id="3037b-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1356">'Identity'</span></span>
- <span data-ttu-id="3037b-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1358">'Razor'</span></span>
- <span data-ttu-id="3037b-1359">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1360">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1361">'Blazor'</span></span>
- <span data-ttu-id="3037b-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1362">'Identity'</span></span>
- <span data-ttu-id="3037b-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1364">'Razor'</span></span>
- <span data-ttu-id="3037b-1365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1366">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1367">'Blazor'</span></span>
- <span data-ttu-id="3037b-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1368">'Identity'</span></span>
- <span data-ttu-id="3037b-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1370">'Razor'</span></span>
- <span data-ttu-id="3037b-1371">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1372">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1373">'Blazor'</span></span>
- <span data-ttu-id="3037b-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1374">'Identity'</span></span>
- <span data-ttu-id="3037b-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1376">'Razor'</span></span>
- <span data-ttu-id="3037b-1377">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1378">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1379">'Blazor'</span></span>
- <span data-ttu-id="3037b-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1380">'Identity'</span></span>
- <span data-ttu-id="3037b-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1382">'Razor'</span></span>
- <span data-ttu-id="3037b-1383">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1384">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1385">'Blazor'</span></span>
- <span data-ttu-id="3037b-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1386">'Identity'</span></span>
- <span data-ttu-id="3037b-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1388">'Razor'</span></span>
- <span data-ttu-id="3037b-1389">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1390">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1391">'Blazor'</span></span>
- <span data-ttu-id="3037b-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1392">'Identity'</span></span>
- <span data-ttu-id="3037b-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1394">'Razor'</span></span>
- <span data-ttu-id="3037b-1395">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1396">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1397">'Blazor'</span></span>
- <span data-ttu-id="3037b-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1398">'Identity'</span></span>
- <span data-ttu-id="3037b-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1400">'Razor'</span></span>
- <span data-ttu-id="3037b-1401">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1402">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1403">'Blazor'</span></span>
- <span data-ttu-id="3037b-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1404">'Identity'</span></span>
- <span data-ttu-id="3037b-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1406">'Razor'</span></span>
- <span data-ttu-id="3037b-1407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1408">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1409">'Blazor'</span></span>
- <span data-ttu-id="3037b-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1410">'Identity'</span></span>
- <span data-ttu-id="3037b-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1412">'Razor'</span></span>
- <span data-ttu-id="3037b-1413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1414">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1415">'Blazor'</span></span>
- <span data-ttu-id="3037b-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1416">'Identity'</span></span>
- <span data-ttu-id="3037b-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1418">'Razor'</span></span>
- <span data-ttu-id="3037b-1419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1420">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1421">'Blazor'</span></span>
- <span data-ttu-id="3037b-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1422">'Identity'</span></span>
- <span data-ttu-id="3037b-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1424">'Razor'</span></span>
- <span data-ttu-id="3037b-1425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1426">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1427">'Blazor'</span></span>
- <span data-ttu-id="3037b-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1428">'Identity'</span></span>
- <span data-ttu-id="3037b-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1430">'Razor'</span></span>
- <span data-ttu-id="3037b-1431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1432">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1433">'Blazor'</span></span>
- <span data-ttu-id="3037b-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1434">'Identity'</span></span>
- <span data-ttu-id="3037b-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1436">'Razor'</span></span>
- <span data-ttu-id="3037b-1437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1438">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1439">'Blazor'</span></span>
- <span data-ttu-id="3037b-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1440">'Identity'</span></span>
- <span data-ttu-id="3037b-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1442">'Razor'</span></span>
- <span data-ttu-id="3037b-1443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1444">--------------------------- | | Ön ek yok | `CommandLineKey1=value1`                               |
| İki kısa çizgi ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Eğik çizgi ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="3037b-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="3037b-1445">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="3037b-1446">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="3037b-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="3037b-1447">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="3037b-1447">Switch mappings</span></span>

<span data-ttu-id="3037b-1448">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="3037b-1449">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="3037b-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="3037b-1450">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="3037b-1451">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="3037b-1452">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="3037b-1453">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="3037b-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="3037b-1454">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="3037b-1455">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="3037b-1456">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3037b-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="3037b-1457">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3037b-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="3037b-1458">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="3037b-1459">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="3037b-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="3037b-1460">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3037b-1461">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="3037b-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="3037b-1462">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="3037b-1463">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-1463">Key</span></span>       | <span data-ttu-id="3037b-1464">Değer</span><span class="sxs-lookup"><span data-stu-id="3037b-1464">Value</span></span>             |
| ---
<span data-ttu-id="3037b-1465">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1466">'Blazor'</span></span>
- <span data-ttu-id="3037b-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1467">'Identity'</span></span>
- <span data-ttu-id="3037b-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1469">'Razor'</span></span>
- <span data-ttu-id="3037b-1470">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1471">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1472">'Blazor'</span></span>
- <span data-ttu-id="3037b-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1473">'Identity'</span></span>
- <span data-ttu-id="3037b-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1475">'Razor'</span></span>
- <span data-ttu-id="3037b-1476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1477">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1478">'Blazor'</span></span>
- <span data-ttu-id="3037b-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1479">'Identity'</span></span>
- <span data-ttu-id="3037b-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1481">'Razor'</span></span>
- <span data-ttu-id="3037b-1482">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1483">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1484">'Blazor'</span></span>
- <span data-ttu-id="3037b-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1485">'Identity'</span></span>
- <span data-ttu-id="3037b-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1487">'Razor'</span></span>
- <span data-ttu-id="3037b-1488">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1489">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1490">'Blazor'</span></span>
- <span data-ttu-id="3037b-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1491">'Identity'</span></span>
- <span data-ttu-id="3037b-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1493">'Razor'</span></span>
- <span data-ttu-id="3037b-1494">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1495">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1496">'Blazor'</span></span>
- <span data-ttu-id="3037b-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1497">'Identity'</span></span>
- <span data-ttu-id="3037b-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1499">'Razor'</span></span>
- <span data-ttu-id="3037b-1500">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1501">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1502">'Blazor'</span></span>
- <span data-ttu-id="3037b-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1503">'Identity'</span></span>
- <span data-ttu-id="3037b-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1505">'Razor'</span></span>
- <span data-ttu-id="3037b-1506">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1507">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1508">'Blazor'</span></span>
- <span data-ttu-id="3037b-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1509">'Identity'</span></span>
- <span data-ttu-id="3037b-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1511">'Razor'</span></span>
- <span data-ttu-id="3037b-1512">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="3037b-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="3037b-1514">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="3037b-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="3037b-1515">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="3037b-1516">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-1516">Key</span></span>               | <span data-ttu-id="3037b-1517">Değer</span><span class="sxs-lookup"><span data-stu-id="3037b-1517">Value</span></span>    |
| ---
<span data-ttu-id="3037b-1518">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1519">'Blazor'</span></span>
- <span data-ttu-id="3037b-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1520">'Identity'</span></span>
- <span data-ttu-id="3037b-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1522">'Razor'</span></span>
- <span data-ttu-id="3037b-1523">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1524">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1525">'Blazor'</span></span>
- <span data-ttu-id="3037b-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1526">'Identity'</span></span>
- <span data-ttu-id="3037b-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1528">'Razor'</span></span>
- <span data-ttu-id="3037b-1529">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1530">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1531">'Blazor'</span></span>
- <span data-ttu-id="3037b-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1532">'Identity'</span></span>
- <span data-ttu-id="3037b-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1534">'Razor'</span></span>
- <span data-ttu-id="3037b-1535">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1536">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1537">'Blazor'</span></span>
- <span data-ttu-id="3037b-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1538">'Identity'</span></span>
- <span data-ttu-id="3037b-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1540">'Razor'</span></span>
- <span data-ttu-id="3037b-1541">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1542">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1543">'Blazor'</span></span>
- <span data-ttu-id="3037b-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1544">'Identity'</span></span>
- <span data-ttu-id="3037b-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1546">'Razor'</span></span>
- <span data-ttu-id="3037b-1547">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1548">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1549">'Blazor'</span></span>
- <span data-ttu-id="3037b-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1550">'Identity'</span></span>
- <span data-ttu-id="3037b-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1552">'Razor'</span></span>
- <span data-ttu-id="3037b-1553">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1554">--------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1555">'Blazor'</span></span>
- <span data-ttu-id="3037b-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1556">'Identity'</span></span>
- <span data-ttu-id="3037b-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1558">'Razor'</span></span>
- <span data-ttu-id="3037b-1559">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1560">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1561">'Blazor'</span></span>
- <span data-ttu-id="3037b-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1562">'Identity'</span></span>
- <span data-ttu-id="3037b-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1564">'Razor'</span></span>
- <span data-ttu-id="3037b-1565">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="3037b-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="3037b-1567">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="3037b-1568">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="3037b-1569">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="3037b-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="3037b-1571">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="3037b-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="3037b-1572">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="3037b-1573">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3037b-1574">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="3037b-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="3037b-1575">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="3037b-1576">*AppSettings. JSON* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="3037b-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="3037b-1577">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="3037b-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3037b-1578">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-1578">Command-line arguments.</span></span>

<span data-ttu-id="3037b-1579">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="3037b-1580">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="3037b-1581">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="3037b-1582">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="3037b-1583">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="3037b-1583">**Example**</span></span>

<span data-ttu-id="3037b-1584">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="3037b-1585">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1585">Run the sample app.</span></span> <span data-ttu-id="3037b-1586">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="3037b-1587">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="3037b-1588">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="3037b-1589">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="3037b-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="3037b-1590">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="3037b-1591">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3037b-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="3037b-1592">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="3037b-1592">Prefixes</span></span>

<span data-ttu-id="3037b-1593">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="3037b-1594">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="3037b-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="3037b-1595">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="3037b-1596">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="3037b-1597">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3037b-1598">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="3037b-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="3037b-1599">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="3037b-1600">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="3037b-1601">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="3037b-1601">Connection string prefix</span></span> | <span data-ttu-id="3037b-1602">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="3037b-1602">Provider</span></span> |
| ---
<span data-ttu-id="3037b-1603">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1604">'Blazor'</span></span>
- <span data-ttu-id="3037b-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1605">'Identity'</span></span>
- <span data-ttu-id="3037b-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1607">'Razor'</span></span>
- <span data-ttu-id="3037b-1608">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1609">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1610">'Blazor'</span></span>
- <span data-ttu-id="3037b-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1611">'Identity'</span></span>
- <span data-ttu-id="3037b-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1613">'Razor'</span></span>
- <span data-ttu-id="3037b-1614">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1615">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1616">'Blazor'</span></span>
- <span data-ttu-id="3037b-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1617">'Identity'</span></span>
- <span data-ttu-id="3037b-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1619">'Razor'</span></span>
- <span data-ttu-id="3037b-1620">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1621">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1622">'Blazor'</span></span>
- <span data-ttu-id="3037b-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1623">'Identity'</span></span>
- <span data-ttu-id="3037b-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1625">'Razor'</span></span>
- <span data-ttu-id="3037b-1626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1627">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1628">'Blazor'</span></span>
- <span data-ttu-id="3037b-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1629">'Identity'</span></span>
- <span data-ttu-id="3037b-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1631">'Razor'</span></span>
- <span data-ttu-id="3037b-1632">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1633">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1634">'Blazor'</span></span>
- <span data-ttu-id="3037b-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1635">'Identity'</span></span>
- <span data-ttu-id="3037b-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1637">'Razor'</span></span>
- <span data-ttu-id="3037b-1638">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1639">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1640">'Blazor'</span></span>
- <span data-ttu-id="3037b-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1641">'Identity'</span></span>
- <span data-ttu-id="3037b-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1643">'Razor'</span></span>
- <span data-ttu-id="3037b-1644">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1645">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1646">'Blazor'</span></span>
- <span data-ttu-id="3037b-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1647">'Identity'</span></span>
- <span data-ttu-id="3037b-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1649">'Razor'</span></span>
- <span data-ttu-id="3037b-1650">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1651">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1652">'Blazor'</span></span>
- <span data-ttu-id="3037b-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1653">'Identity'</span></span>
- <span data-ttu-id="3037b-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1655">'Razor'</span></span>
- <span data-ttu-id="3037b-1656">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1657">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1658">'Blazor'</span></span>
- <span data-ttu-id="3037b-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1659">'Identity'</span></span>
- <span data-ttu-id="3037b-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1661">'Razor'</span></span>
- <span data-ttu-id="3037b-1662">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1663">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1664">'Blazor'</span></span>
- <span data-ttu-id="3037b-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1665">'Identity'</span></span>
- <span data-ttu-id="3037b-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1667">'Razor'</span></span>
- <span data-ttu-id="3037b-1668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1669">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1670">'Blazor'</span></span>
- <span data-ttu-id="3037b-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1671">'Identity'</span></span>
- <span data-ttu-id="3037b-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1673">'Razor'</span></span>
- <span data-ttu-id="3037b-1674">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1675">---- | | `CUSTOMCONNSTR_` | Özel sağlayıcı | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="3037b-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="3037b-1676">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="3037b-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="3037b-1677">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="3037b-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="3037b-1678">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="3037b-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="3037b-1679">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="3037b-1679">Environment variable key</span></span> | <span data-ttu-id="3037b-1680">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="3037b-1680">Converted configuration key</span></span> | <span data-ttu-id="3037b-1681">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="3037b-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="3037b-1682">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1683">'Blazor'</span></span>
- <span data-ttu-id="3037b-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1684">'Identity'</span></span>
- <span data-ttu-id="3037b-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1686">'Razor'</span></span>
- <span data-ttu-id="3037b-1687">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1688">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1689">'Blazor'</span></span>
- <span data-ttu-id="3037b-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1690">'Identity'</span></span>
- <span data-ttu-id="3037b-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1692">'Razor'</span></span>
- <span data-ttu-id="3037b-1693">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1694">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1695">'Blazor'</span></span>
- <span data-ttu-id="3037b-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1696">'Identity'</span></span>
- <span data-ttu-id="3037b-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1698">'Razor'</span></span>
- <span data-ttu-id="3037b-1699">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1700">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1701">'Blazor'</span></span>
- <span data-ttu-id="3037b-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1702">'Identity'</span></span>
- <span data-ttu-id="3037b-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1704">'Razor'</span></span>
- <span data-ttu-id="3037b-1705">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1706">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1707">'Blazor'</span></span>
- <span data-ttu-id="3037b-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1708">'Identity'</span></span>
- <span data-ttu-id="3037b-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1710">'Razor'</span></span>
- <span data-ttu-id="3037b-1711">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1712">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1713">'Blazor'</span></span>
- <span data-ttu-id="3037b-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1714">'Identity'</span></span>
- <span data-ttu-id="3037b-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1716">'Razor'</span></span>
- <span data-ttu-id="3037b-1717">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1718">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1719">'Blazor'</span></span>
- <span data-ttu-id="3037b-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1720">'Identity'</span></span>
- <span data-ttu-id="3037b-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1722">'Razor'</span></span>
- <span data-ttu-id="3037b-1723">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1724">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1725">'Blazor'</span></span>
- <span data-ttu-id="3037b-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1726">'Identity'</span></span>
- <span data-ttu-id="3037b-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1728">'Razor'</span></span>
- <span data-ttu-id="3037b-1729">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1730">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1731">'Blazor'</span></span>
- <span data-ttu-id="3037b-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1732">'Identity'</span></span>
- <span data-ttu-id="3037b-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1734">'Razor'</span></span>
- <span data-ttu-id="3037b-1735">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1736">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1737">'Blazor'</span></span>
- <span data-ttu-id="3037b-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1738">'Identity'</span></span>
- <span data-ttu-id="3037b-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1740">'Razor'</span></span>
- <span data-ttu-id="3037b-1741">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1742">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1743">'Blazor'</span></span>
- <span data-ttu-id="3037b-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1744">'Identity'</span></span>
- <span data-ttu-id="3037b-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1746">'Razor'</span></span>
- <span data-ttu-id="3037b-1747">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1748">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1749">'Blazor'</span></span>
- <span data-ttu-id="3037b-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1750">'Identity'</span></span>
- <span data-ttu-id="3037b-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1752">'Razor'</span></span>
- <span data-ttu-id="3037b-1753">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1754">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1755">'Blazor'</span></span>
- <span data-ttu-id="3037b-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1756">'Identity'</span></span>
- <span data-ttu-id="3037b-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1758">'Razor'</span></span>
- <span data-ttu-id="3037b-1759">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1760">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1761">'Blazor'</span></span>
- <span data-ttu-id="3037b-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1762">'Identity'</span></span>
- <span data-ttu-id="3037b-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1764">'Razor'</span></span>
- <span data-ttu-id="3037b-1765">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1766">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1767">'Blazor'</span></span>
- <span data-ttu-id="3037b-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1768">'Identity'</span></span>
- <span data-ttu-id="3037b-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1770">'Razor'</span></span>
- <span data-ttu-id="3037b-1771">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1772">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1773">'Blazor'</span></span>
- <span data-ttu-id="3037b-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1774">'Identity'</span></span>
- <span data-ttu-id="3037b-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1776">'Razor'</span></span>
- <span data-ttu-id="3037b-1777">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1778">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1779">'Blazor'</span></span>
- <span data-ttu-id="3037b-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1780">'Identity'</span></span>
- <span data-ttu-id="3037b-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1782">'Razor'</span></span>
- <span data-ttu-id="3037b-1783">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1784">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1785">'Blazor'</span></span>
- <span data-ttu-id="3037b-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1786">'Identity'</span></span>
- <span data-ttu-id="3037b-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1788">'Razor'</span></span>
- <span data-ttu-id="3037b-1789">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1790">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1791">'Blazor'</span></span>
- <span data-ttu-id="3037b-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1792">'Identity'</span></span>
- <span data-ttu-id="3037b-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1794">'Razor'</span></span>
- <span data-ttu-id="3037b-1795">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1796">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1797">'Blazor'</span></span>
- <span data-ttu-id="3037b-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1798">'Identity'</span></span>
- <span data-ttu-id="3037b-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1800">'Razor'</span></span>
- <span data-ttu-id="3037b-1801">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1802">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1803">'Blazor'</span></span>
- <span data-ttu-id="3037b-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1804">'Identity'</span></span>
- <span data-ttu-id="3037b-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1806">'Razor'</span></span>
- <span data-ttu-id="3037b-1807">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-1808">-------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1809">'Blazor'</span></span>
- <span data-ttu-id="3037b-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1810">'Identity'</span></span>
- <span data-ttu-id="3037b-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1812">'Razor'</span></span>
- <span data-ttu-id="3037b-1813">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1814">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1815">'Blazor'</span></span>
- <span data-ttu-id="3037b-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1816">'Identity'</span></span>
- <span data-ttu-id="3037b-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1818">'Razor'</span></span>
- <span data-ttu-id="3037b-1819">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1820">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1821">'Blazor'</span></span>
- <span data-ttu-id="3037b-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1822">'Identity'</span></span>
- <span data-ttu-id="3037b-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1824">'Razor'</span></span>
- <span data-ttu-id="3037b-1825">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1826">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1827">'Blazor'</span></span>
- <span data-ttu-id="3037b-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1828">'Identity'</span></span>
- <span data-ttu-id="3037b-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1830">'Razor'</span></span>
- <span data-ttu-id="3037b-1831">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1832">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1833">'Blazor'</span></span>
- <span data-ttu-id="3037b-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1834">'Identity'</span></span>
- <span data-ttu-id="3037b-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1836">'Razor'</span></span>
- <span data-ttu-id="3037b-1837">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1838">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1839">'Blazor'</span></span>
- <span data-ttu-id="3037b-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1840">'Identity'</span></span>
- <span data-ttu-id="3037b-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1842">'Razor'</span></span>
- <span data-ttu-id="3037b-1843">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1844">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1845">'Blazor'</span></span>
- <span data-ttu-id="3037b-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1846">'Identity'</span></span>
- <span data-ttu-id="3037b-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1848">'Razor'</span></span>
- <span data-ttu-id="3037b-1849">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1850">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1851">'Blazor'</span></span>
- <span data-ttu-id="3037b-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1852">'Identity'</span></span>
- <span data-ttu-id="3037b-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1854">'Razor'</span></span>
- <span data-ttu-id="3037b-1855">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1856">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1857">'Blazor'</span></span>
- <span data-ttu-id="3037b-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1858">'Identity'</span></span>
- <span data-ttu-id="3037b-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1860">'Razor'</span></span>
- <span data-ttu-id="3037b-1861">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1862">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1863">'Blazor'</span></span>
- <span data-ttu-id="3037b-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1864">'Identity'</span></span>
- <span data-ttu-id="3037b-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1866">'Razor'</span></span>
- <span data-ttu-id="3037b-1867">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1868">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1869">'Blazor'</span></span>
- <span data-ttu-id="3037b-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1870">'Identity'</span></span>
- <span data-ttu-id="3037b-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1872">'Razor'</span></span>
- <span data-ttu-id="3037b-1873">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1874">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1875">'Blazor'</span></span>
- <span data-ttu-id="3037b-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1876">'Identity'</span></span>
- <span data-ttu-id="3037b-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1878">'Razor'</span></span>
- <span data-ttu-id="3037b-1879">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1880">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1881">'Blazor'</span></span>
- <span data-ttu-id="3037b-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1882">'Identity'</span></span>
- <span data-ttu-id="3037b-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1884">'Razor'</span></span>
- <span data-ttu-id="3037b-1885">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1886">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1887">'Blazor'</span></span>
- <span data-ttu-id="3037b-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1888">'Identity'</span></span>
- <span data-ttu-id="3037b-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1890">'Razor'</span></span>
- <span data-ttu-id="3037b-1891">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1892">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1893">'Blazor'</span></span>
- <span data-ttu-id="3037b-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1894">'Identity'</span></span>
- <span data-ttu-id="3037b-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1896">'Razor'</span></span>
- <span data-ttu-id="3037b-1897">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1898">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1899">'Blazor'</span></span>
- <span data-ttu-id="3037b-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1900">'Identity'</span></span>
- <span data-ttu-id="3037b-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1902">'Razor'</span></span>
- <span data-ttu-id="3037b-1903">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1904">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1905">'Blazor'</span></span>
- <span data-ttu-id="3037b-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1906">'Identity'</span></span>
- <span data-ttu-id="3037b-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1908">'Razor'</span></span>
- <span data-ttu-id="3037b-1909">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1910">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1911">'Blazor'</span></span>
- <span data-ttu-id="3037b-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1912">'Identity'</span></span>
- <span data-ttu-id="3037b-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1914">'Razor'</span></span>
- <span data-ttu-id="3037b-1915">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1916">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1917">'Blazor'</span></span>
- <span data-ttu-id="3037b-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1918">'Identity'</span></span>
- <span data-ttu-id="3037b-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1920">'Razor'</span></span>
- <span data-ttu-id="3037b-1921">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1922">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1923">'Blazor'</span></span>
- <span data-ttu-id="3037b-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1924">'Identity'</span></span>
- <span data-ttu-id="3037b-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1926">'Razor'</span></span>
- <span data-ttu-id="3037b-1927">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1928">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1929">'Blazor'</span></span>
- <span data-ttu-id="3037b-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1930">'Identity'</span></span>
- <span data-ttu-id="3037b-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1932">'Razor'</span></span>
- <span data-ttu-id="3037b-1933">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1934">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1935">'Blazor'</span></span>
- <span data-ttu-id="3037b-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1936">'Identity'</span></span>
- <span data-ttu-id="3037b-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1938">'Razor'</span></span>
- <span data-ttu-id="3037b-1939">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1940">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1941">'Blazor'</span></span>
- <span data-ttu-id="3037b-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1942">'Identity'</span></span>
- <span data-ttu-id="3037b-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1944">'Razor'</span></span>
- <span data-ttu-id="3037b-1945">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1946">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1947">'Blazor'</span></span>
- <span data-ttu-id="3037b-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1948">'Identity'</span></span>
- <span data-ttu-id="3037b-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1950">'Razor'</span></span>
- <span data-ttu-id="3037b-1951">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1952">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1953">'Blazor'</span></span>
- <span data-ttu-id="3037b-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1954">'Identity'</span></span>
- <span data-ttu-id="3037b-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1956">'Razor'</span></span>
- <span data-ttu-id="3037b-1957">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1958">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1959">'Blazor'</span></span>
- <span data-ttu-id="3037b-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1960">'Identity'</span></span>
- <span data-ttu-id="3037b-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1962">'Razor'</span></span>
- <span data-ttu-id="3037b-1963">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1964">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1965">'Blazor'</span></span>
- <span data-ttu-id="3037b-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1966">'Identity'</span></span>
- <span data-ttu-id="3037b-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1968">'Razor'</span></span>
- <span data-ttu-id="3037b-1969">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1970">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1971">'Blazor'</span></span>
- <span data-ttu-id="3037b-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1972">'Identity'</span></span>
- <span data-ttu-id="3037b-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1974">'Razor'</span></span>
- <span data-ttu-id="3037b-1975">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1976">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1977">'Blazor'</span></span>
- <span data-ttu-id="3037b-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1978">'Identity'</span></span>
- <span data-ttu-id="3037b-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1980">'Razor'</span></span>
- <span data-ttu-id="3037b-1981">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1982">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1983">'Blazor'</span></span>
- <span data-ttu-id="3037b-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1984">'Identity'</span></span>
- <span data-ttu-id="3037b-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1986">'Razor'</span></span>
- <span data-ttu-id="3037b-1987">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1988">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1989">'Blazor'</span></span>
- <span data-ttu-id="3037b-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1990">'Identity'</span></span>
- <span data-ttu-id="3037b-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1992">'Razor'</span></span>
- <span data-ttu-id="3037b-1993">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-1994">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1995">'Blazor'</span></span>
- <span data-ttu-id="3037b-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-1996">'Identity'</span></span>
- <span data-ttu-id="3037b-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-1998">'Razor'</span></span>
- <span data-ttu-id="3037b-1999">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2000">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2001">'Blazor'</span></span>
- <span data-ttu-id="3037b-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2002">'Identity'</span></span>
- <span data-ttu-id="3037b-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2004">'Razor'</span></span>
- <span data-ttu-id="3037b-2005">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2006">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2007">'Blazor'</span></span>
- <span data-ttu-id="3037b-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2008">'Identity'</span></span>
- <span data-ttu-id="3037b-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2010">'Razor'</span></span>
- <span data-ttu-id="3037b-2011">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2012">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2013">'Blazor'</span></span>
- <span data-ttu-id="3037b-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2014">'Identity'</span></span>
- <span data-ttu-id="3037b-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2016">'Razor'</span></span>
- <span data-ttu-id="3037b-2017">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2018">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2019">'Blazor'</span></span>
- <span data-ttu-id="3037b-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2020">'Identity'</span></span>
- <span data-ttu-id="3037b-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2022">'Razor'</span></span>
- <span data-ttu-id="3037b-2023">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2024">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2025">'Blazor'</span></span>
- <span data-ttu-id="3037b-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2026">'Identity'</span></span>
- <span data-ttu-id="3037b-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2028">'Razor'</span></span>
- <span data-ttu-id="3037b-2029">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="3037b-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="3037b-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3037b-2032">Değer: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3037b-2033">Değer: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="3037b-2034">Deeri`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="3037b-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="3037b-2035">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="3037b-2035">**Example**</span></span>

<span data-ttu-id="3037b-2036">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3037b-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="3037b-2037">Ad &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="3037b-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="3037b-2038">Değer &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="3037b-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="3037b-2039">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="3037b-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="3037b-2040">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2040">File Configuration Provider</span></span>

<span data-ttu-id="3037b-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="3037b-2042">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="3037b-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="3037b-2043">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="3037b-2044">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="3037b-2045">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="3037b-2046">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2046">INI Configuration Provider</span></span>

<span data-ttu-id="3037b-2047">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="3037b-2048">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3037b-2049">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="3037b-2050">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="3037b-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="3037b-2051">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="3037b-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="3037b-2052">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="3037b-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="3037b-2054">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="3037b-2055">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="3037b-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="3037b-2056">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3037b-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2057">section0:key0</span></span>
* <span data-ttu-id="3037b-2058">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2058">section0:key1</span></span>
* <span data-ttu-id="3037b-2059">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-2059">section1:subsection:key</span></span>
* <span data-ttu-id="3037b-2060">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="3037b-2061">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="3037b-2062">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="3037b-2063">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="3037b-2064">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3037b-2065">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="3037b-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="3037b-2066">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="3037b-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="3037b-2067">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="3037b-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="3037b-2069">`AddJsonFile`, ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3037b-2070">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="3037b-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="3037b-2071">*appSettings. JSON* &ndash; Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="3037b-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="3037b-2072">Dosyanın ortam sürümü, *appSettings. JSON* dosyası tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="3037b-2073">*appSettings. {Environment}. JSON* &ndash; dosyanın ortam sürümü [ıhostingenvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="3037b-2074">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="3037b-2075">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="3037b-2076">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-2076">Environment variables.</span></span>
* <span data-ttu-id="3037b-2077">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="3037b-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="3037b-2078">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="3037b-2078">Command-line arguments.</span></span>

<span data-ttu-id="3037b-2079">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3037b-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="3037b-2080">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3037b-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="3037b-2081">`ConfigureAppConfiguration`Ana bilgisayarı, *appSettings. JSON* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere oluştururken çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="3037b-2082">**Örneğinde**</span><span class="sxs-lookup"><span data-stu-id="3037b-2082">**Example**</span></span>

<span data-ttu-id="3037b-2083">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="3037b-2084">İlk çağrı, `AddJsonFile` *appSettings. JSON*' dan yapılandırmayı yükler:</span><span class="sxs-lookup"><span data-stu-id="3037b-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="3037b-2085">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="3037b-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="3037b-2086">*AppSettings için. Geliştirme. JSON* örnek uygulamada aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="3037b-2087">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2087">Run the sample app.</span></span> <span data-ttu-id="3037b-2088">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="3037b-2089">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="3037b-2090">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="3037b-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="3037b-2091">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="3037b-2092">*Properties/launchSettings. JSON* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="3037b-2093">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="3037b-2094">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="3037b-2095">AppSettings içindeki ayarlar *. Development. JSON* artık *appSettings. JSON*içindeki ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="3037b-2096">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="3037b-2097">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2097">XML Configuration Provider</span></span>

<span data-ttu-id="3037b-2098">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="3037b-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="3037b-2099">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3037b-2100">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="3037b-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="3037b-2101">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="3037b-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="3037b-2102">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="3037b-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="3037b-2104">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="3037b-2105">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="3037b-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="3037b-2106">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="3037b-2107">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="3037b-2108">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3037b-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2109">section0:key0</span></span>
* <span data-ttu-id="3037b-2110">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2110">section0:key1</span></span>
* <span data-ttu-id="3037b-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2111">section1:key0</span></span>
* <span data-ttu-id="3037b-2112">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2112">section1:key1</span></span>

<span data-ttu-id="3037b-2113">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="3037b-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="3037b-2114">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3037b-2115">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="3037b-2116">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="3037b-2117">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="3037b-2118">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2118">section:section1:key:key1</span></span>

<span data-ttu-id="3037b-2119">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="3037b-2120">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="3037b-2121">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="3037b-2121">key:attribute</span></span>
* <span data-ttu-id="3037b-2122">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="3037b-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="3037b-2123">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="3037b-2124">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="3037b-2125">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2125">The key is the file name.</span></span> <span data-ttu-id="3037b-2126">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2126">The value contains the file's contents.</span></span> <span data-ttu-id="3037b-2127">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="3037b-2128">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="3037b-2129">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="3037b-2130">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="3037b-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="3037b-2131">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="3037b-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="3037b-2132">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="3037b-2133">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="3037b-2134">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="3037b-2135">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="3037b-2136">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="3037b-2137">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="3037b-2138">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="3037b-2139">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="3037b-2140">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="3037b-2141">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3037b-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="3037b-2142">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="3037b-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="3037b-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="3037b-2143">GetValue</span></span>

<span data-ttu-id="3037b-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3037b-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="3037b-2145">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="3037b-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="3037b-2146">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="3037b-2146">The following example:</span></span>

* <span data-ttu-id="3037b-2147">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="3037b-2148">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="3037b-2149">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="3037b-2150">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="3037b-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="3037b-2151">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="3037b-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="3037b-2152">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="3037b-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="3037b-2153">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="3037b-2154">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="3037b-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="3037b-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2155">section0:key0</span></span>
* <span data-ttu-id="3037b-2156">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2156">section0:key1</span></span>
* <span data-ttu-id="3037b-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2157">section1:key0</span></span>
* <span data-ttu-id="3037b-2158">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2158">section1:key1</span></span>
* <span data-ttu-id="3037b-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="3037b-2160">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="3037b-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="3037b-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="3037b-2162">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="3037b-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="3037b-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="3037b-2163">GetSection</span></span>

<span data-ttu-id="3037b-2164">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="3037b-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="3037b-2165"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="3037b-2166">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="3037b-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="3037b-2167">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="3037b-2168">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="3037b-2169">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3037b-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="3037b-2170">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="3037b-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="3037b-2171">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3037b-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="3037b-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="3037b-2172">GetChildren</span></span>

<span data-ttu-id="3037b-2173">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="3037b-2174">Var</span><span class="sxs-lookup"><span data-stu-id="3037b-2174">Exists</span></span>

<span data-ttu-id="3037b-2175">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="3037b-2176">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="3037b-2177">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="3037b-2177">Bind to an object graph</span></span>

<span data-ttu-id="3037b-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>, bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="3037b-2179">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="3037b-2180">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="3037b-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="3037b-2181">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow. xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="3037b-2182">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="3037b-2183">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="3037b-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="3037b-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="3037b-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="3037b-2185">`Get<T>`, kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="3037b-2186">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="3037b-2187">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="3037b-2187">Bind an array to a class</span></span>

<span data-ttu-id="3037b-2188">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="3037b-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="3037b-2189">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="3037b-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="3037b-2190">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="3037b-2191">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2191">Binding is provided by convention.</span></span> <span data-ttu-id="3037b-2192">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="3037b-2193">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="3037b-2193">**In-memory array processing**</span></span>

<span data-ttu-id="3037b-2194">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="3037b-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="3037b-2195">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-2195">Key</span></span>             | <span data-ttu-id="3037b-2196">Değer</span><span class="sxs-lookup"><span data-stu-id="3037b-2196">Value</span></span>  |
| :---
<span data-ttu-id="3037b-2197">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2198">'Blazor'</span></span>
- <span data-ttu-id="3037b-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2199">'Identity'</span></span>
- <span data-ttu-id="3037b-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2201">'Razor'</span></span>
- <span data-ttu-id="3037b-2202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2203">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2204">'Blazor'</span></span>
- <span data-ttu-id="3037b-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2205">'Identity'</span></span>
- <span data-ttu-id="3037b-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2207">'Razor'</span></span>
- <span data-ttu-id="3037b-2208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2209">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2210">'Blazor'</span></span>
- <span data-ttu-id="3037b-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2211">'Identity'</span></span>
- <span data-ttu-id="3037b-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2213">'Razor'</span></span>
- <span data-ttu-id="3037b-2214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2215">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2216">'Blazor'</span></span>
- <span data-ttu-id="3037b-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2217">'Identity'</span></span>
- <span data-ttu-id="3037b-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2219">'Razor'</span></span>
- <span data-ttu-id="3037b-2220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2221">-------: | :----: | | dizi: girdiler: 0 | value0 | | dizi: girdiler: 1 | Değer1 | | dizi: girdiler: 2 | değer2 | | dizi: girdiler: 4 | value4 | | dizi: girdiler: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="3037b-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="3037b-2222">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="3037b-2223">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="3037b-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="3037b-2224">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="3037b-2225">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="3037b-2226">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3037b-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="3037b-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="3037b-2228">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="3037b-2229">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="3037b-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="3037b-2230">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="3037b-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="3037b-2231">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2232">'Blazor'</span></span>
- <span data-ttu-id="3037b-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2233">'Identity'</span></span>
- <span data-ttu-id="3037b-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2235">'Razor'</span></span>
- <span data-ttu-id="3037b-2236">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2237">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2238">'Blazor'</span></span>
- <span data-ttu-id="3037b-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2239">'Identity'</span></span>
- <span data-ttu-id="3037b-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2241">'Razor'</span></span>
- <span data-ttu-id="3037b-2242">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2243">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2244">'Blazor'</span></span>
- <span data-ttu-id="3037b-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2245">'Identity'</span></span>
- <span data-ttu-id="3037b-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2247">'Razor'</span></span>
- <span data-ttu-id="3037b-2248">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2249">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2250">'Blazor'</span></span>
- <span data-ttu-id="3037b-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2251">'Identity'</span></span>
- <span data-ttu-id="3037b-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2253">'Razor'</span></span>
- <span data-ttu-id="3037b-2254">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2255">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2256">'Blazor'</span></span>
- <span data-ttu-id="3037b-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2257">'Identity'</span></span>
- <span data-ttu-id="3037b-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2259">'Razor'</span></span>
- <span data-ttu-id="3037b-2260">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2261">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2262">'Blazor'</span></span>
- <span data-ttu-id="3037b-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2263">'Identity'</span></span>
- <span data-ttu-id="3037b-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2265">'Razor'</span></span>
- <span data-ttu-id="3037b-2266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2267">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2268">'Blazor'</span></span>
- <span data-ttu-id="3037b-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2269">'Identity'</span></span>
- <span data-ttu-id="3037b-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2271">'Razor'</span></span>
- <span data-ttu-id="3037b-2272">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2273">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2274">'Blazor'</span></span>
- <span data-ttu-id="3037b-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2275">'Identity'</span></span>
- <span data-ttu-id="3037b-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2277">'Razor'</span></span>
- <span data-ttu-id="3037b-2278">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2279">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2280">'Blazor'</span></span>
- <span data-ttu-id="3037b-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2281">'Identity'</span></span>
- <span data-ttu-id="3037b-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2283">'Razor'</span></span>
- <span data-ttu-id="3037b-2284">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2285">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2286">'Blazor'</span></span>
- <span data-ttu-id="3037b-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2287">'Identity'</span></span>
- <span data-ttu-id="3037b-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2289">'Razor'</span></span>
- <span data-ttu-id="3037b-2290">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2291">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2292">'Blazor'</span></span>
- <span data-ttu-id="3037b-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2293">'Identity'</span></span>
- <span data-ttu-id="3037b-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2295">'Razor'</span></span>
- <span data-ttu-id="3037b-2296">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2297">-------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2298">'Blazor'</span></span>
- <span data-ttu-id="3037b-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2299">'Identity'</span></span>
- <span data-ttu-id="3037b-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2301">'Razor'</span></span>
- <span data-ttu-id="3037b-2302">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2303">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2304">'Blazor'</span></span>
- <span data-ttu-id="3037b-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2305">'Identity'</span></span>
- <span data-ttu-id="3037b-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2307">'Razor'</span></span>
- <span data-ttu-id="3037b-2308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2309">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2310">'Blazor'</span></span>
- <span data-ttu-id="3037b-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2311">'Identity'</span></span>
- <span data-ttu-id="3037b-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2313">'Razor'</span></span>
- <span data-ttu-id="3037b-2314">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2315">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2316">'Blazor'</span></span>
- <span data-ttu-id="3037b-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2317">'Identity'</span></span>
- <span data-ttu-id="3037b-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2319">'Razor'</span></span>
- <span data-ttu-id="3037b-2320">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2321">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2322">'Blazor'</span></span>
- <span data-ttu-id="3037b-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2323">'Identity'</span></span>
- <span data-ttu-id="3037b-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2325">'Razor'</span></span>
- <span data-ttu-id="3037b-2326">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2327">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2328">'Blazor'</span></span>
- <span data-ttu-id="3037b-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2329">'Identity'</span></span>
- <span data-ttu-id="3037b-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2331">'Razor'</span></span>
- <span data-ttu-id="3037b-2332">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2334">'Blazor'</span></span>
- <span data-ttu-id="3037b-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2335">'Identity'</span></span>
- <span data-ttu-id="3037b-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2337">'Razor'</span></span>
- <span data-ttu-id="3037b-2338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2340">'Blazor'</span></span>
- <span data-ttu-id="3037b-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2341">'Identity'</span></span>
- <span data-ttu-id="3037b-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2343">'Razor'</span></span>
- <span data-ttu-id="3037b-2344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2346">'Blazor'</span></span>
- <span data-ttu-id="3037b-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2347">'Identity'</span></span>
- <span data-ttu-id="3037b-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2349">'Razor'</span></span>
- <span data-ttu-id="3037b-2350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2352">'Blazor'</span></span>
- <span data-ttu-id="3037b-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2353">'Identity'</span></span>
- <span data-ttu-id="3037b-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2355">'Razor'</span></span>
- <span data-ttu-id="3037b-2356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2358">'Blazor'</span></span>
- <span data-ttu-id="3037b-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2359">'Identity'</span></span>
- <span data-ttu-id="3037b-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2361">'Razor'</span></span>
- <span data-ttu-id="3037b-2362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2363">-------------: | | 0 | value0 | | 1 | Değer1 | | 2 | değer2 | | 3 | value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="3037b-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="3037b-2364">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="3037b-2365">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="3037b-2366">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="3037b-2367">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="3037b-2368">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="3037b-2369">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="3037b-2370">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="3037b-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="3037b-2371">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="3037b-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="3037b-2372">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-2372">Key</span></span>             | <span data-ttu-id="3037b-2373">Değer</span><span class="sxs-lookup"><span data-stu-id="3037b-2373">Value</span></span>  |
| :---
<span data-ttu-id="3037b-2374">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2375">'Blazor'</span></span>
- <span data-ttu-id="3037b-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2376">'Identity'</span></span>
- <span data-ttu-id="3037b-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2378">'Razor'</span></span>
- <span data-ttu-id="3037b-2379">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2380">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2381">'Blazor'</span></span>
- <span data-ttu-id="3037b-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2382">'Identity'</span></span>
- <span data-ttu-id="3037b-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2384">'Razor'</span></span>
- <span data-ttu-id="3037b-2385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2386">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2387">'Blazor'</span></span>
- <span data-ttu-id="3037b-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2388">'Identity'</span></span>
- <span data-ttu-id="3037b-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2390">'Razor'</span></span>
- <span data-ttu-id="3037b-2391">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2392">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2393">'Blazor'</span></span>
- <span data-ttu-id="3037b-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2394">'Identity'</span></span>
- <span data-ttu-id="3037b-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2396">'Razor'</span></span>
- <span data-ttu-id="3037b-2397">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2398">-------: | :----: | | dizi: girdiler: 3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="3037b-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="3037b-2399">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="3037b-2400">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="3037b-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="3037b-2401">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="3037b-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="3037b-2402">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2403">'Blazor'</span></span>
- <span data-ttu-id="3037b-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2404">'Identity'</span></span>
- <span data-ttu-id="3037b-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2406">'Razor'</span></span>
- <span data-ttu-id="3037b-2407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2408">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2409">'Blazor'</span></span>
- <span data-ttu-id="3037b-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2410">'Identity'</span></span>
- <span data-ttu-id="3037b-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2412">'Razor'</span></span>
- <span data-ttu-id="3037b-2413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2414">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2415">'Blazor'</span></span>
- <span data-ttu-id="3037b-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2416">'Identity'</span></span>
- <span data-ttu-id="3037b-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2418">'Razor'</span></span>
- <span data-ttu-id="3037b-2419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2420">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2421">'Blazor'</span></span>
- <span data-ttu-id="3037b-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2422">'Identity'</span></span>
- <span data-ttu-id="3037b-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2424">'Razor'</span></span>
- <span data-ttu-id="3037b-2425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2426">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2427">'Blazor'</span></span>
- <span data-ttu-id="3037b-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2428">'Identity'</span></span>
- <span data-ttu-id="3037b-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2430">'Razor'</span></span>
- <span data-ttu-id="3037b-2431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2432">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2433">'Blazor'</span></span>
- <span data-ttu-id="3037b-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2434">'Identity'</span></span>
- <span data-ttu-id="3037b-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2436">'Razor'</span></span>
- <span data-ttu-id="3037b-2437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2438">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2439">'Blazor'</span></span>
- <span data-ttu-id="3037b-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2440">'Identity'</span></span>
- <span data-ttu-id="3037b-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2442">'Razor'</span></span>
- <span data-ttu-id="3037b-2443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2444">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2445">'Blazor'</span></span>
- <span data-ttu-id="3037b-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2446">'Identity'</span></span>
- <span data-ttu-id="3037b-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2448">'Razor'</span></span>
- <span data-ttu-id="3037b-2449">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2450">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2451">'Blazor'</span></span>
- <span data-ttu-id="3037b-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2452">'Identity'</span></span>
- <span data-ttu-id="3037b-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2454">'Razor'</span></span>
- <span data-ttu-id="3037b-2455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2456">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2457">'Blazor'</span></span>
- <span data-ttu-id="3037b-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2458">'Identity'</span></span>
- <span data-ttu-id="3037b-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2460">'Razor'</span></span>
- <span data-ttu-id="3037b-2461">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2462">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2463">'Blazor'</span></span>
- <span data-ttu-id="3037b-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2464">'Identity'</span></span>
- <span data-ttu-id="3037b-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2466">'Razor'</span></span>
- <span data-ttu-id="3037b-2467">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2468">-------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2469">'Blazor'</span></span>
- <span data-ttu-id="3037b-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2470">'Identity'</span></span>
- <span data-ttu-id="3037b-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2472">'Razor'</span></span>
- <span data-ttu-id="3037b-2473">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2474">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2475">'Blazor'</span></span>
- <span data-ttu-id="3037b-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2476">'Identity'</span></span>
- <span data-ttu-id="3037b-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2478">'Razor'</span></span>
- <span data-ttu-id="3037b-2479">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2480">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2481">'Blazor'</span></span>
- <span data-ttu-id="3037b-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2482">'Identity'</span></span>
- <span data-ttu-id="3037b-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2484">'Razor'</span></span>
- <span data-ttu-id="3037b-2485">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2486">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2487">'Blazor'</span></span>
- <span data-ttu-id="3037b-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2488">'Identity'</span></span>
- <span data-ttu-id="3037b-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2490">'Razor'</span></span>
- <span data-ttu-id="3037b-2491">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2492">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2493">'Blazor'</span></span>
- <span data-ttu-id="3037b-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2494">'Identity'</span></span>
- <span data-ttu-id="3037b-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2496">'Razor'</span></span>
- <span data-ttu-id="3037b-2497">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2498">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2499">'Blazor'</span></span>
- <span data-ttu-id="3037b-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2500">'Identity'</span></span>
- <span data-ttu-id="3037b-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2502">'Razor'</span></span>
- <span data-ttu-id="3037b-2503">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2504">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2505">'Blazor'</span></span>
- <span data-ttu-id="3037b-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2506">'Identity'</span></span>
- <span data-ttu-id="3037b-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2508">'Razor'</span></span>
- <span data-ttu-id="3037b-2509">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2510">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2511">'Blazor'</span></span>
- <span data-ttu-id="3037b-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2512">'Identity'</span></span>
- <span data-ttu-id="3037b-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2514">'Razor'</span></span>
- <span data-ttu-id="3037b-2515">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2516">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2517">'Blazor'</span></span>
- <span data-ttu-id="3037b-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2518">'Identity'</span></span>
- <span data-ttu-id="3037b-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2520">'Razor'</span></span>
- <span data-ttu-id="3037b-2521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2522">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2523">'Blazor'</span></span>
- <span data-ttu-id="3037b-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2524">'Identity'</span></span>
- <span data-ttu-id="3037b-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2526">'Razor'</span></span>
- <span data-ttu-id="3037b-2527">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2528">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2529">'Blazor'</span></span>
- <span data-ttu-id="3037b-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2530">'Identity'</span></span>
- <span data-ttu-id="3037b-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2532">'Razor'</span></span>
- <span data-ttu-id="3037b-2533">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2534">-------------: | | 0 | value0 | | 1 | Değer1 | | 2 | değer2 | | 3 | value3 | | 4 | value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="3037b-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="3037b-2535">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="3037b-2535">**JSON array processing**</span></span>

<span data-ttu-id="3037b-2536">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3037b-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="3037b-2537">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="3037b-2538">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="3037b-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="3037b-2539">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3037b-2539">Key</span></span>                     | <span data-ttu-id="3037b-2540">Değer</span><span class="sxs-lookup"><span data-stu-id="3037b-2540">Value</span></span>  |
| ---
<span data-ttu-id="3037b-2541">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2542">'Blazor'</span></span>
- <span data-ttu-id="3037b-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2543">'Identity'</span></span>
- <span data-ttu-id="3037b-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2545">'Razor'</span></span>
- <span data-ttu-id="3037b-2546">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2547">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2548">'Blazor'</span></span>
- <span data-ttu-id="3037b-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2549">'Identity'</span></span>
- <span data-ttu-id="3037b-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2551">'Razor'</span></span>
- <span data-ttu-id="3037b-2552">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2553">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2554">'Blazor'</span></span>
- <span data-ttu-id="3037b-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2555">'Identity'</span></span>
- <span data-ttu-id="3037b-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2557">'Razor'</span></span>
- <span data-ttu-id="3037b-2558">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2559">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2560">'Blazor'</span></span>
- <span data-ttu-id="3037b-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2561">'Identity'</span></span>
- <span data-ttu-id="3037b-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2563">'Razor'</span></span>
- <span data-ttu-id="3037b-2564">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2565">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2566">'Blazor'</span></span>
- <span data-ttu-id="3037b-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2567">'Identity'</span></span>
- <span data-ttu-id="3037b-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2569">'Razor'</span></span>
- <span data-ttu-id="3037b-2570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2571">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2572">'Blazor'</span></span>
- <span data-ttu-id="3037b-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2573">'Identity'</span></span>
- <span data-ttu-id="3037b-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2575">'Razor'</span></span>
- <span data-ttu-id="3037b-2576">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2577">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2578">'Blazor'</span></span>
- <span data-ttu-id="3037b-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2579">'Identity'</span></span>
- <span data-ttu-id="3037b-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2581">'Razor'</span></span>
- <span data-ttu-id="3037b-2582">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2583">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2584">'Blazor'</span></span>
- <span data-ttu-id="3037b-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2585">'Identity'</span></span>
- <span data-ttu-id="3037b-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2587">'Razor'</span></span>
- <span data-ttu-id="3037b-2588">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2589">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2590">'Blazor'</span></span>
- <span data-ttu-id="3037b-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2591">'Identity'</span></span>
- <span data-ttu-id="3037b-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2593">'Razor'</span></span>
- <span data-ttu-id="3037b-2594">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2595">------------ | :----: | | json_array: anahtar | Değerea | | json_array: alt bölüm: 0 | valueB | | json_array: alt bölüm: 1 | değer EC | | json_array: alt bölüm: 2 | Değer |</span><span class="sxs-lookup"><span data-stu-id="3037b-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="3037b-2596">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="3037b-2597">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="3037b-2598">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="3037b-2599">`JsonArrayExample.Subsection`İndeks</span><span class="sxs-lookup"><span data-stu-id="3037b-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="3037b-2600">`JsonArrayExample.Subsection`Deeri</span><span class="sxs-lookup"><span data-stu-id="3037b-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="3037b-2601">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2602">'Blazor'</span></span>
- <span data-ttu-id="3037b-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2603">'Identity'</span></span>
- <span data-ttu-id="3037b-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2605">'Razor'</span></span>
- <span data-ttu-id="3037b-2606">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2607">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2608">'Blazor'</span></span>
- <span data-ttu-id="3037b-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2609">'Identity'</span></span>
- <span data-ttu-id="3037b-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2611">'Razor'</span></span>
- <span data-ttu-id="3037b-2612">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2613">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2614">'Blazor'</span></span>
- <span data-ttu-id="3037b-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2615">'Identity'</span></span>
- <span data-ttu-id="3037b-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2617">'Razor'</span></span>
- <span data-ttu-id="3037b-2618">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2619">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2620">'Blazor'</span></span>
- <span data-ttu-id="3037b-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2621">'Identity'</span></span>
- <span data-ttu-id="3037b-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2623">'Razor'</span></span>
- <span data-ttu-id="3037b-2624">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2625">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2626">'Blazor'</span></span>
- <span data-ttu-id="3037b-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2627">'Identity'</span></span>
- <span data-ttu-id="3037b-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2629">'Razor'</span></span>
- <span data-ttu-id="3037b-2630">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2631">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2632">'Blazor'</span></span>
- <span data-ttu-id="3037b-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2633">'Identity'</span></span>
- <span data-ttu-id="3037b-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2635">'Razor'</span></span>
- <span data-ttu-id="3037b-2636">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2637">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2638">'Blazor'</span></span>
- <span data-ttu-id="3037b-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2639">'Identity'</span></span>
- <span data-ttu-id="3037b-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2641">'Razor'</span></span>
- <span data-ttu-id="3037b-2642">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2643">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2644">'Blazor'</span></span>
- <span data-ttu-id="3037b-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2645">'Identity'</span></span>
- <span data-ttu-id="3037b-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2647">'Razor'</span></span>
- <span data-ttu-id="3037b-2648">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2649">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2650">'Blazor'</span></span>
- <span data-ttu-id="3037b-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2651">'Identity'</span></span>
- <span data-ttu-id="3037b-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2653">'Razor'</span></span>
- <span data-ttu-id="3037b-2654">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2655">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2656">'Blazor'</span></span>
- <span data-ttu-id="3037b-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2657">'Identity'</span></span>
- <span data-ttu-id="3037b-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2659">'Razor'</span></span>
- <span data-ttu-id="3037b-2660">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2661">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2662">'Blazor'</span></span>
- <span data-ttu-id="3037b-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2663">'Identity'</span></span>
- <span data-ttu-id="3037b-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2665">'Razor'</span></span>
- <span data-ttu-id="3037b-2666">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2667">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2668">'Blazor'</span></span>
- <span data-ttu-id="3037b-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2669">'Identity'</span></span>
- <span data-ttu-id="3037b-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2671">'Razor'</span></span>
- <span data-ttu-id="3037b-2672">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2673">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2674">'Blazor'</span></span>
- <span data-ttu-id="3037b-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2675">'Identity'</span></span>
- <span data-ttu-id="3037b-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2677">'Razor'</span></span>
- <span data-ttu-id="3037b-2678">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2679">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2680">'Blazor'</span></span>
- <span data-ttu-id="3037b-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2681">'Identity'</span></span>
- <span data-ttu-id="3037b-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2683">'Razor'</span></span>
- <span data-ttu-id="3037b-2684">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2685">-----------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2686">'Blazor'</span></span>
- <span data-ttu-id="3037b-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2687">'Identity'</span></span>
- <span data-ttu-id="3037b-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2689">'Razor'</span></span>
- <span data-ttu-id="3037b-2690">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2691">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2692">'Blazor'</span></span>
- <span data-ttu-id="3037b-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2693">'Identity'</span></span>
- <span data-ttu-id="3037b-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2695">'Razor'</span></span>
- <span data-ttu-id="3037b-2696">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2697">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2698">'Blazor'</span></span>
- <span data-ttu-id="3037b-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2699">'Identity'</span></span>
- <span data-ttu-id="3037b-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2701">'Razor'</span></span>
- <span data-ttu-id="3037b-2702">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2703">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2704">'Blazor'</span></span>
- <span data-ttu-id="3037b-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2705">'Identity'</span></span>
- <span data-ttu-id="3037b-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2707">'Razor'</span></span>
- <span data-ttu-id="3037b-2708">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2709">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2710">'Blazor'</span></span>
- <span data-ttu-id="3037b-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2711">'Identity'</span></span>
- <span data-ttu-id="3037b-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2713">'Razor'</span></span>
- <span data-ttu-id="3037b-2714">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2715">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2716">'Blazor'</span></span>
- <span data-ttu-id="3037b-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2717">'Identity'</span></span>
- <span data-ttu-id="3037b-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2719">'Razor'</span></span>
- <span data-ttu-id="3037b-2720">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2721">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2722">'Blazor'</span></span>
- <span data-ttu-id="3037b-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2723">'Identity'</span></span>
- <span data-ttu-id="3037b-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2725">'Razor'</span></span>
- <span data-ttu-id="3037b-2726">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2727">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2728">'Blazor'</span></span>
- <span data-ttu-id="3037b-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2729">'Identity'</span></span>
- <span data-ttu-id="3037b-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2731">'Razor'</span></span>
- <span data-ttu-id="3037b-2732">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2733">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2734">'Blazor'</span></span>
- <span data-ttu-id="3037b-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2735">'Identity'</span></span>
- <span data-ttu-id="3037b-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2737">'Razor'</span></span>
- <span data-ttu-id="3037b-2738">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2739">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2740">'Blazor'</span></span>
- <span data-ttu-id="3037b-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2741">'Identity'</span></span>
- <span data-ttu-id="3037b-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2743">'Razor'</span></span>
- <span data-ttu-id="3037b-2744">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2745">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2746">'Blazor'</span></span>
- <span data-ttu-id="3037b-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2747">'Identity'</span></span>
- <span data-ttu-id="3037b-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2749">'Razor'</span></span>
- <span data-ttu-id="3037b-2750">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2751">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2752">'Blazor'</span></span>
- <span data-ttu-id="3037b-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2753">'Identity'</span></span>
- <span data-ttu-id="3037b-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2755">'Razor'</span></span>
- <span data-ttu-id="3037b-2756">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2757">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2758">'Blazor'</span></span>
- <span data-ttu-id="3037b-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2759">'Identity'</span></span>
- <span data-ttu-id="3037b-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2761">'Razor'</span></span>
- <span data-ttu-id="3037b-2762">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3037b-2763">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3037b-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3037b-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2764">'Blazor'</span></span>
- <span data-ttu-id="3037b-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3037b-2765">'Identity'</span></span>
- <span data-ttu-id="3037b-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3037b-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="3037b-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3037b-2767">'Razor'</span></span>
- <span data-ttu-id="3037b-2768">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="3037b-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="3037b-2769">-----------------: | | 0 | valueB | | 1 | değer EC | | 2 | Değer |</span><span class="sxs-lookup"><span data-stu-id="3037b-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="3037b-2770">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3037b-2770">Custom configuration provider</span></span>

<span data-ttu-id="3037b-2771">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="3037b-2772">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="3037b-2773">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="3037b-2774">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="3037b-2775">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="3037b-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="3037b-2776">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="3037b-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="3037b-2777">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="3037b-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="3037b-2778">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="3037b-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="3037b-2779">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="3037b-2780">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="3037b-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="3037b-2781">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="3037b-2782">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="3037b-2783">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="3037b-2784">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="3037b-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="3037b-2785">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="3037b-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="3037b-2786">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="3037b-2787">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="3037b-2788">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="3037b-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="3037b-2789">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="3037b-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="3037b-2790">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="3037b-2790">Access configuration during startup</span></span>

<span data-ttu-id="3037b-2791">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3037b-2792">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="3037b-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="3037b-2793">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="3037b-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="3037b-2794">RazorSayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="3037b-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="3037b-2795">Bir Razor Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft. Extensions. Configuration ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3037b-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="3037b-2796">RazorSayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="3037b-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="3037b-2797">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="3037b-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="3037b-2798">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="3037b-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="3037b-2799">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="3037b-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="3037b-2800">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3037b-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3037b-2801">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3037b-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
