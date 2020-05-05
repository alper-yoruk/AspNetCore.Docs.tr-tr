---
title: ASP.NET Core girişSignalR
author: bradygaster
description: ASP.NET Core SignalR kitaplığının uygulamalara gerçek zamanlı işlevsellik eklemeyi nasıl basitleştireceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 4e968659d23330c4b1403ab998dda9c9e6ebe080
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775784"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="474e2-103">ASP.NET Core girişSignalR</span><span class="sxs-lookup"><span data-stu-id="474e2-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="474e2-104">Nedir SignalR?</span><span class="sxs-lookup"><span data-stu-id="474e2-104">What is SignalR?</span></span>

<span data-ttu-id="474e2-105">ASP.NET Core SignalR , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran açık kaynaklı bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="474e2-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="474e2-106">Gerçek zamanlı Web işlevselliği, sunucu tarafı kodun anında istemcilere içerik gönderebilmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="474e2-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="474e2-107">İçin SignalRiyi adaylar:</span><span class="sxs-lookup"><span data-stu-id="474e2-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="474e2-108">Sunucudan yüksek sıklıkta güncelleştirmeler gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="474e2-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="474e2-109">Oyun, sosyal ağlar, oylama, açık artırma, haritalar ve GPS uygulamaları bunlara örnektir.</span><span class="sxs-lookup"><span data-stu-id="474e2-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="474e2-110">Panolar ve izleme uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="474e2-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="474e2-111">Şirket panoları, anlık satış güncelleştirmeleri veya seyahat uyarıları bunlara örnektir.</span><span class="sxs-lookup"><span data-stu-id="474e2-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="474e2-112">İş birliği uygulamaları.</span><span class="sxs-lookup"><span data-stu-id="474e2-112">Collaborative apps.</span></span> <span data-ttu-id="474e2-113">Beyaz tahta uygulamaları ve takım toplantısı yazılımları, iş birliği uygulamalarına örnektir.</span><span class="sxs-lookup"><span data-stu-id="474e2-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="474e2-114">Bildirim gerektiren uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="474e2-114">Apps that require notifications.</span></span> <span data-ttu-id="474e2-115">Sosyal ağlar, e-posta, sohbet, oyunlar, seyahat uyarıları ve diğer birçok uygulama, bildirimleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="474e2-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="474e2-116">sunucudan istemciye [uzak yordam çağrıları (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)oluşturmak IÇIN bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="474e2-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="474e2-117">RPC 'ler, sunucu tarafı .NET Core kodundan gelen istemcilerdeki JavaScript işlevlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="474e2-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="474e2-118">ASP.NET Core SignalR için bazı özellikler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="474e2-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="474e2-119">Bağlantı yönetimini otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="474e2-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="474e2-120">Tüm bağlı istemcilere aynı anda iletiler gönderir.</span><span class="sxs-lookup"><span data-stu-id="474e2-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="474e2-121">Örneğin, bir sohbet odası.</span><span class="sxs-lookup"><span data-stu-id="474e2-121">For example, a chat room.</span></span>
* <span data-ttu-id="474e2-122">Belirli istemcilere veya istemci gruplarına iletiler gönderir.</span><span class="sxs-lookup"><span data-stu-id="474e2-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="474e2-123">Artan trafiği işleyecek şekilde ölçeklendirilir.</span><span class="sxs-lookup"><span data-stu-id="474e2-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="474e2-124">Kaynak [ SignalR GitHub 'daki bir depoda](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)barındırılır.</span><span class="sxs-lookup"><span data-stu-id="474e2-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="474e2-125">Taşımalar</span><span class="sxs-lookup"><span data-stu-id="474e2-125">Transports</span></span>

SignalR<span data-ttu-id="474e2-126">gerçek zamanlı iletişimi işlemek için aşağıdaki teknikleri destekler (düzgün geri dönüş sırasında):</span><span class="sxs-lookup"><span data-stu-id="474e2-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="474e2-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="474e2-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="474e2-128">Sunucu tarafından gönderilen olaylar</span><span class="sxs-lookup"><span data-stu-id="474e2-128">Server-Sent Events</span></span>
* <span data-ttu-id="474e2-129">Uzun yoklama</span><span class="sxs-lookup"><span data-stu-id="474e2-129">Long Polling</span></span>

SignalR<span data-ttu-id="474e2-130">sunucu ve istemci özellikleri içinde en iyi taşıma yöntemini otomatik olarak seçer.</span><span class="sxs-lookup"><span data-stu-id="474e2-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="474e2-131">Merkezler</span><span class="sxs-lookup"><span data-stu-id="474e2-131">Hubs</span></span>

SignalR<span data-ttu-id="474e2-132">istemciler ve sunucular arasında iletişim kurmak için *hub 'ları* kullanır.</span><span class="sxs-lookup"><span data-stu-id="474e2-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="474e2-133">Hub, bir istemcinin ve sunucunun birbirlerine Yöntemler çağırmasını sağlayan üst düzey bir işlem hattdır.</span><span class="sxs-lookup"><span data-stu-id="474e2-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="474e2-134">makinenin sunucu sınırları genelinde dağıtımını otomatik olarak işler ve istemcilerin sunucuda Yöntemler çağırmasını sağlar ve tam tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="474e2-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="474e2-135">Model bağlamayı sağlayan yöntemlere kesin olarak yazılmış parametreler geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="474e2-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="474e2-136">, iki yerleşik hub protokolünü sağlar: JSON tabanlı bir metin Protokolü ve [MessagePack](https://msgpack.org/)temelli bir ikili protokol.</span><span class="sxs-lookup"><span data-stu-id="474e2-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="474e2-137">MessagePack genellikle JSON ile karşılaştırıldığında daha küçük iletiler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="474e2-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="474e2-138">Daha eski tarayıcıların, MessagePack protokolü desteği sağlamak için [XHR düzey 2](https://caniuse.com/#feat=xhr2) ' i desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="474e2-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="474e2-139">Hub 'lar, istemci tarafı yönteminin adını ve parametrelerini içeren iletiler göndererek istemci tarafı kodu çağırır.</span><span class="sxs-lookup"><span data-stu-id="474e2-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="474e2-140">Yöntem parametreleri olarak gönderilen nesneler, yapılandırılan protokol kullanılarak seri durumdan çıkarılacak.</span><span class="sxs-lookup"><span data-stu-id="474e2-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="474e2-141">İstemci, adı istemci tarafı koddaki bir yöntemle eşleştirmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="474e2-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="474e2-142">İstemci bir eşleşme bulduğunda, yöntemini çağırır ve seri durumdan çıkarılan parametre verilerine geçirir.</span><span class="sxs-lookup"><span data-stu-id="474e2-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="474e2-143">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="474e2-143">Additional resources</span></span>

* <span data-ttu-id="474e2-144">[ASP.NET Core SignalR için kullanmaya başlama](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="474e2-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="474e2-145">Desteklenen platformlar</span><span class="sxs-lookup"><span data-stu-id="474e2-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="474e2-146">Merkezler</span><span class="sxs-lookup"><span data-stu-id="474e2-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="474e2-147">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="474e2-147">JavaScript client</span></span>](xref:signalr/javascript-client)
