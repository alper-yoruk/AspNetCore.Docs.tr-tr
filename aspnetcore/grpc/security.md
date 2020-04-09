---
title: ASP.NET Core için gRPC'de güvenlik hususları
author: jamesnk
description: ASP.NET Core için gRPC için güvenlik konuları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667323"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="c4699-103">ASP.NET Core için gRPC'de güvenlik hususları</span><span class="sxs-lookup"><span data-stu-id="c4699-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="c4699-104">Yazar: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c4699-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c4699-105">Bu makalede, .NET Core ile gRPC güvenliğini sağlama hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4699-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="c4699-106">Ulaşım güvenliği</span><span class="sxs-lookup"><span data-stu-id="c4699-106">Transport security</span></span>

<span data-ttu-id="c4699-107">gRPC mesajları HTTP/2 kullanılarak gönderilir ve alınır.</span><span class="sxs-lookup"><span data-stu-id="c4699-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="c4699-108">Şunları öneririz:</span><span class="sxs-lookup"><span data-stu-id="c4699-108">We recommend:</span></span>

* <span data-ttu-id="c4699-109">[Taşıma Katmanı Güvenliği (TLS),](https://tools.ietf.org/html/rfc5246) üretim gRPC uygulamalarındaki iletileri güvenli hale getirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4699-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="c4699-110">gRPC hizmetleri yalnızca güvenli bağlantı noktaları üzerinden dinlemeli ve yanıt vermelidir.</span><span class="sxs-lookup"><span data-stu-id="c4699-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="c4699-111">TLS Kerkenez'de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c4699-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="c4699-112">Kestrel uç noktalarının yapılandırılması hakkında daha fazla bilgi için [Kerkenez uç noktası](xref:fundamentals/servers/kestrel#endpoint-configuration)yapılandırması'na bakın.</span><span class="sxs-lookup"><span data-stu-id="c4699-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="c4699-113">Özel Durumlar</span><span class="sxs-lookup"><span data-stu-id="c4699-113">Exceptions</span></span>

<span data-ttu-id="c4699-114">Özel durum iletileri genellikle istemciye açıklanmaması gereken hassas veriler olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c4699-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="c4699-115">Varsayılan olarak, gRPC istemciye bir gRPC hizmeti tarafından atılan bir özel durum ayrıntılarını göndermez.</span><span class="sxs-lookup"><span data-stu-id="c4699-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="c4699-116">Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır.</span><span class="sxs-lookup"><span data-stu-id="c4699-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="c4699-117">İstemciye özel durum iletisi [teslimi, EnableDetailedErrors](xref:grpc/configuration#configure-services-options)ile (örneğin, geliştirme veya testte) geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="c4699-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="c4699-118">Özel durum iletileri üretim uygulamalarında istemciye maruz kalmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4699-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="c4699-119">İleti boyutu sınırları</span><span class="sxs-lookup"><span data-stu-id="c4699-119">Message size limits</span></span>

<span data-ttu-id="c4699-120">gRPC istemcilerine ve hizmetlerine gelen iletiler belleğe yüklenir.</span><span class="sxs-lookup"><span data-stu-id="c4699-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="c4699-121">İleti boyutu sınırları, gRPC'nin aşırı kaynak tüketmesini önlemeye yardımcı olan bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="c4699-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="c4699-122">gRPC gelen ve giden iletileri yönetmek için ileti başına boyut sınırları kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4699-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="c4699-123">Varsayılan olarak, gRPC gelen iletileri 4 MB ile sınırlar.</span><span class="sxs-lookup"><span data-stu-id="c4699-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="c4699-124">Giden iletilerde sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="c4699-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="c4699-125">Sunucuda, gRPC ileti limitleri aşağıdaki bir uygulamadaki `AddGrpc`tüm hizmetler için yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="c4699-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

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

<span data-ttu-id="c4699-126">Limitler, tek bir hizmet için `AddServiceOptions<TService>`de yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4699-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="c4699-127">İleti boyutu sınırlarını yapılandırma hakkında daha fazla bilgi için [gRPC yapılandırması'na](xref:grpc/configuration)bakın.</span><span class="sxs-lookup"><span data-stu-id="c4699-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="c4699-128">İstemci sertifikası doğrulama</span><span class="sxs-lookup"><span data-stu-id="c4699-128">Client certificate validation</span></span>

<span data-ttu-id="c4699-129">[Bağlantı](https://tools.ietf.org/html/rfc5246#section-7.4.4) kurulduğunda istemci sertifikaları başlangıçta doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="c4699-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="c4699-130">Varsayılan olarak, Kestrel bir bağlantıistemci sertifikasıek doğrulama gerçekleştirmez.</span><span class="sxs-lookup"><span data-stu-id="c4699-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="c4699-131">İstemci sertifikaları tarafından güvence altına verilen gRPC hizmetlerinin [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) paketini kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="c4699-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="c4699-132">ASP.NET Core sertifika kimlik doğrulaması, istemci sertifikasında şunları dahil olmak üzere ek doğrulama gerçekleştirecektir:</span><span class="sxs-lookup"><span data-stu-id="c4699-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="c4699-133">Sertifikanın geçerli bir genişletilmiş anahtar kullanımı (EKU) vardır</span><span class="sxs-lookup"><span data-stu-id="c4699-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="c4699-134">Geçerlilik süresi içinde</span><span class="sxs-lookup"><span data-stu-id="c4699-134">Is within its validity period</span></span>
* <span data-ttu-id="c4699-135">Sertifika iptalini denetle</span><span class="sxs-lookup"><span data-stu-id="c4699-135">Check certificate revocation</span></span>
