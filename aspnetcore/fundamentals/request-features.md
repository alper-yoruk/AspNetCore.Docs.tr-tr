---
title: ASP.NET Core'daki İstek Özellikleri
author: ardalis
description: ASP.NET Core arabirimlerinde tanımlanan HTTP istekleri ve yanıtları ile ilgili web sunucusu uygulama ayrıntıları hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416228"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="e9878-103">ASP.NET Core'daki İstek Özellikleri</span><span class="sxs-lookup"><span data-stu-id="e9878-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="e9878-104">Yazar: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e9878-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e9878-105">HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları arabirimlerde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="e9878-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="e9878-106">Bu arabirimler, sunucu uygulamaları ve ara yazılımlar tarafından uygulamanın barındırma ardışık hattını oluşturmak ve değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9878-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="e9878-107">Özellik arabirimleri</span><span class="sxs-lookup"><span data-stu-id="e9878-107">Feature interfaces</span></span>

<span data-ttu-id="e9878-108">ASP.NET Core, sunucular `Microsoft.AspNetCore.Http.Features` tarafından destekledikleri özellikleri tanımlamak için kullanılan bir dizi HTTP özellik arabirimi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="e9878-109">Aşağıdaki özellik arabirimleri istekleri ve yanıtları döndürün:</span><span class="sxs-lookup"><span data-stu-id="e9878-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="e9878-110">`IHttpRequestFeature`Protokol, yol, sorgu dizesi, üstbilgi ve gövde dahil olmak üzere bir HTTP isteğinin yapısını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="e9878-111">`IHttpResponseFeature`Durum kodu, üstbilgi ve yanıtın gövdesi de dahil olmak üzere bir HTTP yanıtının yapısını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="e9878-112">`IHttpAuthenticationFeature`Kullanıcıları bir `ClaimsPrincipal` temele göre tanımlamak ve kimlik doğrulaması belirleyen desteği tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="e9878-113">`IHttpUpgradeFeature`Sunucu protokolleri değiştirmek isterse hangi ek protokolleri kullanmak istediğini belirtmek için istemciye izin veren [HTTP Yükseltmeleri](https://tools.ietf.org/html/rfc2616.html#section-14.42)desteği tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="e9878-114">`IHttpBufferingFeature`İstek lerin ve/veya yanıtların arabelleğe alma yöntemlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="e9878-115">`IHttpConnectionFeature`Yerel ve uzak adresler ve bağlantı noktaları için özellikleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="e9878-116">`IHttpRequestLifetimeFeature`Bağlantıların iptali veya istemci bağlantısının kesilmesi gibi bir isteğin zamanından önce sonlandırıldığını algılama desteği tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="e9878-117">`IHttpSendFileFeature`Dosyaları eşit bir şekilde göndermek için bir yöntem tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="e9878-118">`IHttpWebSocketFeature`Web yuvalarını desteklemek için bir API tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="e9878-119">`IHttpRequestIdentifierFeature`İstekleri benzersiz olarak tanımlamak için uygulanabilecek bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="e9878-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="e9878-120">`ISessionFeature`Kullanıcı `ISessionFactory` oturumlarını desteklemek için tanımlar ve `ISession` soyutlamalar.</span><span class="sxs-lookup"><span data-stu-id="e9878-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="e9878-121">`ITlsConnectionFeature`İstemci sertifikalarını almak için bir API tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="e9878-122">`ITlsTokenBindingFeature`TLS belirteç bağlama parametreleri ile çalışma yöntemlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="e9878-123">`ISessionFeature`bir sunucu özelliği değildir, ancak `SessionMiddleware` (bkz. [Uygulama Durumunu Yönetme)](app-state.md)tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e9878-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="e9878-124">Özellik koleksiyonları</span><span class="sxs-lookup"><span data-stu-id="e9878-124">Feature collections</span></span>

<span data-ttu-id="e9878-125">Özelliği, `Features` `HttpContext` geçerli istek için kullanılabilir HTTP özelliklerini almak ve ayarlamak için bir arabirim sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="e9878-126">Özellik koleksiyonu bir istek bağlamında bile tustubl olduğundan, ara yazılım koleksiyonu değiştirmek ve ek özellikler için destek eklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e9878-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="e9878-127">Ara yazılım ve istek özellikleri</span><span class="sxs-lookup"><span data-stu-id="e9878-127">Middleware and request features</span></span>

<span data-ttu-id="e9878-128">Sunucular özellik koleksiyonunu oluşturmaktan sorumlu olsa da, ara yazılımlar hem bu koleksiyona ekleyebilir hem de koleksiyondaki özellikleri tüketebilir.</span><span class="sxs-lookup"><span data-stu-id="e9878-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="e9878-129">Örneğin, `IHttpSendFileFeature` özellik `StaticFileMiddleware` erişir.</span><span class="sxs-lookup"><span data-stu-id="e9878-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="e9878-130">Özellik varsa, istenen statik dosyayı fiziksel yolundan göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9878-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="e9878-131">Aksi takdirde, dosyayı göndermek için daha yavaş bir alternatif yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9878-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="e9878-132">Kullanılabilir olduğunda, `IHttpSendFileFeature` işletim sisteminin dosyayı açmasına ve ağ kartına doğrudan çekirdek modu kopyasını gerçekleştirmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e9878-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="e9878-133">Ayrıca, ara yazılım sunucu tarafından kurulan özellik koleksiyonuna ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9878-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="e9878-134">Mevcut özellikler, ara yazılımın sunucunun işlevselliğini genişletmesine olanak tanıyan ara yazılımlarla bile değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e9878-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="e9878-135">Koleksiyona eklenen özellikler, istek ardışık alanında hemen diğer ara yazılımlara veya altta yatan uygulamanın kendisine kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e9878-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="e9878-136">Özel sunucu uygulamaları ve belirli ara yazılım geliştirmeleri birleştirilerek, bir uygulamanın gerektirdiği kesin özellik kümesi oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="e9878-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="e9878-137">Bu, eksik özelliklerin sunucuda değişiklik gerektirmeden eklenmesine olanak tanır ve yalnızca en az sayıda özelliğin açığa çıkarolmasını sağlayarak saldırı yüzey alanını sınırlandırarak performansı artırır.</span><span class="sxs-lookup"><span data-stu-id="e9878-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="e9878-138">Özet</span><span class="sxs-lookup"><span data-stu-id="e9878-138">Summary</span></span>

<span data-ttu-id="e9878-139">Özellik arabirimleri, belirli bir isteğin desteklenebilen belirli HTTP özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e9878-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="e9878-140">Sunucular, özellik koleksiyonlarını ve bu sunucu tarafından desteklenen ilk özellik kümesini tanımlar, ancak bu özellikleri geliştirmek için ara yazılımkullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e9878-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9878-141">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9878-141">Additional resources</span></span>

* [<span data-ttu-id="e9878-142">Sunucular</span><span class="sxs-lookup"><span data-stu-id="e9878-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="e9878-143">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="e9878-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="e9878-144">.NET için Açık Web Arabirimi (OWIN)</span><span class="sxs-lookup"><span data-stu-id="e9878-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
