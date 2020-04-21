---
title: ASP.NET Core'da uygulama sırlarınıgeliştirmede güvenli depolama
author: rick-anderson
description: bir ASP.NET Core uygulaması nın geliştirilmesi sırasında hassas bilgileri uygulama sırları olarak nasıl depolayıp edineceklerini öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661800"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="3fe19-103">ASP.NET Core'da uygulama sırlarınıgeliştirmede güvenli depolama</span><span class="sxs-lookup"><span data-stu-id="3fe19-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3fe19-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), ve [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3fe19-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3fe19-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3fe19-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3fe19-106">Bu belge, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması tekniklerini açıklar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="3fe19-107">Parolaları veya diğer hassas verileri asla kaynak kodunda depolamayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="3fe19-108">Üretim sırları geliştirme veya test için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="3fe19-109">Sırlar uygulamayla birlikte dağıtılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="3fe19-110">Bunun yerine, sırlar çevre değişkenleri, Azure Anahtar Kasası vb. gibi kontrollü bir yolla üretim ortamında kullanıma sunulmalıdır. Azure Test ve üretim sırlarını Azure [Anahtar Kasası yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3fe19-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="3fe19-111">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3fe19-111">Environment variables</span></span>

<span data-ttu-id="3fe19-112">Ortam değişkenleri, uygulama sırlarını kodda veya yerel yapılandırma dosyalarında depolamaktan kaçınmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="3fe19-113">Ortam değişkenleri, önceden belirtilmiş tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="3fe19-114">**Bireysel Kullanıcı Hesapları** güvenliğinin etkin olduğu bir ASP.NET Core web uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="3fe19-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="3fe19-115">Varsayılan veritabanı bağlantı dizesi, projenin *appsettings.json* dosyasında `DefaultConnection`anahtarla birlikte yer alır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="3fe19-116">Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="3fe19-117">Uygulama dağıtımı `DefaultConnection` sırasında, anahtar değeri bir ortam değişkeninin değeriyle geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="3fe19-118">Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle depolayabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="3fe19-119">Ortam değişkenleri genellikle düz, şifrelenmemiş metinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="3fe19-120">Makine veya işlem tehlikeye girerse, ortam değişkenlerine güvenilmeyen taraflarca erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="3fe19-121">Kullanıcı sırlarının açıklanmasını önlemek için ek önlemler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="3fe19-122">Gizli Müdür</span><span class="sxs-lookup"><span data-stu-id="3fe19-122">Secret Manager</span></span>

<span data-ttu-id="3fe19-123">Gizli Yönetici aracı, bir ASP.NET Core projesinin geliştirilmesi sırasında hassas verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="3fe19-124">Bu bağlamda, hassas verilerin bir parçası bir uygulama gizlidir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="3fe19-125">Uygulama sırları proje ağacından ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="3fe19-126">Uygulama sırları belirli bir projeyle ilişkilendirilir veya çeşitli projelerde paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="3fe19-127">Uygulama sırları kaynak denetimine girmez.</span><span class="sxs-lookup"><span data-stu-id="3fe19-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="3fe19-128">Gizli Yönetici aracı depolanan sırları şifrelemez ve güvenilir bir mağaza olarak ele alınamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="3fe19-129">Sadece geliştirme amaçlı.</span><span class="sxs-lookup"><span data-stu-id="3fe19-129">It's for development purposes only.</span></span> <span data-ttu-id="3fe19-130">Anahtarlar ve değerler, kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="3fe19-131">Gizli Yönetici aracı nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="3fe19-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="3fe19-132">Gizli Yönetici aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="3fe19-133">Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3fe19-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="3fe19-134">Değerler, yerel makinedeki sistem korumalı kullanıcı profili klasöründe JSON yapılandırma dosyasında depolanır:</span><span class="sxs-lookup"><span data-stu-id="3fe19-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="3fe19-135">Windows</span><span class="sxs-lookup"><span data-stu-id="3fe19-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="3fe19-136">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="3fe19-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="3fe19-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="3fe19-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="3fe19-138">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="3fe19-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="3fe19-139">Önceki dosya `<user_secrets_id>` yollarında,.csproj dosyasında belirtilen `UserSecretsId` değerle değiştirin. *.csproj*</span><span class="sxs-lookup"><span data-stu-id="3fe19-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="3fe19-140">Gizli Yönetici aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazmayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="3fe19-141">Bu uygulama ayrıntıları değişebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-141">These implementation details may change.</span></span> <span data-ttu-id="3fe19-142">Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="3fe19-143">Gizli depolamayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="3fe19-143">Enable secret storage</span></span>

<span data-ttu-id="3fe19-144">Gizli Yönetici aracı, kullanıcı profilinizde depolanan projeye özel yapılandırma ayarlarında çalışır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="3fe19-145">Gizli Yönetici aracı `init` .NET Core SDK 3.0.100 veya sonrası bir komut içerir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="3fe19-146">Kullanıcı sırlarını kullanmak için proje dizininde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="3fe19-147">Önceki komut `UserSecretsId` *.csproj* dosyasının içinde bir `PropertyGroup` öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="3fe19-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="3fe19-148">Varsayılan olarak, iç `UserSecretsId` metin bir GUID'dir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="3fe19-149">İç metin rasgele, ancak projeye özgüdür.</span><span class="sxs-lookup"><span data-stu-id="3fe19-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="3fe19-150">Visual Studio'da, Solution Explorer'da projeyi sağ tıklatın ve bağlam menüsünden **Kullanıcı Sırlarını Yönet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="3fe19-151">Bu hareket, `UserSecretsId` *.csproj* dosyasına GUID ile doldurulan bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="3fe19-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="3fe19-152">Bir sır ayarlayın</span><span class="sxs-lookup"><span data-stu-id="3fe19-152">Set a secret</span></span>

<span data-ttu-id="3fe19-153">Bir anahtar ve değerinden oluşan bir uygulama sırrı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="3fe19-154">Gizli projenin `UserSecretsId` değeri ile ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="3fe19-155">Örneğin, *.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="3fe19-156">Önceki örnekte, iki nokta üst `Movies` üste bir `ServiceApiKey` özelliği olan bir nesne nin gerçek olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="3fe19-157">Gizli Yönetici aracı diğer dizinlerden de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="3fe19-158">`--project` *.csproj* dosyasının bulunduğu dosya sistemi yolunu sağlama seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="3fe19-159">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="3fe19-160">Visual Studio'da JSON yapısı düzleme</span><span class="sxs-lookup"><span data-stu-id="3fe19-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="3fe19-161">Visual Studio'nun **Kullanıcı Sırlarını Yönet** hareketi metin düzenleyicisinde bir *secrets.json* dosyasını açar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="3fe19-162">*Secrets.json'ın* içeriğini depolanacak anahtar değeri çiftleri ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="3fe19-163">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="3fe19-164">JSON yapısı üzerinden değişiklikler den sonra `dotnet user-secrets remove` `dotnet user-secrets set`düzleştirilmiş veya .</span><span class="sxs-lookup"><span data-stu-id="3fe19-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="3fe19-165">Örneğin, çalışan `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne nin gerçek olarak daraltıyor.</span><span class="sxs-lookup"><span data-stu-id="3fe19-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="3fe19-166">Değiştirilen dosya aşağıdakilere benzer:</span><span class="sxs-lookup"><span data-stu-id="3fe19-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="3fe19-167">Birden çok sır ayarlama</span><span class="sxs-lookup"><span data-stu-id="3fe19-167">Set multiple secrets</span></span>

<span data-ttu-id="3fe19-168">JSON'un `set` komutasına boru lanmasıyla bir dizi sır belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="3fe19-169">Aşağıdaki örnekte, *input.json* dosyasının içeriği `set` komuta aktarılır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="3fe19-170">Windows</span><span class="sxs-lookup"><span data-stu-id="3fe19-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="3fe19-171">Komut kabuğunu açın ve aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="3fe19-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="3fe19-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="3fe19-173">Komut kabuğunu açın ve aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="3fe19-174">Bir gizliye erişin</span><span class="sxs-lookup"><span data-stu-id="3fe19-174">Access a secret</span></span>

<span data-ttu-id="3fe19-175">[ASP.NET Core Configuration API,](xref:fundamentals/configuration/index) Gizli Yönetici sırlarına erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="3fe19-176">Core 2.0 veya daha sonra ASP.NET, proje önceden yapılandırılmış varsayılanları ile <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> ana bilgisayar yeni bir örnek başlatmaya çağırır kullanıcı sırları yapılandırma kaynağı otomatik olarak geliştirme moduna eklenir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-176">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="3fe19-177">`CreateDefaultBuilder`olduğunda <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>çağırır:</span><span class="sxs-lookup"><span data-stu-id="3fe19-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="3fe19-178">Çağrılmadığı zaman, `CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` oluşturucuyu çağırarak kullanıcı sırları yapılandırma kaynağını açıkça ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="3fe19-179">Aşağıdaki `AddUserSecrets` örnekte gösterildiği gibi, yalnızca uygulama Geliştirme ortamında çalıştığında arama:</span><span class="sxs-lookup"><span data-stu-id="3fe19-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="3fe19-180">Kullanıcı sırları `Configuration` API üzerinden alınabilir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="3fe19-181">Bir POCO'ya harita sırları</span><span class="sxs-lookup"><span data-stu-id="3fe19-181">Map secrets to a POCO</span></span>

<span data-ttu-id="3fe19-182">Tüm nesnenin gerçek bir nesnesini bir POCO (özellikleri olan basit bir .NET sınıfı) ile eşleme, ilgili özellikleri bir araya getirmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-183">Önceki sırları bir POCO ile eşlemek `Configuration` için API'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="3fe19-184">Aşağıdaki kod özel `MovieSettings` bir POCO'ya bağlanır `ServiceApiKey` ve özellik değerine erişir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="3fe19-185">Ve sırlar ilgili özelliklere `MovieSettings`eşlenir: `Movies:ServiceApiKey` `Movies:ConnectionString`</span><span class="sxs-lookup"><span data-stu-id="3fe19-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="3fe19-186">Sırlar ile string değiştirme</span><span class="sxs-lookup"><span data-stu-id="3fe19-186">String replacement with secrets</span></span>

<span data-ttu-id="3fe19-187">Parolaları düz metinde depolamak güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="3fe19-188">Örneğin, *appsettings.json'da* depolanan bir veritabanı bağlantı dizesi, belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="3fe19-189">Daha güvenli bir yaklaşım, parolayı bir sır olarak depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="3fe19-190">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="3fe19-191">`Password` *appsettings.json'daki*bağlantı dizesinden anahtar değeri çiftini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="3fe19-192">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-192">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="3fe19-193">Gizlinin değeri, bağlantı dizesini tamamlamak için bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> özelliğine ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="3fe19-194">Sırları listele</span><span class="sxs-lookup"><span data-stu-id="3fe19-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-195">*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="3fe19-196">Şu çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="3fe19-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="3fe19-197">Önceki örnekte, anahtar adlarında bir üst üste *secrets.json*içindeki nesne hiyerarşisini gösterir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="3fe19-198">Tek bir sırrı kaldırma</span><span class="sxs-lookup"><span data-stu-id="3fe19-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-199">*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="3fe19-200">Uygulamanın *secrets.json* `MoviesConnectionString` dosyası, anahtarla ilişkili anahtar değeri çiftini kaldırmak için değiştirildi:</span><span class="sxs-lookup"><span data-stu-id="3fe19-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="3fe19-201">`dotnet user-secrets list`aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3fe19-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="3fe19-202">Tüm sırları kaldırın</span><span class="sxs-lookup"><span data-stu-id="3fe19-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-203">*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="3fe19-204">Uygulamanın tüm kullanıcı sırları *secrets.json* dosyasından silindi:</span><span class="sxs-lookup"><span data-stu-id="3fe19-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="3fe19-205">Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3fe19-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="3fe19-206">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3fe19-206">Additional resources</span></span>

* <span data-ttu-id="3fe19-207">IIS'den Gizli Yönetici'ye erişim hakkında bilgi için [bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3fe19-208">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), ve [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3fe19-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3fe19-209">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3fe19-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3fe19-210">Bu belge, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması tekniklerini açıklar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="3fe19-211">Parolaları veya diğer hassas verileri asla kaynak kodunda depolamayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="3fe19-212">Üretim sırları geliştirme veya test için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="3fe19-213">Sırlar uygulamayla birlikte dağıtılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="3fe19-214">Bunun yerine, sırlar çevre değişkenleri, Azure Anahtar Kasası vb. gibi kontrollü bir yolla üretim ortamında kullanıma sunulmalıdır. Azure Test ve üretim sırlarını Azure [Anahtar Kasası yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3fe19-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="3fe19-215">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="3fe19-215">Environment variables</span></span>

<span data-ttu-id="3fe19-216">Ortam değişkenleri, uygulama sırlarını kodda veya yerel yapılandırma dosyalarında depolamaktan kaçınmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="3fe19-217">Ortam değişkenleri, önceden belirtilmiş tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="3fe19-218">**Bireysel Kullanıcı Hesapları** güvenliğinin etkin olduğu bir ASP.NET Core web uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="3fe19-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="3fe19-219">Varsayılan veritabanı bağlantı dizesi, projenin *appsettings.json* dosyasında `DefaultConnection`anahtarla birlikte yer alır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="3fe19-220">Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="3fe19-221">Uygulama dağıtımı `DefaultConnection` sırasında, anahtar değeri bir ortam değişkeninin değeriyle geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="3fe19-222">Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle depolayabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="3fe19-223">Ortam değişkenleri genellikle düz, şifrelenmemiş metinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="3fe19-224">Makine veya işlem tehlikeye girerse, ortam değişkenlerine güvenilmeyen taraflarca erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="3fe19-225">Kullanıcı sırlarının açıklanmasını önlemek için ek önlemler gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="3fe19-226">Gizli Müdür</span><span class="sxs-lookup"><span data-stu-id="3fe19-226">Secret Manager</span></span>

<span data-ttu-id="3fe19-227">Gizli Yönetici aracı, bir ASP.NET Core projesinin geliştirilmesi sırasında hassas verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="3fe19-228">Bu bağlamda, hassas verilerin bir parçası bir uygulama gizlidir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="3fe19-229">Uygulama sırları proje ağacından ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="3fe19-230">Uygulama sırları belirli bir projeyle ilişkilendirilir veya çeşitli projelerde paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="3fe19-231">Uygulama sırları kaynak denetimine girmez.</span><span class="sxs-lookup"><span data-stu-id="3fe19-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="3fe19-232">Gizli Yönetici aracı depolanan sırları şifrelemez ve güvenilir bir mağaza olarak ele alınamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="3fe19-233">Sadece geliştirme amaçlı.</span><span class="sxs-lookup"><span data-stu-id="3fe19-233">It's for development purposes only.</span></span> <span data-ttu-id="3fe19-234">Anahtarlar ve değerler, kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="3fe19-235">Gizli Yönetici aracı nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="3fe19-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="3fe19-236">Gizli Yönetici aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="3fe19-237">Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3fe19-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="3fe19-238">Değerler, yerel makinedeki sistem korumalı kullanıcı profili klasöründe JSON yapılandırma dosyasında depolanır:</span><span class="sxs-lookup"><span data-stu-id="3fe19-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="3fe19-239">Windows</span><span class="sxs-lookup"><span data-stu-id="3fe19-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="3fe19-240">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="3fe19-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="3fe19-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="3fe19-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="3fe19-242">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="3fe19-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="3fe19-243">Önceki dosya `<user_secrets_id>` yollarında,.csproj dosyasında belirtilen `UserSecretsId` değerle değiştirin. *.csproj*</span><span class="sxs-lookup"><span data-stu-id="3fe19-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="3fe19-244">Gizli Yönetici aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazmayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="3fe19-245">Bu uygulama ayrıntıları değişebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-245">These implementation details may change.</span></span> <span data-ttu-id="3fe19-246">Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="3fe19-247">Gizli depolamayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="3fe19-247">Enable secret storage</span></span>

<span data-ttu-id="3fe19-248">Gizli Yönetici aracı, kullanıcı profilinizde depolanan projeye özel yapılandırma ayarlarında çalışır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="3fe19-249">Kullanıcı sırlarını kullanmak için `UserSecretsId` `PropertyGroup` *.csproj* dosyasının içindeki bir öğeyi tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="3fe19-250">İç metin `UserSecretsId` rasgele, ancak projeye özgüdür.</span><span class="sxs-lookup"><span data-stu-id="3fe19-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="3fe19-251">Geliştiriciler genellikle `UserSecretsId`bir GUID oluşturmak için .</span><span class="sxs-lookup"><span data-stu-id="3fe19-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="3fe19-252">Visual Studio'da, Solution Explorer'da projeyi sağ tıklatın ve bağlam menüsünden **Kullanıcı Sırlarını Yönet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="3fe19-253">Bu hareket, `UserSecretsId` *.csproj* dosyasına GUID ile doldurulan bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="3fe19-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="3fe19-254">Bir sır ayarlayın</span><span class="sxs-lookup"><span data-stu-id="3fe19-254">Set a secret</span></span>

<span data-ttu-id="3fe19-255">Bir anahtar ve değerinden oluşan bir uygulama sırrı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="3fe19-256">Gizli projenin `UserSecretsId` değeri ile ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="3fe19-257">Örneğin, *.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="3fe19-258">Önceki örnekte, iki nokta üst `Movies` üste bir `ServiceApiKey` özelliği olan bir nesne nin gerçek olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="3fe19-259">Gizli Yönetici aracı diğer dizinlerden de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="3fe19-260">`--project` *.csproj* dosyasının bulunduğu dosya sistemi yolunu sağlama seçeneğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="3fe19-261">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="3fe19-262">Visual Studio'da JSON yapısı düzleme</span><span class="sxs-lookup"><span data-stu-id="3fe19-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="3fe19-263">Visual Studio'nun **Kullanıcı Sırlarını Yönet** hareketi metin düzenleyicisinde bir *secrets.json* dosyasını açar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="3fe19-264">*Secrets.json'ın* içeriğini depolanacak anahtar değeri çiftleri ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="3fe19-265">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="3fe19-266">JSON yapısı üzerinden değişiklikler den sonra `dotnet user-secrets remove` `dotnet user-secrets set`düzleştirilmiş veya .</span><span class="sxs-lookup"><span data-stu-id="3fe19-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="3fe19-267">Örneğin, çalışan `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne nin gerçek olarak daraltıyor.</span><span class="sxs-lookup"><span data-stu-id="3fe19-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="3fe19-268">Değiştirilen dosya aşağıdakilere benzer:</span><span class="sxs-lookup"><span data-stu-id="3fe19-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="3fe19-269">Birden çok sır ayarlama</span><span class="sxs-lookup"><span data-stu-id="3fe19-269">Set multiple secrets</span></span>

<span data-ttu-id="3fe19-270">JSON'un `set` komutasına boru lanmasıyla bir dizi sır belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="3fe19-271">Aşağıdaki örnekte, *input.json* dosyasının içeriği `set` komuta aktarılır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="3fe19-272">Windows</span><span class="sxs-lookup"><span data-stu-id="3fe19-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="3fe19-273">Komut kabuğunu açın ve aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="3fe19-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="3fe19-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="3fe19-275">Komut kabuğunu açın ve aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="3fe19-276">Bir gizliye erişin</span><span class="sxs-lookup"><span data-stu-id="3fe19-276">Access a secret</span></span>

<span data-ttu-id="3fe19-277">[ASP.NET Core Configuration API,](xref:fundamentals/configuration/index) Gizli Yönetici sırlarına erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="3fe19-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="3fe19-278">Projeniz .NET Framework'u [hedefliyorsa, Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>


<span data-ttu-id="3fe19-279">Core 2.0 veya daha sonra ASP.NET, proje önceden yapılandırılmış varsayılanları ile <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> ana bilgisayar yeni bir örnek başlatmaya çağırır kullanıcı sırları yapılandırma kaynağı otomatik olarak geliştirme moduna eklenir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="3fe19-280">`CreateDefaultBuilder`olduğunda <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>çağırır:</span><span class="sxs-lookup"><span data-stu-id="3fe19-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


<span data-ttu-id="3fe19-281">Çağrılmadığı zaman, `CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` oluşturucuyu çağırarak kullanıcı sırları yapılandırma kaynağını açıkça ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3fe19-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="3fe19-282">Aşağıdaki `AddUserSecrets` örnekte gösterildiği gibi, yalnızca uygulama Geliştirme ortamında çalıştığında arama:</span><span class="sxs-lookup"><span data-stu-id="3fe19-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="3fe19-283">Kullanıcı sırları `Configuration` API üzerinden alınabilir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="3fe19-284">Bir POCO'ya harita sırları</span><span class="sxs-lookup"><span data-stu-id="3fe19-284">Map secrets to a POCO</span></span>

<span data-ttu-id="3fe19-285">Tüm nesnenin gerçek bir nesnesini bir POCO (özellikleri olan basit bir .NET sınıfı) ile eşleme, ilgili özellikleri bir araya getirmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-286">Önceki sırları bir POCO ile eşlemek `Configuration` için API'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="3fe19-287">Aşağıdaki kod özel `MovieSettings` bir POCO'ya bağlanır `ServiceApiKey` ve özellik değerine erişir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="3fe19-288">Ve sırlar ilgili özelliklere `MovieSettings`eşlenir: `Movies:ServiceApiKey` `Movies:ConnectionString`</span><span class="sxs-lookup"><span data-stu-id="3fe19-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="3fe19-289">Sırlar ile string değiştirme</span><span class="sxs-lookup"><span data-stu-id="3fe19-289">String replacement with secrets</span></span>

<span data-ttu-id="3fe19-290">Parolaları düz metinde depolamak güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="3fe19-291">Örneğin, *appsettings.json'da* depolanan bir veritabanı bağlantı dizesi, belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="3fe19-292">Daha güvenli bir yaklaşım, parolayı bir sır olarak depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="3fe19-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="3fe19-293">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="3fe19-294">`Password` *appsettings.json'daki*bağlantı dizesinden anahtar değeri çiftini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="3fe19-295">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3fe19-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="3fe19-296">Gizlinin değeri, bağlantı dizesini tamamlamak için bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> özelliğine ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="3fe19-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="3fe19-297">Sırları listele</span><span class="sxs-lookup"><span data-stu-id="3fe19-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-298">*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="3fe19-299">Şu çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="3fe19-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="3fe19-300">Önceki örnekte, anahtar adlarında bir üst üste *secrets.json*içindeki nesne hiyerarşisini gösterir.</span><span class="sxs-lookup"><span data-stu-id="3fe19-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="3fe19-301">Tek bir sırrı kaldırma</span><span class="sxs-lookup"><span data-stu-id="3fe19-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-302">*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="3fe19-303">Uygulamanın *secrets.json* `MoviesConnectionString` dosyası, anahtarla ilişkili anahtar değeri çiftini kaldırmak için değiştirildi:</span><span class="sxs-lookup"><span data-stu-id="3fe19-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="3fe19-304">Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3fe19-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="3fe19-305">Tüm sırları kaldırın</span><span class="sxs-lookup"><span data-stu-id="3fe19-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="3fe19-306">*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3fe19-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="3fe19-307">Uygulamanın tüm kullanıcı sırları *secrets.json* dosyasından silindi:</span><span class="sxs-lookup"><span data-stu-id="3fe19-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="3fe19-308">Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3fe19-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="3fe19-309">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3fe19-309">Additional resources</span></span>

* <span data-ttu-id="3fe19-310">IIS'den Gizli Yönetici'ye erişim hakkında bilgi için [bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.</span><span class="sxs-lookup"><span data-stu-id="3fe19-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end