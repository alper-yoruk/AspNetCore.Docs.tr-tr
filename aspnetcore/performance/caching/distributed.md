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
ms.openlocfilehash: 0d27206412a098f4ea749ec10189bf24d2322de1
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712486"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="714d6-103">ASP.NET Core 'de dağıtılmış önbelleğe alma</span><span class="sxs-lookup"><span data-stu-id="714d6-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="714d6-104">Düzenleyen/ [mohsin Nasir](https://github.com/mohsinnasir) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="714d6-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="714d6-105">Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="714d6-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="714d6-106">Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="714d6-107">Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.</span><span class="sxs-lookup"><span data-stu-id="714d6-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="714d6-108">Önbelleğe alınan veriler dağıtıldığında, veriler:</span><span class="sxs-lookup"><span data-stu-id="714d6-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="714d6-109">Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).</span><span class="sxs-lookup"><span data-stu-id="714d6-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="714d6-110">Sunucu yeniden başlatmaları ve uygulama dağıtımları.</span><span class="sxs-lookup"><span data-stu-id="714d6-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="714d6-111">Yerel bellek kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="714d6-111">Doesn't use local memory.</span></span>

<span data-ttu-id="714d6-112">Dağıtılmış önbellek yapılandırması uygulamaya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="714d6-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="714d6-113">Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="714d6-114">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="714d6-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="714d6-115">Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="714d6-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="714d6-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="714d6-117">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="714d6-117">Prerequisites</span></span>

<span data-ttu-id="714d6-118">SQL Server dağıtılmış önbellek kullanmak için [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="714d6-119">Redsıs dağıtılmış önbelleği kullanmak için [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="714d6-120">NCache dağıtılmış önbelleğini kullanmak için [nCache. Microsoft. Extensions. Caching. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="714d6-121">Idistributedcache arabirimi</span><span class="sxs-lookup"><span data-stu-id="714d6-121">IDistributedCache interface</span></span>

<span data-ttu-id="714d6-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="714d6-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="714d6-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="714d6-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="714d6-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.</span><span class="sxs-lookup"><span data-stu-id="714d6-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="714d6-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.</span><span class="sxs-lookup"><span data-stu-id="714d6-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="714d6-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.</span><span class="sxs-lookup"><span data-stu-id="714d6-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="714d6-127">Dağıtılmış önbelleğe alma hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="714d6-127">Establish distributed caching services</span></span>

<span data-ttu-id="714d6-128">Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="714d6-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="714d6-129">Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="714d6-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="714d6-130">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="714d6-131">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="714d6-132">Dağıtılmış Redsıs önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="714d6-133">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="714d6-134">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-134">Distributed Memory Cache</span></span>

<span data-ttu-id="714d6-135">Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="714d6-136">Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir.</span><span class="sxs-lookup"><span data-stu-id="714d6-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="714d6-137">Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="714d6-138">Dağıtılmış bellek önbelleği, yararlı bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="714d6-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="714d6-139">Geliştirme ve test senaryolarında.</span><span class="sxs-lookup"><span data-stu-id="714d6-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="714d6-140">Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="714d6-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="714d6-141">Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="714d6-142">Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="714d6-143">Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="714d6-144">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="714d6-145">Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="714d6-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="714d6-146">Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="714d6-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="714d6-147">Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="714d6-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="714d6-148">Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="714d6-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="714d6-149">SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="714d6-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="714d6-150">Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="714d6-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="714d6-151">Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="714d6-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="714d6-153">Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="714d6-154">Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="714d6-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="714d6-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="714d6-156">Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="714d6-157">Dağıtılmış Redis Cache</span><span class="sxs-lookup"><span data-stu-id="714d6-157">Distributed Redis Cache</span></span>

<span data-ttu-id="714d6-158">[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur.</span><span class="sxs-lookup"><span data-stu-id="714d6-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="714d6-159">Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilir ve yerel geliştirme için bir Azure Redis Cache kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="714d6-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="714d6-160">Bir uygulama bir <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> örnek () kullanarak önbellek uygulamasını yapılandırır <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> .</span><span class="sxs-lookup"><span data-stu-id="714d6-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="714d6-161">Daha fazla bilgi için bkz. [Redis için Azure Cache](/azure/azure-cache-for-redis/cache-overview).</span><span class="sxs-lookup"><span data-stu-id="714d6-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="714d6-162">Yerel bir Redsıs önbelleğine alternatif yaklaşımlar hakkında bir tartışma için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/19542) bakın.</span><span class="sxs-lookup"><span data-stu-id="714d6-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="714d6-163">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-163">Distributed NCache Cache</span></span>

<span data-ttu-id="714d6-164">[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="714d6-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="714d6-165">NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="714d6-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="714d6-166">Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="714d6-166">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="714d6-167">NCache 'yi yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="714d6-167">To configure NCache:</span></span>

1. <span data-ttu-id="714d6-168">[NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="714d6-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="714d6-169">Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="714d6-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="714d6-170">Aşağıdaki kodu `Startup.ConfigureServices` dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="714d6-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="714d6-171">Dağıtılmış önbelleği kullan</span><span class="sxs-lookup"><span data-stu-id="714d6-171">Use the distributed cache</span></span>

<span data-ttu-id="714d6-172">Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="714d6-173">Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="714d6-174">Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="714d6-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="714d6-175">Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (daha fazla bilgi için bkz. [genel konak: ıhostapplicationlifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="714d6-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="714d6-176">Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.</span><span class="sxs-lookup"><span data-stu-id="714d6-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="714d6-177">Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="714d6-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="714d6-178">Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="714d6-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="714d6-179">Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.</span><span class="sxs-lookup"><span data-stu-id="714d6-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="714d6-180">Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="714d6-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="714d6-181">Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="714d6-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="714d6-182">Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).</span><span class="sxs-lookup"><span data-stu-id="714d6-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="714d6-183">Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="714d6-184">Öneriler</span><span class="sxs-lookup"><span data-stu-id="714d6-184">Recommendations</span></span>

<span data-ttu-id="714d6-185"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="714d6-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="714d6-186">Mevcut altyapı</span><span class="sxs-lookup"><span data-stu-id="714d6-186">Existing infrastructure</span></span>
* <span data-ttu-id="714d6-187">Performans gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="714d6-187">Performance requirements</span></span>
* <span data-ttu-id="714d6-188">Cost</span><span class="sxs-lookup"><span data-stu-id="714d6-188">Cost</span></span>
* <span data-ttu-id="714d6-189">Ekip deneyimi</span><span class="sxs-lookup"><span data-stu-id="714d6-189">Team experience</span></span>

<span data-ttu-id="714d6-190">Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="714d6-191">Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.</span><span class="sxs-lookup"><span data-stu-id="714d6-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="714d6-192">Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="714d6-193">Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="714d6-194">SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="714d6-195">Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="714d6-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="714d6-196">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="714d6-196">Additional resources</span></span>

* [<span data-ttu-id="714d6-197">Azure üzerinde Redis Cache</span><span class="sxs-lookup"><span data-stu-id="714d6-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="714d6-198">Azure’da SQL Database</span><span class="sxs-lookup"><span data-stu-id="714d6-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="714d6-199">[Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="714d6-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="714d6-200">Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="714d6-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="714d6-201">Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="714d6-202">Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.</span><span class="sxs-lookup"><span data-stu-id="714d6-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="714d6-203">Önbelleğe alınan veriler dağıtıldığında, veriler:</span><span class="sxs-lookup"><span data-stu-id="714d6-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="714d6-204">Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).</span><span class="sxs-lookup"><span data-stu-id="714d6-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="714d6-205">Sunucu yeniden başlatmaları ve uygulama dağıtımları.</span><span class="sxs-lookup"><span data-stu-id="714d6-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="714d6-206">Yerel bellek kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="714d6-206">Doesn't use local memory.</span></span>

<span data-ttu-id="714d6-207">Dağıtılmış önbellek yapılandırması uygulamaya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="714d6-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="714d6-208">Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="714d6-209">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="714d6-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="714d6-210">Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="714d6-211">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="714d6-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="714d6-212">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="714d6-212">Prerequisites</span></span>

<span data-ttu-id="714d6-213">SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="714d6-214">Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="714d6-215">Redsıs paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="714d6-216">NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="714d6-217">NCache paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle, proje dosyanızda ayrı olarak NCache paketine başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="714d6-218">Idistributedcache arabirimi</span><span class="sxs-lookup"><span data-stu-id="714d6-218">IDistributedCache interface</span></span>

<span data-ttu-id="714d6-219"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="714d6-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="714d6-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="714d6-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="714d6-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.</span><span class="sxs-lookup"><span data-stu-id="714d6-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="714d6-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.</span><span class="sxs-lookup"><span data-stu-id="714d6-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="714d6-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.</span><span class="sxs-lookup"><span data-stu-id="714d6-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="714d6-224">Dağıtılmış önbelleğe alma hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="714d6-224">Establish distributed caching services</span></span>

<span data-ttu-id="714d6-225">Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="714d6-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="714d6-226">Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="714d6-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="714d6-227">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="714d6-228">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="714d6-229">Dağıtılmış Redsıs önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="714d6-230">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="714d6-231">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-231">Distributed Memory Cache</span></span>

<span data-ttu-id="714d6-232">Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="714d6-233">Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir.</span><span class="sxs-lookup"><span data-stu-id="714d6-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="714d6-234">Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="714d6-235">Dağıtılmış bellek önbelleği, yararlı bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="714d6-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="714d6-236">Geliştirme ve test senaryolarında.</span><span class="sxs-lookup"><span data-stu-id="714d6-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="714d6-237">Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="714d6-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="714d6-238">Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="714d6-239">Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="714d6-240">Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="714d6-241">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="714d6-242">Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="714d6-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="714d6-243">Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="714d6-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="714d6-244">Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="714d6-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="714d6-245">Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="714d6-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="714d6-246">SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="714d6-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="714d6-247">Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="714d6-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="714d6-248">Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="714d6-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="714d6-250">Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="714d6-251">Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="714d6-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="714d6-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="714d6-253">Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="714d6-254">Dağıtılmış Redis Cache</span><span class="sxs-lookup"><span data-stu-id="714d6-254">Distributed Redis Cache</span></span>

<span data-ttu-id="714d6-255">[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur.</span><span class="sxs-lookup"><span data-stu-id="714d6-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="714d6-256">Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="714d6-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="714d6-257">Bir uygulama, <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> içinde geliştirme olmayan bir ortamda bir örnek () kullanarak önbellek uygulamasını yapılandırır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="714d6-258">Redsıs 'i yerel makinenize yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="714d6-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="714d6-259">[Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.</span><span class="sxs-lookup"><span data-stu-id="714d6-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="714d6-260">`redis-server`Komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="714d6-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="714d6-261">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-261">Distributed NCache Cache</span></span>

<span data-ttu-id="714d6-262">[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="714d6-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="714d6-263">NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="714d6-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="714d6-264">Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="714d6-264">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="714d6-265">NCache 'yi yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="714d6-265">To configure NCache:</span></span>

1. <span data-ttu-id="714d6-266">[NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="714d6-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="714d6-267">Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="714d6-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="714d6-268">Aşağıdaki kodu `Startup.ConfigureServices` dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="714d6-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="714d6-269">Dağıtılmış önbelleği kullan</span><span class="sxs-lookup"><span data-stu-id="714d6-269">Use the distributed cache</span></span>

<span data-ttu-id="714d6-270">Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="714d6-271">Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="714d6-272">Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="714d6-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="714d6-273">Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="714d6-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="714d6-274">Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.</span><span class="sxs-lookup"><span data-stu-id="714d6-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="714d6-275">Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="714d6-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="714d6-276">Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="714d6-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="714d6-277">Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.</span><span class="sxs-lookup"><span data-stu-id="714d6-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="714d6-278">Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="714d6-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="714d6-279">Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="714d6-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="714d6-280">Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).</span><span class="sxs-lookup"><span data-stu-id="714d6-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="714d6-281">Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="714d6-282">Öneriler</span><span class="sxs-lookup"><span data-stu-id="714d6-282">Recommendations</span></span>

<span data-ttu-id="714d6-283"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="714d6-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="714d6-284">Mevcut altyapı</span><span class="sxs-lookup"><span data-stu-id="714d6-284">Existing infrastructure</span></span>
* <span data-ttu-id="714d6-285">Performans gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="714d6-285">Performance requirements</span></span>
* <span data-ttu-id="714d6-286">Cost</span><span class="sxs-lookup"><span data-stu-id="714d6-286">Cost</span></span>
* <span data-ttu-id="714d6-287">Ekip deneyimi</span><span class="sxs-lookup"><span data-stu-id="714d6-287">Team experience</span></span>

<span data-ttu-id="714d6-288">Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="714d6-289">Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.</span><span class="sxs-lookup"><span data-stu-id="714d6-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="714d6-290">Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="714d6-291">Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="714d6-292">SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="714d6-293">Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="714d6-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="714d6-294">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="714d6-294">Additional resources</span></span>

* [<span data-ttu-id="714d6-295">Azure üzerinde Redis Cache</span><span class="sxs-lookup"><span data-stu-id="714d6-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="714d6-296">Azure’da SQL Database</span><span class="sxs-lookup"><span data-stu-id="714d6-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="714d6-297">[Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="714d6-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="714d6-298">Dağıtılmış önbellek, genellikle ona erişen uygulama sunucuları için bir dış hizmet olarak tutulan birden çok uygulama sunucusu tarafından paylaşılan bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="714d6-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="714d6-299">Dağıtılmış önbellek, özellikle uygulama bir bulut hizmeti veya sunucu grubu tarafından barındırıldığı zaman bir ASP.NET Core uygulamasının performansını ve ölçeklenebilirliğini iyileştirebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="714d6-300">Dağıtılmış bir önbellek, önbelleğe alınan verilerin ayrı uygulama sunucularında depolandığı diğer önbelleğe alma senaryolarından daha fazla avantaj sunar.</span><span class="sxs-lookup"><span data-stu-id="714d6-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="714d6-301">Önbelleğe alınan veriler dağıtıldığında, veriler:</span><span class="sxs-lookup"><span data-stu-id="714d6-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="714d6-302">Birden çok sunucuya yönelik istekler arasında *tutarlı (tutarlı* ).</span><span class="sxs-lookup"><span data-stu-id="714d6-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="714d6-303">Sunucu yeniden başlatmaları ve uygulama dağıtımları.</span><span class="sxs-lookup"><span data-stu-id="714d6-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="714d6-304">Yerel bellek kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="714d6-304">Doesn't use local memory.</span></span>

<span data-ttu-id="714d6-305">Dağıtılmış önbellek yapılandırması uygulamaya özgüdür.</span><span class="sxs-lookup"><span data-stu-id="714d6-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="714d6-306">Bu makalede SQL Server ve Redsıs dağıtılmış önbelleklerinin nasıl yapılandırılacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="714d6-307">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub üzerindeki nCache](https://github.com/Alachisoft/NCache)) gibi üçüncü taraf uygulamalar da mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="714d6-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="714d6-308">Uygulama hangi uygulamanın seçildiğine bakılmaksızın, arabirimi kullanılarak önbellek ile etkileşime girer <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="714d6-309">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="714d6-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="714d6-310">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="714d6-310">Prerequisites</span></span>

<span data-ttu-id="714d6-311">SQL Server dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun veya [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="714d6-312">Redsıs dağıtılmış önbellek kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [Microsoft. Extensions. Caching. redsıs](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="714d6-313">Redsıs paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle Redu paketine proje dosyanızda ayrı olarak başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="714d6-314">NCache dağıtılmış önbelleğini kullanmak için [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvurun ve [nCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="714d6-315">NCache paketi pakete dahil değildir, bu `Microsoft.AspNetCore.App` nedenle, proje dosyanızda ayrı olarak NCache paketine başvurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="714d6-316">Idistributedcache arabirimi</span><span class="sxs-lookup"><span data-stu-id="714d6-316">IDistributedCache interface</span></span>

<span data-ttu-id="714d6-317"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek uygulamasındaki öğeleri işlemek için aşağıdaki yöntemleri sağlar:</span><span class="sxs-lookup"><span data-stu-id="714d6-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="714d6-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Bir dize anahtarını kabul eder ve önbellekte bulunursa önbelleğe alınmış bir öğeyi dizi olarak alır `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="714d6-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="714d6-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` Bir dize anahtarı kullanarak önbelleğe bir öğe (dizi olarak) ekler.</span><span class="sxs-lookup"><span data-stu-id="714d6-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="714d6-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Önbellekte bir öğeyi anahtarını temel alarak yeniler, Kayan süre sonu zaman aşımını (varsa) sıfırlarken.</span><span class="sxs-lookup"><span data-stu-id="714d6-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="714d6-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Bir önbellek öğesini dize anahtarına göre kaldırır.</span><span class="sxs-lookup"><span data-stu-id="714d6-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="714d6-322">Dağıtılmış önbelleğe alma hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="714d6-322">Establish distributed caching services</span></span>

<span data-ttu-id="714d6-323">Uygulamasına bir uygulamasını kaydetme <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="714d6-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="714d6-324">Bu konuda açıklanan Framework tarafından sunulan uygulamalar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="714d6-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="714d6-325">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="714d6-326">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="714d6-327">Dağıtılmış Redsıs önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="714d6-328">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="714d6-329">Dağıtılmış bellek önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-329">Distributed Memory Cache</span></span>

<span data-ttu-id="714d6-330">Dağıtılmış bellek önbelleği ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ), öğeleri bellekte depolayan çerçeve tarafından sağlanmış bir uygulamasıdır <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="714d6-331">Dağıtılmış bellek önbelleği gerçek bir dağıtılmış önbellek değildir.</span><span class="sxs-lookup"><span data-stu-id="714d6-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="714d6-332">Önbelleğe alınan öğeler, uygulamanın çalıştığı sunucuda uygulama örneği tarafından depolanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="714d6-333">Dağıtılmış bellek önbelleği, yararlı bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="714d6-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="714d6-334">Geliştirme ve test senaryolarında.</span><span class="sxs-lookup"><span data-stu-id="714d6-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="714d6-335">Üretimde tek bir sunucu kullanıldığında ve bellek tüketimi bir sorun değildir.</span><span class="sxs-lookup"><span data-stu-id="714d6-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="714d6-336">Dağıtılmış bellek önbelleğinin uygulanması, önbelleğe alınmış veri depolamayı soyutlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="714d6-337">Birden çok düğüm veya hata toleransı gerekliyse, gelecekte doğru bir dağıtılmış önbelleğe alma çözümü uygulamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="714d6-338">Örnek uygulama, uygulamasında geliştirme ortamında uygulama çalıştırıldığında dağıtılmış bellek önbelleğinin kullanımını sağlar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="714d6-339">Dağıtılmış SQL Server önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="714d6-340">Dağıtılmış SQL Server önbellek uygulama ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) dağıtılmış önbelleğin, kendi yedekleme deposu olarak bir SQL Server veritabanı kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="714d6-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="714d6-341">Bir SQL Server örneğinde önbelleğe alınmış SQL Server bir öğe tablosu oluşturmak için `sql-cache` aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="714d6-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="714d6-342">Araç, belirttiğiniz ad ve şemaya sahip bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="714d6-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="714d6-343">Komutunu çalıştırarak SQL Server tablo oluşturun `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="714d6-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="714d6-344">SQL Server örneği ( `Data Source` ), veritabanı (), `Initial Catalog` şema (örneğin,) `dbo` ve tablo adı (örneğin, `TestCache` ) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="714d6-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="714d6-345">Aracın başarılı olduğunu göstermek için bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="714d6-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="714d6-346">Araç tarafından oluşturulan tablo `sql-cache` aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="714d6-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer önbellek tablosu](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="714d6-348">Bir uygulama, bir örneğini kullanarak önbellek değerlerini işlemelidir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="714d6-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="714d6-349">Örnek uygulama, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> Içinde geliştirme dışı bir ortamda uygular `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="714d6-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="714d6-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(Ve isteğe bağlı olarak, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> ve <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) genellikle kaynak denetimi dışında (örneğin, [gizli yönetici](xref:security/app-secrets) tarafından veya appSettings *üzerindeappsettings.js*depolanır / *. { ENVIRONMENT}. JSON* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="714d6-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="714d6-351">Bağlantı dizesinde kaynak denetim sistemlerinden tutulması gereken kimlik bilgileri bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="714d6-352">Dağıtılmış Redis Cache</span><span class="sxs-lookup"><span data-stu-id="714d6-352">Distributed Redis Cache</span></span>

<span data-ttu-id="714d6-353">[Redsıs](https://redis.io/) , genellikle dağıtılmış önbellek olarak kullanılan açık kaynaklı bir bellek içi veri deposudur.</span><span class="sxs-lookup"><span data-stu-id="714d6-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="714d6-354">Redsıs 'yi yerel olarak kullanabilir ve Azure 'da barındırılan ASP.NET Core uygulaması için bir [Azure Redis Cache](https://azure.microsoft.com/services/cache/) yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="714d6-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="714d6-355">Bir uygulama bir <xref:Microsoft.Extensions.Caching.Redis.RedisCache> örnek () kullanarak önbellek uygulamasını yapılandırır <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> :</span><span class="sxs-lookup"><span data-stu-id="714d6-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="714d6-356">Redsıs 'i yerel makinenize yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="714d6-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="714d6-357">[Chocolatey redsıs paketini](https://chocolatey.org/packages/redis-64/)yükler.</span><span class="sxs-lookup"><span data-stu-id="714d6-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="714d6-358">`redis-server`Komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="714d6-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="714d6-359">Dağıtılmış NCache önbelleği</span><span class="sxs-lookup"><span data-stu-id="714d6-359">Distributed NCache Cache</span></span>

<span data-ttu-id="714d6-360">[NCache](https://github.com/Alachisoft/NCache) , .NET ve .NET Core 'da yerel olarak geliştirilen açık kaynaklı bellek içi dağıtılmış bir önbellektir.</span><span class="sxs-lookup"><span data-stu-id="714d6-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="714d6-361">NCache hem yerel olarak hem de Azure 'da veya diğer barındırma platformlarında çalışan bir ASP.NET Core uygulama için dağıtılmış önbellek kümesi olarak yapılandırılmış şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="714d6-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="714d6-362">Yerel makinenizde NCache 'i yüklemek ve yapılandırmak için bkz. [Windows Için nCache Başlangıç Kılavuzu](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="714d6-362">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="714d6-363">NCache 'yi yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="714d6-363">To configure NCache:</span></span>

1. <span data-ttu-id="714d6-364">[NCache açık kaynak NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="714d6-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="714d6-365">Cache kümesini [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)' de yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="714d6-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="714d6-366">Aşağıdaki kodu `Startup.ConfigureServices` dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="714d6-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="714d6-367">Dağıtılmış önbelleği kullan</span><span class="sxs-lookup"><span data-stu-id="714d6-367">Use the distributed cache</span></span>

<span data-ttu-id="714d6-368">Arabirimini kullanmak için <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> uygulamadaki herhangi bir oluşturucudan bir örnek isteyin.</span><span class="sxs-lookup"><span data-stu-id="714d6-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="714d6-369">Örnek, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="714d6-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="714d6-370">Örnek uygulama başlatıldığında <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> öğesine eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="714d6-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="714d6-371">Şu anki süre kullanılarak önbelleğe alındı <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (daha fazla bilgi için bkz [. Web Host: ıapplicationlifetime arabirimi](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="714d6-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="714d6-372">Örnek uygulama <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , `IndexModel` Dizin sayfası tarafından kullanılmak üzere içine çıkarır.</span><span class="sxs-lookup"><span data-stu-id="714d6-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="714d6-373">Dizin sayfası her yüklendiğinde, önbellek, içindeki önbelleğe alınmış zamana göre denetlenir `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="714d6-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="714d6-374">Önbelleğe alınmış sürenin süresi dolmamışsa, zaman görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="714d6-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="714d6-375">Önbelleğe alınan saate son erişildiği zamandan bu yana 20 saniye geçtikten sonra (bu sayfanın son yüklenilişinde), sayfanın *önbelleğe alınma süresi doldu*.</span><span class="sxs-lookup"><span data-stu-id="714d6-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="714d6-376">Önbelleğe alınmış süreyi **Sıfırla** ' yı seçerek önbelleğe alınmış zamanı geçerli saate hemen güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="714d6-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="714d6-377">Düğme `OnPostResetCachedTime` işleyici metodunu tetikler.</span><span class="sxs-lookup"><span data-stu-id="714d6-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="714d6-378">Örnekler için tek veya kapsamlı bir yaşam süresi kullanmanız gerekmez <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (en azından yerleşik uygulamalar için).</span><span class="sxs-lookup"><span data-stu-id="714d6-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="714d6-379">Ayrıca <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , dı kullanmak yerine bir örnek oluşturabilirsiniz, ancak kodda bir örnek oluşturmak kodunuzu test etmek ve [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)ihlal etmek için daha zor hale gelebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="714d6-380">Öneriler</span><span class="sxs-lookup"><span data-stu-id="714d6-380">Recommendations</span></span>

<span data-ttu-id="714d6-381"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Uygulamanız için hangi uygulamanın en iyisi olduğuna karar verirken aşağıdakileri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="714d6-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="714d6-382">Mevcut altyapı</span><span class="sxs-lookup"><span data-stu-id="714d6-382">Existing infrastructure</span></span>
* <span data-ttu-id="714d6-383">Performans gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="714d6-383">Performance requirements</span></span>
* <span data-ttu-id="714d6-384">Cost</span><span class="sxs-lookup"><span data-stu-id="714d6-384">Cost</span></span>
* <span data-ttu-id="714d6-385">Ekip deneyimi</span><span class="sxs-lookup"><span data-stu-id="714d6-385">Team experience</span></span>

<span data-ttu-id="714d6-386">Önbelleğe alma çözümleri genellikle önbelleğe alınmış verilerin hızlı bir şekilde alınmasını sağlamak için bellek içi depolamaya dayanır, ancak bellek sınırlı bir kaynaktır ve genişleyebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="714d6-387">Yalnızca yaygın olarak kullanılan verileri bir önbellekte depolayın.</span><span class="sxs-lookup"><span data-stu-id="714d6-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="714d6-388">Genellikle, Redsıs önbelleği daha yüksek aktarım hızı ve bir SQL Server önbelleğinden daha düşük gecikme süresi sağlar.</span><span class="sxs-lookup"><span data-stu-id="714d6-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="714d6-389">Bununla birlikte, genellikle önbelleğe alma stratejilerinin performans özelliklerini belirlemede bir değerlendirme gerekir.</span><span class="sxs-lookup"><span data-stu-id="714d6-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="714d6-390">SQL Server dağıtılmış önbellek yedekleme deposu olarak kullanıldığında, önbellek için aynı veritabanını kullanın ve uygulamanın sıradan veri depolama ve alma, her ikisinin performansını olumsuz yönde etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="714d6-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="714d6-391">Dağıtılmış önbellek yedekleme deposu için adanmış bir SQL Server örneği kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="714d6-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="714d6-392">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="714d6-392">Additional resources</span></span>

* [<span data-ttu-id="714d6-393">Azure üzerinde Redis Cache</span><span class="sxs-lookup"><span data-stu-id="714d6-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="714d6-394">Azure’da SQL Database</span><span class="sxs-lookup"><span data-stu-id="714d6-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="714d6-395">[Web çiftlerine nCache Için ıdistributedcache sağlayıcısını ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 'da nCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="714d6-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
