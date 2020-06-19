---
title: ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme
author: guardrex
description: BlazorKimlik doğrulama ve yetkilendirme senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/index
ms.openlocfilehash: 8c5e6c6ba741cae2634e2852e9ca141a0a7c33ce
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103793"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="9bfdb-103">ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="9bfdb-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="9bfdb-104">Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9bfdb-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9bfdb-105">ASP.NET Core, uygulamalardaki güvenliğin yapılandırmasını ve yönetimini destekler Blazor .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="9bfdb-106">Güvenlik Senaryoları Blazor sunucu ve Blazor webassembly uygulamaları arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="9bfdb-107">Sunucu Blazor uygulamaları sunucuda çalıştığı için, yetkilendirme denetimleri şunları tespit edebilir:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="9bfdb-108">Kullanıcıya sunulan kullanıcı ARABIRIMI seçenekleri (örneğin, bir kullanıcı için hangi menü girişlerinin kullanılabildiği).</span><span class="sxs-lookup"><span data-stu-id="9bfdb-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="9bfdb-109">Uygulama ve bileşenlerin bölgeleri için erişim kuralları.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-109">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="9bfdb-110">WebAssembly uygulamaları istemcide çalışır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-110"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="9bfdb-111">Yetkilendirme *yalnızca* hangi kullanıcı arabirimi seçeneklerinin gösterileceğini belirlemede kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="9bfdb-112">İstemci tarafı denetimleri bir kullanıcı tarafından değiştirilebilecek veya atlandığından, Blazor webassembly uygulaması yetkilendirme erişim kurallarını zorunlu kılamaz.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="9bfdb-113">[ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization) yönlendirilebilir Razor bileşenlere uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="9bfdb-114">Bir sayfada yönlendirilemeyen bir Razor bileşen [gömüliyorsa](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), sayfanın yetkilendirme kuralları, Razor sayfanın geri kalanı ile birlikte, bileşeni dolaylı olarak etkiler.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="9bfdb-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>ve <xref:Microsoft.AspNetCore.Identity.UserManager%601> Razor bileşenlerde desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="9bfdb-116">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="9bfdb-116">Authentication</span></span>

Blazor<span data-ttu-id="9bfdb-117">, kullanıcının kimliğini kurmak için mevcut ASP.NET Core kimlik doğrulama mekanizmalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-117"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="9bfdb-118">Tam mekanizma Blazor , uygulamanın nasıl barındırıldığını, Blazor webassembly veya Server 'a bağlıdır Blazor .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="9bfdb-119">WebAssembly kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="9bfdb-119"> WebAssembly authentication</span></span>

<span data-ttu-id="9bfdb-120">BlazorWebassembly uygulamalarında, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için kimlik doğrulama denetimleri atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="9bfdb-121">Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="9bfdb-122">Aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-122">Add the following:</span></span>

* <span data-ttu-id="9bfdb-123">Uygulamanın proje dosyasına [Microsoft. AspNetCore. components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) için bir paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-123">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="9bfdb-124">`Microsoft.AspNetCore.Components.Authorization`Uygulamanın *_Imports. Razor* dosyasının ad alanı.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="9bfdb-125">Kimlik doğrulamasını işlemek için, yerleşik veya özel bir <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> hizmetin uygulanması aşağıdaki bölümlerde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-125">To handle authentication, implementation of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="9bfdb-126">Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:blazor/security/webassembly/index> ..</span><span class="sxs-lookup"><span data-stu-id="9bfdb-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="9bfdb-127">Sunucu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="9bfdb-127"> Server authentication</span></span>

Blazor<span data-ttu-id="9bfdb-128">Sunucu uygulamaları kullanılarak oluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır SignalR .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-128"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="9bfdb-129">Bağlantı kurulurken [kimlik doğrulaması SignalR tabanlı uygulamalar](xref:signalr/authn-and-authz) işlenir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="9bfdb-130">Kimlik doğrulaması, bir tanımlama bilgisine veya başka bir taşıyıcı belirtecine dayalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="9bfdb-131">Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:blazor/security/server/index> ..</span><span class="sxs-lookup"><span data-stu-id="9bfdb-131">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="9bfdb-132">AuthenticationStateProvider hizmeti</span><span class="sxs-lookup"><span data-stu-id="9bfdb-132">AuthenticationStateProvider service</span></span>

<span data-ttu-id="9bfdb-133">Yerleşik <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> hizmet ASP.NET Core ' den kimlik doğrulama durumu verileri alır `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-133">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="9bfdb-134">Kimlik doğrulama durumu, mevcut ASP.NET Core kimlik doğrulama mekanizmalarıyla tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-134">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="9bfdb-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bileşen ve <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> bileşen tarafından kimlik doğrulama durumunu almak için kullanılan temel hizmettir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="9bfdb-136">Genellikle doğrudan kullanmazsınız <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="9bfdb-137">Bu makalenin ilerleyen kısımlarında açıklanan [Authorizeview bileşenini](#authorizeview-component) veya [görev \<AuthenticationState> ](#expose-the-authentication-state-as-a-cascading-parameter) yaklaşımlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-137">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="9bfdb-138">Doğrudan kullanmanın ana dezavantajı, <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> temeldeki kimlik doğrulama durumu verileri değişirse bileşen tarafından otomatik olarak bildirilmemektedir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="9bfdb-139"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Hizmet, <xref:System.Security.Claims.ClaimsPrincipal> Aşağıdaki örnekte gösterildiği gibi geçerli kullanıcının verilerini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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
            <li>@claim.Type: @claim.Value</li>
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

<span data-ttu-id="9bfdb-140">`user.Identity.IsAuthenticated`İse `true` ve Kullanıcı bir ise <xref:System.Security.Claims.ClaimsPrincipal> , talepler numaralandırılabilir ve değerlendirilen rollerde üyelik yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="9bfdb-141">Bağımlılık ekleme (dı) ve hizmetleri hakkında daha fazla bilgi için bkz <xref:blazor/fundamentals/dependency-injection> <xref:fundamentals/dependency-injection> . ve.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="9bfdb-142">Özel bir AuthenticationStateProvider uygulama</span><span class="sxs-lookup"><span data-stu-id="9bfdb-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="9bfdb-143">Uygulama için özel bir sağlayıcı gerekiyorsa, uygulayın <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ve geçersiz kılın `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="9bfdb-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="9bfdb-144">BlazorWebassembly uygulamasında, `CustomAuthStateProvider` hizmet program.cs ' de kaydedilir `Main` : *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="9bfdb-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="9bfdb-145">Bir Blazor sunucu uygulamasında `CustomAuthStateProvider` hizmet şu şekilde kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9bfdb-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="9bfdb-146">`CustomAuthStateProvider`Önceki örnekte kullanarak, tüm kullanıcıların Kullanıcı adı ile kimliği doğrulanır `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="9bfdb-147">Kimlik doğrulama durumunu basamaklı bir parametre olarak kullanıma sunma</span><span class="sxs-lookup"><span data-stu-id="9bfdb-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="9bfdb-148">Kullanıcı tarafından tetiklenen bir eylem gerçekleştirilirken gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekliyse, türü geçişli bir parametre tanımlayarak kimlik doğrulama durumu verilerini alın `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="9bfdb-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

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

<span data-ttu-id="9bfdb-149">`user.Identity.IsAuthenticated`İse `true` , talepler, değerlendirilen roller içinde numaralandırılabilir ve üyelenebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="9bfdb-150">`Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> Bileşen içindeki ve bileşenlerini kullanarak basamaklı parametreyi ayarlayın <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `App` (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="9bfdb-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (*App.razor*):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="9bfdb-151">BlazorWebassembly uygulamasında, Seçenekler ve yetkilendirme için Hizmetleri şu şekilde ekleyin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="9bfdb-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="9bfdb-152">Sunucu uygulamasında Blazor , Seçenekler ve yetkilendirme hizmetleri zaten mevcuttur, bu nedenle başka bir eylem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="9bfdb-153">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="9bfdb-153">Authorization</span></span>

<span data-ttu-id="9bfdb-154">Bir kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *Yetkilendirme* kuralları uygulanır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="9bfdb-155">Erişim, genellikle aşağıdakileri yapıp verilmeksizin verilir veya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="9bfdb-156">Bir kullanıcının kimliği doğrulanır (oturum açıldı).</span><span class="sxs-lookup"><span data-stu-id="9bfdb-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="9bfdb-157">Bir Kullanıcı bir *roldür*.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-157">A user is in a *role*.</span></span>
* <span data-ttu-id="9bfdb-158">Bir kullanıcının *talebi*vardır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-158">A user has a *claim*.</span></span>
* <span data-ttu-id="9bfdb-159">Bir *ilke* karşılandı.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="9bfdb-160">Bu kavramların her biri, ASP.NET Core MVC veya Pages uygulamasındaki ile aynıdır Razor .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="9bfdb-161">ASP.NET Core güvenliği hakkında daha fazla bilgi için [ASP.NET Core güvenlik ve Identity ](xref:security/index)altındaki makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="9bfdb-162">AuthorizeView bileşeni</span><span class="sxs-lookup"><span data-stu-id="9bfdb-162">AuthorizeView component</span></span>

<span data-ttu-id="9bfdb-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen Kullanıcı tarafından görme yetkisine sahip olup olmadığına bağlı olarak Kullanıcı arabirimini seçmeli olarak görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="9bfdb-164">Bu yaklaşım yalnızca Kullanıcı için veri *görüntülemesi* gerektiğinde ve Kullanıcı kimliğini yordamsal mantığda kullanmanıza gerek olmadığında yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="9bfdb-165">Bileşeni, `context` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> oturum açmış kullanıcıyla ilgili bilgilere erişmek için kullanabileceğiniz türünde bir değişken kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="9bfdb-166">Kullanıcının kimliği doğrulanmadıysa, görüntülenmek üzere farklı içerikler de sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-166">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="9bfdb-167"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen, `NavMenu` bir gezinti bağlantısı bileşeni () için bir liste öğesi () göstermek üzere bileşende (*paylaşılan/navmenu. Razor*) kullanılabilir `<li>...</li>` [NavLink component](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> , ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a [NavLink component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="9bfdb-168">Kullanıcının bileşene gitmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="9bfdb-169">`<Authorized>`Ve `<NotAuthorized>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="9bfdb-170">Kullanıcı arabirimi seçeneklerini veya erişimini denetleyen roller veya ilkeler gibi yetkilendirme koşulları, [Yetkilendirme](#authorization) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="9bfdb-171">Yetkilendirme koşulları belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> varsayılan bir ilke kullanır ve şu şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="9bfdb-172">Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="9bfdb-173">Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="9bfdb-174">Rol tabanlı ve ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="9bfdb-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="9bfdb-175"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="9bfdb-176">Rol tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="9bfdb-177">Daha fazla bilgi için bkz. <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="9bfdb-178">İlke tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="9bfdb-179">Talep tabanlı yetkilendirme, ilke tabanlı yetkilendirme için özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="9bfdb-180">Örneğin, kullanıcıların belirli bir talebe sahip olmasını gerektiren bir ilke tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="9bfdb-181">Daha fazla bilgi için bkz. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="9bfdb-182">Bu API 'Ler, Blazor sunucuda ya da Blazor webassembly uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="9bfdb-183">Ne <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> de <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> varsayılan ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="9bfdb-184">Zaman uyumsuz kimlik doğrulaması sırasında görünen içerik</span><span class="sxs-lookup"><span data-stu-id="9bfdb-184">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="9bfdb-185">kimlik doğrulaması durumunun *zaman uyumsuz*olarak belirlenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-185"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="9bfdb-186">Bu yaklaşım için birincil senaryo, Blazor kimlik doğrulaması için bir dış uç noktaya istek yapan webassembly uygulamalarında bulunur.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="9bfdb-187">Kimlik doğrulama devam ederken, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> Varsayılan olarak içerik görüntülemez.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="9bfdb-188">Kimlik doğrulaması sırasında içeriği göstermek için `<Authorizing>` öğesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-188">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="9bfdb-189">Bu yaklaşım normalde sunucu uygulamaları için geçerli değildir Blazor .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-189">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="9bfdb-190">Sunucu uygulamaları, durum belirlenir oluşturmaz kimlik doğrulama durumunu bilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-190"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="9bfdb-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>içerik bir Blazor sunucu uygulamasının <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bileşeninde bulunabilir, ancak içerik hiçbir şekilde gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="9bfdb-192">[Yetkilendir] özniteliği</span><span class="sxs-lookup"><span data-stu-id="9bfdb-192">[Authorize] attribute</span></span>

<span data-ttu-id="9bfdb-193">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Özniteliği, Razor bileşenlerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="9bfdb-194">Yalnızca [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) `@page` yönlendirici üzerinden ulaşılan bileşenlerde kullanın Blazor .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="9bfdb-195">Yetkilendirme yalnızca, bir sayfada işlenen alt bileşenler için *değil* , yönlendirmenin bir yönü olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="9bfdb-196">Bir sayfa içindeki belirli parçaların görüntülenmesini yetkilendirmek için, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bunun yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="9bfdb-197">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Özniteliği rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="9bfdb-198">Rol tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="9bfdb-199">İlke tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="9bfdb-200"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>Ya da <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> belirtilmemişse [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) varsayılan ilkeyi kullanır, varsayılan olarak şu şekilde değerlendirilir:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="9bfdb-201">Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="9bfdb-202">Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="9bfdb-203">Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme</span><span class="sxs-lookup"><span data-stu-id="9bfdb-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="9bfdb-204">Bileşen <xref:Microsoft.AspNetCore.Components.Routing.Router> ile birlikte bileşeni, uygulamanın şu <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> durumlarda özel içerik belirtmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="9bfdb-205">İçerik bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-205">Content isn't found.</span></span>
* <span data-ttu-id="9bfdb-206">Kullanıcı, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşene uygulanan bir koşulla başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="9bfdb-207">Özniteliği, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="9bfdb-208">Zaman uyumsuz kimlik doğrulama devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="9bfdb-209">Varsayılan Blazor sunucu projesi şablonunda, `App` bileşen (*app. Razor*) özel içerik ayarlamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-209">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
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
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="9bfdb-210">`<NotFound>`, `<NotAuthorized>` Ve etiketlerinin içeriği, `<Authorizing>` diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="9bfdb-211">`<NotAuthorized>`Öğesi belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> aşağıdaki geri dönüş iletisini kullanır:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-211">If the `<NotAuthorized>` element isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="9bfdb-212">Kimlik doğrulama durumu değişiklikleri hakkında bildirim</span><span class="sxs-lookup"><span data-stu-id="9bfdb-212">Notification about authentication state changes</span></span>

<span data-ttu-id="9bfdb-213">Uygulama, temeldeki kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, Kullanıcı oturumu kapattığından veya başka bir kullanıcı rollerini değiştirdiği için), [özel bir AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) isteğe bağlı olarak <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> temel sınıfta yöntemi çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="9bfdb-214">Bu, yeni verileri kullanarak yeniden kimlik doğrulama durumu verilerini (örneğin,) tüketicilere bildirir <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="9bfdb-215">Yordamsal mantık</span><span class="sxs-lookup"><span data-stu-id="9bfdb-215">Procedural logic</span></span>

<span data-ttu-id="9bfdb-216">Uygulama, yordamsal mantığın bir parçası olarak yetkilendirme kurallarını denetmek için gerekliyse, `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Kullanıcı tarafından elde etmek için türünde basamaklı bir parametre kullanın <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="9bfdb-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>``IAuthorizationService`, ilkeleri değerlendirmek için gibi diğer hizmetlerle birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="9bfdb-218">BlazorWebassembly uygulama bileşeninde, <xref:Microsoft.AspNetCore.Authorization> ve <xref:Microsoft.AspNetCore.Components.Authorization> ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="9bfdb-219">Bu ad alanları, uygulamanın *_Imports. Razor* dosyasına eklenerek Global olarak sağlanıyor.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-219">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="9bfdb-220">Sorun giderme hataları</span><span class="sxs-lookup"><span data-stu-id="9bfdb-220">Troubleshoot errors</span></span>

<span data-ttu-id="9bfdb-221">Yaygın hatalar:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-221">Common errors:</span></span>

* <span data-ttu-id="9bfdb-222">**Yetkilendirme, görev türünde basamaklı bir parametre gerektirir \<AuthenticationState> . Bunu sağlamak için basamaklı Dingauthenticationstate kullanmayı göz önünde bulundurun.**</span><span class="sxs-lookup"><span data-stu-id="9bfdb-222">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="9bfdb-223">**`null`için değer alındı`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="9bfdb-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="9bfdb-224">Projenin Blazor kimlik doğrulaması etkin bir sunucu şablonu kullanılarak oluşturulmamış olması olasıdır.</span><span class="sxs-lookup"><span data-stu-id="9bfdb-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="9bfdb-225">`<CascadingAuthenticationState>`Kullanıcı arabirimi ağacının bir bölümünü (örneğin, `App` bileşeni (*app. Razor*) aşağıdaki şekilde sarmalayın:</span><span class="sxs-lookup"><span data-stu-id="9bfdb-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="9bfdb-226">, <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Temel alınan dı hizmetinden aldığı geçişli parametreyi sağlar <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .</span><span class="sxs-lookup"><span data-stu-id="9bfdb-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9bfdb-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9bfdb-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="9bfdb-228">[Başar Blazor : kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluğu örnek bağlantıları</span><span class="sxs-lookup"><span data-stu-id="9bfdb-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>