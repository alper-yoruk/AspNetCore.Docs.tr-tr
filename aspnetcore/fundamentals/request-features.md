---
title: ASP.NET Core içindeki istek özellikleri
author: ardalis
description: ASP.NET Core arabirimlerde tanımlanan HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
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
uid: fundamentals/request-features
ms.openlocfilehash: 3b5c929519407de5dc582c10a86745efddc8a38a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634520"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="ff842-103">ASP.NET Core içindeki istek özellikleri</span><span class="sxs-lookup"><span data-stu-id="ff842-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="ff842-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="ff842-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ff842-105">HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları arabirimlerde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ff842-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="ff842-106">Bu arabirimler, uygulamanın barındırma işlem hattını oluşturmak ve değiştirmek için sunucu uygulamaları ve ara yazılım tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff842-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="ff842-107">Özellik arabirimleri</span><span class="sxs-lookup"><span data-stu-id="ff842-107">Feature interfaces</span></span>

<span data-ttu-id="ff842-108">ASP.NET Core, `Microsoft.AspNetCore.Http.Features` sunucularının destekledikleri özellikleri tanımlamak için kullandıkları bir dızı http özelliği arabirimini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="ff842-109">Aşağıdaki özellik arabirimleri istekleri ve dönüş yanıtlarını işler:</span><span class="sxs-lookup"><span data-stu-id="ff842-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="ff842-110">`IHttpRequestFeature` Protokol, yol, sorgu dizesi, üst bilgiler ve gövde dahil olmak üzere bir HTTP isteğinin yapısını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="ff842-111">`IHttpResponseFeature` Durum kodu, üst bilgiler ve yanıtın gövdesi dahil olmak üzere bir HTTP yanıtının yapısını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="ff842-112">`IHttpAuthenticationFeature``ClaimsPrincipal`, Ve bir kimlik doğrulama işleyicisi belirterek kullanıcıları tanımlama desteğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="ff842-113">`IHttpUpgradeFeature` Sunucu, protokolleri değiştirmek isterse, istemcinin kullanmak istediğiniz ek protokolleri belirtmesini sağlayan [http yükseltmeleri](https://tools.ietf.org/html/rfc2616.html#section-14.42)için desteği tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="ff842-114">`IHttpBufferingFeature` İsteklerin ve/veya yanıtlarının arabelleğe alınması devre dışı bırakma yöntemlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="ff842-115">`IHttpConnectionFeature` Yerel ve uzak adresler ve bağlantı noktaları için özellikleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="ff842-116">`IHttpRequestLifetimeFeature` Bağlantıların iptal edilme desteğini tanımlar veya bir isteğin erken sonlandırılıp sonlandırılmayacağını (örneğin, bir istemci bağlantısı kesildiğini) belirler.</span><span class="sxs-lookup"><span data-stu-id="ff842-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="ff842-117">`IHttpSendFileFeature` Dosyaları zaman uyumsuz olarak göndermek için bir yöntem tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="ff842-118">`IHttpWebSocketFeature` Web yuvalarını desteklemek için bir API tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="ff842-119">`IHttpRequestIdentifierFeature` İstekleri benzersiz şekilde tanımlamak için uygulanabilecek bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="ff842-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="ff842-120">`ISessionFeature``ISessionFactory` `ISession` Kullanıcı oturumlarını desteklemek için tanımlar ve soyutlamalar tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="ff842-121">`ITlsConnectionFeature` İstemci sertifikalarını almak için bir API tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="ff842-122">`ITlsTokenBindingFeature` TLS belirteci bağlama parametreleriyle çalışma yöntemlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="ff842-123">`ISessionFeature` bir sunucu özelliği değildir, ancak `SessionMiddleware` (bkz. [uygulama durumunu yönetme](app-state.md)) tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ff842-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="ff842-124">Özellik koleksiyonları</span><span class="sxs-lookup"><span data-stu-id="ff842-124">Feature collections</span></span>

<span data-ttu-id="ff842-125">`Features`Özelliği, `HttpContext` geçerli istek IÇIN kullanılabilir http özelliklerini almak ve ayarlamak için bir arabirim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="ff842-126">Özellik koleksiyonu bir istek bağlamı içinde bile değişebilir olduğundan, ara yazılım, koleksiyonu değiştirmek ve ek özellikler için destek eklemek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="ff842-127">Ara yazılım ve istek özellikleri</span><span class="sxs-lookup"><span data-stu-id="ff842-127">Middleware and request features</span></span>

<span data-ttu-id="ff842-128">Sunucular Özellik koleksiyonu oluşturmaktan sorumludur, ancak ara yazılım bu koleksiyona ekleyebilir ve koleksiyondaki özellikleri kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="ff842-129">Örneğin, `StaticFileMiddleware` `IHttpSendFileFeature` özelliğine erişir.</span><span class="sxs-lookup"><span data-stu-id="ff842-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="ff842-130">Özellik varsa, istenen statik dosyayı fiziksel yolundan göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff842-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="ff842-131">Aksi takdirde, dosyayı göndermek için daha yavaş bir alternatif yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff842-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="ff842-132">Kullanılabilir olduğunda, `IHttpSendFileFeature` işletim sisteminin dosyayı açmasına ve ağ kartına doğrudan bir çekirdek modu kopyalaması gerçekleştirmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="ff842-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="ff842-133">Ayrıca, ara yazılım, sunucu tarafından belirlenen özellik koleksiyonuna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="ff842-134">Mevcut özellikler, ara yazılım tarafından da değiştirilebilir ve bu da ara yazılım, sunucunun işlevselliğini artırabilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="ff842-135">Koleksiyona eklenen özellikler, daha sonra istek ardışık düzeninde bulunan diğer ara yazılım veya temeldeki uygulamanın kendisi için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="ff842-136">Özel sunucu uygulamalarını ve belirli ara yazılım geliştirmelerini birleştirerek, bir uygulamanın gerektirdiği kesin özellikler kümesi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="ff842-137">Bu, sunucuda değişiklik gerektirmeden eksik özelliklerin eklenmesine izin verir ve yalnızca en düşük miktarda özelliğin gösterilmesini sağlar, böylece saldırı yüzeyi alanını kısıtlar ve performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="ff842-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="ff842-138">Özet</span><span class="sxs-lookup"><span data-stu-id="ff842-138">Summary</span></span>

<span data-ttu-id="ff842-139">Özellik arabirimleri, belirli bir isteğin destekleyebileceği belirli HTTP özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="ff842-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="ff842-140">Sunucular, özellik koleksiyonlarını ve bu sunucu tarafından desteklenen ilk özellik kümesini tanımlar, ancak bu özellikleri geliştirmek için ara yazılım kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff842-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff842-141">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ff842-141">Additional resources</span></span>

* [<span data-ttu-id="ff842-142">Sunucular</span><span class="sxs-lookup"><span data-stu-id="ff842-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="ff842-143">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="ff842-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ff842-144">.NET için Açık Web Arabirimi (OWIN)</span><span class="sxs-lookup"><span data-stu-id="ff842-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
