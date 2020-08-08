---
title: SignalRHubContext
author: bradygaster
description: SignalRHub dışından istemcilere bildirim göndermek için ASP.NET Core hubcontext hizmetini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
ms.openlocfilehash: b9adc54c1928d6ec11f707b2bd5e1e297973f1ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021932"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="20335-103">Hub dışından ileti gönderme</span><span class="sxs-lookup"><span data-stu-id="20335-103">Send messages from outside a hub</span></span>

<span data-ttu-id="20335-104">X [MIKAEL Mengistu](https://twitter.com/MikaelM_12) tarafından</span><span class="sxs-lookup"><span data-stu-id="20335-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="20335-105">SignalRHub, sunucuya bağlı istemcilere ileti göndermeye yönelik temel soyutlamadır SignalR .</span><span class="sxs-lookup"><span data-stu-id="20335-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="20335-106">Ayrıca, hizmetini kullanarak uygulamanızdaki diğer yerlerden ileti göndermek de mümkündür `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="20335-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="20335-107">Bu makalede, bir SignalR `IHubContext` hub dışından istemcilere bildirim göndermek üzere bir öğesine nasıl erişebileceğiniz açıklanır.</span><span class="sxs-lookup"><span data-stu-id="20335-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="20335-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="20335-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="20335-109">Iubcontext 'in bir örneğini al</span><span class="sxs-lookup"><span data-stu-id="20335-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="20335-110">ASP.NET Core SignalR , `IHubContext` bağımlılık ekleme aracılığıyla bir örneğine erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20335-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="20335-111">`IHubContext`Bir denetleyiciye, ara yazılıma veya diğer BIR dı hizmetine bir örneğini ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20335-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="20335-112">İstemcilere ileti göndermek için örneği kullanın.</span><span class="sxs-lookup"><span data-stu-id="20335-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="20335-113">Bu SignalR , ' ye erişim sağlamak Için GlobalHost kullanan ASP.NET 4. x öğesinden farklıdır `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="20335-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="20335-114">ASP.NET Core, bu genel Singleton gereksinimini ortadan kaldıran bir bağımlılık ekleme çerçevesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="20335-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="20335-115">Denetleyiciye bir ıubcontext örneği ekleme</span><span class="sxs-lookup"><span data-stu-id="20335-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="20335-116">`IHubContext`Yapıcısına ekleyerek bir denetleyiciye bir örneğini ekleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="20335-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="20335-117">Artık bir örneğine erişimle, hub 'ın `IHubContext` kendisinde olduğu gibi hub yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20335-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="20335-118">Ara yazılım içindeki bir ıubcontext örneği alın</span><span class="sxs-lookup"><span data-stu-id="20335-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="20335-119">Şu `IHubContext` şekilde ara yazılım ardışık düzeninde erişin:</span><span class="sxs-lookup"><span data-stu-id="20335-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="20335-120">Hub yöntemleri, sınıf dışından çağrıldığında `Hub` , çağırma ile ilişkili bir arayan yoktur.</span><span class="sxs-lookup"><span data-stu-id="20335-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="20335-121">Bu nedenle,, ve özelliklerine erişim yoktur `ConnectionId` `Caller` `Others` .</span><span class="sxs-lookup"><span data-stu-id="20335-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="20335-122">IHOST 'dan bir ıubcontext örneği al</span><span class="sxs-lookup"><span data-stu-id="20335-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="20335-123">`IHubContext`Web ana bilgisayarına erişmek, örneğin üçüncü taraf bağımlılık ekleme çerçevelerini kullanarak ASP.NET Core dışındaki alanlarla tümleştirme için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="20335-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="20335-124">Türü kesin belirlenmiş bir HubContext Ekle</span><span class="sxs-lookup"><span data-stu-id="20335-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="20335-125">Türü kesin belirlenmiş bir HubContext eklemek için hub 'ınızın öğesinden devralındığından emin olun `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="20335-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="20335-126">Yerine arabirimini kullanarak ekleme `IHubContext<THub, T>` `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="20335-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="20335-127">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="20335-127">Related resources</span></span>

* [<span data-ttu-id="20335-128">Kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="20335-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="20335-129">Hub'lar</span><span class="sxs-lookup"><span data-stu-id="20335-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="20335-130">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="20335-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
