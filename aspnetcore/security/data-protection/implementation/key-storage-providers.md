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
# <a name="key-storage-providers-in-aspnet-core"></a>ASP.NET Core 'de anahtar depolama sağlayıcıları

Veri koruma sistemi, şifreleme anahtarlarının nerede kalıcı olacağını belirlemekte [Varsayılan olarak bir bulma mekanizması kullanır](xref:security/data-protection/configuration/default-settings) . Geliştirici varsayılan bulma mekanizmasını geçersiz kılabilir ve konumu el ile belirtebilir.

> [!WARNING]
> Açık anahtar Kalıcılık konumu belirtirseniz, veri koruma sistemi REST mekanizmasında varsayılan anahtar şifrelemesini kaldırır, bu nedenle anahtarlar artık Rest 'de şifrelenmez. Ayrıca, üretim dağıtımları için [bir açık anahtar şifreleme mekanizması belirtmeniz](xref:security/data-protection/implementation/key-encryption-at-rest) önerilir.

## <a name="file-system"></a>Dosya sistemi

Dosya sistemi tabanlı anahtar deposunu yapılandırmak için, aşağıda gösterildiği gibi [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) yapılandırma yordamını çağırın. Anahtarların depolanacağı depoyu işaret eden bir [DirectoryInfo](/dotnet/api/system.io.directoryinfo) sağlayın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`Yerel makinedeki bir dizine işaret edebilir veya ağ paylaşımındaki bir klasöre işaret edebilir. Yerel makinedeki bir dizine işaret ediyorsanız (ve senaryo yalnızca yerel makinedeki uygulamaların bu depoyu kullanmak için erişim gerektirmesidir), bekleyen anahtarları şifrelemek için [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (Windows üzerinde) kullanmayı göz önünde bulundurun. Aksi takdirde, bekleyen anahtarları şifrelemek için bir [X. 509.952 sertifikası](xref:security/data-protection/implementation/key-encryption-at-rest) kullanmayı düşünün.

## <a name="azure-storage"></a>Azure Storage

[Azure. Extensions. AspNetCore. DataProtection. Blobları](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) , Azure Blob depolamada veri koruma anahtarlarının depolanmasını sağlar. Anahtarlar, bir Web uygulamasının çeşitli örnekleri arasında paylaşılabilir. Uygulamalar, kimlik doğrulama cookie veya CSRF korumasını birden çok sunucu arasında paylaşabilir.

Azure Blob depolama sağlayıcısını yapılandırmak için [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) aşırı yüklemelerinin birini çağırın.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Web uygulaması bir Azure hizmeti olarak çalışıyorsa, Azure depolama 'da Azure [. Storage. blob 'ları](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient)kullanılarak kimlik doğrulaması yapmak için bağlantı dizesi kullanılabilir.

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
> Depolama Hesabınıza yönelik bağlantı dizesi, Azure portalında "erişim anahtarları" bölümünde bulunabilir veya aşağıdaki CLı komutu çalıştırılarak kullanılabilir: 
> ```bash
> az storage account show-connection-string --name <account_name> --resource-group <resource_group>
> ```

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

[Microsoft. AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) paketi, veri koruma anahtarlarının redsıs önbelleğinde depolanmasını sağlar. Anahtarlar, bir Web uygulamasının çeşitli örnekleri arasında paylaşılabilir. Uygulamalar, kimlik doğrulama cookie veya CSRF korumasını birden çok sunucu arasında paylaşabilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[Microsoft. AspNetCore. DataProtection. redsıs](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) paketi, veri koruma anahtarlarının redsıs önbelleğinde depolanmasını sağlar. Anahtarlar, bir Web uygulamasının çeşitli örnekleri arasında paylaşılabilir. Uygulamalar, kimlik doğrulama cookie veya CSRF korumasını birden çok sunucu arasında paylaşabilir.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Redsıs üzerinde yapılandırmak için [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) aşırı yüklerinden birini çağırın:

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

Redsıs üzerinde yapılandırmak için [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) aşırı yüklerinden birini çağırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [StackExchange. Redsıs Connectionçoğullayıcı](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [ASP.NET Core DataProtection örnekleri](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Kayıt Defteri

**Yalnızca Windows dağıtımları için geçerlidir.**

Bazen uygulamanın dosya sistemine yazma erişimi olmayabilir. Bir uygulamanın sanal hizmet hesabı (örneğin, *w3wp.exe* uygulama havuzu kimliği) olarak çalıştığı bir senaryo düşünün. Bu durumlarda, yönetici hizmet hesabı kimliği tarafından erişilebilen bir kayıt defteri anahtarı sağlayabilir. Aşağıda gösterildiği gibi, [Persistkeystoregistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) uzantı yöntemini çağırın. Şifreleme anahtarlarının saklanacağı konuma işaret eden bir [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) belirtin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys", true));
}
```

> [!IMPORTANT]
> Bekleyen anahtarları şifrelemek için [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) kullanmanızı öneririz.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[Microsoft. AspNetCore. DataProtection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) paketi, veri koruma anahtarlarını Entity Framework Core kullanarak bir veritabanına depolamak için bir mekanizma sağlar. `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`NuGet paketi proje dosyasına eklenmelidir, [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'in bir parçası değil.

Bu paket ile, anahtarlar bir Web uygulamasının birden çok örneği arasında paylaşılabilir.

EF Core sağlayıcıyı yapılandırmak için, [Persistkeystodbcontext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) yöntemini çağırın:

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Genel parametresi, `TContext` [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden devralması ve [ıdataprotectionkeycontext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)uygulamalıdır:

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Tablo oluşturun `DataProtectionKeys` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Paket Yöneticisi konsolu** (PMC) penceresinde aşağıdaki komutları yürütün:

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut kabuğu 'nda aşağıdaki komutları yürütün:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` , `DbContext` önceki kod örneğinde tanımlanır. `DbContext`Farklı bir ada sahip kullanıyorsanız, adınızı yerine koyun `DbContext` `MyKeysContext` .

`DataProtectionKeys`Sınıf/varlık, aşağıdaki tabloda gösterilen yapıyı benimsemiştir.

| Özellik/alan | CLR türü | SQL türü              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` , Not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Özel anahtar deposu

Yerleşik mekanizmalar uygun değilse, geliştirici özel bir [ıxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)sağlayarak kendi anahtar Kalıcılık mekanizmasını belirtebilir.
