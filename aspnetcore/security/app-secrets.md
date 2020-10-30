---
title: ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama
author: rick-anderson
description: ASP.NET Core uygulamasının geliştirilmesi sırasında gizli bilgileri uygulama gizli dizileri olarak depolamayı ve almayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056874"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="2e92e-103">ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama</span><span class="sxs-lookup"><span data-stu-id="2e92e-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e92e-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk larkabağı](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2e92e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2e92e-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2e92e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2e92e-106">Bu belgede, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması için teknikler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="2e92e-107">Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="2e92e-108">Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="2e92e-109">Gizli dizileri uygulamayla birlikte dağıtılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="2e92e-110">Bunun yerine, ortamlar, ortam değişkenleri, Azure Key Vault vb. gibi denetimli bir yöntemle üretim ortamında kullanılabilir hale gelmelidir. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e92e-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="2e92e-111">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2e92e-111">Environment variables</span></span>

<span data-ttu-id="2e92e-112">Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="2e92e-113">Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="2e92e-114">**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="2e92e-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="2e92e-115">Varsayılan bir veritabanı bağlantı dizesi, *:::no-loc(appsettings.json):::* anahtar ile projenin dosyasına dahildir `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-115">A default database connection string is included in the project's *:::no-loc(appsettings.json):::* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="2e92e-116">Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="2e92e-117">Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="2e92e-118">Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="2e92e-119">Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="2e92e-120">Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="2e92e-121">Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="2e92e-122">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="2e92e-122">Secret Manager</span></span>

<span data-ttu-id="2e92e-123">Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="2e92e-124">Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi.</span><span class="sxs-lookup"><span data-stu-id="2e92e-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="2e92e-125">Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="2e92e-126">Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="2e92e-127">Uygulama gizli dizileri kaynak denetimine iade edilmedi.</span><span class="sxs-lookup"><span data-stu-id="2e92e-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="2e92e-128">Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="2e92e-129">Yalnızca geliştirme amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-129">It's for development purposes only.</span></span> <span data-ttu-id="2e92e-130">Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="2e92e-131">Gizli dizi Yöneticisi aracı nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="2e92e-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="2e92e-132">Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="2e92e-133">Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e92e-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="2e92e-134">Değerler, yerel makinedeki sistem korumalı bir kullanıcı profili klasöründe bir JSON yapılandırma dosyasında depolanır:</span><span class="sxs-lookup"><span data-stu-id="2e92e-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="2e92e-135">Windows</span><span class="sxs-lookup"><span data-stu-id="2e92e-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="2e92e-136">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="2e92e-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="2e92e-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="2e92e-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="2e92e-138">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="2e92e-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="2e92e-139">Yukarıdaki dosya yollarında, `<user_secrets_id>` `UserSecretsId` *. csproj* dosyasında belirtilen değerle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="2e92e-140">Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma.</span><span class="sxs-lookup"><span data-stu-id="2e92e-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="2e92e-141">Bu uygulama ayrıntıları değişebilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-141">These implementation details may change.</span></span> <span data-ttu-id="2e92e-142">Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="2e92e-143">Gizli depolamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="2e92e-143">Enable secret storage</span></span>

<span data-ttu-id="2e92e-144">Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="2e92e-145">Gizli dizi Yöneticisi Aracı, `init` .NET Core SDK 3.0.100 veya sonraki sürümlerde bir komut içerir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="2e92e-146">Kullanıcı gizli dizilerini kullanmak için, proje dizininde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="2e92e-147">Önceki komut, `UserSecretsId` `PropertyGroup` *. csproj* dosyasının içindeki bir öğesi ekler.</span><span class="sxs-lookup"><span data-stu-id="2e92e-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="2e92e-148">Varsayılan olarak, iç metni `UserSecretsId` BIR GUID 'dir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="2e92e-149">İç metin rastgele, ancak proje için benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="2e92e-150">Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="2e92e-151">Bu hareket `UserSecretsId` , *. csproj* dosyasına bir GUID ile doldurulmuş bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="2e92e-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="2e92e-152">Gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="2e92e-152">Set a secret</span></span>

<span data-ttu-id="2e92e-153">Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="2e92e-154">Gizli dizi, projenin değeri ile ilişkilendirilir `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="2e92e-155">Örneğin, *. csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="2e92e-156">Yukarıdaki örnekte, iki nokta üst üste `Movies` bir özelliği olan bir nesne sabit değeri olduğunu gösterir `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="2e92e-157">Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="2e92e-158">`--project` *. Csproj* dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="2e92e-159">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="2e92e-160">Visual Studio 'da JSON yapısı düzleştirme</span><span class="sxs-lookup"><span data-stu-id="2e92e-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="2e92e-161">Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *secrets.js* dosya açar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="2e92e-162">*Üzerindesecrets.js* içeriğini, saklanacak anahtar-değer çiftleriyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="2e92e-163">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="2e92e-164">JSON yapısı, veya ile yapılan değişikliklerden sonra düzleştirilir `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="2e92e-165">Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="2e92e-166">Değiştirilen dosya şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="2e92e-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="2e92e-167">Birden çok gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="2e92e-167">Set multiple secrets</span></span>

<span data-ttu-id="2e92e-168">Bir dizi gizli dizi, JSON ile komutuna ayırarak ayarlanabilir `set` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="2e92e-169">Aşağıdaki örnekte, dosyanın içeriğindeki *input.js* `set` komutuna verilmez.</span><span class="sxs-lookup"><span data-stu-id="2e92e-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="2e92e-170">Windows</span><span class="sxs-lookup"><span data-stu-id="2e92e-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="2e92e-171">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2e92e-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="2e92e-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="2e92e-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="2e92e-173">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2e92e-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="2e92e-174">Gizli dizi erişimi</span><span class="sxs-lookup"><span data-stu-id="2e92e-174">Access a secret</span></span>

<span data-ttu-id="2e92e-175">[ASP.NET Core Configuration API 'si](xref:fundamentals/configuration/index) , gizli yönetici sırları için erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="2e92e-176">Kullanıcı gizli dizileri yapılandırma kaynağı, proje, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> önceden yapılandırılmış varsayılanlar ile konağın yeni bir örneğini başlatmak için çağırdığında geliştirme moduna otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="2e92e-177">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Şu olduğunda çağırır <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="2e92e-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="2e92e-178">`CreateDefaultBuilder`Çağrılmıyorsa, çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="2e92e-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="2e92e-179">`AddUserSecrets`Aşağıdaki örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="2e92e-180">Kullanıcı gizli dizileri API aracılığıyla alınabilir `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="2e92e-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="2e92e-181">Gizli dizileri bir POCO ile eşleme</span><span class="sxs-lookup"><span data-stu-id="2e92e-181">Map secrets to a POCO</span></span>

<span data-ttu-id="2e92e-182">Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-183">Önceki gizli dizileri bir POCO 'ya eşlemek için, `Configuration` API 'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="2e92e-184">Aşağıdaki kod, özel bir `MovieSettings` poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="2e92e-185">`Movies:ConnectionString`Ve gizli dizileri, `Movies:ServiceApiKey` içindeki ilgili özelliklerle eşlenir `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="2e92e-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="2e92e-186">Gizli dizileri olan dize değiştirme</span><span class="sxs-lookup"><span data-stu-id="2e92e-186">String replacement with secrets</span></span>

<span data-ttu-id="2e92e-187">Parolaların düz metin olarak depolanması güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="2e92e-188">Örneğin, içinde depolanan bir veritabanı bağlantı dizesi, *:::no-loc(appsettings.json):::* belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-188">For example, a database connection string stored in *:::no-loc(appsettings.json):::* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="2e92e-189">Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="2e92e-190">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="2e92e-191">`Password`Anahtar-değer çiftini içindeki bağlantı dizesinden kaldırın *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="2e92e-191">Remove the `Password` key-value pair from the connection string in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="2e92e-192">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/:::no-loc(appsettings.json):::?highlight=3)]

<span data-ttu-id="2e92e-193">Gizli dizi değeri, <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> bağlantı dizesinin tamamlanabilmesi için bir nesnenin özelliğinde ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="2e92e-194">Gizli dizileri listeleyin</span><span class="sxs-lookup"><span data-stu-id="2e92e-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-195">*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="2e92e-196">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="2e92e-197">Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *secrets.js* içindeki nesne hiyerarşisini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="2e92e-198">Tek bir parolayı kaldır</span><span class="sxs-lookup"><span data-stu-id="2e92e-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-199">*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="2e92e-200">Uygulamanın dosyada *secrets.js* , anahtarla ilişkili anahtar-değer çiftini kaldırmak için değiştirilmiştir `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="2e92e-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="2e92e-201">`dotnet user-secrets list` Aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2e92e-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="2e92e-202">Tüm gizli dizileri kaldır</span><span class="sxs-lookup"><span data-stu-id="2e92e-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-203">*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="2e92e-204">Uygulama için tüm Kullanıcı gizli dizileri, dosyadaki *secrets.js* silindi:</span><span class="sxs-lookup"><span data-stu-id="2e92e-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="2e92e-205">Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2e92e-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="2e92e-206">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e92e-206">Additional resources</span></span>

* <span data-ttu-id="2e92e-207">IIS 'den gizli yöneticiye erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e92e-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2e92e-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2e92e-209">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2e92e-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2e92e-210">Bu belgede, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması için teknikler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="2e92e-211">Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="2e92e-212">Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="2e92e-213">Gizli dizileri uygulamayla birlikte dağıtılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="2e92e-214">Bunun yerine, ortamlar, ortam değişkenleri, Azure Key Vault vb. gibi denetimli bir yöntemle üretim ortamında kullanılabilir hale gelmelidir. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e92e-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="2e92e-215">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="2e92e-215">Environment variables</span></span>

<span data-ttu-id="2e92e-216">Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="2e92e-217">Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="2e92e-218">**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="2e92e-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="2e92e-219">Varsayılan bir veritabanı bağlantı dizesi, *:::no-loc(appsettings.json):::* anahtar ile projenin dosyasına dahildir `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-219">A default database connection string is included in the project's *:::no-loc(appsettings.json):::* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="2e92e-220">Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="2e92e-221">Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="2e92e-222">Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="2e92e-223">Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="2e92e-224">Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="2e92e-225">Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="2e92e-226">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="2e92e-226">Secret Manager</span></span>

<span data-ttu-id="2e92e-227">Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="2e92e-228">Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi.</span><span class="sxs-lookup"><span data-stu-id="2e92e-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="2e92e-229">Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="2e92e-230">Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="2e92e-231">Uygulama gizli dizileri kaynak denetimine iade edilmedi.</span><span class="sxs-lookup"><span data-stu-id="2e92e-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="2e92e-232">Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="2e92e-233">Yalnızca geliştirme amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-233">It's for development purposes only.</span></span> <span data-ttu-id="2e92e-234">Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="2e92e-235">Gizli dizi Yöneticisi aracı nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="2e92e-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="2e92e-236">Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="2e92e-237">Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e92e-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="2e92e-238">Değerler, yerel makinedeki sistem korumalı bir kullanıcı profili klasöründe bir JSON yapılandırma dosyasında depolanır:</span><span class="sxs-lookup"><span data-stu-id="2e92e-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="2e92e-239">Windows</span><span class="sxs-lookup"><span data-stu-id="2e92e-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="2e92e-240">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="2e92e-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="2e92e-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="2e92e-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="2e92e-242">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="2e92e-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="2e92e-243">Yukarıdaki dosya yollarında, `<user_secrets_id>` `UserSecretsId` *. csproj* dosyasında belirtilen değerle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="2e92e-244">Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma.</span><span class="sxs-lookup"><span data-stu-id="2e92e-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="2e92e-245">Bu uygulama ayrıntıları değişebilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-245">These implementation details may change.</span></span> <span data-ttu-id="2e92e-246">Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="2e92e-247">Gizli depolamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="2e92e-247">Enable secret storage</span></span>

<span data-ttu-id="2e92e-248">Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="2e92e-249">Kullanıcı gizli dizilerini kullanmak için `UserSecretsId` `PropertyGroup` *. csproj* dosyasının içindeki bir öğesi tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="2e92e-250">İç metni `UserSecretsId` rastgele, ancak proje için benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="2e92e-251">Geliştiriciler, genellikle için bir GUID oluşturur `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="2e92e-252">Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="2e92e-253">Bu hareket `UserSecretsId` , *. csproj* dosyasına bir GUID ile doldurulmuş bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="2e92e-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="2e92e-254">Gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="2e92e-254">Set a secret</span></span>

<span data-ttu-id="2e92e-255">Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="2e92e-256">Gizli dizi, projenin değeri ile ilişkilendirilir `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="2e92e-257">Örneğin, *. csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="2e92e-258">Yukarıdaki örnekte, iki nokta üst üste `Movies` bir özelliği olan bir nesne sabit değeri olduğunu gösterir `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="2e92e-259">Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="2e92e-260">`--project` *. Csproj* dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="2e92e-261">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="2e92e-262">Visual Studio 'da JSON yapısı düzleştirme</span><span class="sxs-lookup"><span data-stu-id="2e92e-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="2e92e-263">Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *secrets.js* dosya açar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="2e92e-264">*Üzerindesecrets.js* içeriğini, saklanacak anahtar-değer çiftleriyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e92e-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="2e92e-265">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="2e92e-266">JSON yapısı, veya ile yapılan değişikliklerden sonra düzleştirilir `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="2e92e-267">Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="2e92e-268">Değiştirilen dosya şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="2e92e-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="2e92e-269">Birden çok gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="2e92e-269">Set multiple secrets</span></span>

<span data-ttu-id="2e92e-270">Bir dizi gizli dizi, JSON ile komutuna ayırarak ayarlanabilir `set` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="2e92e-271">Aşağıdaki örnekte, dosyanın içeriğindeki *input.js* `set` komutuna verilmez.</span><span class="sxs-lookup"><span data-stu-id="2e92e-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="2e92e-272">Windows</span><span class="sxs-lookup"><span data-stu-id="2e92e-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="2e92e-273">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2e92e-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="2e92e-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="2e92e-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="2e92e-275">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2e92e-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="2e92e-276">Gizli dizi erişimi</span><span class="sxs-lookup"><span data-stu-id="2e92e-276">Access a secret</span></span>

<span data-ttu-id="2e92e-277">[ASP.NET Core Configuration API 'si](xref:fundamentals/configuration/index) , gizli yönetici sırları için erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="2e92e-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="2e92e-278">Projeniz .NET Framework hedefliyorsa [Microsoft.Extensions.Configbir para birimi kullanın. Usergizlilikler](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="2e92e-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="2e92e-279">ASP.NET Core 2,0 veya sonraki bir sürümde, proje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> önceden yapılandırılmış varsayılanlar ile konağın yeni bir örneğini başlatmak için çağırdığında, Kullanıcı gizli dizileri yapılandırma kaynağı geliştirme moduna otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="2e92e-280">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Şu olduğunda çağırır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="2e92e-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="2e92e-281">`CreateDefaultBuilder`Çağrılmıyorsa, oluşturucuda çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e92e-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="2e92e-282">`AddUserSecrets`Aşağıdaki örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="2e92e-283">Kullanıcı gizli dizileri API aracılığıyla alınabilir `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="2e92e-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="2e92e-284">Gizli dizileri bir POCO ile eşleme</span><span class="sxs-lookup"><span data-stu-id="2e92e-284">Map secrets to a POCO</span></span>

<span data-ttu-id="2e92e-285">Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-286">Önceki gizli dizileri bir POCO 'ya eşlemek için, `Configuration` API 'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="2e92e-287">Aşağıdaki kod, özel bir `MovieSettings` poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="2e92e-288">`Movies:ConnectionString`Ve gizli dizileri, `Movies:ServiceApiKey` içindeki ilgili özelliklerle eşlenir `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="2e92e-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="2e92e-289">Gizli dizileri olan dize değiştirme</span><span class="sxs-lookup"><span data-stu-id="2e92e-289">String replacement with secrets</span></span>

<span data-ttu-id="2e92e-290">Parolaların düz metin olarak depolanması güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="2e92e-291">Örneğin, içinde depolanan bir veritabanı bağlantı dizesi, *:::no-loc(appsettings.json):::* belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-291">For example, a database connection string stored in *:::no-loc(appsettings.json):::* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="2e92e-292">Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e92e-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="2e92e-293">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="2e92e-294">`Password`Anahtar-değer çiftini içindeki bağlantı dizesinden kaldırın *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="2e92e-294">Remove the `Password` key-value pair from the connection string in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="2e92e-295">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2e92e-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/:::no-loc(appsettings.json):::?highlight=3)]

<span data-ttu-id="2e92e-296">Gizli dizi değeri, <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> bağlantı dizesinin tamamlanabilmesi için bir nesnenin özelliğinde ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="2e92e-297">Gizli dizileri listeleyin</span><span class="sxs-lookup"><span data-stu-id="2e92e-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-298">*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="2e92e-299">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="2e92e-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="2e92e-300">Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *secrets.js* içindeki nesne hiyerarşisini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2e92e-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="2e92e-301">Tek bir parolayı kaldır</span><span class="sxs-lookup"><span data-stu-id="2e92e-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-302">*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="2e92e-303">Uygulamanın dosyada *secrets.js* , anahtarla ilişkili anahtar-değer çiftini kaldırmak için değiştirilmiştir `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="2e92e-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="2e92e-304">Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2e92e-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="2e92e-305">Tüm gizli dizileri kaldır</span><span class="sxs-lookup"><span data-stu-id="2e92e-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="2e92e-306">*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e92e-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="2e92e-307">Uygulama için tüm Kullanıcı gizli dizileri, dosyadaki *secrets.js* silindi:</span><span class="sxs-lookup"><span data-stu-id="2e92e-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="2e92e-308">Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2e92e-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="2e92e-309">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e92e-309">Additional resources</span></span>

* <span data-ttu-id="2e92e-310">IIS 'den gizli yöneticiye erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.</span><span class="sxs-lookup"><span data-stu-id="2e92e-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end