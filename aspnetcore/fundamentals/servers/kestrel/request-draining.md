---
title: ASP.NET Core Kestrel Web sunucusu ile boşaltma isteği
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel ile istek boşaltma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253980"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="d3659-103">ASP.NET Core Kestrel Web sunucusu ile boşaltma isteği</span><span class="sxs-lookup"><span data-stu-id="d3659-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="d3659-104">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="d3659-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d3659-105">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="d3659-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d3659-106">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3659-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d3659-107">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d3659-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d3659-108">Uygulama, sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü HTTP POST istekleri gibi istek gövdesini her zaman tüketmez.</span><span class="sxs-lookup"><span data-stu-id="d3659-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d3659-109">HTTP yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="d3659-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="d3659-110">İstemci, POST verilerinin bir bölümünü zaten göndermiş olabilir.</span><span class="sxs-lookup"><span data-stu-id="d3659-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="d3659-111">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="d3659-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="d3659-112">Önceki istek gövdesinden POST verileri tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d3659-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d3659-113">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3659-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d3659-114">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d3659-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d3659-115">Boşaltma işlemi, bağlantının yeniden kullanılmasına izin verilmesi ve kalan verilerin boşaltımı için geçen süre arasında bir zorunluluğunu getirir oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d3659-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d3659-116">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="d3659-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d3659-117">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="d3659-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d3659-118">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d3659-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d3659-119">Örneğin, istemciler sınırlı veri üst sınırı içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d3659-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="d3659-120">Karşıya yüklenen verileri sınırlama bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="d3659-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d3659-121">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="d3659-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d3659-122">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="d3659-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d3659-123">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d3659-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d3659-124">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="d3659-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d3659-125">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d3659-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d3659-126">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="d3659-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d3659-127">Örneğin, `Abort` kötü amaçlı istemciler VERI göndermeye çalışıyorsa veya istemci kodunda büyük veya çok sayıda istek oluşmasına neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="d3659-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="d3659-128">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="d3659-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d3659-129">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d3659-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d3659-130">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="d3659-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d3659-131">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="d3659-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d3659-132">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d3659-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="d3659-133">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="d3659-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d3659-134">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="d3659-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d3659-135">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanması ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="d3659-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d3659-136">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="d3659-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
