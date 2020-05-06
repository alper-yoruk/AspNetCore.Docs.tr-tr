---
title: ASP.NET Core 'de dağıtılmış önbelleğe alma
author: rick-anderson
description: Özellikle bir bulutta veya sunucu grubu ortamında uygulama performansını ve ölçeklenebilirliğini artırmak için ASP.NET Core dağıtılmış bir önbelleğin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 206ff55aa530cd06c162e49f400b436e9fb9f07a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775303"
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

Dağıtılmış önbellek yapılandırması uygulamaya özgüdür. Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur. Uygulama hangi uygulamanın seçildiğine bakılmaksızın, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> arabirimi kullanılarak önbellek ile etkileşime girer.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

SQL Server dağıtılmış önbellek kullanmak için [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.

Redsıs dağıtılmış önbelleği kullanmak için [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin.

NCache dağıtılmış önbelleğini kullanmak için [nCache. Microsoft. Extensions. Caching. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.

## <a name="idistributedcache-interface"></a>Idistributedcache arabirimi

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir `byte[]` öğeyi dizi olarak alır.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Bir dize anahtarı kullanarak önbelleğe `byte[]` bir öğe (dizi olarak) ekler.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Kendi anahtarını temel alarak önbellekteki bir öğeyi yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Bir önbellek öğesini dize anahtarına göre kaldırır.

## <a name="establish-distributed-caching-services"></a>Dağıtılmış önbelleğe alma hizmetleri oluşturma

Uygulamasına bir uygulamasını <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> kaydetme `Startup.ConfigureServices`. Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:

* [Dağıtılmış bellek önbelleği](#distributed-memory-cache)
* [Dağıtılmış SQL Server önbelleği](#distributed-sql-server-cache)
* [Dağıtılmış Redsıs önbelleği](#distributed-redis-cache)
* [Dağıtılmış NCache önbelleği](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Dağıtılmış bellek önbelleği

Dağıtılmış bellek önbelleği (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamasıdır. Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir. Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.

Dağıtılmış bellek önbelleği, yararlı bir uygulama:

* Geliştirme ve test senaryolarında.
* Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir. Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar. Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.

Örnek uygulama, uygulamasında `Startup.ConfigureServices`geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Dağıtılmış SQL Server önbelleği

Dağıtılmış SQL Server önbellek uygulama (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir. Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz. Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.

`sql-cache create` Komutunu çalıştırarak SQL Server tablo oluşturun. SQL Server`Data Source`örneği (), veritabanı`Initial Catalog`(), şema (örneğin `dbo`,) ve tablo adı (örneğin, `TestCache`) sağlayın:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:

```console
Table and index were created successfully.
```

`sql-cache` Araç tarafından oluşturulan tablo aşağıdaki şemaya sahiptir:

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Bir uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, bir <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>örneğini kullanarak önbellek değerlerini işlemelidir.

Örnek uygulama, içinde <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`geliştirme dışı bir ortamda uygular:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (Ve isteğe bağlı olarak <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> , <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>ve) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) veya *appSettings. JSON*/*appSettings içinde depolanır. { ENVIRONMENT}. JSON* dosyaları). Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.

### <a name="distributed-redis-cache"></a>Dağıtılmış Redis Cache

[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur. Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.

Bir uygulama, içinde <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices`geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Redsıs 'i yerel makinenize yüklemek için:

1. [Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.
1. Komut `redis-server` isteminden çalıştırın.

### <a name="distributed-ncache-cache"></a>Dağıtılmış NCache önbelleği

[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir. NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.

Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

NCache 'yi yapılandırmak için:

1. [NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.
1. Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.
1. Aşağıdaki kodu öğesine `Startup.ConfigureServices`ekleyin:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Dağıtılmış önbelleği kullan

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Arabirimini kullanmak için, uygulamadaki herhangi bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> oluşturucudan bir örnek isteyin. Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.

Örnek uygulama başlatıldığında öğesine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.Configure`eklenir. Şu anki süre kullanılarak <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> önbelleğe alındı (daha fazla bilgi için bkz. [genel konak: ıhostapplicationlifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Örnek uygulama, `IndexModel` Dizin sayfası <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> tarafından kullanılmak üzere içine çıkarır.

Dizin sayfası her yüklendiğinde, önbellek, içindeki `OnGetAsync`önbelleğe alınmış zamana göre denetlenir. Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir. Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.

Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin. Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Örnekler için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez (en azından yerleşik uygulamalar için).
>
> Ayrıca, DI kullanmak yerine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.

## <a name="recommendations"></a>Öneriler

Uygulamanız için hangi uygulamanın en <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:

* Mevcut altyapı
* Performans gereksinimleri
* Maliyet
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

Dağıtılmış önbellek yapılandırması uygulamaya özgüdür. Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur. Uygulama hangi uygulamanın seçildiğine bakılmaksızın, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> arabirimi kullanılarak önbellek ile etkileşime girer.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.

Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin. Redsıs paketi `Microsoft.AspNetCore.App` pakete dahil değildir, bu nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.

NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin. NCache paketi `Microsoft.AspNetCore.App` pakete dahil değildir, bu nedenle, proje dosyanızda ayrı olarak nCache paketine başvurmanız gerekir.

## <a name="idistributedcache-interface"></a>Idistributedcache arabirimi

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir `byte[]` öğeyi dizi olarak alır.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Bir dize anahtarı kullanarak önbelleğe `byte[]` bir öğe (dizi olarak) ekler.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Kendi anahtarını temel alarak önbellekteki bir öğeyi yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Bir önbellek öğesini dize anahtarına göre kaldırır.

## <a name="establish-distributed-caching-services"></a>Dağıtılmış önbelleğe alma hizmetleri oluşturma

Uygulamasına bir uygulamasını <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> kaydetme `Startup.ConfigureServices`. Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:

* [Dağıtılmış bellek önbelleği](#distributed-memory-cache)
* [Dağıtılmış SQL Server önbelleği](#distributed-sql-server-cache)
* [Dağıtılmış Redsıs önbelleği](#distributed-redis-cache)
* [Dağıtılmış NCache önbelleği](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Dağıtılmış bellek önbelleği

Dağıtılmış bellek önbelleği (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamasıdır. Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir. Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.

Dağıtılmış bellek önbelleği, yararlı bir uygulama:

* Geliştirme ve test senaryolarında.
* Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir. Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar. Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.

Örnek uygulama, uygulamasında `Startup.ConfigureServices`geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Dağıtılmış SQL Server önbelleği

Dağıtılmış SQL Server önbellek uygulama (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir. Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz. Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.

`sql-cache create` Komutunu çalıştırarak SQL Server tablo oluşturun. SQL Server`Data Source`örneği (), veritabanı`Initial Catalog`(), şema (örneğin `dbo`,) ve tablo adı (örneğin, `TestCache`) sağlayın:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:

```console
Table and index were created successfully.
```

`sql-cache` Araç tarafından oluşturulan tablo aşağıdaki şemaya sahiptir:

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Bir uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, bir <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>örneğini kullanarak önbellek değerlerini işlemelidir.

Örnek uygulama, içinde <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`geliştirme dışı bir ortamda uygular:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (Ve isteğe bağlı olarak <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> , <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>ve) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) veya *appSettings. JSON*/*appSettings içinde depolanır. { ENVIRONMENT}. JSON* dosyaları). Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.

### <a name="distributed-redis-cache"></a>Dağıtılmış Redis Cache

[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur. Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.

Bir uygulama, içinde <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices`geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Redsıs 'i yerel makinenize yüklemek için:

1. [Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.
1. Komut `redis-server` isteminden çalıştırın.

### <a name="distributed-ncache-cache"></a>Dağıtılmış NCache önbelleği

[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir. NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.

Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

NCache 'yi yapılandırmak için:

1. [NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.
1. Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.
1. Aşağıdaki kodu öğesine `Startup.ConfigureServices`ekleyin:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Dağıtılmış önbelleği kullan

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Arabirimini kullanmak için, uygulamadaki herhangi bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> oluşturucudan bir örnek isteyin. Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.

Örnek uygulama başlatıldığında öğesine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.Configure`eklenir. Şu anki süre kullanılarak <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> önbelleğe alındı (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Örnek uygulama, `IndexModel` Dizin sayfası <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> tarafından kullanılmak üzere içine çıkarır.

Dizin sayfası her yüklendiğinde, önbellek, içindeki `OnGetAsync`önbelleğe alınmış zamana göre denetlenir. Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir. Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.

Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin. Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Örnekler için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez (en azından yerleşik uygulamalar için).
>
> Ayrıca, DI kullanmak yerine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.

## <a name="recommendations"></a>Öneriler

Uygulamanız için hangi uygulamanın en <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:

* Mevcut altyapı
* Performans gereksinimleri
* Maliyet
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

Dağıtılmış önbellek yapılandırması uygulamaya özgüdür. Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur. Uygulama hangi uygulamanın seçildiğine bakılmaksızın, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> arabirimi kullanılarak önbellek ile etkileşime girer.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.

Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. redsıs](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) paketine bir paket başvurusu ekleyin. Redsıs paketi `Microsoft.AspNetCore.App` pakete dahil değildir, bu nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.

NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin. NCache paketi `Microsoft.AspNetCore.App` pakete dahil değildir, bu nedenle, proje dosyanızda ayrı olarak nCache paketine başvurmanız gerekir.

## <a name="idistributedcache-interface"></a>Idistributedcache arabirimi

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir `byte[]` öğeyi dizi olarak alır.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Bir dize anahtarı kullanarak önbelleğe `byte[]` bir öğe (dizi olarak) ekler.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Kendi anahtarını temel alarak önbellekteki bir öğeyi yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Bir önbellek öğesini dize anahtarına göre kaldırır.

## <a name="establish-distributed-caching-services"></a>Dağıtılmış önbelleğe alma hizmetleri oluşturma

Uygulamasına bir uygulamasını <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> kaydetme `Startup.ConfigureServices`. Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:

* [Dağıtılmış bellek önbelleği](#distributed-memory-cache)
* [Dağıtılmış SQL Server önbelleği](#distributed-sql-server-cache)
* [Dağıtılmış Redsıs önbelleği](#distributed-redis-cache)
* [Dağıtılmış NCache önbelleği](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Dağıtılmış bellek önbelleği

Dağıtılmış bellek önbelleği (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamasıdır. Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir. Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.

Dağıtılmış bellek önbelleği, yararlı bir uygulama:

* Geliştirme ve test senaryolarında.
* Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir. Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar. Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.

Örnek uygulama, uygulamasında `Startup.ConfigureServices`geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Dağıtılmış SQL Server önbelleği

Dağıtılmış SQL Server önbellek uygulama (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir. Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz. Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.

`sql-cache create` Komutunu çalıştırarak SQL Server tablo oluşturun. SQL Server`Data Source`örneği (), veritabanı`Initial Catalog`(), şema (örneğin `dbo`,) ve tablo adı (örneğin, `TestCache`) sağlayın:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:

```console
Table and index were created successfully.
```

`sql-cache` Araç tarafından oluşturulan tablo aşağıdaki şemaya sahiptir:

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Bir uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, bir <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>örneğini kullanarak önbellek değerlerini işlemelidir.

Örnek uygulama, içinde <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`geliştirme dışı bir ortamda uygular:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (Ve isteğe bağlı olarak <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> , <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>ve) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) veya *appSettings. JSON*/*appSettings içinde depolanır. { ENVIRONMENT}. JSON* dosyaları). Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.

### <a name="distributed-redis-cache"></a>Dağıtılmış Redis Cache

[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur. Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.

Bir uygulama bir <xref:Microsoft.Extensions.Caching.Redis.RedisCache> örnek (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>) kullanarak önbellek uygulamasını yapılandırır:

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Redsıs 'i yerel makinenize yüklemek için:

1. [Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.
1. Komut `redis-server` isteminden çalıştırın.

### <a name="distributed-ncache-cache"></a>Dağıtılmış NCache önbelleği

[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir. NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.

Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

NCache 'yi yapılandırmak için:

1. [NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.
1. Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.
1. Aşağıdaki kodu öğesine `Startup.ConfigureServices`ekleyin:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Dağıtılmış önbelleği kullan

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Arabirimini kullanmak için, uygulamadaki herhangi bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> oluşturucudan bir örnek isteyin. Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.

Örnek uygulama başlatıldığında öğesine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.Configure`eklenir. Şu anki süre kullanılarak <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> önbelleğe alındı (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Örnek uygulama, `IndexModel` Dizin sayfası <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> tarafından kullanılmak üzere içine çıkarır.

Dizin sayfası her yüklendiğinde, önbellek, içindeki `OnGetAsync`önbelleğe alınmış zamana göre denetlenir. Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir. Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.

Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin. Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Örnekler için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez (en azından yerleşik uygulamalar için).
>
> Ayrıca, DI kullanmak yerine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.

## <a name="recommendations"></a>Öneriler

Uygulamanız için hangi uygulamanın en <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:

* Mevcut altyapı
* Performans gereksinimleri
* Maliyet
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
 