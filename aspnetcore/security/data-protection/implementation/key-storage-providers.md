---
title: ASP.NET Core 'de anahtar depolama sağlayıcıları
author: rick-anderson
description: ASP.NET Core 'de anahtar depolama sağlayıcıları ve anahtar depolama konumlarının nasıl yapılandırılacağı hakkında bilgi edinin.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: e4cf10d09c1629afb298aef0c2b86ad3bf7b646c
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057375"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="19750-103">ASP.NET Core 'de anahtar depolama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="19750-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="19750-104">Veri koruma sistemi, şifreleme anahtarlarının nerede kalıcı olacağını belirlemekte [Varsayılan olarak bir bulma mekanizması kullanır](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="19750-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="19750-105">Geliştirici varsayılan bulma mekanizmasını geçersiz kılabilir ve konumu el ile belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="19750-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="19750-106">Açık anahtar Kalıcılık konumu belirtirseniz, veri koruma sistemi REST mekanizmasında varsayılan anahtar şifrelemesini kaldırır, bu nedenle anahtarlar artık Rest 'de şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="19750-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="19750-107">Ayrıca, üretim dağıtımları için [bir açık anahtar şifreleme mekanizması belirtmeniz](xref:security/data-protection/implementation/key-encryption-at-rest) önerilir.</span><span class="sxs-lookup"><span data-stu-id="19750-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="19750-108">Dosya sistemi</span><span class="sxs-lookup"><span data-stu-id="19750-108">File system</span></span>

<span data-ttu-id="19750-109">Dosya sistemi tabanlı anahtar deposunu yapılandırmak için, aşağıda gösterildiği gibi [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) yapılandırma yordamını çağırın.</span><span class="sxs-lookup"><span data-stu-id="19750-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="19750-110">Anahtarların depolanacağı depoyu işaret eden bir [DirectoryInfo](/dotnet/api/system.io.directoryinfo) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="19750-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="19750-111">`DirectoryInfo`Yerel makinedeki bir dizine işaret edebilir veya ağ paylaşımındaki bir klasöre işaret edebilir.</span><span class="sxs-lookup"><span data-stu-id="19750-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="19750-112">Yerel makinedeki bir dizine işaret ediyorsanız (ve senaryo yalnızca yerel makinedeki uygulamaların bu depoyu kullanmak için erişim gerektirmesidir), bekleyen anahtarları şifrelemek için [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (Windows üzerinde) kullanmayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="19750-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="19750-113">Aksi takdirde, bekleyen anahtarları şifrelemek için bir [X. 509.952 sertifikası](xref:security/data-protection/implementation/key-encryption-at-rest) kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="19750-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="19750-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="19750-114">Azure Storage</span></span>

<span data-ttu-id="19750-115">[Azure. Extensions. AspNetCore. DataProtection. Blobları](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) , Azure Blob depolamada veri koruma anahtarlarının depolanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="19750-115">The [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="19750-116">Anahtarlar, bir Web uygulamasının çeşitli örnekleri arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="19750-117">Uygulamalar, kimlik doğrulama cookie veya CSRF korumasını birden çok sunucu arasında paylaşabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="19750-118">Azure Blob depolama sağlayıcısını yapılandırmak için [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) aşırı yüklemelerinin birini çağırın.</span><span class="sxs-lookup"><span data-stu-id="19750-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="19750-119">Web uygulaması bir Azure hizmeti olarak çalışıyorsa, Azure depolama 'da Azure [. Storage. blob 'ları](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient)kullanılarak kimlik doğrulaması yapmak için bağlantı dizesi kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-119">If the web app is running as an Azure service, connection string can be used to authenticate to Azure storage by using [Azure.Storage.Blobs](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient).</span></span>

```csharp
string connectionString = "<connection_string>";
string containerName = "my-key-container";
BlobContainerClient container = new BlobContainerClient(connectionString, containerName);

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

> [!NOTE]
> <span data-ttu-id="19750-120">Depolama Hesabınıza yönelik bağlantı dizesi, Azure portalında "erişim anahtarları" bölümünde bulunabilir veya aşağıdaki CLı komutu çalıştırılarak kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="19750-120">The connection string to your storage account can be found in the Azure Portal under the "Access Keys" section or by running the following CLI command:</span></span> 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a><span data-ttu-id="19750-121">Redis</span><span class="sxs-lookup"><span data-stu-id="19750-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="19750-122">[Microsoft. AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) paketi, veri koruma anahtarlarının redsıs önbelleğinde depolanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="19750-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="19750-123">Anahtarlar, bir Web uygulamasının çeşitli örnekleri arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="19750-124">Uygulamalar, kimlik doğrulama cookie veya CSRF korumasını birden çok sunucu arasında paylaşabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="19750-125">[Microsoft. AspNetCore. DataProtection. redsıs](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) paketi, veri koruma anahtarlarının redsıs önbelleğinde depolanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="19750-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="19750-126">Anahtarlar, bir Web uygulamasının çeşitli örnekleri arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="19750-127">Uygulamalar, kimlik doğrulama cookie veya CSRF korumasını birden çok sunucu arasında paylaşabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="19750-128">Redsıs üzerinde yapılandırmak için [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) aşırı yüklerinden birini çağırın:</span><span class="sxs-lookup"><span data-stu-id="19750-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="19750-129">Redsıs üzerinde yapılandırmak için [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) aşırı yüklerinden birini çağırın:</span><span class="sxs-lookup"><span data-stu-id="19750-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="19750-130">Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="19750-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="19750-131">StackExchange. Redsıs Connectionçoğullayıcı</span><span class="sxs-lookup"><span data-stu-id="19750-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="19750-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="19750-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="19750-133">ASP.NET Core DataProtection örnekleri</span><span class="sxs-lookup"><span data-stu-id="19750-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="19750-134">Kayıt Defteri</span><span class="sxs-lookup"><span data-stu-id="19750-134">Registry</span></span>

<span data-ttu-id="19750-135">**Yalnızca Windows dağıtımları için geçerlidir.**</span><span class="sxs-lookup"><span data-stu-id="19750-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="19750-136">Bazen uygulamanın dosya sistemine yazma erişimi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="19750-137">Bir uygulamanın sanal hizmet hesabı (örneğin, *w3wp.exe* uygulama havuzu kimliği) olarak çalıştığı bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="19750-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="19750-138">Bu durumlarda, yönetici hizmet hesabı kimliği tarafından erişilebilen bir kayıt defteri anahtarı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="19750-139">Aşağıda gösterildiği gibi, [Persistkeystoregistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) uzantı yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="19750-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="19750-140">Şifreleme anahtarlarının saklanacağı konuma işaret eden bir [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) belirtin:</span><span class="sxs-lookup"><span data-stu-id="19750-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys", true));
}
```

> [!IMPORTANT]
> <span data-ttu-id="19750-141">Bekleyen anahtarları şifrelemek için [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="19750-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="19750-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="19750-142">Entity Framework Core</span></span>

<span data-ttu-id="19750-143">[Microsoft. AspNetCore. DataProtection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) paketi, veri koruma anahtarlarını Entity Framework Core kullanarak bir veritabanına depolamak için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="19750-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="19750-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`NuGet paketi proje dosyasına eklenmelidir, [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'in bir parçası değil.</span><span class="sxs-lookup"><span data-stu-id="19750-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="19750-145">Bu paket ile, anahtarlar bir Web uygulamasının birden çok örneği arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="19750-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="19750-146">EF Core sağlayıcıyı yapılandırmak için, [Persistkeystodbcontext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) yöntemini çağırın:</span><span class="sxs-lookup"><span data-stu-id="19750-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="19750-147">Genel parametresi, `TContext` [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden devralması ve [ıdataprotectionkeycontext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)uygulamalıdır:</span><span class="sxs-lookup"><span data-stu-id="19750-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="19750-148">Tablo oluşturun `DataProtectionKeys` .</span><span class="sxs-lookup"><span data-stu-id="19750-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="19750-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19750-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="19750-150">**Paket Yöneticisi konsolu** (PMC) penceresinde aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="19750-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="19750-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="19750-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="19750-152">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="19750-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="19750-153">`MyKeysContext` , `DbContext` önceki kod örneğinde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="19750-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="19750-154">`DbContext`Farklı bir ada sahip kullanıyorsanız, adınızı yerine koyun `DbContext` `MyKeysContext` .</span><span class="sxs-lookup"><span data-stu-id="19750-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="19750-155">`DataProtectionKeys`Sınıf/varlık, aşağıdaki tabloda gösterilen yapıyı benimsemiştir.</span><span class="sxs-lookup"><span data-stu-id="19750-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="19750-156">Özellik/alan</span><span class="sxs-lookup"><span data-stu-id="19750-156">Property/Field</span></span> | <span data-ttu-id="19750-157">CLR türü</span><span class="sxs-lookup"><span data-stu-id="19750-157">CLR Type</span></span> | <span data-ttu-id="19750-158">SQL türü</span><span class="sxs-lookup"><span data-stu-id="19750-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="19750-159">`int`, PK, `IDENTITY(1,1)` , Not null</span><span class="sxs-lookup"><span data-stu-id="19750-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="19750-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="19750-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="19750-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="19750-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="19750-162">Özel anahtar deposu</span><span class="sxs-lookup"><span data-stu-id="19750-162">Custom key repository</span></span>

<span data-ttu-id="19750-163">Yerleşik mekanizmalar uygun değilse, geliştirici özel bir [ıxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)sağlayarak kendi anahtar Kalıcılık mekanizmasını belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="19750-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
