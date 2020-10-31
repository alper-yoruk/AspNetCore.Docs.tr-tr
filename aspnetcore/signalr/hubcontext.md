---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: :::no-loc(SignalR):::Hub dışından istemcilere bildirim göndermek için ASP.NET Core hubcontext hizmetini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(IHubContext):::'
uid: signalr/hubcontext
ms.openlocfilehash: 0b1940dc85634051e8a566c6859f51c130b69269
ms.sourcegitcommit: 1b7f2e1aabf43fa93b920cad36515d7336bfc2df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066739"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="74216-103">Hub dışından ileti gönderme</span><span class="sxs-lookup"><span data-stu-id="74216-103">Send messages from outside a hub</span></span>

<span data-ttu-id="74216-104">X [MIKAEL Mengistu](https://twitter.com/MikaelM_12) tarafından</span><span class="sxs-lookup"><span data-stu-id="74216-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="74216-105">:::no-loc(SignalR):::Hub, sunucuya bağlı istemcilere ileti göndermeye yönelik temel soyutlamadır :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="74216-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="74216-106">Ayrıca, hizmetini kullanarak uygulamanızdaki diğer yerlerden ileti göndermek de mümkündür `:::no-loc(IHubContext):::` .</span><span class="sxs-lookup"><span data-stu-id="74216-106">It's also possible to send messages from other places in your app using the `:::no-loc(IHubContext):::` service.</span></span> <span data-ttu-id="74216-107">Bu makalede, bir :::no-loc(SignalR)::: `:::no-loc(IHubContext):::` hub dışından istemcilere bildirim göndermek üzere bir öğesine nasıl erişebileceğiniz açıklanır.</span><span class="sxs-lookup"><span data-stu-id="74216-107">This article explains how to access a :::no-loc(SignalR)::: `:::no-loc(IHubContext):::` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="74216-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="74216-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-no-locihubcontext"></a><span data-ttu-id="74216-109">Bir örneğini al :::no-loc(IHubContext):::</span><span class="sxs-lookup"><span data-stu-id="74216-109">Get an instance of :::no-loc(IHubContext):::</span></span>

<span data-ttu-id="74216-110">ASP.NET Core :::no-loc(SignalR)::: , `:::no-loc(IHubContext):::` bağımlılık ekleme aracılığıyla bir örneğine erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74216-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `:::no-loc(IHubContext):::` via dependency injection.</span></span> <span data-ttu-id="74216-111">`:::no-loc(IHubContext):::`Bir denetleyiciye, ara yazılıma veya diğer BIR dı hizmetine bir örneğini ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74216-111">You can inject an instance of `:::no-loc(IHubContext):::` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="74216-112">İstemcilere ileti göndermek için örneği kullanın.</span><span class="sxs-lookup"><span data-stu-id="74216-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="74216-113">Bu :::no-loc(SignalR)::: , ' ye erişim sağlamak Için GlobalHost kullanan ASP.NET 4. x öğesinden farklıdır `:::no-loc(IHubContext):::` .</span><span class="sxs-lookup"><span data-stu-id="74216-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `:::no-loc(IHubContext):::`.</span></span> <span data-ttu-id="74216-114">ASP.NET Core, bu genel Singleton gereksinimini ortadan kaldıran bir bağımlılık ekleme çerçevesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="74216-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-no-locihubcontext-in-a-controller"></a><span data-ttu-id="74216-115">Bir denetleyiciye bir örnek ekleme :::no-loc(IHubContext):::</span><span class="sxs-lookup"><span data-stu-id="74216-115">Inject an instance of :::no-loc(IHubContext)::: in a controller</span></span>

<span data-ttu-id="74216-116">`:::no-loc(IHubContext):::`Yapıcısına ekleyerek bir denetleyiciye bir örneğini ekleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="74216-116">You can inject an instance of `:::no-loc(IHubContext):::` into a controller by adding it to your constructor:</span></span>

[!code-csharp[:::no-loc(IHubContext):::](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="74216-117">Artık bir örneğine erişimle, hub 'ın `:::no-loc(IHubContext):::` kendisinde olduğu gibi hub yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74216-117">Now, with access to an instance of `:::no-loc(IHubContext):::`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[:::no-loc(IHubContext):::](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-no-locihubcontext-in-middleware"></a><span data-ttu-id="74216-118">Ara yazılım örneği alın :::no-loc(IHubContext):::</span><span class="sxs-lookup"><span data-stu-id="74216-118">Get an instance of :::no-loc(IHubContext)::: in middleware</span></span>

<span data-ttu-id="74216-119">Şu `:::no-loc(IHubContext):::` şekilde ara yazılım ardışık düzeninde erişin:</span><span class="sxs-lookup"><span data-stu-id="74216-119">Access the `:::no-loc(IHubContext):::` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<:::no-loc(IHubContext):::<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="74216-120">Hub yöntemleri, sınıf dışından çağrıldığında `Hub` , çağırma ile ilişkili bir arayan yoktur.</span><span class="sxs-lookup"><span data-stu-id="74216-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="74216-121">Bu nedenle,, ve özelliklerine erişim yoktur `ConnectionId` `Caller` `Others` .</span><span class="sxs-lookup"><span data-stu-id="74216-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-no-locihubcontext-from-ihost"></a><span data-ttu-id="74216-122">:::no-loc(IHubContext):::IHOST 'tan bir örnek al</span><span class="sxs-lookup"><span data-stu-id="74216-122">Get an instance of :::no-loc(IHubContext)::: from IHost</span></span>

<span data-ttu-id="74216-123">`:::no-loc(IHubContext):::`Web ana bilgisayarına erişmek, örneğin üçüncü taraf bağımlılık ekleme çerçevelerini kullanarak ASP.NET Core dışındaki alanlarla tümleştirme için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="74216-123">Accessing an `:::no-loc(IHubContext):::` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(:::no-loc(IHubContext):::<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="74216-124">Türü kesin belirlenmiş bir HubContext Ekle</span><span class="sxs-lookup"><span data-stu-id="74216-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="74216-125">Türü kesin belirlenmiş bir HubContext eklemek için hub 'ınızın öğesinden devralındığından emin olun `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="74216-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="74216-126">Yerine arabirimini kullanarak ekleme `:::no-loc(IHubContext):::<THub, T>` `:::no-loc(IHubContext):::<THub>` .</span><span class="sxs-lookup"><span data-stu-id="74216-126">Inject it using the `:::no-loc(IHubContext):::<THub, T>` interface rather than `:::no-loc(IHubContext):::<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public :::no-loc(IHubContext):::<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(:::no-loc(IHubContext):::<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="74216-127">Daha fazla bilgi için bkz. [türü kesin belirlenmiş hub 'lar](xref:signalr/hubs#strongly-typed-hubs) .</span><span class="sxs-lookup"><span data-stu-id="74216-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="74216-128">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="74216-128">Related resources</span></span>

* [<span data-ttu-id="74216-129">Kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="74216-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="74216-130">Merkezler</span><span class="sxs-lookup"><span data-stu-id="74216-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="74216-131">Azure 'da yayımlama</span><span class="sxs-lookup"><span data-stu-id="74216-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
