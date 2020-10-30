---
title: SignalR HubContext
author: bradygaster
description: SignalRHub dışından istemcilere bildirim göndermek için ASP.NET Core hubcontext hizmetini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058187"
---
# <a name="send-messages-from-outside-a-hub"></a>Hub dışından ileti gönderme

X [MIKAEL Mengistu](https://twitter.com/MikaelM_12) tarafından

SignalRHub, sunucuya bağlı istemcilere ileti göndermeye yönelik temel soyutlamadır SignalR . Ayrıca, hizmetini kullanarak uygulamanızdaki diğer yerlerden ileti göndermek de mümkündür `IHubContext` . Bu makalede, bir SignalR `IHubContext` hub dışından istemcilere bildirim göndermek üzere bir öğesine nasıl erişebileceğiniz açıklanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Iubcontext 'in bir örneğini al

ASP.NET Core SignalR , `IHubContext` bağımlılık ekleme aracılığıyla bir örneğine erişebilirsiniz. `IHubContext`Bir denetleyiciye, ara yazılıma veya diğer BIR dı hizmetine bir örneğini ekleyebilirsiniz. İstemcilere ileti göndermek için örneği kullanın.

> [!NOTE]
> Bu SignalR , ' ye erişim sağlamak Için GlobalHost kullanan ASP.NET 4. x öğesinden farklıdır `IHubContext` . ASP.NET Core, bu genel Singleton gereksinimini ortadan kaldıran bir bağımlılık ekleme çerçevesine sahiptir.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Denetleyiciye bir ıubcontext örneği ekleme

`IHubContext`Yapıcısına ekleyerek bir denetleyiciye bir örneğini ekleyebilirsiniz:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Artık bir örneğine erişimle, hub 'ın `IHubContext` kendisinde olduğu gibi hub yöntemlerini çağırabilirsiniz.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Ara yazılım içindeki bir ıubcontext örneği alın

Şu `IHubContext` şekilde ara yazılım ardışık düzeninde erişin:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Hub yöntemleri, sınıf dışından çağrıldığında `Hub` , çağırma ile ilişkili bir arayan yoktur. Bu nedenle,, ve özelliklerine erişim yoktur `ConnectionId` `Caller` `Others` .

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>IHOST 'dan bir ıubcontext örneği al

`IHubContext`Web ana bilgisayarına erişmek, örneğin üçüncü taraf bağımlılık ekleme çerçevelerini kullanarak ASP.NET Core dışındaki alanlarla tümleştirme için yararlıdır:

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Türü kesin belirlenmiş bir HubContext Ekle

Türü kesin belirlenmiş bir HubContext eklemek için hub 'ınızın öğesinden devralındığından emin olun `Hub<T>` . Yerine arabirimini kullanarak ekleme `IHubContext<THub, T>` `IHubContext<THub>` .

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

Daha fazla bilgi için bkz. [türü kesin belirlenmiş hub 'lar](xref:signalr/hubs#strongly-typed-hubs) .

## <a name="related-resources"></a>İlgili kaynaklar

* [Kullanmaya başlama](xref:tutorials/signalr)
* [Merkezler](xref:signalr/hubs)
* [Azure 'da yayımlama](xref:signalr/publish-to-azure-web-app)
