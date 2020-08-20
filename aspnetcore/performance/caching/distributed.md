---
title: ASP.NET Core 'de dağıtılmış önbelleğe alma
author: rick-anderson
description: Özellikle bir bulutta veya sunucu grubu ortamında uygulama performansını ve ölçeklenebilirliğini artırmak için ASP.NET Core dağıtılmış bir önbelleğin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: a25cbaf9a4e7dc5f1bd3706d01f409208a39aaa3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626733"
---
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core 'de dağıtılmış önbelleğe alma

Düzenleyen/ [mohsin Nasir](https://github.com/mohsinnasir) ve [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir. Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.

Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.

Önbelleğe alınan veriler dağıtıldığında, veriler:

* Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).
* Sunucu yeniden başlatmaları ve uygulama dağıtımları.
* Yerel bellek kullanmaz.

Dağıtılmış önbellek yapılandırması uygulamaya özgüdür. Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur. Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

SQL Server dağıtılmış önbellek kullanmak için [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.

Redsıs dağıtılmış önbelleği kullanmak için [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin.

NCache dağıtılmış önbelleğini kullanmak için [nCache. Microsoft. Extensions. Caching. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.

## <a name="idistributedcache-interface"></a>Idistributedcache arabirimi

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.

## <a name="establish-distributed-caching-services"></a>Dağıtılmış önbelleğe alma hizmetleri oluşturma

Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` . Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:

* [Dağıtılmış bellek önbelleği](#distributed-memory-cache)
* [Dağıtılmış SQL Server önbelleği](#distributed-sql-server-cache)
* [Dağıtılmış Redsıs önbelleği](#distributed-redis-cache)
* [Dağıtılmış NCache önbelleği](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Dağıtılmış bellek önbelleği

Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> . Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir. Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.

Dağıtılmış bellek önbelleği, yararlı bir uygulama:

* Geliştirme ve test senaryolarında.
* Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir. Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar. Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.

Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Dağıtılmış SQL Server önbelleği

Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir. Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz. Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.

Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` . SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:

```console
Table and index were created successfully.
```

Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları). Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.

### <a name="distributed-redis-cache"></a>Dağıtılmış Redis Cache

[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur. Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.

Bir uygulama, <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> içinde geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Redsıs 'i yerel makinenize yüklemek için:

1. [Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.
1. `redis-server`Komut isteminden çalıştırın.

### <a name="distributed-ncache-cache"></a>Dağıtılmış NCache önbelleği

[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir. NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.

Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

NCache 'yi yapılandırmak için:

1. [NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.
1. Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.
1. Aşağıdaki kodu `Startup.ConfigureServices` dosyasına ekleyin:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Dağıtılmış önbelleği kullan

Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin. Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.

Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` . Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (daha fazla bilgi için bkz. [genel konak: ıhostapplicationlifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.

Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` . Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir. Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.

Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin. Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).
>
> Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.

## <a name="recommendations"></a>Öneriler

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:

* Mevcut altyapı
* Performans gereksinimleri
* Cost
* Ekip deneyimi

Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir. Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.

Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar. Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.

SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir. Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure üzerinde Redis Cache](/azure/azure-cache-for-redis/)
* [Azure’da SQL Database](/azure/sql-database/)
* [Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir. Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.

Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.

Önbelleğe alınan veriler dağıtıldığında, veriler:

* Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).
* Sunucu yeniden başlatmaları ve uygulama dağıtımları.
* Yerel bellek kullanmaz.

Dağıtılmış önbellek yapılandırması uygulamaya özgüdür. Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur. Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.

Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin. Redsıs paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.

NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin. NCache paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle, proje dosyanızda ayrı olarak NCache paketine başvurmanız gerekir.

## <a name="idistributedcache-interface"></a>Idistributedcache arabirimi

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.

## <a name="establish-distributed-caching-services"></a>Dağıtılmış önbelleğe alma hizmetleri oluşturma

Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` . Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:

* [Dağıtılmış bellek önbelleği](#distributed-memory-cache)
* [Dağıtılmış SQL Server önbelleği](#distributed-sql-server-cache)
* [Dağıtılmış Redsıs önbelleği](#distributed-redis-cache)
* [Dağıtılmış NCache önbelleği](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Dağıtılmış bellek önbelleği

Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> . Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir. Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.

Dağıtılmış bellek önbelleği, yararlı bir uygulama:

* Geliştirme ve test senaryolarında.
* Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir. Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar. Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.

Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Dağıtılmış SQL Server önbelleği

Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir. Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz. Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.

Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` . SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:

```console
Table and index were created successfully.
```

Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları). Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.

### <a name="distributed-redis-cache"></a>Dağıtılmış Redis Cache

[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur. Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.

Bir uygulama, <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> içinde geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Redsıs 'i yerel makinenize yüklemek için:

1. [Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.
1. `redis-server`Komut isteminden çalıştırın.

### <a name="distributed-ncache-cache"></a>Dağıtılmış NCache önbelleği

[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir. NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.

Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

NCache 'yi yapılandırmak için:

1. [NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.
1. Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.
1. Aşağıdaki kodu `Startup.ConfigureServices` dosyasına ekleyin:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Dağıtılmış önbelleği kullan

Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin. Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.

Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` . Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.

Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` . Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir. Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.

Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin. Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).
>
> Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.

## <a name="recommendations"></a>Öneriler

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:

* Mevcut altyapı
* Performans gereksinimleri
* Cost
* Ekip deneyimi

Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir. Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.

Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar. Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.

SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir. Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure üzerinde Redis Cache](/azure/azure-cache-for-redis/)
* [Azure’da SQL Database](/azure/sql-database/)
* [Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir. Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.

Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.

Önbelleğe alınan veriler dağıtıldığında, veriler:

* Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).
* Sunucu yeniden başlatmaları ve uygulama dağıtımları.
* Yerel bellek kullanmaz.

Dağıtılmış önbellek yapılandırması uygulamaya özgüdür. Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur. Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.

Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. redsıs](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) paketine bir paket başvurusu ekleyin. Redsıs paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.

NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin. NCache paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle, proje dosyanızda ayrı olarak NCache paketine başvurmanız gerekir.

## <a name="idistributedcache-interface"></a>Idistributedcache arabirimi

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.

## <a name="establish-distributed-caching-services"></a>Dağıtılmış önbelleğe alma hizmetleri oluşturma

Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` . Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:

* [Dağıtılmış bellek önbelleği](#distributed-memory-cache)
* [Dağıtılmış SQL Server önbelleği](#distributed-sql-server-cache)
* [Dağıtılmış Redsıs önbelleği](#distributed-redis-cache)
* [Dağıtılmış NCache önbelleği](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Dağıtılmış bellek önbelleği

Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> . Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir. Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.

Dağıtılmış bellek önbelleği, yararlı bir uygulama:

* Geliştirme ve test senaryolarında.
* Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir. Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar. Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.

Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Dağıtılmış SQL Server önbelleği

Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir. Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz. Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.

Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` . SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:

```console
Table and index were created successfully.
```

Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları). Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.

### <a name="distributed-redis-cache"></a>Dağıtılmış Redis Cache

[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur. Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.

Bir uygulama bir <xref:Microsoft.Extensions.Caching.Redis.RedisCache> örnek () kullanarak önbellek uygulamasını yapılandırır <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> :

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Redsıs 'i yerel makinenize yüklemek için:

1. [Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.
1. `redis-server`Komut isteminden çalıştırın.

### <a name="distributed-ncache-cache"></a>Dağıtılmış NCache önbelleği

[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir. NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.

Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

NCache 'yi yapılandırmak için:

1. [NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.
1. Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.
1. Aşağıdaki kodu `Startup.ConfigureServices` dosyasına ekleyin:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Dağıtılmış önbelleği kullan

Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin. Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.

Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` . Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.

Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` . Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir. Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.

Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin. Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).
>
> Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.

## <a name="recommendations"></a>Öneriler

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:

* Mevcut altyapı
* Performans gereksinimleri
* Cost
* Ekip deneyimi

Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir. Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.

Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar. Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.

SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir. Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure üzerinde Redis Cache](/azure/azure-cache-for-redis/)
* [Azure’da SQL Database](/azure/sql-database/)
* [Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 