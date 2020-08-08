---
title: ASP.NET Core için gRPC 'de güvenlik konuları
author: jamesnk
description: ASP.NET Core için gRPC güvenlik konuları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 4c805ab1e01b4c70644a12c277441d1d242ce720
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016004"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="dc956-103">ASP.NET Core için gRPC 'de güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="dc956-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="dc956-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="dc956-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="dc956-105">Bu makalede, gRPC 'yi .NET Core ile güvenli hale getirme hakkında bilgi verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="dc956-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="dc956-106">Taşıma güvenliği</span><span class="sxs-lookup"><span data-stu-id="dc956-106">Transport security</span></span>

<span data-ttu-id="dc956-107">gRPC iletileri HTTP/2 kullanılarak gönderilir ve alınır.</span><span class="sxs-lookup"><span data-stu-id="dc956-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="dc956-108">Şunları öneririz:</span><span class="sxs-lookup"><span data-stu-id="dc956-108">We recommend:</span></span>

* <span data-ttu-id="dc956-109">[Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246) , üretim GRPC uygulamalarında iletileri güvenli hale getirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc956-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="dc956-110">gRPC Hizmetleri yalnızca güvenli bağlantı noktalarını dinler ve bunlara yanıt vermelidir.</span><span class="sxs-lookup"><span data-stu-id="dc956-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="dc956-111">TLS, Kestrel içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="dc956-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="dc956-112">Kestrel uç noktalarını yapılandırma hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="dc956-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="dc956-113">Özel durumlar</span><span class="sxs-lookup"><span data-stu-id="dc956-113">Exceptions</span></span>

<span data-ttu-id="dc956-114">Özel durum iletileri genellikle bir istemciye görüntülenmemelidir gizli veriler olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="dc956-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="dc956-115">Varsayılan olarak, gRPC, bir gRPC hizmeti tarafından oluşturulan özel durumun ayrıntılarını istemciye göndermez.</span><span class="sxs-lookup"><span data-stu-id="dc956-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="dc956-116">Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır.</span><span class="sxs-lookup"><span data-stu-id="dc956-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="dc956-117">İstemciye özel durum iletisi teslimi, [Enabledetailederrors](xref:grpc/configuration#configure-services-options)ile geçersiz kılınabilir (örneğin, geliştirme veya test).</span><span class="sxs-lookup"><span data-stu-id="dc956-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="dc956-118">Özel durum iletileri, üretim uygulamalarındaki istemciye gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="dc956-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="dc956-119">İleti boyutu sınırları</span><span class="sxs-lookup"><span data-stu-id="dc956-119">Message size limits</span></span>

<span data-ttu-id="dc956-120">GRPC istemcilerine ve hizmetlerine gelen iletiler belleğe yüklenir.</span><span class="sxs-lookup"><span data-stu-id="dc956-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="dc956-121">İleti boyutu sınırları, gRPC 'nin aşırı kaynak tüketmesini önlemeye yardımcı olmak için bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="dc956-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="dc956-122">gRPC gelen ve giden iletileri yönetmek için ileti başına boyut sınırlarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc956-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="dc956-123">Varsayılan olarak, gRPC gelen iletileri 4 MB ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="dc956-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="dc956-124">Giden iletilerde sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="dc956-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="dc956-125">Sunucusunda, gRPC ileti limitleri bir uygulamadaki tüm hizmetler için ile yapılandırılabilir `AddGrpc` :</span><span class="sxs-lookup"><span data-stu-id="dc956-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="dc956-126">Ayrıca, kullanılarak tek bir hizmet için sınırlamalar da yapılandırılabilir `AddServiceOptions<TService>` .</span><span class="sxs-lookup"><span data-stu-id="dc956-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="dc956-127">İleti boyutu sınırlarını yapılandırma hakkında daha fazla bilgi için bkz. [GRPC yapılandırması](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="dc956-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="dc956-128">İstemci sertifikası doğrulaması</span><span class="sxs-lookup"><span data-stu-id="dc956-128">Client certificate validation</span></span>

<span data-ttu-id="dc956-129">[İstemci sertifikaları](https://tools.ietf.org/html/rfc5246#section-7.4.4) , bağlantı oluşturulduğunda başlangıçta onaylanır.</span><span class="sxs-lookup"><span data-stu-id="dc956-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="dc956-130">Varsayılan olarak, Kestrel bir bağlantının istemci sertifikası için ek doğrulama gerçekleştirmez.</span><span class="sxs-lookup"><span data-stu-id="dc956-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="dc956-131">İstemci sertifikaları tarafından güvenliği sağlanan gRPC hizmetlerinin [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) paketini kullanması önerilir.</span><span class="sxs-lookup"><span data-stu-id="dc956-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="dc956-132">ASP.NET Core sertifika kimlik doğrulaması, bir istemci sertifikasında aşağıdakiler de dahil olmak üzere ek doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="dc956-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="dc956-133">Sertifikada geçerli bir genişletilmiş anahtar kullanımı (EKU) vardır</span><span class="sxs-lookup"><span data-stu-id="dc956-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="dc956-134">Geçerlilik süresi içinde</span><span class="sxs-lookup"><span data-stu-id="dc956-134">Is within its validity period</span></span>
* <span data-ttu-id="dc956-135">Sertifika iptalini denetle</span><span class="sxs-lookup"><span data-stu-id="dc956-135">Check certificate revocation</span></span>
