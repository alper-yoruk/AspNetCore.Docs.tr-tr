---
title: ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama
author: rick-anderson
description: ASP.NET Core uygulamasının geliştirilmesi sırasında hassas bilgileri nasıl depolayacağınızı ve alabileceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
no-loc:
- appsettings.json
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
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486206"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="e1ba9-103">ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama</span><span class="sxs-lookup"><span data-stu-id="e1ba9-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1ba9-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk larkabağı](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e1ba9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e1ba9-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1ba9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1ba9-106">Bu belgede, bir geliştirme makinesindeki ASP.NET Core uygulaması için hassas verilerin nasıl yönetileceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="e1ba9-107">Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="e1ba9-108">Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="e1ba9-109">Gizli dizileri uygulamayla birlikte dağıtılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="e1ba9-110">Bunun yerine, üretim gizli dizileri, ortam değişkenleri veya Azure Key Vault gibi denetimli bir yöntemle erişilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="e1ba9-111">[Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="e1ba9-112">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e1ba9-112">Environment variables</span></span>

<span data-ttu-id="e1ba9-113">Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="e1ba9-114">Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="e1ba9-115">**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="e1ba9-116">Varsayılan bir veritabanı bağlantı dizesi, *appsettings.json* anahtar ile projenin dosyasına dahildir `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="e1ba9-117">Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="e1ba9-118">Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="e1ba9-119">Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="e1ba9-120">Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="e1ba9-121">Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="e1ba9-122">Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="e1ba9-123">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="e1ba9-123">Secret Manager</span></span>

<span data-ttu-id="e1ba9-124">Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="e1ba9-125">Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="e1ba9-126">Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="e1ba9-127">Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="e1ba9-128">Uygulama gizli dizileri kaynak denetimine iade edilmedi.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="e1ba9-129">Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="e1ba9-130">Yalnızca geliştirme amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-130">It's for development purposes only.</span></span> <span data-ttu-id="e1ba9-131">Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="e1ba9-132">Gizli dizi Yöneticisi aracı nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="e1ba9-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="e1ba9-133">Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını gizler.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="e1ba9-134">Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="e1ba9-135">Değerler yerel makinenin Kullanıcı profili klasöründe bir JSON dosyasında depolanır:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e1ba9-136">Windows</span><span class="sxs-lookup"><span data-stu-id="e1ba9-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="e1ba9-137">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="e1ba9-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e1ba9-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e1ba9-139">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="e1ba9-140">Yukarıdaki dosya yollarında, öğesini `<user_secrets_id>` `UserSecretsId` Proje dosyasında belirtilen değerle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="e1ba9-141">Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="e1ba9-142">Bu uygulama ayrıntıları değişebilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-142">These implementation details may change.</span></span> <span data-ttu-id="e1ba9-143">Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="e1ba9-144">Gizli depolamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="e1ba9-144">Enable secret storage</span></span>

<span data-ttu-id="e1ba9-145">Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="e1ba9-146">Gizli dizi Yöneticisi Aracı, `init` .NET Core SDK 3.0.100 veya sonraki sürümlerde bir komut içerir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="e1ba9-147">Kullanıcı gizli dizilerini kullanmak için, proje dizininde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="e1ba9-148">Önceki komut, `UserSecretsId` Proje dosyasının içindeki bir öğesi ekler `PropertyGroup` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="e1ba9-149">Varsayılan olarak, iç metni `UserSecretsId` BIR GUID 'dir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="e1ba9-150">İç metin rastgele, ancak proje için benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="e1ba9-151">Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="e1ba9-152">Bu hareket `UserSecretsId` , proje dosyasına BIR GUID ile doldurulmuş bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="e1ba9-153">Gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="e1ba9-153">Set a secret</span></span>

<span data-ttu-id="e1ba9-154">Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="e1ba9-155">Gizli dizi, projenin değeri ile ilişkilendirilir `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="e1ba9-156">Örneğin, proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="e1ba9-157">Yukarıdaki örnekte, iki nokta üst üste `Movies` bir özelliği olan bir nesne sabit değeri olduğunu gösterir `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="e1ba9-158">Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="e1ba9-159">`--project`Proje dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="e1ba9-160">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="e1ba9-161">Visual Studio 'da JSON yapısı düzleştirme</span><span class="sxs-lookup"><span data-stu-id="e1ba9-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="e1ba9-162">Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *secrets.js* dosya açar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="e1ba9-163">*Üzerindesecrets.js* içeriğini, saklanacak anahtar-değer çiftleriyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="e1ba9-164">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e1ba9-165">JSON yapısı, veya ile yapılan değişikliklerden sonra düzleştirilir `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="e1ba9-166">Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="e1ba9-167">Değiştirilen dosya aşağıdaki JSON 'a benzer:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="e1ba9-168">Birden çok gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="e1ba9-168">Set multiple secrets</span></span>

<span data-ttu-id="e1ba9-169">Bir dizi gizli dizi, JSON ile komutuna ayırarak ayarlanabilir `set` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="e1ba9-170">Aşağıdaki örnekte, dosyanın içeriğindeki *input.js* `set` komutuna verilmez.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="e1ba9-171">Windows</span><span class="sxs-lookup"><span data-stu-id="e1ba9-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="e1ba9-172">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="e1ba9-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e1ba9-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e1ba9-174">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="e1ba9-175">Gizli dizi erişimi</span><span class="sxs-lookup"><span data-stu-id="e1ba9-175">Access a secret</span></span>

<span data-ttu-id="e1ba9-176">Gizli dizi erişmek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="e1ba9-177">Kullanıcı parolaları yapılandırma kaynağını Kaydet</span><span class="sxs-lookup"><span data-stu-id="e1ba9-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="e1ba9-178">Yapılandırma API 'SI aracılığıyla gizli anahtarı okuyun</span><span class="sxs-lookup"><span data-stu-id="e1ba9-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="e1ba9-179">Kullanıcı parolaları yapılandırma kaynağını Kaydet</span><span class="sxs-lookup"><span data-stu-id="e1ba9-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="e1ba9-180">Kullanıcı gizli dizileri [yapılandırma sağlayıcısı](/dotnet/core/extensions/configuration-providers) uygun yapılandırma kaynağını .net [Yapılandırma API 'sine](xref:fundamentals/configuration/index)kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="e1ba9-181">Kullanıcı gizli dizileri yapılandırma kaynağı, proje çağırdığında geliştirme moduna otomatik olarak eklenir <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="e1ba9-182">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Şu olduğunda çağırır <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="e1ba9-183">`CreateDefaultBuilder`Çağrılmıyorsa, ' de çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="e1ba9-184">`AddUserSecrets`Aşağıdaki örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="e1ba9-185">Yapılandırma API 'SI aracılığıyla gizli anahtarı okuyun</span><span class="sxs-lookup"><span data-stu-id="e1ba9-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="e1ba9-186">Kullanıcı gizli dizileri yapılandırma kaynağı kayıtlıysa, .NET yapılandırma API 'SI gizli dizileri okuyabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="e1ba9-187">[Oluşturucu Ekleme](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) .NET yapılandırma API 'sine erişim kazanmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="e1ba9-188">Anahtarı okurken aşağıdaki örnekleri göz önünde bulundurun `Movies:ServiceApiKey` :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="e1ba9-189">**Başlangıç sınıfı:**</span><span class="sxs-lookup"><span data-stu-id="e1ba9-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="e1ba9-190">**Razor Sayfa sayfası modeli:**</span><span class="sxs-lookup"><span data-stu-id="e1ba9-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="e1ba9-191">Daha fazla bilgi için bkz. sayfalarda [erişim yapılandırması](xref:fundamentals/configuration/index#access-configuration-in-startup) ve [erişim yapılandırması. Razor ](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)</span><span class="sxs-lookup"><span data-stu-id="e1ba9-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="e1ba9-192">Gizli dizileri bir POCO ile eşleme</span><span class="sxs-lookup"><span data-stu-id="e1ba9-192">Map secrets to a POCO</span></span>

<span data-ttu-id="e1ba9-193">Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-194">Önceki gizli dizileri bir POCO 'ya eşlemek için .NET yapılandırma API 'sinin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="e1ba9-195">Aşağıdaki kod, özel bir `MovieSettings` poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="e1ba9-196">`Movies:ConnectionString`Ve gizli dizileri, `Movies:ServiceApiKey` içindeki ilgili özelliklerle eşlenir `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="e1ba9-197">Gizli dizileri olan dize değiştirme</span><span class="sxs-lookup"><span data-stu-id="e1ba9-197">String replacement with secrets</span></span>

<span data-ttu-id="e1ba9-198">Parolaların düz metin olarak depolanması güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="e1ba9-199">Örneğin, içinde depolanan bir veritabanı bağlantı dizesi, *appsettings.json* belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="e1ba9-200">Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="e1ba9-201">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="e1ba9-202">`Password`Anahtar-değer çiftini içindeki bağlantı dizesinden kaldırın *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="e1ba9-203">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="e1ba9-204">Gizli dizi değeri, <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> bağlantı dizesinin tamamlanabilmesi için bir nesnenin özelliğinde ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="e1ba9-205">Gizli dizileri listeleyin</span><span class="sxs-lookup"><span data-stu-id="e1ba9-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-206">Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="e1ba9-207">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="e1ba9-208">Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *secrets.js* içindeki nesne hiyerarşisini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="e1ba9-209">Tek bir parolayı kaldır</span><span class="sxs-lookup"><span data-stu-id="e1ba9-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-210">Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="e1ba9-211">Uygulamanın dosyada *secrets.js* , anahtarla ilişkili anahtar-değer çiftini kaldırmak için değiştirilmiştir `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e1ba9-212">`dotnet user-secrets list` Aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="e1ba9-213">Tüm gizli dizileri kaldır</span><span class="sxs-lookup"><span data-stu-id="e1ba9-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-214">Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="e1ba9-215">Uygulama için tüm Kullanıcı gizli dizileri, dosyadaki *secrets.js* silindi:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="e1ba9-216">Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="e1ba9-217">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e1ba9-217">Additional resources</span></span>

* <span data-ttu-id="e1ba9-218">IIS 'deki Kullanıcı gizli bilgilerine erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1ba9-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e1ba9-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e1ba9-220">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1ba9-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1ba9-221">Bu belgede, bir geliştirme makinesindeki ASP.NET Core uygulaması için hassas verilerin nasıl yönetileceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="e1ba9-222">Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="e1ba9-223">Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="e1ba9-224">Gizli dizileri uygulamayla birlikte dağıtılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="e1ba9-225">Bunun yerine, üretim gizli dizileri, ortam değişkenleri veya Azure Key Vault gibi denetimli bir yöntemle erişilmelidir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="e1ba9-226">[Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="e1ba9-227">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="e1ba9-227">Environment variables</span></span>

<span data-ttu-id="e1ba9-228">Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="e1ba9-229">Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="e1ba9-230">**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="e1ba9-231">Varsayılan bir veritabanı bağlantı dizesi, *appsettings.json* anahtar ile projenin dosyasına dahildir `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="e1ba9-232">Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="e1ba9-233">Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="e1ba9-234">Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="e1ba9-235">Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="e1ba9-236">Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="e1ba9-237">Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="e1ba9-238">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="e1ba9-238">Secret Manager</span></span>

<span data-ttu-id="e1ba9-239">Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="e1ba9-240">Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="e1ba9-241">Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="e1ba9-242">Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="e1ba9-243">Uygulama gizli dizileri kaynak denetimine iade edilmedi.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="e1ba9-244">Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="e1ba9-245">Yalnızca geliştirme amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-245">It's for development purposes only.</span></span> <span data-ttu-id="e1ba9-246">Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="e1ba9-247">Gizli dizi Yöneticisi aracı nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="e1ba9-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="e1ba9-248">Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını gizler.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="e1ba9-249">Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="e1ba9-250">Değerler yerel makinenin Kullanıcı profili klasöründe bir JSON dosyasında depolanır:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e1ba9-251">Windows</span><span class="sxs-lookup"><span data-stu-id="e1ba9-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="e1ba9-252">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="e1ba9-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e1ba9-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e1ba9-254">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="e1ba9-255">Yukarıdaki dosya yollarında, öğesini `<user_secrets_id>` `UserSecretsId` Proje dosyasında belirtilen değerle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="e1ba9-256">Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="e1ba9-257">Bu uygulama ayrıntıları değişebilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-257">These implementation details may change.</span></span> <span data-ttu-id="e1ba9-258">Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="e1ba9-259">Gizli depolamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="e1ba9-259">Enable secret storage</span></span>

<span data-ttu-id="e1ba9-260">Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="e1ba9-261">Kullanıcı gizli dizilerini kullanmak için `UserSecretsId` Proje dosyasının içindeki bir öğesi tanımlayın `PropertyGroup` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="e1ba9-262">İç metni `UserSecretsId` rastgele, ancak proje için benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="e1ba9-263">Geliştiriciler, genellikle için bir GUID oluşturur `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="e1ba9-264">Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="e1ba9-265">Bu hareket `UserSecretsId` , proje dosyasına BIR GUID ile doldurulmuş bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="e1ba9-266">Gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="e1ba9-266">Set a secret</span></span>

<span data-ttu-id="e1ba9-267">Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="e1ba9-268">Gizli dizi, projenin değeri ile ilişkilendirilir `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="e1ba9-269">Örneğin, proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="e1ba9-270">Yukarıdaki örnekte, iki nokta üst üste `Movies` bir özelliği olan bir nesne sabit değeri olduğunu gösterir `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="e1ba9-271">Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="e1ba9-272">`--project`Proje dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="e1ba9-273">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="e1ba9-274">Visual Studio 'da JSON yapısı düzleştirme</span><span class="sxs-lookup"><span data-stu-id="e1ba9-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="e1ba9-275">Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *secrets.js* dosya açar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="e1ba9-276">*Üzerindesecrets.js* içeriğini, saklanacak anahtar-değer çiftleriyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="e1ba9-277">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e1ba9-278">JSON yapısı, veya ile yapılan değişikliklerden sonra düzleştirilir `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="e1ba9-279">Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="e1ba9-280">Değiştirilen dosya aşağıdaki JSON 'a benzer:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="e1ba9-281">Birden çok gizli dizi ayarla</span><span class="sxs-lookup"><span data-stu-id="e1ba9-281">Set multiple secrets</span></span>

<span data-ttu-id="e1ba9-282">Bir dizi gizli dizi, JSON ile komutuna ayırarak ayarlanabilir `set` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="e1ba9-283">Aşağıdaki örnekte, dosyanın içeriğindeki *input.js* `set` komutuna verilmez.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="e1ba9-284">Windows</span><span class="sxs-lookup"><span data-stu-id="e1ba9-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="e1ba9-285">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="e1ba9-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e1ba9-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e1ba9-287">Bir komut kabuğu açın ve şu komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="e1ba9-288">Gizli dizi erişimi</span><span class="sxs-lookup"><span data-stu-id="e1ba9-288">Access a secret</span></span>

<span data-ttu-id="e1ba9-289">[Yapılandırma API 'si](xref:fundamentals/configuration/index) Kullanıcı gizli dizileri için erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="e1ba9-290">Projeniz .NET Framework hedefliyorsa [Microsoft.Extensions.Configbir para birimi kullanın. Usergizlilikler](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="e1ba9-291">ASP.NET Core 2,0 veya sonraki sürümlerde, Kullanıcı gizli dizileri yapılandırma kaynağı, proje çağırdığında geliştirme moduna otomatik olarak eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="e1ba9-292">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Şu olduğunda çağırır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="e1ba9-293">`CreateDefaultBuilder`Çağrılmıyorsa, oluşturucuda çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="e1ba9-294">`AddUserSecrets`Aşağıdaki örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="e1ba9-295">Kullanıcı gizli dizileri .NET yapılandırma API 'SI aracılığıyla alınabilir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="e1ba9-296">Gizli dizileri bir POCO ile eşleme</span><span class="sxs-lookup"><span data-stu-id="e1ba9-296">Map secrets to a POCO</span></span>

<span data-ttu-id="e1ba9-297">Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-298">Önceki gizli dizileri bir POCO 'ya eşlemek için .NET yapılandırma API 'sinin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="e1ba9-299">Aşağıdaki kod, özel bir `MovieSettings` poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="e1ba9-300">`Movies:ConnectionString`Ve gizli dizileri, `Movies:ServiceApiKey` içindeki ilgili özelliklerle eşlenir `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="e1ba9-301">Gizli dizileri olan dize değiştirme</span><span class="sxs-lookup"><span data-stu-id="e1ba9-301">String replacement with secrets</span></span>

<span data-ttu-id="e1ba9-302">Parolaların düz metin olarak depolanması güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="e1ba9-303">Örneğin, içinde depolanan bir veritabanı bağlantı dizesi, *appsettings.json* belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="e1ba9-304">Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="e1ba9-305">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="e1ba9-306">`Password`Anahtar-değer çiftini içindeki bağlantı dizesinden kaldırın *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1ba9-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="e1ba9-307">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="e1ba9-308">Gizli dizi değeri, <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> bağlantı dizesinin tamamlanabilmesi için bir nesnenin özelliğinde ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="e1ba9-309">Gizli dizileri listeleyin</span><span class="sxs-lookup"><span data-stu-id="e1ba9-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-310">Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="e1ba9-311">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="e1ba9-312">Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *secrets.js* içindeki nesne hiyerarşisini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="e1ba9-313">Tek bir parolayı kaldır</span><span class="sxs-lookup"><span data-stu-id="e1ba9-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-314">Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="e1ba9-315">Uygulamanın dosyada *secrets.js* , anahtarla ilişkili anahtar-değer çiftini kaldırmak için değiştirilmiştir `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="e1ba9-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e1ba9-316">Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="e1ba9-317">Tüm gizli dizileri kaldır</span><span class="sxs-lookup"><span data-stu-id="e1ba9-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e1ba9-318">Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="e1ba9-319">Uygulama için tüm Kullanıcı gizli dizileri, dosyadaki *secrets.js* silindi:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="e1ba9-320">Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e1ba9-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="e1ba9-321">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e1ba9-321">Additional resources</span></span>

* <span data-ttu-id="e1ba9-322">IIS 'deki Kullanıcı gizli bilgilerine erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.</span><span class="sxs-lookup"><span data-stu-id="e1ba9-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end