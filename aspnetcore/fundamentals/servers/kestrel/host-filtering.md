---
title: ASP.NET Core Kestrel Web sunucusuyla konak filtrelemesi
author: rick-anderson
description: ASP.NET Core için platformlar arası Web sunucusu olan Kestrel ile konak filtrelemeyi kullanma hakkında bilgi edinin.
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253992"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="da0c5-103">ASP.NET Core Kestrel Web sunucusuyla konak filtrelemesi</span><span class="sxs-lookup"><span data-stu-id="da0c5-103">Host filtering with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="da0c5-104">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="da0c5-104">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="da0c5-105">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="da0c5-105">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="da0c5-106">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="da0c5-106">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="da0c5-107">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="da0c5-107">`Host` headers aren't validated.</span></span>

<span data-ttu-id="da0c5-108">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="da0c5-108">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="da0c5-109">Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="da0c5-109">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="da0c5-110">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A> :</span><span class="sxs-lookup"><span data-stu-id="da0c5-110">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>:</span></span>

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="da0c5-111">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="da0c5-111">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="da0c5-112">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="da0c5-112">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="da0c5-113">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="da0c5-113">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="da0c5-114">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="da0c5-114">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="da0c5-115">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="da0c5-115">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="da0c5-116">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="da0c5-116">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="da0c5-117">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="da0c5-117">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="da0c5-118">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="da0c5-118">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="da0c5-119">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="da0c5-119">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>
