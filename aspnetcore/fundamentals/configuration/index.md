---
<span data-ttu-id="4da52-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-102">'Blazor'</span></span>
- <span data-ttu-id="4da52-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-103">'Identity'</span></span>
- <span data-ttu-id="4da52-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-105">'Razor'</span></span>
- <span data-ttu-id="4da52-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="4da52-107">ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="4da52-108">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4da52-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4da52-109">ASP.NET Core yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="4da52-110">Yapılandırma sağlayıcıları çeşitli yapılandırma kaynakları kullanarak anahtar-değer çiftlerinden yapılandırma verilerini okur:</span><span class="sxs-lookup"><span data-stu-id="4da52-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="4da52-111">*AppSettings. JSON* gibi ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="4da52-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="4da52-112">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-112">Environment variables</span></span>
* <span data-ttu-id="4da52-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4da52-113">Azure Key Vault</span></span>
* <span data-ttu-id="4da52-114">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4da52-114">Azure App Configuration</span></span>
* <span data-ttu-id="4da52-115">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-115">Command-line arguments</span></span>
* <span data-ttu-id="4da52-116">Özel sağlayıcılar, yüklendi veya oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="4da52-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="4da52-117">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="4da52-117">Directory files</span></span>
* <span data-ttu-id="4da52-118">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="4da52-118">In-memory .NET objects</span></span>

<span data-ttu-id="4da52-119">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4da52-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="4da52-120">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-120">Default configuration</span></span>

<span data-ttu-id="4da52-121">[DotNet New](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan web Apps ASP.NET Core aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="4da52-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="4da52-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="4da52-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="4da52-123">[Chainedconfigurationprovider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : var olan bir `IConfiguration` kaynak olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="4da52-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="4da52-124">Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırması için ilk kaynak olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4da52-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="4da52-125">[JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak [appSettings. JSON](#appsettingsjson) .</span><span class="sxs-lookup"><span data-stu-id="4da52-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="4da52-126">*appSettings.* `Environment` [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak *. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4da52-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="4da52-127">Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="4da52-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="4da52-128">Uygulama ortamda çalıştırıldığında [uygulama gizli](xref:security/app-secrets) dizileri `Development` .</span><span class="sxs-lookup"><span data-stu-id="4da52-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="4da52-129">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4da52-130">Komut satırı [yapılandırma sağlayıcısını](#command-line)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="4da52-131">Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4da52-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="4da52-132">Örneğin, `MyKey` *appSettings. JSON* ve ortamda ayarlanırsa, ortam değeri kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="4da52-133">Varsayılan yapılandırma sağlayıcılarını kullanarak, [komut satırı yapılandırma sağlayıcısı](#command-line-configuration-provider) diğer tüm sağlayıcıları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4da52-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="4da52-134">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz. [Varsayılan Oluşturucu ayarları](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="4da52-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="4da52-135">Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendiği sırayla görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="4da52-136">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="4da52-136">appsettings.json</span></span>

<span data-ttu-id="4da52-137">Aşağıdaki *appSettings. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4da52-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4da52-138">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-139">Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yapılandırma yapılandırması aşağıdaki sırayla yüklenir:</span><span class="sxs-lookup"><span data-stu-id="4da52-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="4da52-140">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="4da52-140">*appsettings.json*</span></span>
1. <span data-ttu-id="4da52-141">*appSettings.* `Environment` *. JSON* : Örneğin, *appSettings*. ***Üretim***. *JSON* ve *appSettings*. ***Geliştirme***. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4da52-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="4da52-142">Dosyanın ortam sürümü, [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="4da52-143">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4da52-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4da52-144">*appSettings*. `Environment` . *JSON* değerleri *appSettings. JSON*içindeki anahtarları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4da52-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="4da52-145">Örneğin, varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="4da52-145">For example, by default:</span></span>

* <span data-ttu-id="4da52-146">Geliştirme sürümünde *appSettings*. ***Geliştirme***. *JSON* yapılandırması *appSettings. JSON*içinde bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="4da52-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="4da52-147">Üretimde, *appSettings*. ***Üretim***. *JSON* yapılandırması *appSettings. JSON*içinde bulunan değerlerin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="4da52-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="4da52-148">Örneğin, uygulamayı Azure 'a dağıtma.</span><span class="sxs-lookup"><span data-stu-id="4da52-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="4da52-149">Seçenek modelini kullanarak hiyerarşik yapılandırma verilerini bağlama</span><span class="sxs-lookup"><span data-stu-id="4da52-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="4da52-150">[Varsayılan](#default) yapılandırmayı kullanarak *appSettings. JSON* ve *appSettings.* `Environment` *. JSON* dosyaları [reloadonchange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="4da52-151">*AppSettings. JSON* ve appSettings üzerinde yapılan değişiklikler *.* `Environment` uygulama başladıktan ***sonra*** *. JSON* dosyası [JSON yapılandırma sağlayıcısı](#jcp)tarafından okundu.</span><span class="sxs-lookup"><span data-stu-id="4da52-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4da52-152">Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="4da52-153">Güvenlik ve gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="4da52-153">Security and secret manager</span></span>

<span data-ttu-id="4da52-154">Yapılandırma verileri yönergeleri:</span><span class="sxs-lookup"><span data-stu-id="4da52-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="4da52-155">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="4da52-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="4da52-156">[Gizli](xref:security/app-secrets) dizi, geliştirmelerde gizli dizileri depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="4da52-157">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4da52-158">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="4da52-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4da52-159">[Varsayılan](#default)olarak, [gizli yönetici](xref:security/app-secrets) *appSettings. JSON* ve appSettings sonrasında yapılandırma ayarlarını okur *.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="4da52-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="4da52-160">Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="4da52-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4da52-161"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4da52-162">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için [dosya yapılandırma sağlayıcısını](#fcp) kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="4da52-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="4da52-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4da52-164">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4da52-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="4da52-165">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-165">Environment variables</span></span>

<span data-ttu-id="4da52-166">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> *appSettings. JSON*, appSettings 'i okuduktan sonra anahtar-değer çiftlerinden yapılandırmayı yükler *.* `Environment` *. JSON*ve [gizli yönetici](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="4da52-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4da52-167">Bu nedenle, ortamdan okunan anahtar değerleri *appSettings. JSON*, appSettings öğesinden okunan değerleri geçersiz kılar *.* `Environment` *. JSON*ve gizli yönetici.</span><span class="sxs-lookup"><span data-stu-id="4da52-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4da52-168">Aşağıdaki `set` Komutlar:</span><span class="sxs-lookup"><span data-stu-id="4da52-168">The following `set` commands:</span></span>

* <span data-ttu-id="4da52-169">Windows üzerinde [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="4da52-170">[Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin.</span><span class="sxs-lookup"><span data-stu-id="4da52-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="4da52-171">`dotnet run`Komutun proje dizininde çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4da52-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="4da52-172">Önceki ortam ayarları:</span><span class="sxs-lookup"><span data-stu-id="4da52-172">The preceding environment settings:</span></span>

* <span data-ttu-id="4da52-173">Yalnızca ' de ayarlanan komut penceresinden başlatılan işlemlerde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="4da52-174">Visual Studio ile başlatılan tarayıcılar tarafından okunmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="4da52-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="4da52-175">Aşağıdaki [Setx](/windows-server/administration/windows-commands/setx) komutları Windows üzerinde ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="4da52-176">Farklı olarak `set` , `setx` ayarlar kalıcı hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="4da52-177">`/M`değişkeni sistem ortamında ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4da52-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="4da52-178">`/M`Anahtar kullanılmazsa, bir kullanıcı ortam değişkeni ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="4da52-179">Yukarıdaki komutların *appSettings. JSON* ve appSettings 'i geçersiz kılmasını test etmek için *.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="4da52-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="4da52-180">Visual Studio ile: Exit ve Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4da52-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="4da52-181">CLı ile: yeni bir komut penceresi başlatın ve girin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="4da52-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="4da52-182"><xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>Ortam değişkenlerinin önekini belirtmek için bir dizeyle çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="4da52-183">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="4da52-183">In the preceding code:</span></span>

* <span data-ttu-id="4da52-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`[varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4da52-185">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4da52-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="4da52-186">Önek ile ayarlanan ortam değişkenleri `MyCustomPrefix_` [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4da52-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="4da52-187">Bu, öneki olmayan ortam değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="4da52-188">Yapılandırma anahtar-değer çiftleri okunduktan sonra önek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="4da52-189">Aşağıdaki komutlar özel öneki test et:</span><span class="sxs-lookup"><span data-stu-id="4da52-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="4da52-190">[Varsayılan yapılandırma](#default) , ve önekini önekli ortam değişkenlerini ve komut satırı bağımsız değişkenlerini yükler `DOTNET_` `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="4da52-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="4da52-191">`DOTNET_`Ve `ASPNETCORE_` önekleri [konak ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="4da52-192">Konak ve uygulama yapılandırması hakkında daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4da52-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="4da52-193">[Azure App Service](https://azure.microsoft.com/services/app-service/), **Ayarlar > yapılandırma** sayfasında **Yeni uygulama ayarı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="4da52-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="4da52-194">Azure App Service uygulama ayarları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4da52-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="4da52-195">Rest 'de şifrelenir ve şifreli bir kanal üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="4da52-196">Ortam değişkenleri olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="4da52-196">Exposed as environment variables.</span></span>

<span data-ttu-id="4da52-197">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="4da52-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4da52-198">Azure veritabanı bağlantı dizeleri hakkında bilgi için bkz. [bağlantı dizesi önekleri](#constr) .</span><span class="sxs-lookup"><span data-stu-id="4da52-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="4da52-199">Komut Satırı</span><span class="sxs-lookup"><span data-stu-id="4da52-199">Command-line</span></span>

<span data-ttu-id="4da52-200">[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişkeninden anahtar-değer çiftlerinden yapılandırma yükler:</span><span class="sxs-lookup"><span data-stu-id="4da52-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="4da52-201">*appSettings. JSON* ve *appSettings*. `Environment` . *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4da52-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4da52-202">Geliştirme ortamında [uygulama gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="4da52-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4da52-203">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-203">Environment variables.</span></span>

<span data-ttu-id="4da52-204">[Varsayılan](#default)olarak, komut satırı geçersiz kılma yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla ayarlanan yapılandırma değerleri olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="4da52-205">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-205">Command-line arguments</span></span>

<span data-ttu-id="4da52-206">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `=` :</span><span class="sxs-lookup"><span data-stu-id="4da52-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="4da52-207">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `/` :</span><span class="sxs-lookup"><span data-stu-id="4da52-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="4da52-208">Aşağıdaki komut kullanarak anahtarları ve değerleri ayarlar `--` :</span><span class="sxs-lookup"><span data-stu-id="4da52-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="4da52-209">Anahtar değeri:</span><span class="sxs-lookup"><span data-stu-id="4da52-209">The key value:</span></span>

* <span data-ttu-id="4da52-210">`=`' İ izlemelidir, ya da anahtarın bir `--` `/` boşluk izleyen değeri veya öneki olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="4da52-211">Kullanılıyorsa gerekli değildir `=` .</span><span class="sxs-lookup"><span data-stu-id="4da52-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="4da52-212">Örneğin, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="4da52-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="4da52-213">Aynı komut içinde, `=` bir boşluk kullanan anahtar-değer çiftleri ile birlikte kullanılan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="4da52-214">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="4da52-214">Switch mappings</span></span>

<span data-ttu-id="4da52-215">Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4da52-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="4da52-216">Metoda anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="4da52-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4da52-217">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4da52-218">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri, anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4da52-219">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="4da52-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4da52-220">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="4da52-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4da52-221">Anahtarlar veya ile başlamalıdır `-` `--` .</span><span class="sxs-lookup"><span data-stu-id="4da52-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="4da52-222">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="4da52-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4da52-223">Anahtar eşlemeleri sözlüğünü kullanmak için, çağrısı içine geçirin `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="4da52-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="4da52-224">Aşağıdaki kod, değiştirilmiş anahtarların anahtar değerlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="4da52-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-225">Anahtar değişimini test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="4da52-226">Note: Şu anda, `=` anahtar değiştirme değerlerini tek bir çizgiyle ayarlamak için kullanılamaz `-` .</span><span class="sxs-lookup"><span data-stu-id="4da52-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="4da52-227">[Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="4da52-228">Aşağıdaki komut, anahtar değişimini test etmek için işe yarar:</span><span class="sxs-lookup"><span data-stu-id="4da52-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="4da52-229">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="4da52-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4da52-230">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4da52-231">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="4da52-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4da52-232">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="4da52-232">Hierarchical configuration data</span></span>

<span data-ttu-id="4da52-233">Yapılandırma API 'SI, hiyerarşik verileri, yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini okur.</span><span class="sxs-lookup"><span data-stu-id="4da52-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4da52-234">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *appSettings. JSON* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="4da52-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4da52-235">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-236">Hiyerarşik yapılandırma verilerini okumak için tercih edilen yol, Seçenekler modelini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="4da52-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="4da52-237">Daha fazla bilgi için, bkz. bu belgedeki [Hiyerarşik yapılandırma verilerini bağlama](#optpat) .</span><span class="sxs-lookup"><span data-stu-id="4da52-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="4da52-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4da52-239">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4da52-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="4da52-240">Yapılandırma anahtarları ve değerleri</span><span class="sxs-lookup"><span data-stu-id="4da52-240">Configuration keys and values</span></span>

<span data-ttu-id="4da52-241">Yapılandırma anahtarları:</span><span class="sxs-lookup"><span data-stu-id="4da52-241">Configuration keys:</span></span>

* <span data-ttu-id="4da52-242">Büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-242">Are case-insensitive.</span></span> <span data-ttu-id="4da52-243">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4da52-244">Bir anahtar ve değer birden fazla yapılandırma sağlayıcısından ayarlandıysa, eklenen son sağlayıcıdan alınan değer kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="4da52-245">Daha fazla bilgi için bkz. [varsayılan yapılandırma](#default).</span><span class="sxs-lookup"><span data-stu-id="4da52-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="4da52-246">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="4da52-246">Hierarchical keys</span></span>
  * <span data-ttu-id="4da52-247">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4da52-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4da52-248">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4da52-249">Çift alt çizgi, `__` tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür `:` .</span><span class="sxs-lookup"><span data-stu-id="4da52-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="4da52-250">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="4da52-251">Gizli diziler uygulamanın yapılandırmasına yüklendiğinde [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) otomatik olarak `--` bir ile değiştirilir `:` .</span><span class="sxs-lookup"><span data-stu-id="4da52-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4da52-252">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="4da52-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4da52-253">Dizi bağlama, [diziyi bir sınıfa bağlama](#boa) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4da52-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="4da52-254">Yapılandırma değerleri:</span><span class="sxs-lookup"><span data-stu-id="4da52-254">Configuration values:</span></span>

* <span data-ttu-id="4da52-255">Dizeler.</span><span class="sxs-lookup"><span data-stu-id="4da52-255">Are strings.</span></span>
* <span data-ttu-id="4da52-256">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="4da52-257">Yapılandırma sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="4da52-257">Configuration providers</span></span>

<span data-ttu-id="4da52-258">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4da52-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4da52-259">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="4da52-259">Provider</span></span> | <span data-ttu-id="4da52-260">Şuradan yapılandırma sağlar</span><span class="sxs-lookup"><span data-stu-id="4da52-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="4da52-261">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-262">'Blazor'</span></span>
- <span data-ttu-id="4da52-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-263">'Identity'</span></span>
- <span data-ttu-id="4da52-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-265">'Razor'</span></span>
- <span data-ttu-id="4da52-266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-267">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-268">'Blazor'</span></span>
- <span data-ttu-id="4da52-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-269">'Identity'</span></span>
- <span data-ttu-id="4da52-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-271">'Razor'</span></span>
- <span data-ttu-id="4da52-272">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-272">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-273">---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-274">'Blazor'</span></span>
- <span data-ttu-id="4da52-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-275">'Identity'</span></span>
- <span data-ttu-id="4da52-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-277">'Razor'</span></span>
- <span data-ttu-id="4da52-278">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-279">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-280">'Blazor'</span></span>
- <span data-ttu-id="4da52-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-281">'Identity'</span></span>
- <span data-ttu-id="4da52-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-283">'Razor'</span></span>
- <span data-ttu-id="4da52-284">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-285">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-286">'Blazor'</span></span>
- <span data-ttu-id="4da52-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-287">'Identity'</span></span>
- <span data-ttu-id="4da52-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-289">'Razor'</span></span>
- <span data-ttu-id="4da52-290">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-291">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-292">'Blazor'</span></span>
- <span data-ttu-id="4da52-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-293">'Identity'</span></span>
- <span data-ttu-id="4da52-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-295">'Razor'</span></span>
- <span data-ttu-id="4da52-296">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-297">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-298">'Blazor'</span></span>
- <span data-ttu-id="4da52-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-299">'Identity'</span></span>
- <span data-ttu-id="4da52-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-301">'Razor'</span></span>
- <span data-ttu-id="4da52-302">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-303">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-304">'Blazor'</span></span>
- <span data-ttu-id="4da52-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-305">'Identity'</span></span>
- <span data-ttu-id="4da52-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-307">'Razor'</span></span>
- <span data-ttu-id="4da52-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-309">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-310">'Blazor'</span></span>
- <span data-ttu-id="4da52-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-311">'Identity'</span></span>
- <span data-ttu-id="4da52-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-313">'Razor'</span></span>
- <span data-ttu-id="4da52-314">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-315">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-316">'Blazor'</span></span>
- <span data-ttu-id="4da52-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-317">'Identity'</span></span>
- <span data-ttu-id="4da52-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-319">'Razor'</span></span>
- <span data-ttu-id="4da52-320">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-321">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-322">'Blazor'</span></span>
- <span data-ttu-id="4da52-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-323">'Identity'</span></span>
- <span data-ttu-id="4da52-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-325">'Razor'</span></span>
- <span data-ttu-id="4da52-326">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-327">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-328">'Blazor'</span></span>
- <span data-ttu-id="4da52-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-329">'Identity'</span></span>
- <span data-ttu-id="4da52-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-331">'Razor'</span></span>
- <span data-ttu-id="4da52-332">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-334">'Blazor'</span></span>
- <span data-ttu-id="4da52-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-335">'Identity'</span></span>
- <span data-ttu-id="4da52-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-337">'Razor'</span></span>
- <span data-ttu-id="4da52-338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-340">'Blazor'</span></span>
- <span data-ttu-id="4da52-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-341">'Identity'</span></span>
- <span data-ttu-id="4da52-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-343">'Razor'</span></span>
- <span data-ttu-id="4da52-344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-346">'Blazor'</span></span>
- <span data-ttu-id="4da52-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-347">'Identity'</span></span>
- <span data-ttu-id="4da52-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-349">'Razor'</span></span>
- <span data-ttu-id="4da52-350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-352">'Blazor'</span></span>
- <span data-ttu-id="4da52-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-353">'Identity'</span></span>
- <span data-ttu-id="4da52-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-355">'Razor'</span></span>
- <span data-ttu-id="4da52-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-358">'Blazor'</span></span>
- <span data-ttu-id="4da52-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-359">'Identity'</span></span>
- <span data-ttu-id="4da52-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-361">'Razor'</span></span>
- <span data-ttu-id="4da52-362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-362">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-363">------------------ | | [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure Uygulama yapılandırması | | [Komut satırı yapılandırma sağlayıcısı](#clcp) | Komut satırı parametreleri | | [Özel yapılandırma sağlayıcısı](#custom-configuration-provider) | Özel kaynak | | [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) | Ortam değişkenleri | | [Dosya yapılandırma sağlayıcısı](#file-configuration-provider) | INı, JSON ve XML dosyaları | | [Dosya başına anahtar yapılandırma sağlayıcısı](#key-per-file-configuration-provider) | Dizin dosyaları | | [Bellek yapılandırma sağlayıcısı](#memory-configuration-provider) | Bellek içi Koleksiyonlar | | [Gizli dizi Yöneticisi](xref:security/app-secrets) | Kullanıcı profili dizinindeki dosya |</span><span class="sxs-lookup"><span data-stu-id="4da52-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="4da52-364">Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="4da52-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="4da52-365">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4da52-366">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4da52-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4da52-367">*appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="4da52-367">*appsettings.json*</span></span>
1. <span data-ttu-id="4da52-368">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="4da52-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="4da52-369">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="4da52-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="4da52-370">Ortam [değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4da52-371">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="4da52-372">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için komut satırı yapılandırma sağlayıcısını en son bir sağlayıcı dizisine eklemektir.</span><span class="sxs-lookup"><span data-stu-id="4da52-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4da52-373">Önceki sağlayıcı dizisi [Varsayılan yapılandırmada](#default)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="4da52-374">Bağlantı dizesi önekleri</span><span class="sxs-lookup"><span data-stu-id="4da52-374">Connection string prefixes</span></span>

<span data-ttu-id="4da52-375">Yapılandırma API 'sinin dört bağlantı dizesi ortam değişkeni için özel işleme kuralları vardır.</span><span class="sxs-lookup"><span data-stu-id="4da52-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="4da52-376">Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizelerini yapılandırmaya dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4da52-377">Tabloda gösterilen öneklerle ortam değişkenleri, [varsayılan yapılandırmayla](#default) veya uygulamasına hiçbir önek sağlanmadığında uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="4da52-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4da52-378">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="4da52-378">Connection string prefix</span></span> | <span data-ttu-id="4da52-379">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="4da52-379">Provider</span></span> |
| ---
<span data-ttu-id="4da52-380">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-381">'Blazor'</span></span>
- <span data-ttu-id="4da52-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-382">'Identity'</span></span>
- <span data-ttu-id="4da52-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-384">'Razor'</span></span>
- <span data-ttu-id="4da52-385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-386">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-387">'Blazor'</span></span>
- <span data-ttu-id="4da52-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-388">'Identity'</span></span>
- <span data-ttu-id="4da52-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-390">'Razor'</span></span>
- <span data-ttu-id="4da52-391">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-392">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-393">'Blazor'</span></span>
- <span data-ttu-id="4da52-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-394">'Identity'</span></span>
- <span data-ttu-id="4da52-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-396">'Razor'</span></span>
- <span data-ttu-id="4da52-397">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-398">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-399">'Blazor'</span></span>
- <span data-ttu-id="4da52-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-400">'Identity'</span></span>
- <span data-ttu-id="4da52-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-402">'Razor'</span></span>
- <span data-ttu-id="4da52-403">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-404">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-405">'Blazor'</span></span>
- <span data-ttu-id="4da52-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-406">'Identity'</span></span>
- <span data-ttu-id="4da52-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-408">'Razor'</span></span>
- <span data-ttu-id="4da52-409">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-410">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-411">'Blazor'</span></span>
- <span data-ttu-id="4da52-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-412">'Identity'</span></span>
- <span data-ttu-id="4da52-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-414">'Razor'</span></span>
- <span data-ttu-id="4da52-415">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-416">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-417">'Blazor'</span></span>
- <span data-ttu-id="4da52-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-418">'Identity'</span></span>
- <span data-ttu-id="4da52-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-420">'Razor'</span></span>
- <span data-ttu-id="4da52-421">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-422">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-423">'Blazor'</span></span>
- <span data-ttu-id="4da52-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-424">'Identity'</span></span>
- <span data-ttu-id="4da52-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-426">'Razor'</span></span>
- <span data-ttu-id="4da52-427">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-428">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-429">'Blazor'</span></span>
- <span data-ttu-id="4da52-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-430">'Identity'</span></span>
- <span data-ttu-id="4da52-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-432">'Razor'</span></span>
- <span data-ttu-id="4da52-433">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-434">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-435">'Blazor'</span></span>
- <span data-ttu-id="4da52-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-436">'Identity'</span></span>
- <span data-ttu-id="4da52-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-438">'Razor'</span></span>
- <span data-ttu-id="4da52-439">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-439">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-440">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-441">'Blazor'</span></span>
- <span data-ttu-id="4da52-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-442">'Identity'</span></span>
- <span data-ttu-id="4da52-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-444">'Razor'</span></span>
- <span data-ttu-id="4da52-445">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-446">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-447">'Blazor'</span></span>
- <span data-ttu-id="4da52-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-448">'Identity'</span></span>
- <span data-ttu-id="4da52-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-450">'Razor'</span></span>
- <span data-ttu-id="4da52-451">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-451">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-452">---- | | `CUSTOMCONNSTR_` | Özel sağlayıcı | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="4da52-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="4da52-453">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="4da52-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4da52-454">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="4da52-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4da52-455">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="4da52-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4da52-456">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="4da52-456">Environment variable key</span></span> | <span data-ttu-id="4da52-457">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="4da52-457">Converted configuration key</span></span> | <span data-ttu-id="4da52-458">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="4da52-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="4da52-459">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-460">'Blazor'</span></span>
- <span data-ttu-id="4da52-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-461">'Identity'</span></span>
- <span data-ttu-id="4da52-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-463">'Razor'</span></span>
- <span data-ttu-id="4da52-464">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-465">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-466">'Blazor'</span></span>
- <span data-ttu-id="4da52-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-467">'Identity'</span></span>
- <span data-ttu-id="4da52-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-469">'Razor'</span></span>
- <span data-ttu-id="4da52-470">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-471">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-472">'Blazor'</span></span>
- <span data-ttu-id="4da52-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-473">'Identity'</span></span>
- <span data-ttu-id="4da52-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-475">'Razor'</span></span>
- <span data-ttu-id="4da52-476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-477">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-478">'Blazor'</span></span>
- <span data-ttu-id="4da52-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-479">'Identity'</span></span>
- <span data-ttu-id="4da52-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-481">'Razor'</span></span>
- <span data-ttu-id="4da52-482">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-483">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-484">'Blazor'</span></span>
- <span data-ttu-id="4da52-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-485">'Identity'</span></span>
- <span data-ttu-id="4da52-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-487">'Razor'</span></span>
- <span data-ttu-id="4da52-488">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-489">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-490">'Blazor'</span></span>
- <span data-ttu-id="4da52-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-491">'Identity'</span></span>
- <span data-ttu-id="4da52-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-493">'Razor'</span></span>
- <span data-ttu-id="4da52-494">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-495">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-496">'Blazor'</span></span>
- <span data-ttu-id="4da52-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-497">'Identity'</span></span>
- <span data-ttu-id="4da52-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-499">'Razor'</span></span>
- <span data-ttu-id="4da52-500">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-501">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-502">'Blazor'</span></span>
- <span data-ttu-id="4da52-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-503">'Identity'</span></span>
- <span data-ttu-id="4da52-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-505">'Razor'</span></span>
- <span data-ttu-id="4da52-506">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-507">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-508">'Blazor'</span></span>
- <span data-ttu-id="4da52-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-509">'Identity'</span></span>
- <span data-ttu-id="4da52-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-511">'Razor'</span></span>
- <span data-ttu-id="4da52-512">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-513">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-514">'Blazor'</span></span>
- <span data-ttu-id="4da52-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-515">'Identity'</span></span>
- <span data-ttu-id="4da52-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-517">'Razor'</span></span>
- <span data-ttu-id="4da52-518">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-518">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-519">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-520">'Blazor'</span></span>
- <span data-ttu-id="4da52-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-521">'Identity'</span></span>
- <span data-ttu-id="4da52-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-523">'Razor'</span></span>
- <span data-ttu-id="4da52-524">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-525">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-526">'Blazor'</span></span>
- <span data-ttu-id="4da52-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-527">'Identity'</span></span>
- <span data-ttu-id="4da52-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-529">'Razor'</span></span>
- <span data-ttu-id="4da52-530">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-531">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-532">'Blazor'</span></span>
- <span data-ttu-id="4da52-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-533">'Identity'</span></span>
- <span data-ttu-id="4da52-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-535">'Razor'</span></span>
- <span data-ttu-id="4da52-536">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-537">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-538">'Blazor'</span></span>
- <span data-ttu-id="4da52-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-539">'Identity'</span></span>
- <span data-ttu-id="4da52-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-541">'Razor'</span></span>
- <span data-ttu-id="4da52-542">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-543">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-544">'Blazor'</span></span>
- <span data-ttu-id="4da52-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-545">'Identity'</span></span>
- <span data-ttu-id="4da52-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-547">'Razor'</span></span>
- <span data-ttu-id="4da52-548">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-549">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-550">'Blazor'</span></span>
- <span data-ttu-id="4da52-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-551">'Identity'</span></span>
- <span data-ttu-id="4da52-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-553">'Razor'</span></span>
- <span data-ttu-id="4da52-554">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-555">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-556">'Blazor'</span></span>
- <span data-ttu-id="4da52-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-557">'Identity'</span></span>
- <span data-ttu-id="4da52-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-559">'Razor'</span></span>
- <span data-ttu-id="4da52-560">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-561">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-562">'Blazor'</span></span>
- <span data-ttu-id="4da52-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-563">'Identity'</span></span>
- <span data-ttu-id="4da52-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-565">'Razor'</span></span>
- <span data-ttu-id="4da52-566">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-567">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-568">'Blazor'</span></span>
- <span data-ttu-id="4da52-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-569">'Identity'</span></span>
- <span data-ttu-id="4da52-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-571">'Razor'</span></span>
- <span data-ttu-id="4da52-572">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-573">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-574">'Blazor'</span></span>
- <span data-ttu-id="4da52-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-575">'Identity'</span></span>
- <span data-ttu-id="4da52-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-577">'Razor'</span></span>
- <span data-ttu-id="4da52-578">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-579">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-580">'Blazor'</span></span>
- <span data-ttu-id="4da52-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-581">'Identity'</span></span>
- <span data-ttu-id="4da52-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-583">'Razor'</span></span>
- <span data-ttu-id="4da52-584">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-584">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-585">-------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-586">'Blazor'</span></span>
- <span data-ttu-id="4da52-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-587">'Identity'</span></span>
- <span data-ttu-id="4da52-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-589">'Razor'</span></span>
- <span data-ttu-id="4da52-590">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-591">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-592">'Blazor'</span></span>
- <span data-ttu-id="4da52-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-593">'Identity'</span></span>
- <span data-ttu-id="4da52-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-595">'Razor'</span></span>
- <span data-ttu-id="4da52-596">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-597">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-598">'Blazor'</span></span>
- <span data-ttu-id="4da52-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-599">'Identity'</span></span>
- <span data-ttu-id="4da52-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-601">'Razor'</span></span>
- <span data-ttu-id="4da52-602">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-603">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-604">'Blazor'</span></span>
- <span data-ttu-id="4da52-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-605">'Identity'</span></span>
- <span data-ttu-id="4da52-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-607">'Razor'</span></span>
- <span data-ttu-id="4da52-608">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-609">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-610">'Blazor'</span></span>
- <span data-ttu-id="4da52-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-611">'Identity'</span></span>
- <span data-ttu-id="4da52-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-613">'Razor'</span></span>
- <span data-ttu-id="4da52-614">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-615">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-616">'Blazor'</span></span>
- <span data-ttu-id="4da52-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-617">'Identity'</span></span>
- <span data-ttu-id="4da52-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-619">'Razor'</span></span>
- <span data-ttu-id="4da52-620">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-621">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-622">'Blazor'</span></span>
- <span data-ttu-id="4da52-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-623">'Identity'</span></span>
- <span data-ttu-id="4da52-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-625">'Razor'</span></span>
- <span data-ttu-id="4da52-626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-627">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-628">'Blazor'</span></span>
- <span data-ttu-id="4da52-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-629">'Identity'</span></span>
- <span data-ttu-id="4da52-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-631">'Razor'</span></span>
- <span data-ttu-id="4da52-632">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-633">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-634">'Blazor'</span></span>
- <span data-ttu-id="4da52-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-635">'Identity'</span></span>
- <span data-ttu-id="4da52-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-637">'Razor'</span></span>
- <span data-ttu-id="4da52-638">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-639">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-640">'Blazor'</span></span>
- <span data-ttu-id="4da52-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-641">'Identity'</span></span>
- <span data-ttu-id="4da52-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-643">'Razor'</span></span>
- <span data-ttu-id="4da52-644">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-645">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-646">'Blazor'</span></span>
- <span data-ttu-id="4da52-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-647">'Identity'</span></span>
- <span data-ttu-id="4da52-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-649">'Razor'</span></span>
- <span data-ttu-id="4da52-650">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-651">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-652">'Blazor'</span></span>
- <span data-ttu-id="4da52-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-653">'Identity'</span></span>
- <span data-ttu-id="4da52-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-655">'Razor'</span></span>
- <span data-ttu-id="4da52-656">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-657">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-658">'Blazor'</span></span>
- <span data-ttu-id="4da52-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-659">'Identity'</span></span>
- <span data-ttu-id="4da52-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-661">'Razor'</span></span>
- <span data-ttu-id="4da52-662">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-663">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-664">'Blazor'</span></span>
- <span data-ttu-id="4da52-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-665">'Identity'</span></span>
- <span data-ttu-id="4da52-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-667">'Razor'</span></span>
- <span data-ttu-id="4da52-668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-669">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-670">'Blazor'</span></span>
- <span data-ttu-id="4da52-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-671">'Identity'</span></span>
- <span data-ttu-id="4da52-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-673">'Razor'</span></span>
- <span data-ttu-id="4da52-674">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-675">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-676">'Blazor'</span></span>
- <span data-ttu-id="4da52-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-677">'Identity'</span></span>
- <span data-ttu-id="4da52-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-679">'Razor'</span></span>
- <span data-ttu-id="4da52-680">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-681">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-682">'Blazor'</span></span>
- <span data-ttu-id="4da52-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-683">'Identity'</span></span>
- <span data-ttu-id="4da52-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-685">'Razor'</span></span>
- <span data-ttu-id="4da52-686">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-687">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-688">'Blazor'</span></span>
- <span data-ttu-id="4da52-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-689">'Identity'</span></span>
- <span data-ttu-id="4da52-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-691">'Razor'</span></span>
- <span data-ttu-id="4da52-692">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-693">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-694">'Blazor'</span></span>
- <span data-ttu-id="4da52-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-695">'Identity'</span></span>
- <span data-ttu-id="4da52-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-697">'Razor'</span></span>
- <span data-ttu-id="4da52-698">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-699">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-700">'Blazor'</span></span>
- <span data-ttu-id="4da52-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-701">'Identity'</span></span>
- <span data-ttu-id="4da52-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-703">'Razor'</span></span>
- <span data-ttu-id="4da52-704">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-705">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-706">'Blazor'</span></span>
- <span data-ttu-id="4da52-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-707">'Identity'</span></span>
- <span data-ttu-id="4da52-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-709">'Razor'</span></span>
- <span data-ttu-id="4da52-710">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-711">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-712">'Blazor'</span></span>
- <span data-ttu-id="4da52-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-713">'Identity'</span></span>
- <span data-ttu-id="4da52-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-715">'Razor'</span></span>
- <span data-ttu-id="4da52-716">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-717">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-718">'Blazor'</span></span>
- <span data-ttu-id="4da52-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-719">'Identity'</span></span>
- <span data-ttu-id="4da52-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-721">'Razor'</span></span>
- <span data-ttu-id="4da52-722">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-723">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-724">'Blazor'</span></span>
- <span data-ttu-id="4da52-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-725">'Identity'</span></span>
- <span data-ttu-id="4da52-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-727">'Razor'</span></span>
- <span data-ttu-id="4da52-728">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-729">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-730">'Blazor'</span></span>
- <span data-ttu-id="4da52-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-731">'Identity'</span></span>
- <span data-ttu-id="4da52-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-733">'Razor'</span></span>
- <span data-ttu-id="4da52-734">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-735">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-736">'Blazor'</span></span>
- <span data-ttu-id="4da52-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-737">'Identity'</span></span>
- <span data-ttu-id="4da52-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-739">'Razor'</span></span>
- <span data-ttu-id="4da52-740">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-741">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-742">'Blazor'</span></span>
- <span data-ttu-id="4da52-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-743">'Identity'</span></span>
- <span data-ttu-id="4da52-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-745">'Razor'</span></span>
- <span data-ttu-id="4da52-746">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-747">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-748">'Blazor'</span></span>
- <span data-ttu-id="4da52-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-749">'Identity'</span></span>
- <span data-ttu-id="4da52-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-751">'Razor'</span></span>
- <span data-ttu-id="4da52-752">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-753">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-754">'Blazor'</span></span>
- <span data-ttu-id="4da52-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-755">'Identity'</span></span>
- <span data-ttu-id="4da52-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-757">'Razor'</span></span>
- <span data-ttu-id="4da52-758">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-759">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-760">'Blazor'</span></span>
- <span data-ttu-id="4da52-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-761">'Identity'</span></span>
- <span data-ttu-id="4da52-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-763">'Razor'</span></span>
- <span data-ttu-id="4da52-764">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-765">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-766">'Blazor'</span></span>
- <span data-ttu-id="4da52-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-767">'Identity'</span></span>
- <span data-ttu-id="4da52-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-769">'Razor'</span></span>
- <span data-ttu-id="4da52-770">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-771">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-772">'Blazor'</span></span>
- <span data-ttu-id="4da52-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-773">'Identity'</span></span>
- <span data-ttu-id="4da52-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-775">'Razor'</span></span>
- <span data-ttu-id="4da52-776">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-777">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-778">'Blazor'</span></span>
- <span data-ttu-id="4da52-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-779">'Identity'</span></span>
- <span data-ttu-id="4da52-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-781">'Razor'</span></span>
- <span data-ttu-id="4da52-782">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-783">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-784">'Blazor'</span></span>
- <span data-ttu-id="4da52-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-785">'Identity'</span></span>
- <span data-ttu-id="4da52-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-787">'Razor'</span></span>
- <span data-ttu-id="4da52-788">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-789">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-790">'Blazor'</span></span>
- <span data-ttu-id="4da52-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-791">'Identity'</span></span>
- <span data-ttu-id="4da52-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-793">'Razor'</span></span>
- <span data-ttu-id="4da52-794">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-795">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-796">'Blazor'</span></span>
- <span data-ttu-id="4da52-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-797">'Identity'</span></span>
- <span data-ttu-id="4da52-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-799">'Razor'</span></span>
- <span data-ttu-id="4da52-800">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-801">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-802">'Blazor'</span></span>
- <span data-ttu-id="4da52-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-803">'Identity'</span></span>
- <span data-ttu-id="4da52-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-805">'Razor'</span></span>
- <span data-ttu-id="4da52-806">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-806">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="4da52-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="4da52-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="4da52-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4da52-809">Değer: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="4da52-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4da52-810">Değer: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="4da52-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4da52-811">Deeri`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="4da52-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="4da52-812">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-812">JSON configuration provider</span></span>

<span data-ttu-id="4da52-813">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> YAPıLANDıRMAYı JSON dosya anahtar-değer çiftleriyle yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="4da52-814">Aşırı yüklemeler şunları belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-814">Overloads can specify:</span></span>

* <span data-ttu-id="4da52-815">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="4da52-815">Whether the file is optional.</span></span>
* <span data-ttu-id="4da52-816">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4da52-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="4da52-817">Aşağıdaki kodu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="4da52-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="4da52-818">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="4da52-818">The preceding code:</span></span>

* <span data-ttu-id="4da52-819">JSON yapılandırma sağlayıcısını, *MyConfig. JSON* dosyasını yüklemek için aşağıdaki seçeneklerle yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="4da52-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="4da52-820">`optional: true`: Dosya isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="4da52-821">`reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="4da52-822">*MyConfig. JSON* dosyasından önce [varsayılan yapılandırma sağlayıcılarını](#default) okur.</span><span class="sxs-lookup"><span data-stu-id="4da52-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="4da52-823">[Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki *MyConfig. JSON* dosyası geçersiz kılma ayarındaki ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4da52-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4da52-824">Genellikle, [ortam değişkenleri Yapılandırma sağlayıcısında](#evcp) ve [komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanmış özel bir JSON dosyası değerlerini geçersiz ***kılmayı istemezsiniz.***</span><span class="sxs-lookup"><span data-stu-id="4da52-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4da52-825">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="4da52-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="4da52-826">Yukarıdaki kodda, *MyConfig. JSON* ve *MyConfig*içindeki `Environment` ayarlar. *JSON* dosyaları:</span><span class="sxs-lookup"><span data-stu-id="4da52-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="4da52-827">*AppSettings. JSON* ve *appSettings*içindeki ayarları geçersiz kılın. `Environment` .. *JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4da52-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4da52-828">, [Ortam değişkenleri yapılandırma sağlayıcısı](#evcp) ve [komut satırı yapılandırma sağlayıcısı](#clcp)ayarları tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="4da52-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4da52-829">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , şu *MyConfig. JSON* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="4da52-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="4da52-830">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="4da52-831">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-831">File configuration provider</span></span>

<span data-ttu-id="4da52-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="4da52-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4da52-833">Aşağıdaki yapılandırma sağlayıcıları öğesinden türetilir `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="4da52-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="4da52-834">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4da52-835">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="4da52-836">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4da52-837">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-837">INI configuration provider</span></span>

<span data-ttu-id="4da52-838">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4da52-839">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="4da52-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="4da52-840">Yukarıdaki kodda, *myıniconfig. ini* ve *myıniconfig*içindeki `Environment` ayarlar. *ını* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="4da52-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4da52-841">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4da52-842">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4da52-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4da52-843">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , aşağıdaki *Myıniconfig. ini* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="4da52-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="4da52-844">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="4da52-845">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-845">XML configuration provider</span></span>

<span data-ttu-id="4da52-846">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4da52-847">Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve çeşitli yapılandırma sağlayıcıları ekler:</span><span class="sxs-lookup"><span data-stu-id="4da52-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="4da52-848">Yukarıdaki kodda, *myXMLfile. xml* ve *myXMLfile*içindeki ayarlar. `Environment` .. *XML* dosyaları, içindeki ayarlar tarafından geçersiz kılınır:</span><span class="sxs-lookup"><span data-stu-id="4da52-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4da52-849">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4da52-850">[Komut satırı yapılandırma sağlayıcısı](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4da52-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4da52-851">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) , şu *myXMLfile. xml* dosyasını içerir:</span><span class="sxs-lookup"><span data-stu-id="4da52-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="4da52-852">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarından birkaçını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-853">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="4da52-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="4da52-854">Aşağıdaki kod, önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-855">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4da52-856">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="4da52-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4da52-857">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="4da52-857">key:attribute</span></span>
* <span data-ttu-id="4da52-858">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="4da52-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4da52-859">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="4da52-860">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4da52-861">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-861">The key is the file name.</span></span> <span data-ttu-id="4da52-862">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-862">The value contains the file's contents.</span></span> <span data-ttu-id="4da52-863">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4da52-864">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4da52-865">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4da52-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4da52-866">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="4da52-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="4da52-867">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="4da52-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4da52-868">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="4da52-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4da52-869">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4da52-870">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="4da52-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4da52-871">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="4da52-872">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-872">Memory configuration provider</span></span>

<span data-ttu-id="4da52-873">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4da52-874">Aşağıdaki kod, yapılandırma sistemine bir bellek koleksiyonu ekler:</span><span class="sxs-lookup"><span data-stu-id="4da52-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="4da52-875">[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kodu, önceki yapılandırma ayarlarını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-876">Önceki kodda, `config.AddInMemoryCollection(Dict)` [varsayılan yapılandırma sağlayıcılarından](#default)sonra eklenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4da52-877">Yapılandırma sağlayıcılarını sipariş eden bir örnek için bkz. [JSON yapılandırma sağlayıcısı](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4da52-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4da52-878">Bkz. kullanarak bir diziyi başka bir örnek için [bağlama](#boa) `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="4da52-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="4da52-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="4da52-879">GetValue</span></span>

<span data-ttu-id="4da52-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:</span><span class="sxs-lookup"><span data-stu-id="4da52-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-881">Önceki kodda, `NumberKey` yapılandırmada bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4da52-882">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="4da52-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4da52-883">İzleyen örnekler için aşağıdaki *Myalt bölüm. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4da52-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="4da52-884">Aşağıdaki kod, *Myalt bölüm. JSON* öğesini yapılandırma sağlayıcılarına ekler:</span><span class="sxs-lookup"><span data-stu-id="4da52-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="4da52-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="4da52-885">GetSection</span></span>

<span data-ttu-id="4da52-886">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="4da52-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4da52-887">Aşağıdaki kod şu değerleri döndürür `section1` :</span><span class="sxs-lookup"><span data-stu-id="4da52-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-888">Aşağıdaki kod şu değerleri döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="4da52-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-889">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="4da52-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4da52-890">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4da52-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4da52-891">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="4da52-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4da52-892">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4da52-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="4da52-893">GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="4da52-893">GetChildren and Exists</span></span>

<span data-ttu-id="4da52-894">Aşağıdaki kod, [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) öğesini çağırır ve değerlerini döndürür `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="4da52-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-895">Yukarıdaki kod, bir bölümü doğrulamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="4da52-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="4da52-896">Bir diziyi bağlama</span><span class="sxs-lookup"><span data-stu-id="4da52-896">Bind an array</span></span>

<span data-ttu-id="4da52-897">[Configurationciltçi. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) , yapılandırma anahtarlarındaki dizi dizinlerini kullanarak dizilere dizi nesneleri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="4da52-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4da52-898">Sayısal anahtar segmentini ortaya çıkaran herhangi bir dizi biçimi, [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4da52-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="4da52-899">[Örnek indirmenizde](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *myArray. JSON* ' i göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4da52-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="4da52-900">Aşağıdaki kod, yapılandırma sağlayıcılarına *myArray. JSON* ekler:</span><span class="sxs-lookup"><span data-stu-id="4da52-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="4da52-901">Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-902">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="4da52-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="4da52-903">Önceki çıktıda, Dizin 3 ' `value40` `"4": "value40",` te, *myArray. JSON*içinde öğesine karşılık gelen bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="4da52-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="4da52-904">Bağlantılı dizi dizinleri sürekli ve yapılandırma anahtarı diziniyle bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="4da52-905">Yapılandırma Bağlayıcısı, bağlantılı nesnelerde null değerleri bağlama veya null girişler oluşturma yeteneğine sahip değil</span><span class="sxs-lookup"><span data-stu-id="4da52-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="4da52-906">Aşağıdaki kod, `array:entries` yapılandırmayı <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> genişletme yöntemiyle yükler:</span><span class="sxs-lookup"><span data-stu-id="4da52-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="4da52-907">Aşağıdaki kod, içindeki yapılandırmayı okur `arrayDict` `Dictionary` ve değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-908">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="4da52-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="4da52-909">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="4da52-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4da52-910">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri, nesne oluştururken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4da52-911">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4da52-912">Dizin 3 &num; `ArrayExample` &num; anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="4da52-913">Örnek indirmenin aşağıdaki *Value3. JSON* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4da52-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="4da52-914">Aşağıdaki kod *Value3. JSON* için yapılandırmayı ve şunları içerir `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="4da52-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="4da52-915">Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-916">Yukarıdaki kod aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="4da52-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="4da52-917">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="4da52-918">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-918">Custom configuration provider</span></span>

<span data-ttu-id="4da52-919">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="4da52-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4da52-920">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="4da52-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4da52-921">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4da52-922">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4da52-923">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="4da52-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4da52-924">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4da52-925">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4da52-926">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4da52-927">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4da52-928">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="4da52-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4da52-929">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4da52-930">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="4da52-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4da52-931">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4da52-932">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="4da52-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4da52-933">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="4da52-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="4da52-934">[Yapılandırma anahtarları büyük/küçük harfe duyarsız](#keys)olduğundan, veritabanını başlatmak için kullanılan sözlük, büyük/küçük harf duyarsız karşılaştırıcı ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4da52-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="4da52-935">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4da52-936">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4da52-937">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4da52-938">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="4da52-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="4da52-939">Başlangıçta erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4da52-939">Access configuration in Startup</span></span>

<span data-ttu-id="4da52-940">Aşağıdaki kod, yöntemlerde yapılandırma verilerini görüntüler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="4da52-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="4da52-941">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="4da52-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="4da52-942">Sayfalarda erişim yapılandırması Razor</span><span class="sxs-lookup"><span data-stu-id="4da52-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="4da52-943">Aşağıdaki kod, yapılandırma verilerini bir sayfada görüntüler Razor :</span><span class="sxs-lookup"><span data-stu-id="4da52-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="4da52-944">Aşağıdaki kodda, `MyOptions` ile hizmet kapsayıcısına eklenir <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> ve yapılandırmaya bağlanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="4da52-945">Aşağıdaki biçimlendirme, [`@inject`](xref:mvc/views/razor#inject) Razor seçenek değerlerini çözümlemek ve göstermek için yönergesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="4da52-946">MVC görünüm dosyasındaki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4da52-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="4da52-947">Aşağıdaki kod, yapılandırma verilerini bir MVC görünümünde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="4da52-948">Bir temsilciyle seçenekleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-948">Configure options with a delegate</span></span>

<span data-ttu-id="4da52-949">Bir temsilci içinde yapılandırılan seçenekler yapılandırma sağlayıcılarında ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4da52-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="4da52-950">Bir temsilciyle seçenekleri yapılandırmak örnek uygulamada 2 örnek olarak gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4da52-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="4da52-951">Aşağıdaki kodda, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> hizmet kapsayıcısına bir hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="4da52-952">Şu değerleri yapılandırmak için bir temsilci kullanır `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="4da52-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="4da52-953">Aşağıdaki kod, seçenek değerlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4da52-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="4da52-954">Önceki örnekte, `Option1` ve değerleri `Option2` *appSettings. JSON* içinde belirtilmiştir ve sonra yapılandırılan temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="4da52-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4da52-955">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="4da52-955">Host versus app configuration</span></span>

<span data-ttu-id="4da52-956">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4da52-957">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="4da52-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4da52-958">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4da52-959">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4da52-960">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="4da52-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="4da52-961">Varsayılan konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4da52-961">Default host configuration</span></span>

<span data-ttu-id="4da52-962">[Web konağını](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [bu konunun ASP.NET Core 2,2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="4da52-963">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4da52-964">Ortam değişkenleri `DOTNET_` `DOTNET_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="4da52-965">`DOTNET_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4da52-966">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4da52-967">Web ana bilgisayar varsayılan yapılandırması oluşturuldu ( `ConfigureWebHostDefaults` ):</span><span class="sxs-lookup"><span data-stu-id="4da52-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="4da52-968">Kestrel, Web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="4da52-969">Konak filtreleme ara yazılımı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4da52-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="4da52-970">Ortam değişkeni olarak ayarlanmışsa Iletilen üstbilgiler ara yazılımı ekleyin `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="4da52-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="4da52-971">IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="4da52-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4da52-972">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-972">Other configuration</span></span>

<span data-ttu-id="4da52-973">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="4da52-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4da52-974">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="4da52-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4da52-975">*Launch. JSON* / *Launchsettings. JSON* , geliştirme ortamı için yapılandırma dosyalarını araçlar, aşağıda açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="4da52-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4da52-976">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="4da52-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4da52-977">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="4da52-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4da52-978">*Web. config* aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="4da52-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4da52-979">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="4da52-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4da52-980">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="4da52-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="4da52-981">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="4da52-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4da52-982">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4da52-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4da52-983">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4da52-983">Additional resources</span></span>

* [<span data-ttu-id="4da52-984">Yapılandırma kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="4da52-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4da52-985">ASP.NET Core içindeki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından belirlenen anahtar-değer çiftlerini temel alır.</span><span class="sxs-lookup"><span data-stu-id="4da52-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="4da52-986">Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="4da52-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="4da52-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4da52-987">Azure Key Vault</span></span>
* <span data-ttu-id="4da52-988">Azure Uygulama Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4da52-988">Azure App Configuration</span></span>
* <span data-ttu-id="4da52-989">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-989">Command-line arguments</span></span>
* <span data-ttu-id="4da52-990">Özel sağlayıcılar (yüklü veya oluşturulmuş)</span><span class="sxs-lookup"><span data-stu-id="4da52-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="4da52-991">Dizin dosyaları</span><span class="sxs-lookup"><span data-stu-id="4da52-991">Directory files</span></span>
* <span data-ttu-id="4da52-992">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-992">Environment variables</span></span>
* <span data-ttu-id="4da52-993">Bellek içi .NET nesneleri</span><span class="sxs-lookup"><span data-stu-id="4da52-993">In-memory .NET objects</span></span>
* <span data-ttu-id="4da52-994">Ayarlar dosyaları</span><span class="sxs-lookup"><span data-stu-id="4da52-994">Settings files</span></span>

<span data-ttu-id="4da52-995">Ortak yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)), [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="4da52-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4da52-996">Örnek uygulamada ve aşağıdaki kod örnekleri <xref:Microsoft.Extensions.Configuration> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="4da52-997">*Seçenekler stili* , bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="4da52-998">Seçenekler, ilgili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="4da52-999">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="4da52-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="4da52-1000">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4da52-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4da52-1001">Uygulama yapılandırmasına karşı konak</span><span class="sxs-lookup"><span data-stu-id="4da52-1001">Host versus app configuration</span></span>

<span data-ttu-id="4da52-1002">Uygulama yapılandırıldıktan ve başlatılmadan önce, bir *konak* yapılandırılır ve başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4da52-1003">Ana bilgisayar, uygulama başlatma ve ömür yönetiminden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="4da52-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4da52-1004">Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4da52-1005">Ana bilgisayar yapılandırma anahtar-değer çiftleri, uygulamanın yapılandırmasına de dahildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4da52-1006">Konak oluşturulduğunda yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının konak yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz <xref:fundamentals/index#host> ..</span><span class="sxs-lookup"><span data-stu-id="4da52-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4da52-1007">Diğer yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-1007">Other configuration</span></span>

<span data-ttu-id="4da52-1008">Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4da52-1009">ASP.NET Core uygulamalarını çalıştırmanın diğer yönleri, bu konuda kapsanmayan yapılandırma dosyaları kullanılarak yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4da52-1010">*Launch. JSON* / *Launchsettings. JSON* , geliştirme ortamı için yapılandırma dosyalarını araçlar, aşağıda açıklanmıştır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4da52-1011">İçinde <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="4da52-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4da52-1012">Dosyaların geliştirme senaryolarında ASP.NET Core Uygulamaları yapılandırmak için kullanıldığı belge kümesi boyunca.</span><span class="sxs-lookup"><span data-stu-id="4da52-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4da52-1013">*Web. config* aşağıdaki konularda açıklanan bir sunucu yapılandırma dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4da52-1014">ASP.NET 'in önceki sürümlerinden uygulama yapılandırmasını geçirme hakkında daha fazla bilgi için bkz <xref:migration/proper-to-2x/index#store-configurations> ..</span><span class="sxs-lookup"><span data-stu-id="4da52-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="4da52-1015">Varsayılan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-1015">Default configuration</span></span>

<span data-ttu-id="4da52-1016">Ana bilgisayar oluştururken ASP.NET Core [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar çağrısı tabanlı Web Apps <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="4da52-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="4da52-1017">`CreateDefaultBuilder`uygulama için varsayılan yapılandırmayı aşağıdaki sırayla sağlar:</span><span class="sxs-lookup"><span data-stu-id="4da52-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="4da52-1018">[Web ana bilgisayarı](xref:fundamentals/host/web-host)kullanan uygulamalar için aşağıdakiler geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4da52-1019">[Genel ana bilgisayarı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için, [Bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="4da52-1020">Ana bilgisayar yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4da52-1021">Ortam değişkenleri `ASPNETCORE_` `ASPNETCORE_ENVIRONMENT` [yapılandırma sağlayıcısı](#environment-variables-configuration-provider)kullanılarak (örneğin,) ön eki olan ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="4da52-1022">`ASPNETCORE_`Yapılandırma anahtar-değer çiftleri yüklendiğinde önek () çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4da52-1023">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4da52-1024">Uygulama yapılandırması şuradan sağlanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="4da52-1025">[dosya yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4da52-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4da52-1026">\*appSettings. \* [Dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanılarak {Environment}. JSON.</span><span class="sxs-lookup"><span data-stu-id="4da52-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4da52-1027">[Secret Manager](xref:security/app-secrets) Uygulama, `Development` giriş derlemesini kullanarak ortamda çalıştırıldığında gizli Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="4da52-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4da52-1028">Ortam [değişkenleri yapılandırma sağlayıcısını](#environment-variables-configuration-provider)kullanarak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="4da52-1029">Komut satırı [yapılandırma sağlayıcısını](#command-line-configuration-provider)kullanan komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="4da52-1030">Güvenlik</span><span class="sxs-lookup"><span data-stu-id="4da52-1030">Security</span></span>

<span data-ttu-id="4da52-1031">Hassas yapılandırma verilerini güvenli hale getirmek için aşağıdaki uygulamaları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="4da52-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="4da52-1032">Yapılandırma sağlayıcısı kodunda veya düz metin yapılandırma dosyalarında parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="4da52-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="4da52-1033">Geliştirme veya test ortamlarında üretim gizli dizileri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4da52-1034">Yanlışlıkla bir kaynak kodu deposuna uygulanamazlar için proje dışındaki gizli dizileri belirtin.</span><span class="sxs-lookup"><span data-stu-id="4da52-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4da52-1035">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="4da52-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4da52-1036"><xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerini kullanma hakkında öneriler içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4da52-1037">Gizli dizi Yöneticisi, Kullanıcı gizli dizilerini yerel sistemdeki bir JSON dosyasında depolamak için dosya yapılandırma sağlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="4da52-1038">Dosya yapılandırma sağlayıcısı, bu konunun ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="4da52-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamalar için uygulama gizli dizilerini güvenli bir şekilde depolar.</span><span class="sxs-lookup"><span data-stu-id="4da52-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4da52-1040">Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4da52-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4da52-1041">Hiyerarşik yapılandırma verileri</span><span class="sxs-lookup"><span data-stu-id="4da52-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="4da52-1042">Yapılandırma API 'SI, hiyerarşik verileri yapılandırma anahtarlarında bir sınırlayıcı kullanımıyla birlikte düzleştirerek hiyerarşik yapılandırma verilerini muhafaza ediyor.</span><span class="sxs-lookup"><span data-stu-id="4da52-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4da52-1043">Aşağıdaki JSON dosyasında, iki bölümün yapılandırılmış hiyerarşisinde dört anahtar mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="4da52-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="4da52-1044">Dosya yapılandırmaya okunduğu zaman, yapılandırma kaynağının özgün hiyerarşik veri yapısını sürdürmek için benzersiz anahtarlar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4da52-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="4da52-1045">Özgün yapıyı sürdürmek için bölümler ve anahtarlar iki nokta () kullanımıyla düzleştirilir `:` :</span><span class="sxs-lookup"><span data-stu-id="4da52-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="4da52-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-1046">section0:key0</span></span>
* <span data-ttu-id="4da52-1047">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-1047">section0:key1</span></span>
* <span data-ttu-id="4da52-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-1048">section1:key0</span></span>
* <span data-ttu-id="4da52-1049">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-1049">section1:key1</span></span>

<span data-ttu-id="4da52-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verileri bölümünün bölümlerini ve alt öğelerini yalıtmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4da52-1051">Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)içinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="4da52-1052">Kurallar</span><span class="sxs-lookup"><span data-stu-id="4da52-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="4da52-1053">Kaynaklar ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="4da52-1053">Sources and providers</span></span>

<span data-ttu-id="4da52-1054">Uygulama başlangıcında yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="4da52-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="4da52-1055">Değişiklik algılamayı uygulayan yapılandırma sağlayıcılarının, temel alınan bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneği vardır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="4da52-1056">Örneğin, dosya yapılandırma sağlayıcısı (Bu konunun ilerleyen kısımlarında açıklanmıştır) ve [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılamayı uygular.</span><span class="sxs-lookup"><span data-stu-id="4da52-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="4da52-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kapsayıcısında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4da52-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.Controller> , sınıfının yapılandırmasını elde etmek için BIR sayfalara veya MVC 'ye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="4da52-1059">Aşağıdaki örneklerde, `_config` alanı yapılandırma değerlerine erişmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="4da52-1060">Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandıklarında kullanılamadığından, DI 'yi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="4da52-1061">Anahtarlar</span><span class="sxs-lookup"><span data-stu-id="4da52-1061">Keys</span></span>

<span data-ttu-id="4da52-1062">Yapılandırma anahtarları aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="4da52-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="4da52-1063">Anahtarlar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="4da52-1064">Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4da52-1065">Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlandıysa, anahtardaki en son değer kullanılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="4da52-1066">Hiyerarşik anahtarlar</span><span class="sxs-lookup"><span data-stu-id="4da52-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="4da52-1067">Yapılandırma API 'SI içinde, iki nokta üst üste ayırıcı ( `:` ) tüm platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4da52-1068">Ortam değişkenlerinde, tüm platformlarda bir iki nokta ayırıcı çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4da52-1069">Çift alt çizgi ( `__` ) tüm platformlar tarafından desteklenir ve otomatik olarak iki nokta olarak dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="4da52-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="4da52-1070">Azure Key Vault, hiyerarşik anahtarlar `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="4da52-1071">Gizli dizileri uygulamanın yapılandırmasına yüklendiğinde tireleri bir iki nokta ile değiştirmek için kod yazın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4da52-1072">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="4da52-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4da52-1073">Dizi bağlama, [diziyi bir sınıfa bağlama](#bind-an-array-to-a-class) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="4da52-1074">Değerler</span><span class="sxs-lookup"><span data-stu-id="4da52-1074">Values</span></span>

<span data-ttu-id="4da52-1075">Yapılandırma değerleri aşağıdaki kuralları benimseyin:</span><span class="sxs-lookup"><span data-stu-id="4da52-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="4da52-1076">Değerler dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1076">Values are strings.</span></span>
* <span data-ttu-id="4da52-1077">Null değerler yapılandırmada saklanamaz veya nesnelere bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="4da52-1078">Sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="4da52-1078">Providers</span></span>

<span data-ttu-id="4da52-1079">Aşağıdaki tabloda ASP.NET Core uygulamalar için kullanılabilen yapılandırma sağlayıcıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4da52-1080">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="4da52-1080">Provider</span></span> | <span data-ttu-id="4da52-1081">Şuradan yapılandırma sağlar&hellip;</span><span class="sxs-lookup"><span data-stu-id="4da52-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="4da52-1082">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1083">'Blazor'</span></span>
- <span data-ttu-id="4da52-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1084">'Identity'</span></span>
- <span data-ttu-id="4da52-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1086">'Razor'</span></span>
- <span data-ttu-id="4da52-1087">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1088">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1089">'Blazor'</span></span>
- <span data-ttu-id="4da52-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1090">'Identity'</span></span>
- <span data-ttu-id="4da52-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1092">'Razor'</span></span>
- <span data-ttu-id="4da52-1093">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1094">---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1095">'Blazor'</span></span>
- <span data-ttu-id="4da52-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1096">'Identity'</span></span>
- <span data-ttu-id="4da52-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1098">'Razor'</span></span>
- <span data-ttu-id="4da52-1099">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1100">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1101">'Blazor'</span></span>
- <span data-ttu-id="4da52-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1102">'Identity'</span></span>
- <span data-ttu-id="4da52-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1104">'Razor'</span></span>
- <span data-ttu-id="4da52-1105">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1106">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1107">'Blazor'</span></span>
- <span data-ttu-id="4da52-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1108">'Identity'</span></span>
- <span data-ttu-id="4da52-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1110">'Razor'</span></span>
- <span data-ttu-id="4da52-1111">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1112">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1113">'Blazor'</span></span>
- <span data-ttu-id="4da52-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1114">'Identity'</span></span>
- <span data-ttu-id="4da52-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1116">'Razor'</span></span>
- <span data-ttu-id="4da52-1117">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1118">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1119">'Blazor'</span></span>
- <span data-ttu-id="4da52-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1120">'Identity'</span></span>
- <span data-ttu-id="4da52-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1122">'Razor'</span></span>
- <span data-ttu-id="4da52-1123">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1124">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1125">'Blazor'</span></span>
- <span data-ttu-id="4da52-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1126">'Identity'</span></span>
- <span data-ttu-id="4da52-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1128">'Razor'</span></span>
- <span data-ttu-id="4da52-1129">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1130">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1131">'Blazor'</span></span>
- <span data-ttu-id="4da52-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1132">'Identity'</span></span>
- <span data-ttu-id="4da52-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1134">'Razor'</span></span>
- <span data-ttu-id="4da52-1135">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1136">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1137">'Blazor'</span></span>
- <span data-ttu-id="4da52-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1138">'Identity'</span></span>
- <span data-ttu-id="4da52-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1140">'Razor'</span></span>
- <span data-ttu-id="4da52-1141">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1142">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1143">'Blazor'</span></span>
- <span data-ttu-id="4da52-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1144">'Identity'</span></span>
- <span data-ttu-id="4da52-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1146">'Razor'</span></span>
- <span data-ttu-id="4da52-1147">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1148">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1149">'Blazor'</span></span>
- <span data-ttu-id="4da52-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1150">'Identity'</span></span>
- <span data-ttu-id="4da52-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1152">'Razor'</span></span>
- <span data-ttu-id="4da52-1153">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1154">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1155">'Blazor'</span></span>
- <span data-ttu-id="4da52-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1156">'Identity'</span></span>
- <span data-ttu-id="4da52-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1158">'Razor'</span></span>
- <span data-ttu-id="4da52-1159">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1160">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1161">'Blazor'</span></span>
- <span data-ttu-id="4da52-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1162">'Identity'</span></span>
- <span data-ttu-id="4da52-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1164">'Razor'</span></span>
- <span data-ttu-id="4da52-1165">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1166">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1167">'Blazor'</span></span>
- <span data-ttu-id="4da52-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1168">'Identity'</span></span>
- <span data-ttu-id="4da52-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1170">'Razor'</span></span>
- <span data-ttu-id="4da52-1171">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1172">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1173">'Blazor'</span></span>
- <span data-ttu-id="4da52-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1174">'Identity'</span></span>
- <span data-ttu-id="4da52-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1176">'Razor'</span></span>
- <span data-ttu-id="4da52-1177">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1178">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1179">'Blazor'</span></span>
- <span data-ttu-id="4da52-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1180">'Identity'</span></span>
- <span data-ttu-id="4da52-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1182">'Razor'</span></span>
- <span data-ttu-id="4da52-1183">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1184">------------------ | | [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) (*güvenlik* konuları) | Azure Key Vault | | [Azure uygulama yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri) | Azure Uygulama yapılandırması | | [Komut satırı yapılandırma sağlayıcısı](#command-line-configuration-provider) | Komut satırı parametreleri | | [Özel yapılandırma sağlayıcısı](#custom-configuration-provider) | Özel kaynak | | [Ortam değişkenleri yapılandırma sağlayıcısı](#environment-variables-configuration-provider) | Ortam değişkenleri | | [Dosya yapılandırma sağlayıcısı](#file-configuration-provider) | Dosyalar (ıNı, JSON, XML) | | [Dosya başına anahtar yapılandırma sağlayıcısı](#key-per-file-configuration-provider) | Dizin dosyaları | | [Bellek yapılandırma sağlayıcısı](#memory-configuration-provider) | Bellek içi Koleksiyonlar | | [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (*güvenlik* konuları) | Kullanıcı profili dizinindeki dosya |</span><span class="sxs-lookup"><span data-stu-id="4da52-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="4da52-1185">Yapılandırma kaynakları, başlangıçta yapılandırma sağlayıcılarının belirtilme sırasına göre okundu.</span><span class="sxs-lookup"><span data-stu-id="4da52-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="4da52-1186">Bu konu başlığı altında açıklanan yapılandırma sağlayıcıları, kodun onları düzenler sırasına göre değil alfabetik sırayla açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="4da52-1187">Koddaki yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynakları için önceliklere uyacak şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4da52-1188">Yapılandırma sağlayıcılarının tipik bir sırası şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4da52-1189">Dosyalar (*appSettings. JSON*, *appSettings. { Environment}. JSON*, `{Environment}` uygulamanın geçerli barındırma ortamıdır</span><span class="sxs-lookup"><span data-stu-id="4da52-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="4da52-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4da52-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="4da52-1191">[Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) (yalnızca geliştirme ortamı)</span><span class="sxs-lookup"><span data-stu-id="4da52-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="4da52-1192">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-1192">Environment variables</span></span>
1. <span data-ttu-id="4da52-1193">Komut satırı bağımsız değişkenleri</span><span class="sxs-lookup"><span data-stu-id="4da52-1193">Command-line arguments</span></span>

<span data-ttu-id="4da52-1194">Ortak bir uygulama, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasını sağlamak üzere komut satırı yapılandırma sağlayıcısını bir sağlayıcı serisinde en son konumlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4da52-1195">Yeni bir ana bilgisayar Oluşturucu ile başlatıldığında önceki sağlayıcı dizisi kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4da52-1196">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="4da52-1197">Konak oluşturucuyu UseConfiguration ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4da52-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="4da52-1198">Konak oluşturucuyu yapılandırmak için, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırma ile konak Oluşturucu üzerinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="4da52-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4da52-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4da52-1200">`ConfigureAppConfiguration`Ana bilgisayarı oluşturma sırasında otomatik olarak eklenenlere ek olarak, uygulamanın yapılandırma sağlayıcılarını belirtmek için çağırın `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="4da52-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="4da52-1201">Önceki yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="4da52-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="4da52-1202">Komut satırı bağımsız değişkenleriyle geçersiz kılınabilen uygulama yapılandırması sağlamak için, en son ' u çağırın `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="4da52-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="4da52-1203">CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldır</span><span class="sxs-lookup"><span data-stu-id="4da52-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="4da52-1204">Tarafından eklenen sağlayıcıları kaldırmak için `CreateDefaultBuilder` , önce [ılisteationbuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) üzerinde [clear](/dotnet/api/system.collections.generic.icollection-1.clear) öğesini çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="4da52-1205">Uygulama başlatma sırasında yapılandırmayı kullan</span><span class="sxs-lookup"><span data-stu-id="4da52-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="4da52-1206">Uygulamasında uygulamaya sağlanan yapılandırma `ConfigureAppConfiguration` , uygulamanın başlangıcında, dahil olmak üzere kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4da52-1207">Daha fazla bilgi için [başlatma sırasında erişim yapılandırması](#access-configuration-during-startup) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="4da52-1208">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="4da52-1209">, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Çalışma zamanında anahtar-değer çiftinden yapılandırma yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="4da52-1210">Komut satırı yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> bir örneğinde genişletme yöntemi çağırılır <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4da52-1211">`AddCommandLine`çağrıldığında otomatik olarak çağrılır `CreateDefaultBuilder(string [])` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="4da52-1212">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4da52-1213">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="4da52-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4da52-1214">*AppSettings. JSON* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4da52-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4da52-1215">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="4da52-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4da52-1216">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-1216">Environment variables.</span></span>

<span data-ttu-id="4da52-1217">`CreateDefaultBuilder`Komut satırı yapılandırma sağlayıcısını son ekler.</span><span class="sxs-lookup"><span data-stu-id="4da52-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="4da52-1218">Diğer sağlayıcılar tarafından ayarlanan çalışma zamanında geçersiz kılma yapılandırmasında komut satırı bağımsız değişkenleri geçirildi.</span><span class="sxs-lookup"><span data-stu-id="4da52-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="4da52-1219">`CreateDefaultBuilder`Ana bilgisayar oluşturulduğunda davranır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="4da52-1220">Bu nedenle, tarafından etkinleştirilen komut satırı yapılandırması `CreateDefaultBuilder` konağın nasıl yapılandırıldığını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="4da52-1221">ASP.NET Core şablonlarına dayalı uygulamalar için, `AddCommandLine` tarafından zaten çağırılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4da52-1222">Ek yapılandırma sağlayıcıları eklemek ve bu sağlayıcılardan yapılandırmayı komut satırı bağımsız değişkenleriyle geçersiz kılmak için uygulamanın ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddCommandLine` en son çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="4da52-1223">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="4da52-1223">**Example**</span></span>

<span data-ttu-id="4da52-1224">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="4da52-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="4da52-1225">Projenin dizininde bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="4da52-1226">Komutuna bir komut satırı bağımsız değişkeni sağlayın `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="4da52-1227">Uygulama çalıştırıldıktan sonra, konumundaki uygulamaya bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4da52-1228">Çıktının ' de belirtilen yapılandırma komut satırı bağımsız değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="4da52-1229">Bağımsız değişkenler</span><span class="sxs-lookup"><span data-stu-id="4da52-1229">Arguments</span></span>

<span data-ttu-id="4da52-1230">Değer bir eşittir işaretini ( `=` ) izlemelidir veya `--` `/` değer bir boşluk izleyen anahtar bir ön eke (ya da) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="4da52-1231">Eşittir işareti kullanılırsa değer gerekli değildir (örneğin, `CommandLineKey=` ).</span><span class="sxs-lookup"><span data-stu-id="4da52-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="4da52-1232">Anahtar ön eki</span><span class="sxs-lookup"><span data-stu-id="4da52-1232">Key prefix</span></span>               | <span data-ttu-id="4da52-1233">Örnek</span><span class="sxs-lookup"><span data-stu-id="4da52-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="4da52-1234">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1235">'Blazor'</span></span>
- <span data-ttu-id="4da52-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1236">'Identity'</span></span>
- <span data-ttu-id="4da52-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1238">'Razor'</span></span>
- <span data-ttu-id="4da52-1239">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1240">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1241">'Blazor'</span></span>
- <span data-ttu-id="4da52-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1242">'Identity'</span></span>
- <span data-ttu-id="4da52-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1244">'Razor'</span></span>
- <span data-ttu-id="4da52-1245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1246">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1247">'Blazor'</span></span>
- <span data-ttu-id="4da52-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1248">'Identity'</span></span>
- <span data-ttu-id="4da52-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1250">'Razor'</span></span>
- <span data-ttu-id="4da52-1251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1252">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1253">'Blazor'</span></span>
- <span data-ttu-id="4da52-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1254">'Identity'</span></span>
- <span data-ttu-id="4da52-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1256">'Razor'</span></span>
- <span data-ttu-id="4da52-1257">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1258">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1259">'Blazor'</span></span>
- <span data-ttu-id="4da52-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1260">'Identity'</span></span>
- <span data-ttu-id="4da52-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1262">'Razor'</span></span>
- <span data-ttu-id="4da52-1263">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1264">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1265">'Blazor'</span></span>
- <span data-ttu-id="4da52-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1266">'Identity'</span></span>
- <span data-ttu-id="4da52-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1268">'Razor'</span></span>
- <span data-ttu-id="4da52-1269">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1270">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1271">'Blazor'</span></span>
- <span data-ttu-id="4da52-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1272">'Identity'</span></span>
- <span data-ttu-id="4da52-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1274">'Razor'</span></span>
- <span data-ttu-id="4da52-1275">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1276">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1277">'Blazor'</span></span>
- <span data-ttu-id="4da52-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1278">'Identity'</span></span>
- <span data-ttu-id="4da52-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1280">'Razor'</span></span>
- <span data-ttu-id="4da52-1281">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1282">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1283">'Blazor'</span></span>
- <span data-ttu-id="4da52-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1284">'Identity'</span></span>
- <span data-ttu-id="4da52-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1286">'Razor'</span></span>
- <span data-ttu-id="4da52-1287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1288">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1289">'Blazor'</span></span>
- <span data-ttu-id="4da52-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1290">'Identity'</span></span>
- <span data-ttu-id="4da52-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1292">'Razor'</span></span>
- <span data-ttu-id="4da52-1293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1294">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1295">'Blazor'</span></span>
- <span data-ttu-id="4da52-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1296">'Identity'</span></span>
- <span data-ttu-id="4da52-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1298">'Razor'</span></span>
- <span data-ttu-id="4da52-1299">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1300">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1301">'Blazor'</span></span>
- <span data-ttu-id="4da52-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1302">'Identity'</span></span>
- <span data-ttu-id="4da52-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1304">'Razor'</span></span>
- <span data-ttu-id="4da52-1305">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1306">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1307">'Blazor'</span></span>
- <span data-ttu-id="4da52-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1308">'Identity'</span></span>
- <span data-ttu-id="4da52-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1310">'Razor'</span></span>
- <span data-ttu-id="4da52-1311">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1312">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1313">'Blazor'</span></span>
- <span data-ttu-id="4da52-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1314">'Identity'</span></span>
- <span data-ttu-id="4da52-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1316">'Razor'</span></span>
- <span data-ttu-id="4da52-1317">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1318">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1319">'Blazor'</span></span>
- <span data-ttu-id="4da52-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1320">'Identity'</span></span>
- <span data-ttu-id="4da52-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1322">'Razor'</span></span>
- <span data-ttu-id="4da52-1323">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1324">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1325">'Blazor'</span></span>
- <span data-ttu-id="4da52-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1326">'Identity'</span></span>
- <span data-ttu-id="4da52-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1328">'Razor'</span></span>
- <span data-ttu-id="4da52-1329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1330">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1331">'Blazor'</span></span>
- <span data-ttu-id="4da52-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1332">'Identity'</span></span>
- <span data-ttu-id="4da52-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1334">'Razor'</span></span>
- <span data-ttu-id="4da52-1335">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1336">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1337">'Blazor'</span></span>
- <span data-ttu-id="4da52-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1338">'Identity'</span></span>
- <span data-ttu-id="4da52-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1340">'Razor'</span></span>
- <span data-ttu-id="4da52-1341">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1342">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1343">'Blazor'</span></span>
- <span data-ttu-id="4da52-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1344">'Identity'</span></span>
- <span data-ttu-id="4da52-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1346">'Razor'</span></span>
- <span data-ttu-id="4da52-1347">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1348">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1349">'Blazor'</span></span>
- <span data-ttu-id="4da52-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1350">'Identity'</span></span>
- <span data-ttu-id="4da52-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1352">'Razor'</span></span>
- <span data-ttu-id="4da52-1353">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1354">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1355">'Blazor'</span></span>
- <span data-ttu-id="4da52-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1356">'Identity'</span></span>
- <span data-ttu-id="4da52-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1358">'Razor'</span></span>
- <span data-ttu-id="4da52-1359">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1360">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1361">'Blazor'</span></span>
- <span data-ttu-id="4da52-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1362">'Identity'</span></span>
- <span data-ttu-id="4da52-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1364">'Razor'</span></span>
- <span data-ttu-id="4da52-1365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1366">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1367">'Blazor'</span></span>
- <span data-ttu-id="4da52-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1368">'Identity'</span></span>
- <span data-ttu-id="4da52-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1370">'Razor'</span></span>
- <span data-ttu-id="4da52-1371">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1372">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1373">'Blazor'</span></span>
- <span data-ttu-id="4da52-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1374">'Identity'</span></span>
- <span data-ttu-id="4da52-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1376">'Razor'</span></span>
- <span data-ttu-id="4da52-1377">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1378">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1379">'Blazor'</span></span>
- <span data-ttu-id="4da52-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1380">'Identity'</span></span>
- <span data-ttu-id="4da52-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1382">'Razor'</span></span>
- <span data-ttu-id="4da52-1383">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1384">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1385">'Blazor'</span></span>
- <span data-ttu-id="4da52-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1386">'Identity'</span></span>
- <span data-ttu-id="4da52-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1388">'Razor'</span></span>
- <span data-ttu-id="4da52-1389">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1390">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1391">'Blazor'</span></span>
- <span data-ttu-id="4da52-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1392">'Identity'</span></span>
- <span data-ttu-id="4da52-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1394">'Razor'</span></span>
- <span data-ttu-id="4da52-1395">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1396">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1397">'Blazor'</span></span>
- <span data-ttu-id="4da52-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1398">'Identity'</span></span>
- <span data-ttu-id="4da52-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1400">'Razor'</span></span>
- <span data-ttu-id="4da52-1401">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1402">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1403">'Blazor'</span></span>
- <span data-ttu-id="4da52-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1404">'Identity'</span></span>
- <span data-ttu-id="4da52-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1406">'Razor'</span></span>
- <span data-ttu-id="4da52-1407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1408">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1409">'Blazor'</span></span>
- <span data-ttu-id="4da52-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1410">'Identity'</span></span>
- <span data-ttu-id="4da52-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1412">'Razor'</span></span>
- <span data-ttu-id="4da52-1413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1414">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1415">'Blazor'</span></span>
- <span data-ttu-id="4da52-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1416">'Identity'</span></span>
- <span data-ttu-id="4da52-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1418">'Razor'</span></span>
- <span data-ttu-id="4da52-1419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1420">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1421">'Blazor'</span></span>
- <span data-ttu-id="4da52-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1422">'Identity'</span></span>
- <span data-ttu-id="4da52-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1424">'Razor'</span></span>
- <span data-ttu-id="4da52-1425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1426">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1427">'Blazor'</span></span>
- <span data-ttu-id="4da52-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1428">'Identity'</span></span>
- <span data-ttu-id="4da52-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1430">'Razor'</span></span>
- <span data-ttu-id="4da52-1431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1432">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1433">'Blazor'</span></span>
- <span data-ttu-id="4da52-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1434">'Identity'</span></span>
- <span data-ttu-id="4da52-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1436">'Razor'</span></span>
- <span data-ttu-id="4da52-1437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1438">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1439">'Blazor'</span></span>
- <span data-ttu-id="4da52-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1440">'Identity'</span></span>
- <span data-ttu-id="4da52-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1442">'Razor'</span></span>
- <span data-ttu-id="4da52-1443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1444">--------------------------- | | Ön ek yok | `CommandLineKey1=value1`                               |
| İki kısa çizgi ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Eğik çizgi ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="4da52-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="4da52-1445">Aynı komut içinde, bir boşluk kullanan anahtar-değer çiftleri ile bir eşittir işareti kullanan komut satırı bağımsız değişkeni anahtar-değer çiftlerini karıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="4da52-1446">Örnek komutlar:</span><span class="sxs-lookup"><span data-stu-id="4da52-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="4da52-1447">Eşleme Değiştir</span><span class="sxs-lookup"><span data-stu-id="4da52-1447">Switch mappings</span></span>

<span data-ttu-id="4da52-1448">Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="4da52-1449">İle el ile yapılandırma oluştururken <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , yöntemine anahtar değiştirme sözlüğü sağlar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="4da52-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4da52-1450">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük bir komut satırı bağımsız değişkeni tarafından sunulan anahtarla eşleşen bir anahtar için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4da52-1451">Komut satırı anahtarı sözlükte bulunursa, sözlük değeri (anahtar değiştirme), anahtar-değer çiftini uygulamanın yapılandırmasına ayarlamak için geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4da52-1452">Tek tireyle () ön eki eklenmiş herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir `-` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4da52-1453">Anahtar eşlemeleri sözlük anahtarı kuralları:</span><span class="sxs-lookup"><span data-stu-id="4da52-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4da52-1454">Anahtarlar bir tire ( `-` ) veya çift tireyle ( `--` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="4da52-1455">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4da52-1456">Anahtar eşlemeleri sözlüğü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4da52-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="4da52-1457">Aşağıdaki örnekte, iki anahtar eşlemesi oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="4da52-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="4da52-1458">Konak oluşturulduğunda, `AddCommandLine` anahtar eşlemeleri sözlüğüne çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="4da52-1459">Anahtar eşlemeleri kullanan uygulamalar için, ' ye yapılan çağrı `CreateDefaultBuilder` bağımsız değişkenleri geçirmez.</span><span class="sxs-lookup"><span data-stu-id="4da52-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4da52-1460">`CreateDefaultBuilder`Yöntemin `AddCommandLine` çağrısı eşlenmiş anahtarlar içermez ve anahtar eşleme sözlüğünü öğesine geçirmenin bir yolu yoktur `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4da52-1461">Çözüm, bağımsız değişkenleri öğesine geçirmektir, `CreateDefaultBuilder` bunun yerine `ConfigurationBuilder` metodun `AddCommandLine` yönteminin hem bağımsız değişkenleri hem de anahtar eşleme sözlüğünü işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="4da52-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="4da52-1462">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="4da52-1463">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-1463">Key</span></span>       | <span data-ttu-id="4da52-1464">Değer</span><span class="sxs-lookup"><span data-stu-id="4da52-1464">Value</span></span>             |
| ---
<span data-ttu-id="4da52-1465">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1466">'Blazor'</span></span>
- <span data-ttu-id="4da52-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1467">'Identity'</span></span>
- <span data-ttu-id="4da52-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1469">'Razor'</span></span>
- <span data-ttu-id="4da52-1470">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1471">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1472">'Blazor'</span></span>
- <span data-ttu-id="4da52-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1473">'Identity'</span></span>
- <span data-ttu-id="4da52-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1475">'Razor'</span></span>
- <span data-ttu-id="4da52-1476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1477">----- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1478">'Blazor'</span></span>
- <span data-ttu-id="4da52-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1479">'Identity'</span></span>
- <span data-ttu-id="4da52-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1481">'Razor'</span></span>
- <span data-ttu-id="4da52-1482">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1483">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1484">'Blazor'</span></span>
- <span data-ttu-id="4da52-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1485">'Identity'</span></span>
- <span data-ttu-id="4da52-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1487">'Razor'</span></span>
- <span data-ttu-id="4da52-1488">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1489">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1490">'Blazor'</span></span>
- <span data-ttu-id="4da52-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1491">'Identity'</span></span>
- <span data-ttu-id="4da52-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1493">'Razor'</span></span>
- <span data-ttu-id="4da52-1494">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1495">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1496">'Blazor'</span></span>
- <span data-ttu-id="4da52-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1497">'Identity'</span></span>
- <span data-ttu-id="4da52-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1499">'Razor'</span></span>
- <span data-ttu-id="4da52-1500">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1501">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1502">'Blazor'</span></span>
- <span data-ttu-id="4da52-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1503">'Identity'</span></span>
- <span data-ttu-id="4da52-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1505">'Razor'</span></span>
- <span data-ttu-id="4da52-1506">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1507">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1508">'Blazor'</span></span>
- <span data-ttu-id="4da52-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1509">'Identity'</span></span>
- <span data-ttu-id="4da52-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1511">'Razor'</span></span>
- <span data-ttu-id="4da52-1512">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="4da52-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="4da52-1514">Uygulama başlatılırken anahtar eşlenmiş anahtarlar kullanılıyorsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:</span><span class="sxs-lookup"><span data-stu-id="4da52-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="4da52-1515">Önceki komutu çalıştırdıktan sonra, yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="4da52-1516">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-1516">Key</span></span>               | <span data-ttu-id="4da52-1517">Değer</span><span class="sxs-lookup"><span data-stu-id="4da52-1517">Value</span></span>    |
| ---
<span data-ttu-id="4da52-1518">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1519">'Blazor'</span></span>
- <span data-ttu-id="4da52-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1520">'Identity'</span></span>
- <span data-ttu-id="4da52-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1522">'Razor'</span></span>
- <span data-ttu-id="4da52-1523">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1524">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1525">'Blazor'</span></span>
- <span data-ttu-id="4da52-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1526">'Identity'</span></span>
- <span data-ttu-id="4da52-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1528">'Razor'</span></span>
- <span data-ttu-id="4da52-1529">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1530">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1531">'Blazor'</span></span>
- <span data-ttu-id="4da52-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1532">'Identity'</span></span>
- <span data-ttu-id="4da52-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1534">'Razor'</span></span>
- <span data-ttu-id="4da52-1535">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1536">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1537">'Blazor'</span></span>
- <span data-ttu-id="4da52-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1538">'Identity'</span></span>
- <span data-ttu-id="4da52-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1540">'Razor'</span></span>
- <span data-ttu-id="4da52-1541">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1542">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1543">'Blazor'</span></span>
- <span data-ttu-id="4da52-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1544">'Identity'</span></span>
- <span data-ttu-id="4da52-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1546">'Razor'</span></span>
- <span data-ttu-id="4da52-1547">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1548">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1549">'Blazor'</span></span>
- <span data-ttu-id="4da52-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1550">'Identity'</span></span>
- <span data-ttu-id="4da52-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1552">'Razor'</span></span>
- <span data-ttu-id="4da52-1553">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1554">--------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1555">'Blazor'</span></span>
- <span data-ttu-id="4da52-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1556">'Identity'</span></span>
- <span data-ttu-id="4da52-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1558">'Razor'</span></span>
- <span data-ttu-id="4da52-1559">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1560">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1561">'Blazor'</span></span>
- <span data-ttu-id="4da52-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1562">'Identity'</span></span>
- <span data-ttu-id="4da52-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1564">'Razor'</span></span>
- <span data-ttu-id="4da52-1565">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="4da52-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="4da52-1567">Ortam değişkenleri yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="4da52-1568">, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Çalışma zamanında anahtar-değer çiftlerinde bulunan yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="4da52-1569">Ortam değişkenleri yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4da52-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) , Azure portalında ortam değişkenleri yapılandırma sağlayıcısını kullanarak uygulama yapılandırmasını geçersiz kılabileceği ortam değişkenlerini ayarlamaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="4da52-1571">Daha fazla bilgi için bkz. [Azure uygulamaları: Azure portalını kullanarak uygulama yapılandırmasını geçersiz kılma](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="4da52-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4da52-1572">`AddEnvironmentVariables`, `ASPNETCORE_` [Web ana](xref:fundamentals/host/web-host) bilgisayarıyla yeni bir konak Oluşturucu başlatıldığında ve çağrıldığında, [ana bilgisayar yapılandırması](#host-versus-app-configuration) için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="4da52-1573">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4da52-1574">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="4da52-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4da52-1575">Önek olmadan çağırarak ön eki edilmemiş ortam değişkenlerinden uygulama yapılandırması `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="4da52-1576">*AppSettings. JSON* ve appSettings 'ten isteğe bağlı yapılandırma *. { Environment}. JSON* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="4da52-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4da52-1577">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="4da52-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4da52-1578">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-1578">Command-line arguments.</span></span>

<span data-ttu-id="4da52-1579">Ortam değişkenleri yapılandırma sağlayıcısı, Kullanıcı gizli dizileri ve *appSettings* dosyalarından yapılandırma kurulduktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="4da52-1580">Bu konumda sağlayıcıyı çağırmak, çalışma zamanında ortam değişkenlerinin Kullanıcı parolaları ve *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmak için okumasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="4da52-1581">Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamasının uygulamasındaki ek sağlayıcılarını çağırın `ConfigureAppConfiguration` ve `AddEnvironmentVariables` önekiyle birlikte çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="4da52-1582">`AddEnvironmentVariables`Diğer sağlayıcılardan gelen değerleri geçersiz kılmak için verilen öneke sahip ortam değişkenlerine izin vermek için son ' a çağırın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="4da52-1583">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="4da52-1583">**Example**</span></span>

<span data-ttu-id="4da52-1584">Örnek uygulama, `CreateDefaultBuilder` ' a çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="4da52-1585">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1585">Run the sample app.</span></span> <span data-ttu-id="4da52-1586">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4da52-1587">Çıktının ortam değişkeni için anahtar-değer çiftini içerdiğini gözlemleyin `ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="4da52-1588">Değer, genellikle yerel olarak çalışırken uygulamanın çalıştığı ortamı yansıtır `Development` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="4da52-1589">Uygulama kısaltması tarafından oluşturulan ortam değişkenlerinin listesini tutmak için, uygulama ortam değişkenlerini filtreler.</span><span class="sxs-lookup"><span data-stu-id="4da52-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="4da52-1590">Örnek uygulamanın *Pages/Index. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="4da52-1591">Uygulamanın kullanabildiği tüm ortam değişkenlerini göstermek için, `FilteredConfiguration` *Pages/Index. cshtml. cs* ' deki ' i şu şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4da52-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="4da52-1592">Ön Ekler</span><span class="sxs-lookup"><span data-stu-id="4da52-1592">Prefixes</span></span>

<span data-ttu-id="4da52-1593">Uygulamanın yapılandırmasına yüklenen ortam değişkenleri, yöntemine bir ön ek sağlanırken filtrelenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="4da52-1594">Örneğin, önekte ortam değişkenlerini filtrelemek için `CUSTOM_` , yapılandırma sağlayıcısına öneki sağlayın:</span><span class="sxs-lookup"><span data-stu-id="4da52-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="4da52-1595">Yapılandırma anahtar-değer çiftleri oluşturulduğunda ön ek çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="4da52-1596">Konak Oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="4da52-1597">Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4da52-1598">**Bağlantı dizesi önekleri**</span><span class="sxs-lookup"><span data-stu-id="4da52-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="4da52-1599">Yapılandırma API 'SI, uygulama ortamı için Azure bağlantı dizelerini yapılandırma ile ilgili dört bağlantı dizesi ortam değişkeni için özel işlem kuralları içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4da52-1600">Üzerinde bir önek sağlanmazsa, tabloda gösterilen öneklere sahip ortam değişkenleri uygulamaya yüklenir `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4da52-1601">Bağlantı dizesi öneki</span><span class="sxs-lookup"><span data-stu-id="4da52-1601">Connection string prefix</span></span> | <span data-ttu-id="4da52-1602">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="4da52-1602">Provider</span></span> |
| ---
<span data-ttu-id="4da52-1603">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1604">'Blazor'</span></span>
- <span data-ttu-id="4da52-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1605">'Identity'</span></span>
- <span data-ttu-id="4da52-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1607">'Razor'</span></span>
- <span data-ttu-id="4da52-1608">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1609">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1610">'Blazor'</span></span>
- <span data-ttu-id="4da52-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1611">'Identity'</span></span>
- <span data-ttu-id="4da52-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1613">'Razor'</span></span>
- <span data-ttu-id="4da52-1614">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1615">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1616">'Blazor'</span></span>
- <span data-ttu-id="4da52-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1617">'Identity'</span></span>
- <span data-ttu-id="4da52-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1619">'Razor'</span></span>
- <span data-ttu-id="4da52-1620">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1621">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1622">'Blazor'</span></span>
- <span data-ttu-id="4da52-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1623">'Identity'</span></span>
- <span data-ttu-id="4da52-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1625">'Razor'</span></span>
- <span data-ttu-id="4da52-1626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1627">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1628">'Blazor'</span></span>
- <span data-ttu-id="4da52-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1629">'Identity'</span></span>
- <span data-ttu-id="4da52-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1631">'Razor'</span></span>
- <span data-ttu-id="4da52-1632">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1633">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1634">'Blazor'</span></span>
- <span data-ttu-id="4da52-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1635">'Identity'</span></span>
- <span data-ttu-id="4da52-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1637">'Razor'</span></span>
- <span data-ttu-id="4da52-1638">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1639">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1640">'Blazor'</span></span>
- <span data-ttu-id="4da52-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1641">'Identity'</span></span>
- <span data-ttu-id="4da52-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1643">'Razor'</span></span>
- <span data-ttu-id="4da52-1644">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1645">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1646">'Blazor'</span></span>
- <span data-ttu-id="4da52-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1647">'Identity'</span></span>
- <span data-ttu-id="4da52-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1649">'Razor'</span></span>
- <span data-ttu-id="4da52-1650">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1651">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1652">'Blazor'</span></span>
- <span data-ttu-id="4da52-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1653">'Identity'</span></span>
- <span data-ttu-id="4da52-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1655">'Razor'</span></span>
- <span data-ttu-id="4da52-1656">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1657">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1658">'Blazor'</span></span>
- <span data-ttu-id="4da52-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1659">'Identity'</span></span>
- <span data-ttu-id="4da52-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1661">'Razor'</span></span>
- <span data-ttu-id="4da52-1662">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1663">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1664">'Blazor'</span></span>
- <span data-ttu-id="4da52-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1665">'Identity'</span></span>
- <span data-ttu-id="4da52-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1667">'Razor'</span></span>
- <span data-ttu-id="4da52-1668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1669">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1670">'Blazor'</span></span>
- <span data-ttu-id="4da52-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1671">'Identity'</span></span>
- <span data-ttu-id="4da52-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1673">'Razor'</span></span>
- <span data-ttu-id="4da52-1674">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1675">---- | | `CUSTOMCONNSTR_` | Özel sağlayıcı | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="4da52-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="4da52-1676">Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önekle yapılandırmaya yüklendiğinde:</span><span class="sxs-lookup"><span data-stu-id="4da52-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4da52-1677">Yapılandırma anahtarı, ortam değişkeni öneki kaldırılarak ve bir yapılandırma anahtarı bölümü () eklenerek oluşturulur `ConnectionStrings` .</span><span class="sxs-lookup"><span data-stu-id="4da52-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4da52-1678">Veritabanı bağlantı sağlayıcısını temsil eden yeni bir yapılandırma anahtar-değer çifti oluşturulur (için `CUSTOMCONNSTR_` , belirtilen sağlayıcı olmayan).</span><span class="sxs-lookup"><span data-stu-id="4da52-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4da52-1679">Ortam değişkeni anahtarı</span><span class="sxs-lookup"><span data-stu-id="4da52-1679">Environment variable key</span></span> | <span data-ttu-id="4da52-1680">Dönüştürülen yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="4da52-1680">Converted configuration key</span></span> | <span data-ttu-id="4da52-1681">Sağlayıcı yapılandırma girişi</span><span class="sxs-lookup"><span data-stu-id="4da52-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="4da52-1682">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1683">'Blazor'</span></span>
- <span data-ttu-id="4da52-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1684">'Identity'</span></span>
- <span data-ttu-id="4da52-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1686">'Razor'</span></span>
- <span data-ttu-id="4da52-1687">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1688">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1689">'Blazor'</span></span>
- <span data-ttu-id="4da52-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1690">'Identity'</span></span>
- <span data-ttu-id="4da52-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1692">'Razor'</span></span>
- <span data-ttu-id="4da52-1693">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1694">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1695">'Blazor'</span></span>
- <span data-ttu-id="4da52-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1696">'Identity'</span></span>
- <span data-ttu-id="4da52-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1698">'Razor'</span></span>
- <span data-ttu-id="4da52-1699">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1700">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1701">'Blazor'</span></span>
- <span data-ttu-id="4da52-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1702">'Identity'</span></span>
- <span data-ttu-id="4da52-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1704">'Razor'</span></span>
- <span data-ttu-id="4da52-1705">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1706">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1707">'Blazor'</span></span>
- <span data-ttu-id="4da52-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1708">'Identity'</span></span>
- <span data-ttu-id="4da52-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1710">'Razor'</span></span>
- <span data-ttu-id="4da52-1711">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1712">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1713">'Blazor'</span></span>
- <span data-ttu-id="4da52-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1714">'Identity'</span></span>
- <span data-ttu-id="4da52-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1716">'Razor'</span></span>
- <span data-ttu-id="4da52-1717">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1718">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1719">'Blazor'</span></span>
- <span data-ttu-id="4da52-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1720">'Identity'</span></span>
- <span data-ttu-id="4da52-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1722">'Razor'</span></span>
- <span data-ttu-id="4da52-1723">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1724">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1725">'Blazor'</span></span>
- <span data-ttu-id="4da52-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1726">'Identity'</span></span>
- <span data-ttu-id="4da52-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1728">'Razor'</span></span>
- <span data-ttu-id="4da52-1729">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1730">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1731">'Blazor'</span></span>
- <span data-ttu-id="4da52-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1732">'Identity'</span></span>
- <span data-ttu-id="4da52-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1734">'Razor'</span></span>
- <span data-ttu-id="4da52-1735">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1736">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1737">'Blazor'</span></span>
- <span data-ttu-id="4da52-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1738">'Identity'</span></span>
- <span data-ttu-id="4da52-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1740">'Razor'</span></span>
- <span data-ttu-id="4da52-1741">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1742">------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1743">'Blazor'</span></span>
- <span data-ttu-id="4da52-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1744">'Identity'</span></span>
- <span data-ttu-id="4da52-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1746">'Razor'</span></span>
- <span data-ttu-id="4da52-1747">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1748">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1749">'Blazor'</span></span>
- <span data-ttu-id="4da52-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1750">'Identity'</span></span>
- <span data-ttu-id="4da52-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1752">'Razor'</span></span>
- <span data-ttu-id="4da52-1753">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1754">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1755">'Blazor'</span></span>
- <span data-ttu-id="4da52-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1756">'Identity'</span></span>
- <span data-ttu-id="4da52-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1758">'Razor'</span></span>
- <span data-ttu-id="4da52-1759">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1760">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1761">'Blazor'</span></span>
- <span data-ttu-id="4da52-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1762">'Identity'</span></span>
- <span data-ttu-id="4da52-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1764">'Razor'</span></span>
- <span data-ttu-id="4da52-1765">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1766">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1767">'Blazor'</span></span>
- <span data-ttu-id="4da52-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1768">'Identity'</span></span>
- <span data-ttu-id="4da52-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1770">'Razor'</span></span>
- <span data-ttu-id="4da52-1771">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1772">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1773">'Blazor'</span></span>
- <span data-ttu-id="4da52-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1774">'Identity'</span></span>
- <span data-ttu-id="4da52-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1776">'Razor'</span></span>
- <span data-ttu-id="4da52-1777">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1778">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1779">'Blazor'</span></span>
- <span data-ttu-id="4da52-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1780">'Identity'</span></span>
- <span data-ttu-id="4da52-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1782">'Razor'</span></span>
- <span data-ttu-id="4da52-1783">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1784">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1785">'Blazor'</span></span>
- <span data-ttu-id="4da52-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1786">'Identity'</span></span>
- <span data-ttu-id="4da52-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1788">'Razor'</span></span>
- <span data-ttu-id="4da52-1789">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1790">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1791">'Blazor'</span></span>
- <span data-ttu-id="4da52-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1792">'Identity'</span></span>
- <span data-ttu-id="4da52-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1794">'Razor'</span></span>
- <span data-ttu-id="4da52-1795">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1796">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1797">'Blazor'</span></span>
- <span data-ttu-id="4da52-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1798">'Identity'</span></span>
- <span data-ttu-id="4da52-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1800">'Razor'</span></span>
- <span data-ttu-id="4da52-1801">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1802">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1803">'Blazor'</span></span>
- <span data-ttu-id="4da52-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1804">'Identity'</span></span>
- <span data-ttu-id="4da52-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1806">'Razor'</span></span>
- <span data-ttu-id="4da52-1807">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-1808">-------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1809">'Blazor'</span></span>
- <span data-ttu-id="4da52-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1810">'Identity'</span></span>
- <span data-ttu-id="4da52-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1812">'Razor'</span></span>
- <span data-ttu-id="4da52-1813">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1814">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1815">'Blazor'</span></span>
- <span data-ttu-id="4da52-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1816">'Identity'</span></span>
- <span data-ttu-id="4da52-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1818">'Razor'</span></span>
- <span data-ttu-id="4da52-1819">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1820">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1821">'Blazor'</span></span>
- <span data-ttu-id="4da52-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1822">'Identity'</span></span>
- <span data-ttu-id="4da52-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1824">'Razor'</span></span>
- <span data-ttu-id="4da52-1825">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1826">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1827">'Blazor'</span></span>
- <span data-ttu-id="4da52-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1828">'Identity'</span></span>
- <span data-ttu-id="4da52-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1830">'Razor'</span></span>
- <span data-ttu-id="4da52-1831">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1832">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1833">'Blazor'</span></span>
- <span data-ttu-id="4da52-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1834">'Identity'</span></span>
- <span data-ttu-id="4da52-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1836">'Razor'</span></span>
- <span data-ttu-id="4da52-1837">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1838">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1839">'Blazor'</span></span>
- <span data-ttu-id="4da52-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1840">'Identity'</span></span>
- <span data-ttu-id="4da52-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1842">'Razor'</span></span>
- <span data-ttu-id="4da52-1843">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1844">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1845">'Blazor'</span></span>
- <span data-ttu-id="4da52-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1846">'Identity'</span></span>
- <span data-ttu-id="4da52-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1848">'Razor'</span></span>
- <span data-ttu-id="4da52-1849">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1850">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1851">'Blazor'</span></span>
- <span data-ttu-id="4da52-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1852">'Identity'</span></span>
- <span data-ttu-id="4da52-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1854">'Razor'</span></span>
- <span data-ttu-id="4da52-1855">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1856">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1857">'Blazor'</span></span>
- <span data-ttu-id="4da52-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1858">'Identity'</span></span>
- <span data-ttu-id="4da52-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1860">'Razor'</span></span>
- <span data-ttu-id="4da52-1861">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1862">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1863">'Blazor'</span></span>
- <span data-ttu-id="4da52-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1864">'Identity'</span></span>
- <span data-ttu-id="4da52-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1866">'Razor'</span></span>
- <span data-ttu-id="4da52-1867">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1868">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1869">'Blazor'</span></span>
- <span data-ttu-id="4da52-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1870">'Identity'</span></span>
- <span data-ttu-id="4da52-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1872">'Razor'</span></span>
- <span data-ttu-id="4da52-1873">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1874">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1875">'Blazor'</span></span>
- <span data-ttu-id="4da52-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1876">'Identity'</span></span>
- <span data-ttu-id="4da52-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1878">'Razor'</span></span>
- <span data-ttu-id="4da52-1879">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1880">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1881">'Blazor'</span></span>
- <span data-ttu-id="4da52-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1882">'Identity'</span></span>
- <span data-ttu-id="4da52-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1884">'Razor'</span></span>
- <span data-ttu-id="4da52-1885">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1886">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1887">'Blazor'</span></span>
- <span data-ttu-id="4da52-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1888">'Identity'</span></span>
- <span data-ttu-id="4da52-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1890">'Razor'</span></span>
- <span data-ttu-id="4da52-1891">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1892">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1893">'Blazor'</span></span>
- <span data-ttu-id="4da52-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1894">'Identity'</span></span>
- <span data-ttu-id="4da52-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1896">'Razor'</span></span>
- <span data-ttu-id="4da52-1897">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1898">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1899">'Blazor'</span></span>
- <span data-ttu-id="4da52-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1900">'Identity'</span></span>
- <span data-ttu-id="4da52-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1902">'Razor'</span></span>
- <span data-ttu-id="4da52-1903">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1904">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1905">'Blazor'</span></span>
- <span data-ttu-id="4da52-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1906">'Identity'</span></span>
- <span data-ttu-id="4da52-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1908">'Razor'</span></span>
- <span data-ttu-id="4da52-1909">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1910">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1911">'Blazor'</span></span>
- <span data-ttu-id="4da52-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1912">'Identity'</span></span>
- <span data-ttu-id="4da52-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1914">'Razor'</span></span>
- <span data-ttu-id="4da52-1915">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1916">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1917">'Blazor'</span></span>
- <span data-ttu-id="4da52-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1918">'Identity'</span></span>
- <span data-ttu-id="4da52-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1920">'Razor'</span></span>
- <span data-ttu-id="4da52-1921">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1922">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1923">'Blazor'</span></span>
- <span data-ttu-id="4da52-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1924">'Identity'</span></span>
- <span data-ttu-id="4da52-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1926">'Razor'</span></span>
- <span data-ttu-id="4da52-1927">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1928">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1929">'Blazor'</span></span>
- <span data-ttu-id="4da52-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1930">'Identity'</span></span>
- <span data-ttu-id="4da52-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1932">'Razor'</span></span>
- <span data-ttu-id="4da52-1933">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1934">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1935">'Blazor'</span></span>
- <span data-ttu-id="4da52-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1936">'Identity'</span></span>
- <span data-ttu-id="4da52-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1938">'Razor'</span></span>
- <span data-ttu-id="4da52-1939">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1940">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1941">'Blazor'</span></span>
- <span data-ttu-id="4da52-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1942">'Identity'</span></span>
- <span data-ttu-id="4da52-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1944">'Razor'</span></span>
- <span data-ttu-id="4da52-1945">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1946">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1947">'Blazor'</span></span>
- <span data-ttu-id="4da52-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1948">'Identity'</span></span>
- <span data-ttu-id="4da52-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1950">'Razor'</span></span>
- <span data-ttu-id="4da52-1951">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1952">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1953">'Blazor'</span></span>
- <span data-ttu-id="4da52-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1954">'Identity'</span></span>
- <span data-ttu-id="4da52-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1956">'Razor'</span></span>
- <span data-ttu-id="4da52-1957">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1958">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1959">'Blazor'</span></span>
- <span data-ttu-id="4da52-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1960">'Identity'</span></span>
- <span data-ttu-id="4da52-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1962">'Razor'</span></span>
- <span data-ttu-id="4da52-1963">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1964">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1965">'Blazor'</span></span>
- <span data-ttu-id="4da52-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1966">'Identity'</span></span>
- <span data-ttu-id="4da52-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1968">'Razor'</span></span>
- <span data-ttu-id="4da52-1969">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1970">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1971">'Blazor'</span></span>
- <span data-ttu-id="4da52-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1972">'Identity'</span></span>
- <span data-ttu-id="4da52-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1974">'Razor'</span></span>
- <span data-ttu-id="4da52-1975">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1976">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1977">'Blazor'</span></span>
- <span data-ttu-id="4da52-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1978">'Identity'</span></span>
- <span data-ttu-id="4da52-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1980">'Razor'</span></span>
- <span data-ttu-id="4da52-1981">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1982">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1983">'Blazor'</span></span>
- <span data-ttu-id="4da52-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1984">'Identity'</span></span>
- <span data-ttu-id="4da52-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1986">'Razor'</span></span>
- <span data-ttu-id="4da52-1987">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1988">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1989">'Blazor'</span></span>
- <span data-ttu-id="4da52-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1990">'Identity'</span></span>
- <span data-ttu-id="4da52-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1992">'Razor'</span></span>
- <span data-ttu-id="4da52-1993">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-1994">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1995">'Blazor'</span></span>
- <span data-ttu-id="4da52-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-1996">'Identity'</span></span>
- <span data-ttu-id="4da52-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-1998">'Razor'</span></span>
- <span data-ttu-id="4da52-1999">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2000">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2001">'Blazor'</span></span>
- <span data-ttu-id="4da52-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2002">'Identity'</span></span>
- <span data-ttu-id="4da52-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2004">'Razor'</span></span>
- <span data-ttu-id="4da52-2005">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2006">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2007">'Blazor'</span></span>
- <span data-ttu-id="4da52-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2008">'Identity'</span></span>
- <span data-ttu-id="4da52-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2010">'Razor'</span></span>
- <span data-ttu-id="4da52-2011">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2012">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2013">'Blazor'</span></span>
- <span data-ttu-id="4da52-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2014">'Identity'</span></span>
- <span data-ttu-id="4da52-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2016">'Razor'</span></span>
- <span data-ttu-id="4da52-2017">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2018">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2019">'Blazor'</span></span>
- <span data-ttu-id="4da52-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2020">'Identity'</span></span>
- <span data-ttu-id="4da52-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2022">'Razor'</span></span>
- <span data-ttu-id="4da52-2023">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2024">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2025">'Blazor'</span></span>
- <span data-ttu-id="4da52-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2026">'Identity'</span></span>
- <span data-ttu-id="4da52-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2028">'Razor'</span></span>
- <span data-ttu-id="4da52-2029">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Yapılandırma girişi oluşturulmamış.</span><span class="sxs-lookup"><span data-stu-id="4da52-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="4da52-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4da52-2032">Değer: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4da52-2033">Değer: `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4da52-2034">Deeri`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="4da52-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="4da52-2035">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="4da52-2035">**Example**</span></span>

<span data-ttu-id="4da52-2036">Sunucuda özel bir bağlantı dizesi ortam değişkeni oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="4da52-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="4da52-2037">Ada`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="4da52-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="4da52-2038">Değer:`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="4da52-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="4da52-2039">`IConfiguration`Eklenmiş ve adlı bir alana atanmışsa `_config` , şu değeri okuyun:</span><span class="sxs-lookup"><span data-stu-id="4da52-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="4da52-2040">Dosya yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2040">File Configuration Provider</span></span>

<span data-ttu-id="4da52-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>, dosya sisteminden yapılandırma yüklemeye yönelik temel sınıftır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4da52-2042">Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine ayrılmıştır:</span><span class="sxs-lookup"><span data-stu-id="4da52-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="4da52-2043">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4da52-2044">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="4da52-2045">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4da52-2046">INı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2046">INI Configuration Provider</span></span>

<span data-ttu-id="4da52-2047">, <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Çalışma ZAMANıNDA INI dosyası anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4da52-2048">INI dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4da52-2049">İki nokta üst üste, ıNı dosya yapılandırmasındaki bir bölüm sınırlayıcısı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="4da52-2050">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="4da52-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="4da52-2051">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="4da52-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="4da52-2052">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4da52-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4da52-2054">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4da52-2055">Bir ıNı yapılandırma dosyasına genel bir örnek:</span><span class="sxs-lookup"><span data-stu-id="4da52-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="4da52-2056">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4da52-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2057">section0:key0</span></span>
* <span data-ttu-id="4da52-2058">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2058">section0:key1</span></span>
* <span data-ttu-id="4da52-2059">Section1: alt bölüm: anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-2059">section1:subsection:key</span></span>
* <span data-ttu-id="4da52-2060">section2: subsection0: anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="4da52-2061">section2: subsection1: anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="4da52-2062">JSON yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="4da52-2063">, <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Çalışma zamanı SıRASıNDA JSON dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="4da52-2064">JSON dosya yapılandırmasını etkinleştirmek için <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4da52-2065">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="4da52-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="4da52-2066">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="4da52-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="4da52-2067">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4da52-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4da52-2069">`AddJsonFile`, ile yeni bir ana bilgisayar Oluşturucu başlatıldığında otomatik olarak iki kez çağrılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4da52-2070">Yöntemi, yapılandırmayı şuradan yüklemek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="4da52-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="4da52-2071">*appSettings. JSON*: Bu dosya ilk kez okundu.</span><span class="sxs-lookup"><span data-stu-id="4da52-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="4da52-2072">Dosyanın ortam sürümü, *appSettings. JSON* dosyası tarafından belirtilen değerleri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="4da52-2073">*appSettings. {Environment}. JSON*: dosyanın ortam sürümü [ıhostingenvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)temel alınarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="4da52-2074">Daha fazla bilgi için [varsayılan yapılandırma](#default-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4da52-2075">`CreateDefaultBuilder`Ayrıca yüklenir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4da52-2076">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-2076">Environment variables.</span></span>
* <span data-ttu-id="4da52-2077">Geliştirme ortamında [Kullanıcı gizli dizileri (gizli yönetici)](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="4da52-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4da52-2078">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="4da52-2078">Command-line arguments.</span></span>

<span data-ttu-id="4da52-2079">JSON yapılandırma sağlayıcısı önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4da52-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="4da52-2080">Bu nedenle, Kullanıcı gizli dizileri, ortam değişkenleri ve komut satırı bağımsız değişkenleri, *appSettings* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="4da52-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="4da52-2081">`ConfigureAppConfiguration`Ana bilgisayarı, *appSettings. JSON* ve appSettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek üzere oluştururken çağırın *. { Environment}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4da52-2082">**Örnek**</span><span class="sxs-lookup"><span data-stu-id="4da52-2082">**Example**</span></span>

<span data-ttu-id="4da52-2083">Örnek uygulama, `CreateDefaultBuilder` için iki çağrı içeren konağı oluşturmak için statik kolaylık yönteminden yararlanır `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="4da52-2084">İlk çağrı, `AddJsonFile` *appSettings. JSON*' dan yapılandırmayı yükler:</span><span class="sxs-lookup"><span data-stu-id="4da52-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="4da52-2085">`AddJsonFile`Yapılandırma appSettings 'ten yüklenen ikinci çağrı *. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="4da52-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="4da52-2086">*AppSettings için. Geliştirme. JSON* örnek uygulamada aşağıdaki dosya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="4da52-2087">Örnek uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2087">Run the sample app.</span></span> <span data-ttu-id="4da52-2088">Üzerinde uygulama için bir tarayıcı açın `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4da52-2089">Çıktı, uygulamanın ortamına göre yapılandırma için anahtar-değer çiftleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="4da52-2090">Anahtarın günlük düzeyi, `Logging:LogLevel:Default` `Debug` uygulamanın geliştirme ortamında çalıştırıldığı zaman.</span><span class="sxs-lookup"><span data-stu-id="4da52-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="4da52-2091">Örnek uygulamayı üretim ortamında yeniden çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="4da52-2092">*Properties/launchSettings. JSON* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="4da52-2093">`ConfigurationSample`Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini olarak değiştirin `Production` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="4da52-2094">Dosyayı kaydedin ve uygulamayı `dotnet run` bir komut kabuğu içinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="4da52-2095">AppSettings içindeki ayarlar *. Development. JSON* artık *appSettings. JSON*içindeki ayarları geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="4da52-2096">Anahtarın günlük düzeyi `Logging:LogLevel:Default` `Warning` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="4da52-2097">XML yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2097">XML Configuration Provider</span></span>

<span data-ttu-id="4da52-2098">, <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Çalışma ZAMANıNDA XML dosya anahtar-değer çiftlerinden yapılandırmayı yükler.</span><span class="sxs-lookup"><span data-stu-id="4da52-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4da52-2099">XML dosya yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4da52-2100">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="4da52-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="4da52-2101">Dosyanın isteğe bağlı olup olmadığı.</span><span class="sxs-lookup"><span data-stu-id="4da52-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="4da52-2102">Dosya değişirse yapılandırmanın yeniden yüklenip yüklenmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4da52-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider>Dosyaya erişmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4da52-2104">Yapılandırma anahtar-değer çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="4da52-2105">Dosyada bir belge türü tanımı (DTD) veya ad alanı belirtmeyin.</span><span class="sxs-lookup"><span data-stu-id="4da52-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="4da52-2106">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4da52-2107">XML yapılandırma dosyaları, yinelenen bölümler için farklı öğe adları kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="4da52-2108">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4da52-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2109">section0:key0</span></span>
* <span data-ttu-id="4da52-2110">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2110">section0:key1</span></span>
* <span data-ttu-id="4da52-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2111">section1:key0</span></span>
* <span data-ttu-id="4da52-2112">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2112">section1:key1</span></span>

<span data-ttu-id="4da52-2113">Aynı öğe adını kullanan yinelenen öğeler, `name` özniteliği öğeleri ayırt etmek için kullanılacaksa çalışır:</span><span class="sxs-lookup"><span data-stu-id="4da52-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="4da52-2114">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4da52-2115">Bölüm: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="4da52-2116">Bölüm: section0: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="4da52-2117">Bölüm: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="4da52-2118">Bölüm: Section1: Key: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2118">section:section1:key:key1</span></span>

<span data-ttu-id="4da52-2119">Öznitelikler, değerler sağlamak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4da52-2120">Önceki yapılandırma dosyası aşağıdaki anahtarları ile yükler `value` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4da52-2121">anahtar: öznitelik</span><span class="sxs-lookup"><span data-stu-id="4da52-2121">key:attribute</span></span>
* <span data-ttu-id="4da52-2122">Section: Key: özniteliği</span><span class="sxs-lookup"><span data-stu-id="4da52-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4da52-2123">Dosya başına anahtar yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="4da52-2124">, <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Dizin dosyalarını yapılandırma anahtar-değer çiftleri olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4da52-2125">Anahtar, dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2125">The key is the file name.</span></span> <span data-ttu-id="4da52-2126">Değer, dosyanın içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2126">The value contains the file's contents.</span></span> <span data-ttu-id="4da52-2127">Dosya başına anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4da52-2128">Dosya başına anahtar yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4da52-2129">`directoryPath`Dosyaların mutlak bir yol olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4da52-2130">Aşırı yüklemeler belirtmeye izin ver:</span><span class="sxs-lookup"><span data-stu-id="4da52-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="4da52-2131">`Action<KeyPerFileConfigurationSource>`Kaynağı yapılandıran bir temsilci.</span><span class="sxs-lookup"><span data-stu-id="4da52-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4da52-2132">Dizinin isteğe bağlı olup olmadığını ve dizinin yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4da52-2133">Çift alt çizgi ( `__` ), dosya adlarında yapılandırma anahtarı sınırlayıcısı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4da52-2134">Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını üretir `Logging:LogLevel:System` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4da52-2135">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="4da52-2136">Bellek yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="4da52-2137">, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Yapılandırma anahtar-değer çiftleri olarak bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4da52-2138">Bellek içi koleksiyon yapılandırmasını etkinleştirmek için, <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örneğinde genişletme yöntemini çağırın <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4da52-2139">Yapılandırma sağlayıcısı bir ile başlatılabilir `IEnumerable<KeyValuePair<String,String>>` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="4da52-2140">`ConfigureAppConfiguration`Uygulamanın yapılandırmasını belirtmek için Konağı oluştururken çağırın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="4da52-2141">Aşağıdaki örnekte bir yapılandırma sözlüğü oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="4da52-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="4da52-2142">Sözlük, yapılandırmayı sağlamak için çağrısı ile kullanılır `AddInMemoryCollection` :</span><span class="sxs-lookup"><span data-stu-id="4da52-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="4da52-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="4da52-2143">GetValue</span></span>

<span data-ttu-id="4da52-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirli bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen koleksiyon olmayan türe dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="4da52-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="4da52-2145">Aşırı yükleme varsayılan bir değeri kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4da52-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="4da52-2146">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="4da52-2146">The following example:</span></span>

* <span data-ttu-id="4da52-2147">Yapılandırmadaki dize değerini anahtarla ayıklar `NumberKey` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="4da52-2148">`NumberKey`Yapılandırma anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="4da52-2149">Değeri olarak bir `int` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="4da52-2150">Değeri, `NumberConfig` sayfanın kullanımı için özelliği içinde depolar.</span><span class="sxs-lookup"><span data-stu-id="4da52-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4da52-2151">GetSection, GetChildren ve Exists</span><span class="sxs-lookup"><span data-stu-id="4da52-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4da52-2152">İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="4da52-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="4da52-2153">İki bölüm arasında dört anahtar bulunur ve bunlardan biri alt bölümleri çifti içerir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="4da52-2154">Dosya yapılandırmaya okunduğu zaman yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="4da52-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="4da52-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2155">section0:key0</span></span>
* <span data-ttu-id="4da52-2156">section0: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2156">section0:key1</span></span>
* <span data-ttu-id="4da52-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2157">section1:key0</span></span>
* <span data-ttu-id="4da52-2158">Section1: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2158">section1:key1</span></span>
* <span data-ttu-id="4da52-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="4da52-2160">section2: subsection0: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="4da52-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="4da52-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="4da52-2162">section2: subsection1: KEY1</span><span class="sxs-lookup"><span data-stu-id="4da52-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="4da52-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="4da52-2163">GetSection</span></span>

<span data-ttu-id="4da52-2164">[Iconation. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) , belirtilen alt bölüm anahtarıyla bir yapılandırma alt bölümü ayıklar.</span><span class="sxs-lookup"><span data-stu-id="4da52-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4da52-2165"><xref:Microsoft.Extensions.Configuration.IConfigurationSection>Yalnızca içindeki anahtar-değer çiftlerini içeren bir öğesini döndürmek için `section1` , `GetSection` bölüm adını çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="4da52-2166">`configSection`Bir değere sahip değil, yalnızca bir anahtar ve yol yoktur.</span><span class="sxs-lookup"><span data-stu-id="4da52-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="4da52-2167">Benzer şekilde, içindeki anahtarların değerlerini almak için `section2:subsection0` , `GetSection` Bölüm yolunu çağırın ve sağlayın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="4da52-2168">`GetSection`hiçbir süre geri döndürmez `null` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4da52-2169">Eşleşen bir bölüm bulunamazsa boş `IConfigurationSection` değer döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4da52-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4da52-2170">`GetSection`Eşleşen bir bölüm döndürdüğünde, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> doldurulmuyor.</span><span class="sxs-lookup"><span data-stu-id="4da52-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4da52-2171">Bir <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> ve <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> , bölüm varsa döndürülür.</span><span class="sxs-lookup"><span data-stu-id="4da52-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="4da52-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="4da52-2172">GetChildren</span></span>

<span data-ttu-id="4da52-2173">Üzerinde bir [Iconation. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağrısı `section2` `IEnumerable<IConfigurationSection>` , şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="4da52-2174">Var</span><span class="sxs-lookup"><span data-stu-id="4da52-2174">Exists</span></span>

<span data-ttu-id="4da52-2175">Bir yapılandırma bölümünün mevcut olup olmadığını anlamak için [Configurationextensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="4da52-2176">Örnek veriler verildiğinde, `sectionExists` `false` yapılandırma verilerinde bir bölüm olmadığı için `section2:subsection2` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="4da52-2177">Bir nesne grafiğine bağlama</span><span class="sxs-lookup"><span data-stu-id="4da52-2177">Bind to an object graph</span></span>

<span data-ttu-id="4da52-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>, bir POCO nesne grafiğinin tamamına bağlama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="4da52-2179">Basit bir nesne bağlamakla birlikte yalnızca genel okuma/yazma özellikleri bağlanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="4da52-2180">Örnek, `TvShow` nesne grafı içeren ve sınıfları olan bir model içerir `Metadata` `Actors` (*modeller/tvshow. cs*):</span><span class="sxs-lookup"><span data-stu-id="4da52-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="4da52-2181">Örnek uygulama, yapılandırma verilerini içeren bir *tvshow. xml* dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="4da52-2182">Yapılandırma `TvShow` , yöntemiyle nesne grafiğinin tamamına bağlanır `Bind` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="4da52-2183">Bağlantılı örnek, işleme için bir özelliğe atandı:</span><span class="sxs-lookup"><span data-stu-id="4da52-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="4da52-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)belirtilen türü bağlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="4da52-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="4da52-2185">`Get<T>`, kullanmaktan daha uygundur `Bind` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="4da52-2186">Aşağıdaki kod, `Get<T>` önceki örnekle birlikte nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="4da52-2187">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="4da52-2187">Bind an array to a class</span></span>

<span data-ttu-id="4da52-2188">*Örnek uygulama, bu bölümde açıklanan kavramları gösterir.*</span><span class="sxs-lookup"><span data-stu-id="4da52-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="4da52-2189">, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Yapılandırma anahtarlarındaki dizi dizinlerini kullanarak nesnelere dizileri bağlamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="4da52-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4da52-2190">Bir sayısal anahtar segmenti (,,) sunan herhangi bir dizi biçimi, `:0:` `:1:` &hellip; `:{n}:` bir poco sınıfı dizisine dizi bağlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="4da52-2191">Bağlama, kural tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2191">Binding is provided by convention.</span></span> <span data-ttu-id="4da52-2192">Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcıları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="4da52-2193">**Bellek içi dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="4da52-2193">**In-memory array processing**</span></span>

<span data-ttu-id="4da52-2194">Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="4da52-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="4da52-2195">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-2195">Key</span></span>             | <span data-ttu-id="4da52-2196">Değer</span><span class="sxs-lookup"><span data-stu-id="4da52-2196">Value</span></span>  |
| :---
<span data-ttu-id="4da52-2197">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2198">'Blazor'</span></span>
- <span data-ttu-id="4da52-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2199">'Identity'</span></span>
- <span data-ttu-id="4da52-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2201">'Razor'</span></span>
- <span data-ttu-id="4da52-2202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2203">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2204">'Blazor'</span></span>
- <span data-ttu-id="4da52-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2205">'Identity'</span></span>
- <span data-ttu-id="4da52-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2207">'Razor'</span></span>
- <span data-ttu-id="4da52-2208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2209">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2210">'Blazor'</span></span>
- <span data-ttu-id="4da52-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2211">'Identity'</span></span>
- <span data-ttu-id="4da52-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2213">'Razor'</span></span>
- <span data-ttu-id="4da52-2214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2215">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2216">'Blazor'</span></span>
- <span data-ttu-id="4da52-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2217">'Identity'</span></span>
- <span data-ttu-id="4da52-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2219">'Razor'</span></span>
- <span data-ttu-id="4da52-2220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2221">-------: | :----: | | dizi: girdiler: 0 | value0 | | dizi: girdiler: 1 | Değer1 | | dizi: girdiler: 2 | değer2 | | dizi: girdiler: 4 | value4 | | dizi: girdiler: 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="4da52-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="4da52-2222">Bu anahtarlar ve değerler, bellek yapılandırma sağlayıcısı kullanılarak örnek uygulamaya yüklenir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="4da52-2223">Dizi, Dizin 3 için bir değeri atlar &num; .</span><span class="sxs-lookup"><span data-stu-id="4da52-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="4da52-2224">Yapılandırma Bağlayıcısı, null değerleri bağlama veya bağlantılı nesnelerde null girişler oluşturma yeteneğine sahip değildir. Bu, bu diziyi bir nesneye bağlamanın sonucu gösterildiği sırada bir süre açık hale gelir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="4da52-2225">Örnek uygulamada, bir POCO sınıfı, bağlantılı yapılandırma verilerini tutmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="4da52-2226">Yapılandırma verileri nesnesine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="4da52-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="4da52-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir ve bu da daha küçük kod elde edilebilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="4da52-2228">Bir örneği olan bağlantılı nesne, `ArrayExample` yapılandırmadan dizi verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="4da52-2229">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="4da52-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4da52-2230">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="4da52-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="4da52-2231">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2232">'Blazor'</span></span>
- <span data-ttu-id="4da52-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2233">'Identity'</span></span>
- <span data-ttu-id="4da52-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2235">'Razor'</span></span>
- <span data-ttu-id="4da52-2236">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2237">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2238">'Blazor'</span></span>
- <span data-ttu-id="4da52-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2239">'Identity'</span></span>
- <span data-ttu-id="4da52-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2241">'Razor'</span></span>
- <span data-ttu-id="4da52-2242">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2243">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2244">'Blazor'</span></span>
- <span data-ttu-id="4da52-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2245">'Identity'</span></span>
- <span data-ttu-id="4da52-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2247">'Razor'</span></span>
- <span data-ttu-id="4da52-2248">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2249">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2250">'Blazor'</span></span>
- <span data-ttu-id="4da52-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2251">'Identity'</span></span>
- <span data-ttu-id="4da52-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2253">'Razor'</span></span>
- <span data-ttu-id="4da52-2254">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2255">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2256">'Blazor'</span></span>
- <span data-ttu-id="4da52-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2257">'Identity'</span></span>
- <span data-ttu-id="4da52-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2259">'Razor'</span></span>
- <span data-ttu-id="4da52-2260">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2261">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2262">'Blazor'</span></span>
- <span data-ttu-id="4da52-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2263">'Identity'</span></span>
- <span data-ttu-id="4da52-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2265">'Razor'</span></span>
- <span data-ttu-id="4da52-2266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2267">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2268">'Blazor'</span></span>
- <span data-ttu-id="4da52-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2269">'Identity'</span></span>
- <span data-ttu-id="4da52-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2271">'Razor'</span></span>
- <span data-ttu-id="4da52-2272">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2273">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2274">'Blazor'</span></span>
- <span data-ttu-id="4da52-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2275">'Identity'</span></span>
- <span data-ttu-id="4da52-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2277">'Razor'</span></span>
- <span data-ttu-id="4da52-2278">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2279">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2280">'Blazor'</span></span>
- <span data-ttu-id="4da52-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2281">'Identity'</span></span>
- <span data-ttu-id="4da52-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2283">'Razor'</span></span>
- <span data-ttu-id="4da52-2284">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2285">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2286">'Blazor'</span></span>
- <span data-ttu-id="4da52-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2287">'Identity'</span></span>
- <span data-ttu-id="4da52-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2289">'Razor'</span></span>
- <span data-ttu-id="4da52-2290">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2291">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2292">'Blazor'</span></span>
- <span data-ttu-id="4da52-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2293">'Identity'</span></span>
- <span data-ttu-id="4da52-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2295">'Razor'</span></span>
- <span data-ttu-id="4da52-2296">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2297">-------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2298">'Blazor'</span></span>
- <span data-ttu-id="4da52-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2299">'Identity'</span></span>
- <span data-ttu-id="4da52-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2301">'Razor'</span></span>
- <span data-ttu-id="4da52-2302">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2303">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2304">'Blazor'</span></span>
- <span data-ttu-id="4da52-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2305">'Identity'</span></span>
- <span data-ttu-id="4da52-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2307">'Razor'</span></span>
- <span data-ttu-id="4da52-2308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2309">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2310">'Blazor'</span></span>
- <span data-ttu-id="4da52-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2311">'Identity'</span></span>
- <span data-ttu-id="4da52-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2313">'Razor'</span></span>
- <span data-ttu-id="4da52-2314">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2315">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2316">'Blazor'</span></span>
- <span data-ttu-id="4da52-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2317">'Identity'</span></span>
- <span data-ttu-id="4da52-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2319">'Razor'</span></span>
- <span data-ttu-id="4da52-2320">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2321">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2322">'Blazor'</span></span>
- <span data-ttu-id="4da52-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2323">'Identity'</span></span>
- <span data-ttu-id="4da52-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2325">'Razor'</span></span>
- <span data-ttu-id="4da52-2326">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2327">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2328">'Blazor'</span></span>
- <span data-ttu-id="4da52-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2329">'Identity'</span></span>
- <span data-ttu-id="4da52-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2331">'Razor'</span></span>
- <span data-ttu-id="4da52-2332">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2334">'Blazor'</span></span>
- <span data-ttu-id="4da52-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2335">'Identity'</span></span>
- <span data-ttu-id="4da52-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2337">'Razor'</span></span>
- <span data-ttu-id="4da52-2338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2340">'Blazor'</span></span>
- <span data-ttu-id="4da52-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2341">'Identity'</span></span>
- <span data-ttu-id="4da52-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2343">'Razor'</span></span>
- <span data-ttu-id="4da52-2344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2346">'Blazor'</span></span>
- <span data-ttu-id="4da52-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2347">'Identity'</span></span>
- <span data-ttu-id="4da52-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2349">'Razor'</span></span>
- <span data-ttu-id="4da52-2350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2352">'Blazor'</span></span>
- <span data-ttu-id="4da52-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2353">'Identity'</span></span>
- <span data-ttu-id="4da52-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2355">'Razor'</span></span>
- <span data-ttu-id="4da52-2356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2358">'Blazor'</span></span>
- <span data-ttu-id="4da52-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2359">'Identity'</span></span>
- <span data-ttu-id="4da52-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2361">'Razor'</span></span>
- <span data-ttu-id="4da52-2362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2363">-------------: | | 0 | value0 | | 1 | Değer1 | | 2 | değer2 | | 3 | value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="4da52-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="4da52-2364">&num;İlişkili nesnede Dizin 3 `array:4` yapılandırma anahtarı ve değeri için yapılandırma verilerini barındırır `value4` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4da52-2365">Bir diziyi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizinleri yalnızca nesne oluşturulurken yapılandırma verilerini yinelemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4da52-2366">Yapılandırma verilerinde null değer korunmaz ve bir yapılandırma anahtarlarındaki bir dizi bir veya daha fazla dizini atlamazsanız, bağlantılı nesnede NULL değerli bir giriş oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4da52-2367">&num; `ArrayExample` Yapılandırmada doğru anahtar-değer çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından örneğe bağlamadan önce dizin 3 için eksik yapılandırma öğesi sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="4da52-2368">Örnek, eksik anahtar-değer çiftine sahip ek bir JSON yapılandırma sağlayıcısı içeriyorsa, `ArrayExample.Entries` tam yapılandırma dizisiyle eşleşir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="4da52-2369">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="4da52-2370">`ConfigureAppConfiguration` içinde:</span><span class="sxs-lookup"><span data-stu-id="4da52-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="4da52-2371">Tabloda gösterilen anahtar-değer çifti, yapılandırmaya yüklendi.</span><span class="sxs-lookup"><span data-stu-id="4da52-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="4da52-2372">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-2372">Key</span></span>             | <span data-ttu-id="4da52-2373">Değer</span><span class="sxs-lookup"><span data-stu-id="4da52-2373">Value</span></span>  |
| :---
<span data-ttu-id="4da52-2374">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2375">'Blazor'</span></span>
- <span data-ttu-id="4da52-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2376">'Identity'</span></span>
- <span data-ttu-id="4da52-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2378">'Razor'</span></span>
- <span data-ttu-id="4da52-2379">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2380">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2381">'Blazor'</span></span>
- <span data-ttu-id="4da52-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2382">'Identity'</span></span>
- <span data-ttu-id="4da52-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2384">'Razor'</span></span>
- <span data-ttu-id="4da52-2385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2386">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2387">'Blazor'</span></span>
- <span data-ttu-id="4da52-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2388">'Identity'</span></span>
- <span data-ttu-id="4da52-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2390">'Razor'</span></span>
- <span data-ttu-id="4da52-2391">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2392">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2393">'Blazor'</span></span>
- <span data-ttu-id="4da52-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2394">'Identity'</span></span>
- <span data-ttu-id="4da52-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2396">'Razor'</span></span>
- <span data-ttu-id="4da52-2397">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2398">-------: | :----: | | dizi: girdiler: 3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="4da52-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="4da52-2399">`ArrayExample`Sınıf örneği, JSON yapılandırma sağlayıcısı Dizin &num; 3 ' ü içeriyorsa, `ArrayExample.Entries` dizi değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="4da52-2400">`ArrayExample.Entries`İndeks</span><span class="sxs-lookup"><span data-stu-id="4da52-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4da52-2401">`ArrayExample.Entries`Deeri</span><span class="sxs-lookup"><span data-stu-id="4da52-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="4da52-2402">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2403">'Blazor'</span></span>
- <span data-ttu-id="4da52-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2404">'Identity'</span></span>
- <span data-ttu-id="4da52-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2406">'Razor'</span></span>
- <span data-ttu-id="4da52-2407">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2408">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2409">'Blazor'</span></span>
- <span data-ttu-id="4da52-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2410">'Identity'</span></span>
- <span data-ttu-id="4da52-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2412">'Razor'</span></span>
- <span data-ttu-id="4da52-2413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2414">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2415">'Blazor'</span></span>
- <span data-ttu-id="4da52-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2416">'Identity'</span></span>
- <span data-ttu-id="4da52-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2418">'Razor'</span></span>
- <span data-ttu-id="4da52-2419">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2420">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2421">'Blazor'</span></span>
- <span data-ttu-id="4da52-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2422">'Identity'</span></span>
- <span data-ttu-id="4da52-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2424">'Razor'</span></span>
- <span data-ttu-id="4da52-2425">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2426">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2427">'Blazor'</span></span>
- <span data-ttu-id="4da52-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2428">'Identity'</span></span>
- <span data-ttu-id="4da52-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2430">'Razor'</span></span>
- <span data-ttu-id="4da52-2431">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2432">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2433">'Blazor'</span></span>
- <span data-ttu-id="4da52-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2434">'Identity'</span></span>
- <span data-ttu-id="4da52-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2436">'Razor'</span></span>
- <span data-ttu-id="4da52-2437">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2438">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2439">'Blazor'</span></span>
- <span data-ttu-id="4da52-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2440">'Identity'</span></span>
- <span data-ttu-id="4da52-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2442">'Razor'</span></span>
- <span data-ttu-id="4da52-2443">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2444">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2445">'Blazor'</span></span>
- <span data-ttu-id="4da52-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2446">'Identity'</span></span>
- <span data-ttu-id="4da52-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2448">'Razor'</span></span>
- <span data-ttu-id="4da52-2449">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2450">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2451">'Blazor'</span></span>
- <span data-ttu-id="4da52-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2452">'Identity'</span></span>
- <span data-ttu-id="4da52-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2454">'Razor'</span></span>
- <span data-ttu-id="4da52-2455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2456">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2457">'Blazor'</span></span>
- <span data-ttu-id="4da52-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2458">'Identity'</span></span>
- <span data-ttu-id="4da52-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2460">'Razor'</span></span>
- <span data-ttu-id="4da52-2461">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2462">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2463">'Blazor'</span></span>
- <span data-ttu-id="4da52-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2464">'Identity'</span></span>
- <span data-ttu-id="4da52-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2466">'Razor'</span></span>
- <span data-ttu-id="4da52-2467">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2468">-------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2469">'Blazor'</span></span>
- <span data-ttu-id="4da52-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2470">'Identity'</span></span>
- <span data-ttu-id="4da52-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2472">'Razor'</span></span>
- <span data-ttu-id="4da52-2473">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2474">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2475">'Blazor'</span></span>
- <span data-ttu-id="4da52-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2476">'Identity'</span></span>
- <span data-ttu-id="4da52-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2478">'Razor'</span></span>
- <span data-ttu-id="4da52-2479">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2480">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2481">'Blazor'</span></span>
- <span data-ttu-id="4da52-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2482">'Identity'</span></span>
- <span data-ttu-id="4da52-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2484">'Razor'</span></span>
- <span data-ttu-id="4da52-2485">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2486">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2487">'Blazor'</span></span>
- <span data-ttu-id="4da52-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2488">'Identity'</span></span>
- <span data-ttu-id="4da52-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2490">'Razor'</span></span>
- <span data-ttu-id="4da52-2491">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2492">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2493">'Blazor'</span></span>
- <span data-ttu-id="4da52-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2494">'Identity'</span></span>
- <span data-ttu-id="4da52-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2496">'Razor'</span></span>
- <span data-ttu-id="4da52-2497">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2498">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2499">'Blazor'</span></span>
- <span data-ttu-id="4da52-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2500">'Identity'</span></span>
- <span data-ttu-id="4da52-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2502">'Razor'</span></span>
- <span data-ttu-id="4da52-2503">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2504">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2505">'Blazor'</span></span>
- <span data-ttu-id="4da52-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2506">'Identity'</span></span>
- <span data-ttu-id="4da52-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2508">'Razor'</span></span>
- <span data-ttu-id="4da52-2509">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2510">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2511">'Blazor'</span></span>
- <span data-ttu-id="4da52-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2512">'Identity'</span></span>
- <span data-ttu-id="4da52-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2514">'Razor'</span></span>
- <span data-ttu-id="4da52-2515">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2516">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2517">'Blazor'</span></span>
- <span data-ttu-id="4da52-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2518">'Identity'</span></span>
- <span data-ttu-id="4da52-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2520">'Razor'</span></span>
- <span data-ttu-id="4da52-2521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2522">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2523">'Blazor'</span></span>
- <span data-ttu-id="4da52-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2524">'Identity'</span></span>
- <span data-ttu-id="4da52-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2526">'Razor'</span></span>
- <span data-ttu-id="4da52-2527">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2528">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2529">'Blazor'</span></span>
- <span data-ttu-id="4da52-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2530">'Identity'</span></span>
- <span data-ttu-id="4da52-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2532">'Razor'</span></span>
- <span data-ttu-id="4da52-2533">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2534">-------------: | | 0 | value0 | | 1 | Değer1 | | 2 | değer2 | | 3 | value3 | | 4 | value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="4da52-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="4da52-2535">**JSON dizi işleme**</span><span class="sxs-lookup"><span data-stu-id="4da52-2535">**JSON array processing**</span></span>

<span data-ttu-id="4da52-2536">JSON dosyası bir dizi içeriyorsa, sıfır tabanlı bölüm diziniyle dizi öğeleri için yapılandırma anahtarları oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4da52-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="4da52-2537">Aşağıdaki yapılandırma dosyasında `subsection` bir dizidir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="4da52-2538">JSON yapılandırma sağlayıcısı, yapılandırma verilerini aşağıdaki anahtar-değer çiftlerine okur:</span><span class="sxs-lookup"><span data-stu-id="4da52-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="4da52-2539">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4da52-2539">Key</span></span>                     | <span data-ttu-id="4da52-2540">Değer</span><span class="sxs-lookup"><span data-stu-id="4da52-2540">Value</span></span>  |
| ---
<span data-ttu-id="4da52-2541">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2542">'Blazor'</span></span>
- <span data-ttu-id="4da52-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2543">'Identity'</span></span>
- <span data-ttu-id="4da52-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2545">'Razor'</span></span>
- <span data-ttu-id="4da52-2546">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2547">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2548">'Blazor'</span></span>
- <span data-ttu-id="4da52-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2549">'Identity'</span></span>
- <span data-ttu-id="4da52-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2551">'Razor'</span></span>
- <span data-ttu-id="4da52-2552">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2553">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2554">'Blazor'</span></span>
- <span data-ttu-id="4da52-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2555">'Identity'</span></span>
- <span data-ttu-id="4da52-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2557">'Razor'</span></span>
- <span data-ttu-id="4da52-2558">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2559">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2560">'Blazor'</span></span>
- <span data-ttu-id="4da52-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2561">'Identity'</span></span>
- <span data-ttu-id="4da52-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2563">'Razor'</span></span>
- <span data-ttu-id="4da52-2564">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2565">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2566">'Blazor'</span></span>
- <span data-ttu-id="4da52-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2567">'Identity'</span></span>
- <span data-ttu-id="4da52-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2569">'Razor'</span></span>
- <span data-ttu-id="4da52-2570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2571">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2572">'Blazor'</span></span>
- <span data-ttu-id="4da52-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2573">'Identity'</span></span>
- <span data-ttu-id="4da52-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2575">'Razor'</span></span>
- <span data-ttu-id="4da52-2576">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2577">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2578">'Blazor'</span></span>
- <span data-ttu-id="4da52-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2579">'Identity'</span></span>
- <span data-ttu-id="4da52-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2581">'Razor'</span></span>
- <span data-ttu-id="4da52-2582">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2583">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2584">'Blazor'</span></span>
- <span data-ttu-id="4da52-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2585">'Identity'</span></span>
- <span data-ttu-id="4da52-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2587">'Razor'</span></span>
- <span data-ttu-id="4da52-2588">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2589">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2590">'Blazor'</span></span>
- <span data-ttu-id="4da52-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2591">'Identity'</span></span>
- <span data-ttu-id="4da52-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2593">'Razor'</span></span>
- <span data-ttu-id="4da52-2594">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2595">------------ | :----: | | json_array: anahtar | Değerea | | json_array: alt bölüm: 0 | valueB | | json_array: alt bölüm: 1 | değer EC | | json_array: alt bölüm: 2 | Değer |</span><span class="sxs-lookup"><span data-stu-id="4da52-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="4da52-2596">Örnek uygulamada, yapılandırma anahtar-değer çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="4da52-2597">Bağlama işleminden sonra `JsonArrayExample.Key` değeri barındırır `valueA` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="4da52-2598">Alt bölüm değerleri POCO dizisi özelliğinde depolanır `Subsection` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="4da52-2599">`JsonArrayExample.Subsection`İndeks</span><span class="sxs-lookup"><span data-stu-id="4da52-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="4da52-2600">`JsonArrayExample.Subsection`Deeri</span><span class="sxs-lookup"><span data-stu-id="4da52-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="4da52-2601">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2602">'Blazor'</span></span>
- <span data-ttu-id="4da52-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2603">'Identity'</span></span>
- <span data-ttu-id="4da52-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2605">'Razor'</span></span>
- <span data-ttu-id="4da52-2606">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2607">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2608">'Blazor'</span></span>
- <span data-ttu-id="4da52-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2609">'Identity'</span></span>
- <span data-ttu-id="4da52-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2611">'Razor'</span></span>
- <span data-ttu-id="4da52-2612">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2613">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2614">'Blazor'</span></span>
- <span data-ttu-id="4da52-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2615">'Identity'</span></span>
- <span data-ttu-id="4da52-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2617">'Razor'</span></span>
- <span data-ttu-id="4da52-2618">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2619">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2620">'Blazor'</span></span>
- <span data-ttu-id="4da52-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2621">'Identity'</span></span>
- <span data-ttu-id="4da52-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2623">'Razor'</span></span>
- <span data-ttu-id="4da52-2624">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2625">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2626">'Blazor'</span></span>
- <span data-ttu-id="4da52-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2627">'Identity'</span></span>
- <span data-ttu-id="4da52-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2629">'Razor'</span></span>
- <span data-ttu-id="4da52-2630">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2631">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2632">'Blazor'</span></span>
- <span data-ttu-id="4da52-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2633">'Identity'</span></span>
- <span data-ttu-id="4da52-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2635">'Razor'</span></span>
- <span data-ttu-id="4da52-2636">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2637">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2638">'Blazor'</span></span>
- <span data-ttu-id="4da52-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2639">'Identity'</span></span>
- <span data-ttu-id="4da52-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2641">'Razor'</span></span>
- <span data-ttu-id="4da52-2642">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2643">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2644">'Blazor'</span></span>
- <span data-ttu-id="4da52-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2645">'Identity'</span></span>
- <span data-ttu-id="4da52-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2647">'Razor'</span></span>
- <span data-ttu-id="4da52-2648">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2649">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2650">'Blazor'</span></span>
- <span data-ttu-id="4da52-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2651">'Identity'</span></span>
- <span data-ttu-id="4da52-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2653">'Razor'</span></span>
- <span data-ttu-id="4da52-2654">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2655">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2656">'Blazor'</span></span>
- <span data-ttu-id="4da52-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2657">'Identity'</span></span>
- <span data-ttu-id="4da52-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2659">'Razor'</span></span>
- <span data-ttu-id="4da52-2660">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2661">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2662">'Blazor'</span></span>
- <span data-ttu-id="4da52-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2663">'Identity'</span></span>
- <span data-ttu-id="4da52-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2665">'Razor'</span></span>
- <span data-ttu-id="4da52-2666">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2667">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2668">'Blazor'</span></span>
- <span data-ttu-id="4da52-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2669">'Identity'</span></span>
- <span data-ttu-id="4da52-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2671">'Razor'</span></span>
- <span data-ttu-id="4da52-2672">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2673">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2674">'Blazor'</span></span>
- <span data-ttu-id="4da52-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2675">'Identity'</span></span>
- <span data-ttu-id="4da52-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2677">'Razor'</span></span>
- <span data-ttu-id="4da52-2678">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2679">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2680">'Blazor'</span></span>
- <span data-ttu-id="4da52-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2681">'Identity'</span></span>
- <span data-ttu-id="4da52-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2683">'Razor'</span></span>
- <span data-ttu-id="4da52-2684">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2685">-----------------: | :---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2686">'Blazor'</span></span>
- <span data-ttu-id="4da52-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2687">'Identity'</span></span>
- <span data-ttu-id="4da52-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2689">'Razor'</span></span>
- <span data-ttu-id="4da52-2690">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2691">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2692">'Blazor'</span></span>
- <span data-ttu-id="4da52-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2693">'Identity'</span></span>
- <span data-ttu-id="4da52-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2695">'Razor'</span></span>
- <span data-ttu-id="4da52-2696">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2697">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2698">'Blazor'</span></span>
- <span data-ttu-id="4da52-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2699">'Identity'</span></span>
- <span data-ttu-id="4da52-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2701">'Razor'</span></span>
- <span data-ttu-id="4da52-2702">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2703">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2704">'Blazor'</span></span>
- <span data-ttu-id="4da52-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2705">'Identity'</span></span>
- <span data-ttu-id="4da52-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2707">'Razor'</span></span>
- <span data-ttu-id="4da52-2708">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2709">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2710">'Blazor'</span></span>
- <span data-ttu-id="4da52-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2711">'Identity'</span></span>
- <span data-ttu-id="4da52-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2713">'Razor'</span></span>
- <span data-ttu-id="4da52-2714">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2715">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2716">'Blazor'</span></span>
- <span data-ttu-id="4da52-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2717">'Identity'</span></span>
- <span data-ttu-id="4da52-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2719">'Razor'</span></span>
- <span data-ttu-id="4da52-2720">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2721">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2722">'Blazor'</span></span>
- <span data-ttu-id="4da52-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2723">'Identity'</span></span>
- <span data-ttu-id="4da52-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2725">'Razor'</span></span>
- <span data-ttu-id="4da52-2726">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2727">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2728">'Blazor'</span></span>
- <span data-ttu-id="4da52-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2729">'Identity'</span></span>
- <span data-ttu-id="4da52-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2731">'Razor'</span></span>
- <span data-ttu-id="4da52-2732">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2733">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2734">'Blazor'</span></span>
- <span data-ttu-id="4da52-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2735">'Identity'</span></span>
- <span data-ttu-id="4da52-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2737">'Razor'</span></span>
- <span data-ttu-id="4da52-2738">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2739">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2740">'Blazor'</span></span>
- <span data-ttu-id="4da52-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2741">'Identity'</span></span>
- <span data-ttu-id="4da52-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2743">'Razor'</span></span>
- <span data-ttu-id="4da52-2744">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2745">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2746">'Blazor'</span></span>
- <span data-ttu-id="4da52-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2747">'Identity'</span></span>
- <span data-ttu-id="4da52-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2749">'Razor'</span></span>
- <span data-ttu-id="4da52-2750">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2751">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2752">'Blazor'</span></span>
- <span data-ttu-id="4da52-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2753">'Identity'</span></span>
- <span data-ttu-id="4da52-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2755">'Razor'</span></span>
- <span data-ttu-id="4da52-2756">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2757">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2758">'Blazor'</span></span>
- <span data-ttu-id="4da52-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2759">'Identity'</span></span>
- <span data-ttu-id="4da52-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2761">'Razor'</span></span>
- <span data-ttu-id="4da52-2762">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4da52-2763">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="4da52-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4da52-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2764">'Blazor'</span></span>
- <span data-ttu-id="4da52-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4da52-2765">'Identity'</span></span>
- <span data-ttu-id="4da52-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4da52-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="4da52-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4da52-2767">'Razor'</span></span>
- <span data-ttu-id="4da52-2768">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="4da52-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="4da52-2769">-----------------: | | 0 | valueB | | 1 | değer EC | | 2 | Değer |</span><span class="sxs-lookup"><span data-stu-id="4da52-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="4da52-2770">Özel yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="4da52-2770">Custom configuration provider</span></span>

<span data-ttu-id="4da52-2771">Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar-değer çiftlerini okuyan temel bir yapılandırma sağlayıcısı oluşturmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4da52-2772">Sağlayıcı aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4da52-2773">EF bellek içi veritabanı, tanıtım amacıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4da52-2774">Bağlantı dizesi gerektiren bir veritabanını kullanmak için, `ConfigurationBuilder` başka bir yapılandırma sağlayıcısından bağlantı dizesini sağlamak üzere bir ikincil uygulayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4da52-2775">Sağlayıcı bir veritabanı tablosunu başlangıçta yapılandırmaya okur.</span><span class="sxs-lookup"><span data-stu-id="4da52-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4da52-2776">Sağlayıcı, her anahtar temelinde veritabanını sorgulayamaz.</span><span class="sxs-lookup"><span data-stu-id="4da52-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4da52-2777">Değişiklik değişikliği uygulanmadı, bu nedenle uygulama başladıktan sonra veritabanının güncelleştirilmesi uygulamanın yapılandırması üzerinde hiçbir etkiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="4da52-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4da52-2778">`EFConfigurationValue`Yapılandırma değerlerini veritabanında depolamak için bir varlık tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="4da52-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4da52-2779">*Modeller/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4da52-2780">Bir `EFConfigurationContext` mağazaya ekleyin ve yapılandırılan değerlere erişin.</span><span class="sxs-lookup"><span data-stu-id="4da52-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4da52-2781">*Efconfigurationprovider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4da52-2782">Uygulayan bir sınıf oluşturun <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4da52-2783">*Efconfigurationprovider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4da52-2784">Öğesinden devralarak özel yapılandırma sağlayıcısını oluşturun <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> .</span><span class="sxs-lookup"><span data-stu-id="4da52-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4da52-2785">Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatır.</span><span class="sxs-lookup"><span data-stu-id="4da52-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="4da52-2786">*Efconfigurationprovider/efconfigurationprovider. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4da52-2787">`AddEFConfiguration`Genişletme yöntemi, yapılandırma kaynağını bir öğesine eklemeye izin verir `ConfigurationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4da52-2788">*Uzantılar/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="4da52-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4da52-2789">Aşağıdaki kod, `EFConfigurationProvider` *program.cs*içinde özel kullanımı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="4da52-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="4da52-2790">Başlangıç sırasında yapılandırmaya erişim</span><span class="sxs-lookup"><span data-stu-id="4da52-2790">Access configuration during startup</span></span>

<span data-ttu-id="4da52-2791">`IConfiguration` `Startup` İçindeki yapılandırma değerlerine erişmek için oluşturucuya ekleme `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4da52-2792">' Da yapılandırmaya erişmek için `Startup.Configure` , `IConfiguration` doğrudan yönteme ekleyin veya örneği oluşturucudan kullanın:</span><span class="sxs-lookup"><span data-stu-id="4da52-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="4da52-2793">Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişme örneği için bkz. [uygulama başlatma: kullanışlı yöntemler](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="4da52-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="4da52-2794">RazorSayfalar sayfasında veya MVC görünümünde erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4da52-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="4da52-2795">Bir Razor Sayfalar sayfasındaki veya MVC görünümündeki yapılandırma ayarlarına erişmek için, [Microsoft. Extensions. Configuration ad alanı](xref:Microsoft.Extensions.Configuration) için bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) ekleyin ve <xref:Microsoft.Extensions.Configuration.IConfiguration> sayfa ya da görünüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4da52-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="4da52-2796">RazorSayfalar sayfasında:</span><span class="sxs-lookup"><span data-stu-id="4da52-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="4da52-2797">MVC görünümünde:</span><span class="sxs-lookup"><span data-stu-id="4da52-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4da52-2798">Bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="4da52-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="4da52-2799">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="4da52-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4da52-2800">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4da52-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4da52-2801">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4da52-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
