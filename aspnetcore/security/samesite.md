---
title: cookieASP.NET Core ' de SameSite s ile çalışma
author: rick-anderson
description: "ASP.NET Core ' de aynı siteye yönelik kullanımı öğrenin cookie"
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
- 'Electron'
uid: security/samesite
ms.openlocfilehash: 6f826416e3045df32abf41e94e667120e71ae717
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051622"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="01ebb-103">cookieASP.NET Core ' de SameSite s ile çalışma</span><span class="sxs-lookup"><span data-stu-id="01ebb-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="01ebb-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01ebb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="01ebb-105">SameSite, siteler arası istek sahteciliği (CSRF) saldırılarına karşı bir koruma sağlamak için tasarlanmış bir [IETF](https://ietf.org/about/) taslak standardıdır.</span><span class="sxs-lookup"><span data-stu-id="01ebb-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="01ebb-106">[2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)içinde orijinal drafted, taslak standart [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)' de güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="01ebb-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="01ebb-107">Güncelleştirilmiş standart, önceki standartlarla geriye dönük olarak uyumlu değildir ve aşağıdakiler en belirgin farklılıklardır:</span><span class="sxs-lookup"><span data-stu-id="01ebb-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="01ebb-108">Cookie, SameSite üst bilgisi olmayan s varsayılan olarak kabul edilir `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="01ebb-109">`SameSite=None` siteler arası kullanıma izin vermek için kullanılmalıdır cookie .</span><span class="sxs-lookup"><span data-stu-id="01ebb-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="01ebb-110">Cookies bu `SameSite=None` onay ayrıca olarak işaretlenmelidir `Secure` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="01ebb-111">Kullanan uygulamalar [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) `sameSite=Lax` `sameSite=Strict` cookie `<iframe>` , siteler arası senaryolar olarak kabul edildiği için veya öğeleri ile ilgili sorunlar yaşayabilir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="01ebb-112">Değerin `SameSite=None` [2016 standart](https://tools.ietf.org/html/draft-west-first-party-cookies-07) tarafından yapılmasına izin verilmez ve bazı uygulamaların gibi bu tür öğeleri ele almasına neden olur cookie `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="01ebb-113">Bkz. bu belgede [eski tarayıcıları destekleme](#sob) .</span><span class="sxs-lookup"><span data-stu-id="01ebb-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="01ebb-114">Bu `SameSite=Lax` ayar çoğu uygulama için geçerlidir cookie .</span><span class="sxs-lookup"><span data-stu-id="01ebb-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="01ebb-115">[OpenID Connect](https://openid.net/connect/) (OIDC) ve [WS-Federation](https://auth0.com/docs/protocols/ws-fed) gibi bazı kimlik doğrulama biçimlerinden bırı, temel yönlendirmeye gönderi sağlar.</span><span class="sxs-lookup"><span data-stu-id="01ebb-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="01ebb-116">POST tabanlı yeniden yönlendirmeler, SameSite tarayıcı korumalarının tetiklenmesi, bu nedenle bu bileşenler için SameSite devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="01ebb-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="01ebb-117">Çoğu [OAuth](https://oauth.net/) oturum açma, istek akışının farklılığı nedeniyle etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="01ebb-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="01ebb-118">Her bir ASP.NET Core bileşeni, cookie SameSite ' ın uygun olup olmadığına karar vermek için gerekir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="01ebb-119">SameSite ve Identity</span><span class="sxs-lookup"><span data-stu-id="01ebb-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="01ebb-120">SameSite test örnek kodu</span><span class="sxs-lookup"><span data-stu-id="01ebb-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="01ebb-121">Aşağıdaki örnekler indirilebilir ve test edilebilir:</span><span class="sxs-lookup"><span data-stu-id="01ebb-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="01ebb-122">Örnek</span><span class="sxs-lookup"><span data-stu-id="01ebb-122">Sample</span></span>               | <span data-ttu-id="01ebb-123">Belge</span><span class="sxs-lookup"><span data-stu-id="01ebb-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="01ebb-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="01ebb-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="01ebb-125">[.NET Core Razor sayfaları](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="01ebb-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01ebb-126">Aşağıdaki örnek indirilebilir ve test edilebilir:</span><span class="sxs-lookup"><span data-stu-id="01ebb-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="01ebb-127">Örnek</span><span class="sxs-lookup"><span data-stu-id="01ebb-127">Sample</span></span>               | <span data-ttu-id="01ebb-128">Belge</span><span class="sxs-lookup"><span data-stu-id="01ebb-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="01ebb-129">[.NET Core Razor sayfaları](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="01ebb-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="01ebb-130">SameSite özniteliği için .NET Core desteği</span><span class="sxs-lookup"><span data-stu-id="01ebb-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="01ebb-131">.NET Core 2,2 ve üzeri sürümleri, Aralık 2019 ' de güncelleştirmelerin yayımlanmasından bu yana SameSite için 2019 taslak standardını destekler.</span><span class="sxs-lookup"><span data-stu-id="01ebb-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="01ebb-132">Geliştiriciler özelliği kullanarak sameSite özniteliğinin değerini programlı bir şekilde denetleyebilir `HttpCookie.SameSite` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="01ebb-133">`SameSite`Özelliği Strict, LAX veya None olarak ayarlamak, bu değerlerin ile ağda yazıldığı sonuçlara neden olur cookie .</span><span class="sxs-lookup"><span data-stu-id="01ebb-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="01ebb-134">Şuna eşit ayarı `(SameSiteMode)(-1)` , ağ üzerinde şu şekilde bir sameSite özniteliği ekleneceğini belirtir cookie</span><span class="sxs-lookup"><span data-stu-id="01ebb-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="01ebb-135">.NET Core 3,0 ve üzeri, güncelleştirilmiş SameSite değerlerini destekler ve sabit listesine ek bir sabit listesi değeri ekler `SameSiteMode.Unspecified` `SameSiteMode` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="01ebb-136">Bu yeni değer, ile hiçbir sameSite gönderilmesi gerektiğini gösterir cookie .</span><span class="sxs-lookup"><span data-stu-id="01ebb-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="01ebb-137">Aralık düzeltme eki davranış değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="01ebb-137">December patch behavior changes</span></span>

<span data-ttu-id="01ebb-138">.NET Framework ve .NET Core 2,1 için belirli davranış değişikliği `SameSite` özelliğin değeri nasıl yorumlayacağını açıklamaktadır `None` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="01ebb-139">Düzeltme ekinin bir değeri " `None` hiçbir" özniteliği hiçbir "değil" olarak bir değere yaymadan önce, düzeltme ekinin ardından "özniteliği bir değeriyle göster" anlamına gelir `None` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="01ebb-140">Düzeltme ekiyle bir `SameSite` değeri, `(SameSiteMode)(-1)` özniteliğin yayınlanmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="01ebb-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="01ebb-141">Form kimlik doğrulaması ve oturum durumu için varsayılan SameSite değeri, ' cookie dan ' a değiştirilmiştir `None` `Lax` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="01ebb-142">SameSite ile API kullanımı</span><span class="sxs-lookup"><span data-stu-id="01ebb-142">API usage with SameSite</span></span>

<span data-ttu-id="01ebb-143">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) varsayılan olarak öğesine `Unspecified` eklenir, anlamı kendisine bir SameSite özniteliği eklenmez cookie ve istemciye varsayılan davranışını (eski tarayıcılarda olmayan) kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="01ebb-144">Aşağıdaki kod, cookie SameSite değerinin nasıl değiştirileceğini göstermektedir `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="01ebb-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="01ebb-145">Tüm ASP.NET Core bileşenleri cookie , önceki varsayılan değerleri, senaryoları için uygun ayarlarla geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="01ebb-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="01ebb-146">Geçersiz kılınan önceki varsayılan değerler değişmemiştir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="01ebb-147">Bileşen</span><span class="sxs-lookup"><span data-stu-id="01ebb-147">Component</span></span> | cookie | <span data-ttu-id="01ebb-148">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="01ebb-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="01ebb-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="01ebb-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="01ebb-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="01ebb-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="01ebb-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="01ebb-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="01ebb-152">[Cookie Yetkilendirmesi](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="01ebb-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="01ebb-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="01ebb-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="01ebb-154">[Dallı bir seçenek. durum Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="01ebb-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="01ebb-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. bağıntıCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="01ebb-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="01ebb-156">[Openıdconnectoptions. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="01ebb-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="01ebb-157">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="01ebb-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="01ebb-158">ASP.NET Core 3,1 ve üzeri, aşağıdaki SameSite desteğini sağlamaktadır:</span><span class="sxs-lookup"><span data-stu-id="01ebb-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="01ebb-159">' Nin durumunu yayma davranışını tekrar tanımlar `SameSiteMode.None``SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="01ebb-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="01ebb-160">`SameSiteMode.Unspecified`SameSite özniteliğini atlamak için yeni bir değer ekler.</span><span class="sxs-lookup"><span data-stu-id="01ebb-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="01ebb-161">Tüm cookie s API 'leri için varsayılan `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="01ebb-162">S kullanan bazı bileşenler cookie , senaryolarına daha belirgin bir şekilde değer kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="01ebb-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="01ebb-163">Örnekler için yukarıdaki tabloya bakın.</span><span class="sxs-lookup"><span data-stu-id="01ebb-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01ebb-164">ASP.NET Core 3,0 ' de ve sonraki sürümlerde, tutarsız istemci varsayılanlarıyla çakışmadan kaçınmak için SameSite Varsayılanları değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="01ebb-165">Aşağıdaki API 'Ler, `SameSiteMode.Lax ` `-1` Bu öğeleri Için bir SameSite özniteliği yaymamak üzere varsayılan olan ' i olarak değiştirmiştir cookie :</span><span class="sxs-lookup"><span data-stu-id="01ebb-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="01ebb-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>[HttpContext. Response ile kullanılır. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="01ebb-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="01ebb-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  için bir fabrika olarak kullanılır `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="01ebb-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="01ebb-168">[CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="01ebb-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="01ebb-169">Geçmiş ve değişiklikler</span><span class="sxs-lookup"><span data-stu-id="01ebb-169">History and changes</span></span>

<span data-ttu-id="01ebb-170">SameSite desteği ilk olarak [2016 taslak standardı](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)kullanılarak 2,0 ' de ASP.NET Core uygulanmıştır.</span><span class="sxs-lookup"><span data-stu-id="01ebb-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="01ebb-171">2016 standardı kabul edildi.</span><span class="sxs-lookup"><span data-stu-id="01ebb-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="01ebb-172">ASP.NET Core, varsayılan olarak birkaç tane olarak ayarlanarak kabul cookie edildi `Lax` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="01ebb-173">Kimlik doğrulaması ile ilgili birkaç [sorunla](https://github.com/aspnet/Announcements/issues/318) karşılaşduktan sonra, en fazla site kullanımı [devre dışı bırakıldı](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="01ebb-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="01ebb-174">Kasım 2019 ' de 2016 standartdan 2019 standardına güncelleştirme için [düzeltme ekleri](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) yayınlandı.</span><span class="sxs-lookup"><span data-stu-id="01ebb-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="01ebb-175">[SameSite belirtiminin 2019 taslağı](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="01ebb-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="01ebb-176">, 2016 taslağı ile geriye dönük olarak uyumlu **değildir** .</span><span class="sxs-lookup"><span data-stu-id="01ebb-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="01ebb-177">Daha fazla bilgi için bu belgede [eski tarayıcıları destekleme](#sob) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="01ebb-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="01ebb-178">cookie, Varsayılan olarak olarak değerlendirilir `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="01ebb-179">cookie `SameSite=None` Siteler arası teslimin etkinleştirilmesi için açıkça onay veren öğeleri belirtir `Secure` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="01ebb-180">`None` , geri çevirmek için yeni bir giriştir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="01ebb-181">ASP.NET Core 2,1, 2,2 ve 3,0 için verilen düzeltme ekleri tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="01ebb-182">ASP.NET Core 3,1, ek SameSite desteğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="01ebb-183">, [Şubat 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)' de varsayılan olarak [Chrome](https://chromestatus.com/feature/5088147346030592) tarafından etkinleştirilmek üzere zamanlanır.</span><span class="sxs-lookup"><span data-stu-id="01ebb-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="01ebb-184">Tarayıcılar 2019 içinde bu standarda geçmeyi başlattı.</span><span class="sxs-lookup"><span data-stu-id="01ebb-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="01ebb-185">2016 SameSite taslak standartındaki değişiklikten etkilenen API 'Ler 2019 taslak standardına</span><span class="sxs-lookup"><span data-stu-id="01ebb-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="01ebb-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="01ebb-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="01ebb-187">[CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="01ebb-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="01ebb-188">[CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="01ebb-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="01ebb-189">[CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="01ebb-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="01ebb-190">Eski tarayıcıları destekleme</span><span class="sxs-lookup"><span data-stu-id="01ebb-190">Supporting older browsers</span></span>

<span data-ttu-id="01ebb-191">2016 SameSite Standard uygulanan, bilinmeyen değerlerin değer olarak değerlendirilmelidir `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="01ebb-192">2016 SameSite standardını destekleyen eski tarayıcılardan erişilen uygulamalar, bir değeri olan bir SameSite özelliği edindiklerinde kesintiye uğramayabilir `None` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="01ebb-193">Web uygulamaları, eski tarayıcıları desteklemek istiyorlarsa, tarayıcı algılaması gerçekleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="01ebb-194">ASP.NET Core tarayıcı algılamayı uygulamaz çünkü User-Agents değerler yüksek ölçüde geçici ve sık sık değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="01ebb-195">İçindeki bir uzantı noktası <xref:Microsoft.AspNetCore.CookiePolicy> , belirli bir mantık User-Agent takmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="01ebb-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="01ebb-196">İçinde `Startup.Configure` , çağrılmadan önce çağıran kodu ekleyin <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> veya şunu yazan *herhangi bir* yöntemi cookie :</span><span class="sxs-lookup"><span data-stu-id="01ebb-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="01ebb-197">' De `Startup.ConfigureServices` aşağıdakine benzer bir kod ekleyin:</span><span class="sxs-lookup"><span data-stu-id="01ebb-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="01ebb-198">Önceki örnekte, `MyUserAgentDetectionLib.DisallowsSameSiteNone` Kullanıcı aracısının SameSite 'yi desteklemeymediğini algılayan Kullanıcı tarafından sağlanan bir kitaplıktır `None` :</span><span class="sxs-lookup"><span data-stu-id="01ebb-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="01ebb-199">Aşağıdaki kod örnek bir yöntemi gösterir `DisallowsSameSiteNone` :</span><span class="sxs-lookup"><span data-stu-id="01ebb-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="01ebb-200">Aşağıdaki kod yalnızca tanıtım amaçlıdır:</span><span class="sxs-lookup"><span data-stu-id="01ebb-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="01ebb-201">Tamamlanmış olarak düşünülmemelidir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="01ebb-202">Korunmaz veya desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="01ebb-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="01ebb-203">SameSite sorunları için test uygulamaları</span><span class="sxs-lookup"><span data-stu-id="01ebb-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="01ebb-204">Üçüncü taraf oturum açma bilgileri gibi uzak sitelerle etkileşim kuran uygulamalar şunlar gerekir:</span><span class="sxs-lookup"><span data-stu-id="01ebb-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="01ebb-205">Etkileşimi birden çok tarayıcıda test edin.</span><span class="sxs-lookup"><span data-stu-id="01ebb-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="01ebb-206">Bu belgede açıklanan [ Cookie ilke tarayıcı algılamasını ve hafifletme](#sob) işlemini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="01ebb-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="01ebb-207">Yeni SameSite davranışını kabul edebilir bir istemci sürümünü kullanarak Web uygulamalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="01ebb-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="01ebb-208">Chrome, Firefox ve Kmıum Edge tümünde test için kullanılabilecek yeni bir katılım özelliği bayrakları vardır.</span><span class="sxs-lookup"><span data-stu-id="01ebb-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="01ebb-209">Uygulamanız SameSite düzeltme eklerini uyguladıktan sonra, daha eski istemci sürümleriyle test edin, özellikle Safari.</span><span class="sxs-lookup"><span data-stu-id="01ebb-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="01ebb-210">Daha fazla bilgi için bu belgede [eski tarayıcıları destekleme](#sob) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="01ebb-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="01ebb-211">Chrome ile test etme</span><span class="sxs-lookup"><span data-stu-id="01ebb-211">Test with Chrome</span></span>

<span data-ttu-id="01ebb-212">Chrome 78 + bir yerde geçici bir risk azaltma yaptığından yanıltıcı sonuçlar verir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="01ebb-213">Chrome 78 + geçici risk azaltma, cookie iki dakikadan daha eski bir süre için izin verir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="01ebb-214">Uygun test bayraklarıyla etkin olan Chrome 76 veya 77 daha doğru sonuçlar sağlar.</span><span class="sxs-lookup"><span data-stu-id="01ebb-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="01ebb-215">Yeni bir SameSite davranışını test etmek için `chrome://flags/#same-site-by-default-cookies` **etkin** olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="01ebb-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled** .</span></span> <span data-ttu-id="01ebb-216">Chrome 'un (75 ve üzeri) eski sürümleri, yeni ayar ile başarısız olarak bildirilir `None` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="01ebb-217">Bkz. bu belgede [eski tarayıcıları destekleme](#sob) .</span><span class="sxs-lookup"><span data-stu-id="01ebb-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="01ebb-218">Google, eski Chrome sürümlerini kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="01ebb-219">Chrome 'un eski sürümlerini test etmek için [Kmıum indirme](https://www.chromium.org/getting-involved/download-chromium) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="01ebb-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="01ebb-220">Chrome 'un eski sürümlerini arayarak sunulan bağlantılardan **Chrome indirmeyin** .</span><span class="sxs-lookup"><span data-stu-id="01ebb-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="01ebb-221">Kmıum 76 Win64</span><span class="sxs-lookup"><span data-stu-id="01ebb-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="01ebb-222">Kmıum 74 Win64</span><span class="sxs-lookup"><span data-stu-id="01ebb-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="01ebb-223">Canary sürümünden itibaren `80.0.3975.0` , LAX + GEÇICI sonrası risk azaltma, `--enable-features=SameSiteDefaultChecksMethodRigorously` azaltma 'nın kaldırıldığı özelliğin son bitiş durumunda siteler ve hizmetlerin test edilmesine izin vermek için yeni bayrağını kullanarak test amacıyla devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="01ebb-224">Daha fazla bilgi için, bkz. Kmıum projeleri [SameSite Updates](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="01ebb-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="01ebb-225">Safari ile test etme</span><span class="sxs-lookup"><span data-stu-id="01ebb-225">Test with Safari</span></span>

<span data-ttu-id="01ebb-226">Safari 12, önceki taslağı kesin olarak uyguladık ve yeni `None` değer bir içinde olduğunda başarısız olur cookie .</span><span class="sxs-lookup"><span data-stu-id="01ebb-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="01ebb-227">`None` Bu belgede [eski tarayıcıları destekleyen](#sob) tarayıcı algılama kodu aracılığıyla kaçınılmaz.</span><span class="sxs-lookup"><span data-stu-id="01ebb-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="01ebb-228">MSAL, ADAL veya kullandığınız herhangi bir kitaplığı kullanarak Safari 12, Safari 13 ve WebKit tabanlı işletim sistemi stili oturum açma stilini test edin.</span><span class="sxs-lookup"><span data-stu-id="01ebb-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="01ebb-229">Sorun, temel alınan işletim sistemi sürümüne bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="01ebb-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="01ebb-230">OSX Mojave (10,14) ve iOS 12 ' nin yeni SameSite davranışıyla uyumluluk sorunlarına sahip olduğu bilinmektedir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="01ebb-231">İşletim sistemini OSX Catalina (10,15) veya iOS 13 ' e yükseltmek sorunu düzeltir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="01ebb-232">Safari 'nin şu anda yeni belirtim davranışını test etmek için bir katılım bayrağı yoktur.</span><span class="sxs-lookup"><span data-stu-id="01ebb-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="01ebb-233">Firefox ile test etme</span><span class="sxs-lookup"><span data-stu-id="01ebb-233">Test with Firefox</span></span>

<span data-ttu-id="01ebb-234">Yeni standart için Firefox desteği, `about:config` sayfada özellik bayrağıyla birlikte seçilerek, 68 + sürümünde test edilebilir `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="01ebb-235">Daha eski Firefox sürümleriyle uyumluluk sorunları hakkında rapor yoktu.</span><span class="sxs-lookup"><span data-stu-id="01ebb-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="01ebb-236">Edge tarayıcısı ile test</span><span class="sxs-lookup"><span data-stu-id="01ebb-236">Test with Edge browser</span></span>

<span data-ttu-id="01ebb-237">Edge, eski SameSite standardını destekler.</span><span class="sxs-lookup"><span data-stu-id="01ebb-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="01ebb-238">Edge sürüm 44, yeni standart ile bilinen uyumluluk sorunlarına sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="01ebb-239">Edge ile test (Kmıum)</span><span class="sxs-lookup"><span data-stu-id="01ebb-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="01ebb-240">Sayfada SameSite bayrakları ayarlanır `edge://flags/#same-site-by-default-cookies` .</span><span class="sxs-lookup"><span data-stu-id="01ebb-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="01ebb-241">Edge Kmıum ile hiçbir uyumluluk sorunu bulunmadı.</span><span class="sxs-lookup"><span data-stu-id="01ebb-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="01ebb-242">Test etme Electron</span><span class="sxs-lookup"><span data-stu-id="01ebb-242">Test with Electron</span></span>

<span data-ttu-id="01ebb-243">Sürümleri, Electron Kmıum 'un eski sürümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="01ebb-244">Örneğin, Electron takımlar tarafından kullanılan sürümü, eski davranışı gösteren Kmıum 66 ' dir.</span><span class="sxs-lookup"><span data-stu-id="01ebb-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="01ebb-245">Kendi uyumluluk testinizi ürününüzün kullandığı sürümle gerçekleştirmeniz gerekir Electron .</span><span class="sxs-lookup"><span data-stu-id="01ebb-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="01ebb-246">Aşağıdaki bölümde [daha eski tarayıcıları destekleme](#sob) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="01ebb-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01ebb-247">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="01ebb-247">Additional resources</span></span>

* [<span data-ttu-id="01ebb-248">Kmıum blogu: geliştiriciler: yeni SameSite için hazırlanın = yok; Güvenli Cookie Ayarlar</span><span class="sxs-lookup"><span data-stu-id="01ebb-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="01ebb-249">[SameSite cookie açıklanıyor](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="01ebb-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="01ebb-250">Kasım 2019 düzeltme ekleri</span><span class="sxs-lookup"><span data-stu-id="01ebb-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="01ebb-251">Örnek</span><span class="sxs-lookup"><span data-stu-id="01ebb-251">Sample</span></span>               | <span data-ttu-id="01ebb-252">Belge</span><span class="sxs-lookup"><span data-stu-id="01ebb-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="01ebb-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="01ebb-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="01ebb-254">[.NET Core Razor sayfaları](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="01ebb-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="01ebb-255">Örnek</span><span class="sxs-lookup"><span data-stu-id="01ebb-255">Sample</span></span>               | <span data-ttu-id="01ebb-256">Belge</span><span class="sxs-lookup"><span data-stu-id="01ebb-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="01ebb-257">[.NET Core Razor sayfaları](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="01ebb-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
