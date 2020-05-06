---
title: ASP.NET Core SignalR ölçeği genişletme için redsıs geri düzlemi
author: bradygaster
description: Bir ASP.NET Core SignalR uygulaması için ölçeklendirmeyi etkinleştirmek üzere redsıs arka düzlemi ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775355"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>ASP.NET Core SignalR ölçeği genişletme için redsıs arka düzlemi ayarlama

, [Andrew Stanton-nuri](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)ve [Tom Dykstra](https://github.com/tdykstra),

Bu makalede, SignalRbir ASP.NET Core SignalR uygulamasının ölçeğini genişletmek için bir [Redl](https://redis.io/) sunucusu ayarlamanın belirli yönleri açıklanmaktadır.

## <a name="set-up-a-redis-backplane"></a>Redsıs geri düzlemi ayarlama

* Redsıs sunucusunu dağıtın.

  > [!IMPORTANT] 
  > Üretim kullanımı için, bir redin geri düzlemi yalnızca SignalR uygulamayla aynı veri merkezinde çalışırken önerilir. Aksi takdirde, ağ gecikmesi performansı düşürür. SignalR Uygulamanız Azure bulutu 'nda çalışıyorsa, redin geri düzlemi yerine Azure SignalR hizmeti önerilir. Geliştirme ve test ortamları için Azure Redis Cache hizmetini kullanabilirsiniz.

  Daha fazla bilgi için aşağıdaki kaynaklara bakın:

  * <xref:signalr/scale>
  * [Redsıs belgeleri](https://redis.io/)
  * [Azure Redis Cache belgeleri](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* SignalR Uygulamada, `Microsoft.AspNetCore.SignalR.Redis` NuGet paketini yükledikten sonra.
* `Startup.ConfigureServices` Yönteminde, şu tarihten sonra `AddRedis` `AddSignalR`çağırın:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Seçenekleri gerektiği şekilde yapılandırın:
 
  Çoğu seçenek bağlantı dizesinde veya [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) nesnesinde ayarlanabilir. ' De `ConfigurationOptions` belirtilen seçenekler bağlantı dizesinde ayarlanmış olanları geçersiz kılar.

  Aşağıdaki örnek, `ConfigurationOptions` nesnesindeki seçeneklerin nasıl ayarlanacağını gösterir. Bu örnek, aşağıdaki adımda anlatıldığı gibi birden çok uygulamanın aynı redo örneğini paylaşabilmesi için bir kanal öneki ekler.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Yukarıdaki kodda, `options.Configuration` bağlantı dizesinde belirtilen şeyle birlikte başlatılır.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* SignalR Uygulamada, aşağıdaki NuGet paketlerinden birini yüklemelisiniz:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`-StackExchange 'e bağlıdır. Redsıs 2. X.X. Bu, ASP.NET Core 2,2 ve üzeri için önerilen pakettir.
  * `Microsoft.AspNetCore.SignalR.Redis`-StackExchange 'e bağlıdır. Redsıs 1. X.X. Bu paket ASP.NET Core 3,0 ve üzeri bir sürüme dahil değildir.

* `Startup.ConfigureServices` Yönteminde, şunu çağırın <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Kullanırken `Microsoft.AspNetCore.SignalR.Redis`, çağırın <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

* Seçenekleri gerektiği şekilde yapılandırın:
 
  Çoğu seçenek bağlantı dizesinde veya [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) nesnesinde ayarlanabilir. ' De `ConfigurationOptions` belirtilen seçenekler bağlantı dizesinde ayarlanmış olanları geçersiz kılar.

  Aşağıdaki örnek, `ConfigurationOptions` nesnesindeki seçeneklerin nasıl ayarlanacağını gösterir. Bu örnek, aşağıdaki adımda anlatıldığı gibi birden çok uygulamanın aynı redo örneğini paylaşabilmesi için bir kanal öneki ekler.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Kullanırken `Microsoft.AspNetCore.SignalR.Redis`, çağırın <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

  Yukarıdaki kodda, `options.Configuration` bağlantı dizesinde belirtilen şeyle birlikte başlatılır.

  Redu seçenekleri hakkında daha fazla bilgi için bkz. [StackExchange redin belgeleri](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* SignalR Uygulamada, aşağıdaki NuGet paketini yüklerken:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* `Startup.ConfigureServices` Yönteminde, şunu çağırın <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Seçenekleri gerektiği şekilde yapılandırın:
 
  Çoğu seçenek bağlantı dizesinde veya [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) nesnesinde ayarlanabilir. ' De `ConfigurationOptions` belirtilen seçenekler bağlantı dizesinde ayarlanmış olanları geçersiz kılar.

  Aşağıdaki örnek, `ConfigurationOptions` nesnesindeki seçeneklerin nasıl ayarlanacağını gösterir. Bu örnek, aşağıdaki adımda anlatıldığı gibi birden çok uygulamanın aynı redo örneğini paylaşabilmesi için bir kanal öneki ekler.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Yukarıdaki kodda, `options.Configuration` bağlantı dizesinde belirtilen şeyle birlikte başlatılır.

  Redu seçenekleri hakkında daha fazla bilgi için bkz. [StackExchange redin belgeleri](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Birden çok SignalR uygulama Için bir redsıs sunucusu kullanıyorsanız, her SignalR uygulama için farklı bir kanal öneki kullanın.

  Bir kanal öneki ayarlandığında, farklı SignalR kanal ön ekleri kullanan diğerlerinden bir uygulama yalıtır. Farklı ön ekler atamadıysanız, bir uygulamadan tüm istemcilerine gönderilen bir ileti, Redo sunucusunu bir geri düzlemi olarak kullanan tüm uygulamaların tüm istemcilerine gider.

* Sunucu grubu yük dengeleme yazılımınızı yapışkan oturumlar için yapılandırın. Bunun nasıl yapılacağını gösteren bazı örnekler aşağıda verilmiştir:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [NGINX](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redsıs sunucu hataları

Redsıs sunucusu aşağı gittiğinde, SignalR iletilerin teslim edilmediğini belirten özel durumlar oluşturur. Bazı tipik özel durum iletileri:

* *İleti yazılamadı*
* *' MethodName ' hub yöntemi çağrılamadı*
* *Redsıs bağlantısı başarısız oldu*

SignalRsunucu yeniden geldiğinde iletileri göndermek için arabelleğe almaz. Redsıs sunucusu kapatıldığında gönderilen iletiler kaybedilir.

SignalRRedsıs sunucusu yeniden kullanılabilir olduğunda otomatik olarak yeniden bağlanır.

### <a name="custom-behavior-for-connection-failures"></a>Bağlantı hatalarıyla ilgili özel davranış

Redsıs bağlantı hatası olaylarının nasıl işleneceğini gösteren bir örnek aşağıda verilmiştir.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Redsıs Kümelemesi

[Redsıs Kümelemesi](https://redis.io/topics/cluster-spec) , birden çok redo sunucusu kullanarak yüksek kullanılabilirlik elde etmek için kullanılan bir yöntemdir. Kümeleme resmi olarak desteklenmez, ancak çalışmayabilir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:signalr/scale>
* [Redsıs belgeleri](https://redis.io/documentation)
* [StackExchange redin belgeleri](https://stackexchange.github.io/StackExchange.Redis/)
* [Azure Redis Cache belgeleri](https://docs.microsoft.com/azure/redis-cache/)
