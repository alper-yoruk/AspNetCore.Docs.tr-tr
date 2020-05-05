---
title: ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme
author: guardrex
description: Kimlik doğrulama Blazor ve yetkilendirme senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: d55880265ed1ceedf8f115412e5ac47309521239
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772901"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="e94dd-103">ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="e94dd-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="e94dd-104">Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e94dd-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="e94dd-105">Webassembly için Blazor geçerli olan bu makaledeki kılavuz için, ASP.NET Core Blazor webassembly şablon sürümü 3,2 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="e94dd-106">Visual Studio sürüm 16,6 Preview 2 veya sonraki bir sürümünü kullanmıyorsanız, içindeki Blazor <xref:blazor/get-started>kılavuzu izleyerek en son webassembly şablonunu edinin.</span><span class="sxs-lookup"><span data-stu-id="e94dd-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="e94dd-107">ASP.NET Core, Blazor uygulamalardaki güvenliğin yapılandırmasını ve yönetimini destekler.</span><span class="sxs-lookup"><span data-stu-id="e94dd-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="e94dd-108">Güvenlik senaryoları sunucu ve Blazor Blazor webassembly uygulamaları arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="e94dd-109">Blazor Sunucu uygulamaları sunucuda çalıştığı için, yetkilendirme denetimleri şunları tespit edebilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="e94dd-110">Kullanıcıya sunulan kullanıcı ARABIRIMI seçenekleri (örneğin, bir kullanıcı için hangi menü girişlerinin kullanılabildiği).</span><span class="sxs-lookup"><span data-stu-id="e94dd-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="e94dd-111">Uygulama ve bileşenlerin bölgeleri için erişim kuralları.</span><span class="sxs-lookup"><span data-stu-id="e94dd-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="e94dd-112">WebAssembly uygulamaları istemcide çalışır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="e94dd-113">Yetkilendirme *yalnızca* hangi kullanıcı arabirimi seçeneklerinin gösterileceğini belirlemede kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="e94dd-114">İstemci tarafı denetimleri bir kullanıcı tarafından değiştirilebilecek veya atlandığından, Blazor webassembly uygulaması yetkilendirme erişim kurallarını zorunlu kılamaz.</span><span class="sxs-lookup"><span data-stu-id="e94dd-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="e94dd-115">Sayfalar yetkilendirme kuralları yönlendirilebilir Razor bileşenlere uygulanmaz. [ Razor ](xref:security/authorization/razor-pages-authorization)</span><span class="sxs-lookup"><span data-stu-id="e94dd-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="e94dd-116">Bir sayfada yönlendirilemeyen Razor bir bileşen [gömüliyorsa](xref:blazor/integrate-components#render-components-from-a-page-or-view), sayfanın yetkilendirme kuralları, sayfanın geri kalanı ile birlikte, Razor bileşeni dolaylı olarak etkiler.</span><span class="sxs-lookup"><span data-stu-id="e94dd-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="e94dd-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>ve <xref:Microsoft.AspNetCore.Identity.UserManager%601> Razor bileşenlerde desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e94dd-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="e94dd-118">Kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e94dd-118">Authentication</span></span>

Blazor<span data-ttu-id="e94dd-119">, kullanıcının kimliğini kurmak için mevcut ASP.NET Core kimlik doğrulama mekanizmalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="e94dd-120">Tam mekanizma, Blazor uygulamanın nasıl barındırıldığını, webassembly Blazor veya Blazor Server 'a bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="e94dd-121">WebAssembly kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e94dd-121"> WebAssembly authentication</span></span>

<span data-ttu-id="e94dd-122">Blazor Webassembly uygulamalarında, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için kimlik doğrulama denetimleri atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="e94dd-123">Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="e94dd-124">Aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e94dd-124">Add the following:</span></span>

* <span data-ttu-id="e94dd-125">Uygulamanın proje dosyasına [Microsoft. AspNetCore. components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) için bir paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="e94dd-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="e94dd-126">Uygulamanın `Microsoft.AspNetCore.Components.Authorization` *_Imports. Razor* dosyasının ad alanı.</span><span class="sxs-lookup"><span data-stu-id="e94dd-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="e94dd-127">Kimlik doğrulamasını işlemek için, yerleşik veya özel `AuthenticationStateProvider` bir hizmetin uygulanması aşağıdaki bölümlerde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="e94dd-128">Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:security/blazor/webassembly/index>..</span><span class="sxs-lookup"><span data-stu-id="e94dd-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="e94dd-129">Sunucu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e94dd-129"> Server authentication</span></span>

Blazor<span data-ttu-id="e94dd-130">Sunucu uygulamaları kullanılarak SignalRoluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="e94dd-131">Bağlantı kurulurken [kimlik doğrulaması tabanlı uygulamalar işlenir. SignalR](xref:signalr/authn-and-authz)</span><span class="sxs-lookup"><span data-stu-id="e94dd-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="e94dd-132">Kimlik doğrulaması, bir tanımlama bilgisine veya başka bir taşıyıcı belirtecine dayalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="e94dd-133">Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:security/blazor/server/index>..</span><span class="sxs-lookup"><span data-stu-id="e94dd-133">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="e94dd-134">AuthenticationStateProvider hizmeti</span><span class="sxs-lookup"><span data-stu-id="e94dd-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="e94dd-135">Yerleşik `AuthenticationStateProvider` hizmet ASP.NET Core ' den kimlik doğrulama durumu verileri alır `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="e94dd-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="e94dd-136">Kimlik doğrulama durumu, mevcut ASP.NET Core kimlik doğrulama mekanizmalarıyla tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="e94dd-137">`AuthenticationStateProvider`, `AuthorizeView` bileşen ve `CascadingAuthenticationState` bileşen tarafından kimlik doğrulama durumunu almak için kullanılan temel hizmettir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="e94dd-138">Genellikle doğrudan kullanmazsınız `AuthenticationStateProvider` .</span><span class="sxs-lookup"><span data-stu-id="e94dd-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="e94dd-139">Bu makalenin ilerleyen kısımlarında açıklanan [Authorizeview bileşenini](#authorizeview-component) veya [görev\<authenticationstate>](#expose-the-authentication-state-as-a-cascading-parameter) yaklaşımlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="e94dd-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="e94dd-140">Doğrudan kullanmanın `AuthenticationStateProvider` ana dezavantajı, temeldeki kimlik doğrulama durumu verileri değişirse bileşen tarafından otomatik olarak bildirilmemektedir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="e94dd-141">`AuthenticationStateProvider` Hizmet, aşağıdaki örnekte gösterildiği gibi geçerli kullanıcının <xref:System.Security.Claims.ClaimsPrincipal> verilerini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="e94dd-142">`user.Identity.IsAuthenticated` İse `true` ve Kullanıcı bir <xref:System.Security.Claims.ClaimsPrincipal>ise, talepler numaralandırılabilir ve değerlendirilen rollerde üyelik yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="e94dd-143">Bağımlılık ekleme (dı) ve hizmetleri hakkında daha fazla bilgi için bkz <xref:blazor/dependency-injection> . <xref:fundamentals/dependency-injection>ve.</span><span class="sxs-lookup"><span data-stu-id="e94dd-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="e94dd-144">Özel bir AuthenticationStateProvider uygulama</span><span class="sxs-lookup"><span data-stu-id="e94dd-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="e94dd-145">Uygulama için özel bir sağlayıcı gerekiyorsa, uygulayın `AuthenticationStateProvider` ve geçersiz kılın `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="e94dd-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="e94dd-146">Blazor Webassembly uygulamasında, `CustomAuthStateProvider` hizmet *program.cs*' de `Main` kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="e94dd-147">Bir Blazor sunucu uygulamasında `CustomAuthStateProvider` hizmet şu şekilde `Startup.ConfigureServices`kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="e94dd-148">`CustomAuthStateProvider` Önceki örnekte kullanarak, tüm kullanıcıların Kullanıcı adı `mrfibuli`ile kimliği doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="e94dd-149">Kimlik doğrulama durumunu basamaklı bir parametre olarak kullanıma sunma</span><span class="sxs-lookup"><span data-stu-id="e94dd-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="e94dd-150">Kullanıcı tarafından tetiklenen bir eylem gerçekleştirilirken gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekliyse, türü `Task<AuthenticationState>`geçişli bir parametre tanımlayarak kimlik doğrulama durumu verilerini alın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="e94dd-151">`user.Identity.IsAuthenticated` İse `true`, talepler, değerlendirilen roller içinde numaralandırılabilir ve üyelenebilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="e94dd-152">`App` Bileşen içindeki `AuthorizeRouteView` `CascadingAuthenticationState` ve `Task<AuthenticationState>` bileşenlerini kullanarak basamaklı parametreyi ayarlayın (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e94dd-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="e94dd-153">Blazor Webassembly uygulamasında, Seçenekler ve yetkilendirme için Hizmetleri şu şekilde `Program.Main`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e94dd-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="e94dd-154">Blazor Sunucu uygulamasında, Seçenekler ve yetkilendirme hizmetleri zaten mevcuttur, bu nedenle başka bir eylem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e94dd-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="e94dd-155">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="e94dd-155">Authorization</span></span>

<span data-ttu-id="e94dd-156">Bir kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *Yetkilendirme* kuralları uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="e94dd-157">Erişim, genellikle aşağıdakileri yapıp verilmeksizin verilir veya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="e94dd-158">Bir kullanıcının kimliği doğrulanır (oturum açıldı).</span><span class="sxs-lookup"><span data-stu-id="e94dd-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="e94dd-159">Bir Kullanıcı bir *roldür*.</span><span class="sxs-lookup"><span data-stu-id="e94dd-159">A user is in a *role*.</span></span>
* <span data-ttu-id="e94dd-160">Bir kullanıcının *talebi*vardır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-160">A user has a *claim*.</span></span>
* <span data-ttu-id="e94dd-161">Bir *ilke* karşılandı.</span><span class="sxs-lookup"><span data-stu-id="e94dd-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="e94dd-162">Bu kavramların her biri, ASP.NET Core MVC veya Razor Pages uygulamasındaki ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="e94dd-163">ASP.NET Core güvenliği hakkında daha fazla bilgi için [ASP.NET Core güvenlik ve Identity ](xref:security/index)altındaki makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="e94dd-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="e94dd-164">AuthorizeView bileşeni</span><span class="sxs-lookup"><span data-stu-id="e94dd-164">AuthorizeView component</span></span>

<span data-ttu-id="e94dd-165">`AuthorizeView` Bileşen Kullanıcı tarafından görme yetkisine sahip olup olmadığına bağlı olarak Kullanıcı arabirimini seçmeli olarak görüntüler.</span><span class="sxs-lookup"><span data-stu-id="e94dd-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="e94dd-166">Bu yaklaşım yalnızca Kullanıcı için veri *görüntülemesi* gerektiğinde ve Kullanıcı kimliğini yordamsal mantığda kullanmanıza gerek olmadığında yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="e94dd-167">Bileşeni, oturum açmış `context` kullanıcıyla ilgili bilgilere `AuthenticationState`erişmek için kullanabileceğiniz türünde bir değişken kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="e94dd-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="e94dd-168">Kullanıcının kimliği doğrulanmadıysa, görüntülenmek üzere farklı içerikler de sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e94dd-168">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="e94dd-169">`AuthorizeView` Bileşen, bir `NavMenu` `NavLink`liste öğesi (`<li>...</li>`) göstermek için bileşende (*Shared/navmenu. Razor*) kullanılabilir, ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="e94dd-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="e94dd-170">Kullanıcının bileşene gitmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="e94dd-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="e94dd-171">`<Authorized>` Ve `<NotAuthorized>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="e94dd-172">Kullanıcı arabirimi seçeneklerini veya erişimini denetleyen roller veya ilkeler gibi yetkilendirme koşulları, [Yetkilendirme](#authorization) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="e94dd-173">Yetkilendirme koşulları belirtilmemişse, `AuthorizeView` varsayılan bir ilke kullanır ve şu şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="e94dd-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="e94dd-174">Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.</span><span class="sxs-lookup"><span data-stu-id="e94dd-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="e94dd-175">Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="e94dd-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="e94dd-176">Rol tabanlı ve ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="e94dd-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="e94dd-177">`AuthorizeView` Bileşen *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="e94dd-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="e94dd-178">Rol tabanlı yetkilendirme için `Roles` parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="e94dd-179">Daha fazla bilgi için bkz. <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="e94dd-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="e94dd-180">İlke tabanlı yetkilendirme için `Policy` parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="e94dd-181">Talep tabanlı yetkilendirme, ilke tabanlı yetkilendirme için özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="e94dd-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="e94dd-182">Örneğin, kullanıcıların belirli bir talebe sahip olmasını gerektiren bir ilke tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e94dd-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="e94dd-183">Daha fazla bilgi için bkz. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="e94dd-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="e94dd-184">Bu API 'Ler, sunucuda ya Blazor Blazor da webassembly uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="e94dd-185">Ne `Roles` de `Policy` belirtilmemişse, `AuthorizeView` varsayılan ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="e94dd-186">Zaman uyumsuz kimlik doğrulaması sırasında görünen içerik</span><span class="sxs-lookup"><span data-stu-id="e94dd-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="e94dd-187">kimlik doğrulaması durumunun *zaman uyumsuz*olarak belirlenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="e94dd-188">Bu yaklaşım için birincil senaryo, kimlik doğrulaması Blazor için bir dış uç noktaya istek yapan webassembly uygulamalarında bulunur.</span><span class="sxs-lookup"><span data-stu-id="e94dd-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="e94dd-189">Kimlik doğrulama devam ederken, `AuthorizeView` varsayılan olarak içerik görüntülemez.</span><span class="sxs-lookup"><span data-stu-id="e94dd-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="e94dd-190">Kimlik doğrulaması sırasında içeriği göstermek için `<Authorizing>` öğesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="e94dd-191">Bu yaklaşım normalde sunucu uygulamaları için Blazor geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="e94dd-192">Sunucu uygulamaları, durum belirlenir oluşturmaz kimlik doğrulama durumunu bilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="e94dd-193">`Authorizing`içerik bir Blazor sunucu uygulamasının `AuthorizeView` bileşeninde bulunabilir, ancak içerik hiçbir şekilde gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="e94dd-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="e94dd-194">[Yetkilendir] özniteliği</span><span class="sxs-lookup"><span data-stu-id="e94dd-194">[Authorize] attribute</span></span>

<span data-ttu-id="e94dd-195">Özniteliği `[Authorize]` , Razor bileşenlerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="e94dd-196">Yalnızca Blazor yönlendirici `[Authorize]` üzerinden `@page` ulaşılan bileşenlerde kullanın.</span><span class="sxs-lookup"><span data-stu-id="e94dd-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="e94dd-197">Yetkilendirme yalnızca, bir sayfada işlenen alt bileşenler için *değil* , yönlendirmenin bir yönü olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="e94dd-198">Bir sayfa içindeki belirli parçaların görüntülenmesini yetkilendirmek için, bunun yerine kullanın `AuthorizeView` .</span><span class="sxs-lookup"><span data-stu-id="e94dd-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="e94dd-199">`[Authorize]` Özniteliği rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="e94dd-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="e94dd-200">Rol tabanlı yetkilendirme için `Roles` parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="e94dd-201">İlke tabanlı yetkilendirme için `Policy` parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="e94dd-202">`Roles` Ya da `Policy` belirtilmemişse varsayılan ilkeyi `[Authorize]` kullanır, varsayılan olarak şu şekilde değerlendirilir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="e94dd-203">Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.</span><span class="sxs-lookup"><span data-stu-id="e94dd-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="e94dd-204">Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="e94dd-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="e94dd-205">Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme</span><span class="sxs-lookup"><span data-stu-id="e94dd-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="e94dd-206">Bileşen `Router` ile `AuthorizeRouteView` birlikte bileşeni, uygulamanın şu durumlarda özel içerik belirtmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="e94dd-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="e94dd-207">İçerik bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="e94dd-207">Content isn't found.</span></span>
* <span data-ttu-id="e94dd-208">Kullanıcı, bileşene uygulanan `[Authorize]` bir koşulla başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="e94dd-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="e94dd-209">`[Authorize]` Özniteliği, [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="e94dd-210">Zaman uyumsuz kimlik doğrulama devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="e94dd-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="e94dd-211">Varsayılan Blazor sunucu projesi şablonunda, `App` bileşen (*app. Razor*) özel içerik ayarlamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="e94dd-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="e94dd-212">`<NotFound>`, `<NotAuthorized>`Ve `<Authorizing>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="e94dd-213">`<NotAuthorized>` Öğesi belirtilmemişse, aşağıdaki geri dönüş iletisini `AuthorizeRouteView` kullanır:</span><span class="sxs-lookup"><span data-stu-id="e94dd-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="e94dd-214">Kimlik doğrulama durumu değişiklikleri hakkında bildirim</span><span class="sxs-lookup"><span data-stu-id="e94dd-214">Notification about authentication state changes</span></span>

<span data-ttu-id="e94dd-215">Uygulama, temeldeki kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, Kullanıcı oturumu kapattığından veya başka bir kullanıcı rollerini değiştirdiği için), [özel bir AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) isteğe bağlı olarak `NotifyAuthenticationStateChanged` `AuthenticationStateProvider` temel sınıfta yöntemi çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="e94dd-216">Bu, yeni verileri kullanarak yeniden kimlik doğrulama durumu verilerini (örneğin `AuthorizeView`,) tüketicilere bildirir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="e94dd-217">Yordamsal mantık</span><span class="sxs-lookup"><span data-stu-id="e94dd-217">Procedural logic</span></span>

<span data-ttu-id="e94dd-218">Uygulama, yordamsal mantığın bir parçası olarak yetkilendirme kurallarını denetmek için gerekliyse, Kullanıcı tarafından `Task<AuthenticationState>` <xref:System.Security.Claims.ClaimsPrincipal>elde etmek için türünde basamaklı bir parametre kullanın.</span><span class="sxs-lookup"><span data-stu-id="e94dd-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e94dd-219">`Task<AuthenticationState>``IAuthorizationService`, ilkeleri değerlendirmek için gibi diğer hizmetlerle birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="e94dd-220">Blazor Webassembly uygulama bileşeninde, `Microsoft.AspNetCore.Authorization` ve `Microsoft.AspNetCore.Components.Authorization` ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e94dd-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="e94dd-221">Bu ad alanları, uygulamanın *_Imports. Razor* dosyasına eklenerek Global olarak sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="e94dd-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="e94dd-222">Blazor Webassembly uygulamalarında yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="e94dd-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="e94dd-223">Blazor Webassembly uygulamalarında, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için yetkilendirme denetimleri atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="e94dd-224">Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e94dd-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="e94dd-225">**İstemci tarafı uygulamanız tarafından erişilen tüm API uç noktalarında sunucuda her zaman yetkilendirme denetimleri gerçekleştirin.**</span><span class="sxs-lookup"><span data-stu-id="e94dd-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="e94dd-226">Daha fazla bilgi için, altındaki <xref:security/blazor/webassembly/index>makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="e94dd-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="e94dd-227">Sorun giderme hataları</span><span class="sxs-lookup"><span data-stu-id="e94dd-227">Troubleshoot errors</span></span>

<span data-ttu-id="e94dd-228">Yaygın hatalar:</span><span class="sxs-lookup"><span data-stu-id="e94dd-228">Common errors:</span></span>

* <span data-ttu-id="e94dd-229">**Yetkilendirme, görev\<authenticationstate> türünde bir geçişli parametre gerektirir. Bunu sağlamak için basamaklı Dingauthenticationstate kullanmayı göz önünde bulundurun.**</span><span class="sxs-lookup"><span data-stu-id="e94dd-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="e94dd-230">**`null`için değer alındı`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="e94dd-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="e94dd-231">Projenin kimlik doğrulaması etkin bir Blazor sunucu şablonu kullanılarak oluşturulmamış olması olasıdır.</span><span class="sxs-lookup"><span data-stu-id="e94dd-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="e94dd-232">Kullanıcı arabirimi `<CascadingAuthenticationState>` ağacının bir bölümünü (örneğin, `App` bileşeni (*app. Razor*) aşağıdaki şekilde sarmalayın:</span><span class="sxs-lookup"><span data-stu-id="e94dd-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="e94dd-233">, `CascadingAuthenticationState` Temel alınan `Task<AuthenticationState>` `AuthenticationStateProvider` dı hizmetinden aldığı geçişli parametreyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e94dd-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e94dd-234">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e94dd-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="e94dd-235">[Başar Blazor: kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluğu örnek bağlantıları</span><span class="sxs-lookup"><span data-stu-id="e94dd-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
