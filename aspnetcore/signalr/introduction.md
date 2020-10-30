---
title: 'ASP.NET Core giriş :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: kitaplığının uygulamalara gerçek zamanlı işlevsellik eklemeyi nasıl basitleştireceğinizi öğrenin.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 1810fef903362addcef4a6c9ec53264604f58d2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051479"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="87324-103">ASP.NET Core giriş :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="87324-103">Introduction to ASP.NET Core :::no-loc(SignalR):::</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="87324-104">Nedir :::no-loc(SignalR)::: ?</span><span class="sxs-lookup"><span data-stu-id="87324-104">What is :::no-loc(SignalR):::?</span></span>

<span data-ttu-id="87324-105">ASP.NET Core :::no-loc(SignalR)::: , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran açık kaynaklı bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="87324-105">ASP.NET Core :::no-loc(SignalR)::: is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="87324-106">Gerçek zamanlı Web işlevselliği, sunucu tarafı kodun anında istemcilere içerik gönderebilmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="87324-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="87324-107">İçin iyi adaylar :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="87324-107">Good candidates for :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="87324-108">Sunucudan yüksek sıklıkta güncelleştirmeler gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="87324-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="87324-109">Oyun, sosyal ağlar, oylama, açık artırma, haritalar ve GPS uygulamaları bunlara örnektir.</span><span class="sxs-lookup"><span data-stu-id="87324-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="87324-110">Panolar ve izleme uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="87324-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="87324-111">Şirket panoları, anlık satış güncelleştirmeleri veya seyahat uyarıları bunlara örnektir.</span><span class="sxs-lookup"><span data-stu-id="87324-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="87324-112">İş birliği uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="87324-112">Collaborative apps.</span></span> <span data-ttu-id="87324-113">Beyaz tahta uygulamaları ve takım toplantısı yazılımları, iş birliği uygulamalarına örnektir.</span><span class="sxs-lookup"><span data-stu-id="87324-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="87324-114">Bildirim gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="87324-114">Apps that require notifications.</span></span> <span data-ttu-id="87324-115">Sosyal ağlar, e-posta, sohbet, oyunlar, seyahat uyarıları ve diğer birçok uygulama, bildirimleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="87324-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="87324-116">:::no-loc(SignalR)::: sunucudan istemciye [uzak yordam çağrıları (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)oluşturmak IÇIN bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="87324-116">:::no-loc(SignalR)::: provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="87324-117">RPC 'ler, sunucu tarafı .NET Core kodundan gelen istemcilerdeki JavaScript işlevlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="87324-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="87324-118">ASP.NET Core için bazı özellikler şunlardır :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="87324-118">Here are some features of :::no-loc(SignalR)::: for ASP.NET Core:</span></span>

* <span data-ttu-id="87324-119">Bağlantı yönetimini otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="87324-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="87324-120">Tüm bağlı istemcilere aynı anda iletiler gönderir.</span><span class="sxs-lookup"><span data-stu-id="87324-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="87324-121">Örneğin, bir sohbet odası.</span><span class="sxs-lookup"><span data-stu-id="87324-121">For example, a chat room.</span></span>
* <span data-ttu-id="87324-122">Belirli istemcilere veya istemci gruplarına iletiler gönderir.</span><span class="sxs-lookup"><span data-stu-id="87324-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="87324-123">Artan trafiği işleyecek şekilde ölçeklendirilir.</span><span class="sxs-lookup"><span data-stu-id="87324-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="87324-124">Kaynak [ :::no-loc(SignalR)::: GitHub 'daki bir depoda](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(SignalR):::)barındırılır.</span><span class="sxs-lookup"><span data-stu-id="87324-124">The source is hosted in a [:::no-loc(SignalR)::: repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(SignalR):::).</span></span>

## <a name="transports"></a><span data-ttu-id="87324-125">Taşımalar</span><span class="sxs-lookup"><span data-stu-id="87324-125">Transports</span></span>

<span data-ttu-id="87324-126">:::no-loc(SignalR)::: gerçek zamanlı iletişimi işlemek için aşağıdaki teknikleri destekler (düzgün geri dönüş sırasında):</span><span class="sxs-lookup"><span data-stu-id="87324-126">:::no-loc(SignalR)::: supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="87324-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="87324-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="87324-128">Server-Sent olaylar</span><span class="sxs-lookup"><span data-stu-id="87324-128">Server-Sent Events</span></span>
* <span data-ttu-id="87324-129">Uzun yoklama</span><span class="sxs-lookup"><span data-stu-id="87324-129">Long Polling</span></span>

<span data-ttu-id="87324-130">:::no-loc(SignalR)::: sunucu ve istemci özellikleri içinde en iyi taşıma yöntemini otomatik olarak seçer.</span><span class="sxs-lookup"><span data-stu-id="87324-130">:::no-loc(SignalR)::: automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="87324-131">Merkezler</span><span class="sxs-lookup"><span data-stu-id="87324-131">Hubs</span></span>

<span data-ttu-id="87324-132">:::no-loc(SignalR)::: istemciler ve sunucular arasında iletişim kurmak için *hub 'ları* kullanır.</span><span class="sxs-lookup"><span data-stu-id="87324-132">:::no-loc(SignalR)::: uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="87324-133">Hub, bir istemcinin ve sunucunun birbirlerine Yöntemler çağırmasını sağlayan üst düzey bir işlem hattdır.</span><span class="sxs-lookup"><span data-stu-id="87324-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="87324-134">:::no-loc(SignalR)::: makinenin sunucu sınırları genelinde dağıtımını otomatik olarak işler ve istemcilerin sunucuda Yöntemler çağırmasını sağlar ve tam tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="87324-134">:::no-loc(SignalR)::: handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="87324-135">Model bağlamayı sağlayan yöntemlere kesin olarak yazılmış parametreler geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87324-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="87324-136">:::no-loc(SignalR)::: , iki yerleşik hub protokolünü sağlar: JSON tabanlı bir metin Protokolü ve [MessagePack](https://msgpack.org/)temelli bir ikili protokol.</span><span class="sxs-lookup"><span data-stu-id="87324-136">:::no-loc(SignalR)::: provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="87324-137">MessagePack genellikle JSON ile karşılaştırıldığında daha küçük iletiler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87324-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="87324-138">Daha eski tarayıcıların, MessagePack protokolü desteği sağlamak için [XHR düzey 2](https://caniuse.com/#feat=xhr2) ' i desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="87324-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="87324-139">Hub 'lar, istemci tarafı yönteminin adını ve parametrelerini içeren iletiler göndererek istemci tarafı kodu çağırır.</span><span class="sxs-lookup"><span data-stu-id="87324-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="87324-140">Yöntem parametreleri olarak gönderilen nesneler, yapılandırılan protokol kullanılarak seri durumdan çıkarılacak.</span><span class="sxs-lookup"><span data-stu-id="87324-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="87324-141">İstemci, adı istemci tarafı koddaki bir yöntemle eşleştirmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="87324-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="87324-142">İstemci bir eşleşme bulduğunda, yöntemini çağırır ve seri durumdan çıkarılan parametre verilerine geçirir.</span><span class="sxs-lookup"><span data-stu-id="87324-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87324-143">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="87324-143">Additional resources</span></span>

* [<span data-ttu-id="87324-144">:::no-loc(SignalR):::ASP.NET Core için kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="87324-144">Get started with :::no-loc(SignalR)::: for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="87324-145">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="87324-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="87324-146">Merkezler</span><span class="sxs-lookup"><span data-stu-id="87324-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="87324-147">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="87324-147">JavaScript client</span></span>](xref:signalr/javascript-client)
