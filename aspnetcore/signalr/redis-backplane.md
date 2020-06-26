---
title: ASP.NET Core ölçeği genişletme için redsıs geri düzlemi SignalR
author: bradygaster
description: Bir ASP.NET Core uygulaması için ölçeklendirmeyi etkinleştirmek üzere Redsıs arka düzlemi ayarlamayı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 58c1ff2c9334e75535f6e5f0f418976176822724
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408480"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>ASP.NET Core ölçeği genişletme için Redsıs arka düzlemi ayarlama SignalR

, [Andrew Stanton-nuri](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)ve [Tom Dykstra](https://github.com/tdykstra),

Bu makalede SignalR , bir ASP.NET Core uygulamasının ölçeğini genişletmek için bir [Redl](https://redis.io/) sunucusu ayarlamanın belirli yönleri açıklanmaktadır SignalR .

## <a name="set-up-a-redis-backplane"></a>Redsıs geri düzlemi ayarlama

* Redsıs sunucusunu dağıtın.

  > [!IMPORTANT] 
  > Üretim kullanımı için, bir redin geri düzlemi yalnızca uygulamayla aynı veri merkezinde çalışırken önerilir SignalR . Aksi takdirde, ağ gecikmesi performansı düşürür. SignalRUygulamanız Azure bulutu 'nda çalışıyorsa, SignalR redin geri düzlemi yerine Azure hizmeti önerilir. Geliştirme ve test ortamları için Azure Redis Cache hizmetini kullanabilirsiniz.

  Daha fazla bilgi için aşağıdaki kaynaklara bakın:

  * <xref:signalr/scale>
  * [Redsıs belgeleri](https://redis.io/)
  * [Azure Redis Cache belgeleri](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* SignalRUygulamada, NuGet paketini yükledikten sonra `Microsoft.AspNetCore.SignalR.Redis` .
* `Startup.ConfigureServices`Yönteminde, şu `AddRedis` tarihten sonra çağırın `AddSignalR` :

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Seçenekleri gerektiği şekilde yapılandırın:
 
  Çoğu seçenek bağlantı dizesinde veya [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) nesnesinde ayarlanabilir. ' De belirtilen seçenekler `ConfigurationOptions` bağlantı dizesinde ayarlanmış olanları geçersiz kılar.

  Aşağıdaki örnek, nesnesindeki seçeneklerin nasıl ayarlanacağını gösterir `ConfigurationOptions` . Bu örnek, aşağıdaki adımda anlatıldığı gibi birden çok uygulamanın aynı redo örneğini paylaşabilmesi için bir kanal öneki ekler.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Yukarıdaki kodda, `options.Configuration` bağlantı dizesinde belirtilen şeyle birlikte başlatılır.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* SignalRUygulamada, aşağıdaki NuGet paketlerinden birini yüklemelisiniz:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`-StackExchange 'e bağlıdır. Redsıs 2. X.X. Bu, ASP.NET Core 2,2 ve üzeri için önerilen pakettir.
  * `Microsoft.AspNetCore.SignalR.Redis`-StackExchange 'e bağlıdır. Redsıs 1. X.X. Bu paket ASP.NET Core 3,0 ve üzeri bir sürüme dahil değildir.

* `Startup.ConfigureServices`Yönteminde, şunu çağırın <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Kullanırken `Microsoft.AspNetCore.SignalR.Redis` , çağırın <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

* Seçenekleri gerektiği şekilde yapılandırın:
 
  Çoğu seçenek bağlantı dizesinde veya [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) nesnesinde ayarlanabilir. ' De belirtilen seçenekler `ConfigurationOptions` bağlantı dizesinde ayarlanmış olanları geçersiz kılar.

  Aşağıdaki örnek, nesnesindeki seçeneklerin nasıl ayarlanacağını gösterir `ConfigurationOptions` . Bu örnek, aşağıdaki adımda anlatıldığı gibi birden çok uygulamanın aynı redo örneğini paylaşabilmesi için bir kanal öneki ekler.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Kullanırken `Microsoft.AspNetCore.SignalR.Redis` , çağırın <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

  Yukarıdaki kodda, `options.Configuration` bağlantı dizesinde belirtilen şeyle birlikte başlatılır.

  Redu seçenekleri hakkında daha fazla bilgi için bkz. [StackExchange redin belgeleri](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* SignalRUygulamada, aşağıdaki NuGet paketini yüklerken:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* `Startup.ConfigureServices`Yönteminde, şunu çağırın <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Seçenekleri gerektiği şekilde yapılandırın:
 
  Çoğu seçenek bağlantı dizesinde veya [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) nesnesinde ayarlanabilir. ' De belirtilen seçenekler `ConfigurationOptions` bağlantı dizesinde ayarlanmış olanları geçersiz kılar.

  Aşağıdaki örnek, nesnesindeki seçeneklerin nasıl ayarlanacağını gösterir `ConfigurationOptions` . Bu örnek, aşağıdaki adımda anlatıldığı gibi birden çok uygulamanın aynı redo örneğini paylaşabilmesi için bir kanal öneki ekler.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Yukarıdaki kodda, `options.Configuration` bağlantı dizesinde belirtilen şeyle birlikte başlatılır.

  Redu seçenekleri hakkında daha fazla bilgi için bkz. [StackExchange redin belgeleri](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Birden çok uygulama için bir Redsıs sunucusu kullanıyorsanız SignalR , her uygulama için farklı bir kanal öneki kullanın SignalR .

  Bir kanal öneki ayarlandığında SignalR , farklı kanal ön ekleri kullanan diğerlerinden bir uygulama yalıtır. Farklı ön ekler atamadıysanız, bir uygulamadan tüm istemcilerine gönderilen bir ileti, Redo sunucusunu bir geri düzlemi olarak kullanan tüm uygulamaların tüm istemcilerine gider.

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
