---
title: ASP.NET Core 'de ilke şemaları
author: rick-anderson
description: Kimlik doğrulama ilkesi şemaları, tek bir mantıksal kimlik doğrulama şemasına sahip olmasını kolaylaştırır
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053234"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="cc8f9-103">ASP.NET Core 'de ilke şemaları</span><span class="sxs-lookup"><span data-stu-id="cc8f9-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="cc8f9-104">Kimlik doğrulama ilkesi şemaları, tek bir mantıksal kimlik doğrulama düzeninin birden çok yaklaşım kullanmasına daha kolay olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="cc8f9-105">Örneğin, bir ilke şeması, sorunlar için Google kimlik doğrulaması ve :::no-loc(cookie)::: diğer her şey için kimlik doğrulaması kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-105">For example, a policy scheme might use Google authentication for challenges, and :::no-loc(cookie)::: authentication for everything else.</span></span> <span data-ttu-id="cc8f9-106">Kimlik doğrulama ilkesi şemaları şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="cc8f9-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="cc8f9-107">Herhangi bir kimlik doğrulama eylemini başka bir düzene iletmek kolay.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="cc8f9-108">İsteğe göre dinamik olarak ilet.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="cc8f9-109">Türetilmiş <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> ve Ilişkili [authenticationhandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)kullanan tüm kimlik doğrulama şemaları:</span><span class="sxs-lookup"><span data-stu-id="cc8f9-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="cc8f9-110">ASP.NET Core 2,1 ve sonraki sürümlerde otomatik olarak ilke düzenleridir.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="cc8f9-111">Düzenin seçenekleri yapılandırılarak etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="cc8f9-112">Örnekler</span><span class="sxs-lookup"><span data-stu-id="cc8f9-112">Examples</span></span>

<span data-ttu-id="cc8f9-113">Aşağıdaki örnek, alt düzey şemaları birleştiren daha yüksek düzey bir düzeni gösterir.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="cc8f9-114">Daha zorluk için Google kimlik doğrulaması kullanılır ve :::no-loc(cookie)::: diğer her şey için kimlik doğrulaması kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cc8f9-114">Google authentication is used for challenges, and :::no-loc(cookie)::: authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="cc8f9-115">Aşağıdaki örnek, istek temelinde dinamik düzen seçimini mümkün bir şekilde sunar.</span><span class="sxs-lookup"><span data-stu-id="cc8f9-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="cc8f9-116">Diğer bir deyişle, :::no-loc(cookie)::: ve API kimlik doğrulamasını karıştırma:</span><span class="sxs-lookup"><span data-stu-id="cc8f9-116">That is, how to mix :::no-loc(cookie):::s and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
