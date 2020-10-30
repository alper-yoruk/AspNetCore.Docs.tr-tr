---
title: 'ASP.NET Core :::no-loc(Blazor)::: kimlik doğrulaması ve yetkilendirme'
author: guardrex
description: :::no-loc(Blazor):::Kimlik doğrulama ve yetkilendirme senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: 6110f51eb5d5ee40d6ce5a5d49ddda3329d1f033
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055548"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a><span data-ttu-id="9b99f-103">ASP.NET Core :::no-loc(Blazor)::: kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="9b99f-103">ASP.NET Core :::no-loc(Blazor)::: authentication and authorization</span></span>

<span data-ttu-id="9b99f-104">Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9b99f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9b99f-105">ASP.NET Core, uygulamalardaki güvenliğin yapılandırmasını ve yönetimini destekler :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-105">ASP.NET Core supports the configuration and management of security in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="9b99f-106">Güvenlik senaryoları ve uygulamalar arasında farklılık gösterir :::no-loc(Blazor Server)::: :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-106">Security scenarios differ between :::no-loc(Blazor Server)::: and :::no-loc(Blazor WebAssembly)::: apps.</span></span> <span data-ttu-id="9b99f-107">:::no-loc(Blazor Server):::Uygulamalar sunucuda çalıştığı için, yetkilendirme denetimleri şunları tespit edebilir:</span><span class="sxs-lookup"><span data-stu-id="9b99f-107">Because :::no-loc(Blazor Server)::: apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="9b99f-108">Kullanıcıya sunulan kullanıcı ARABIRIMI seçenekleri (örneğin, bir kullanıcı için hangi menü girişlerinin kullanılabildiği).</span><span class="sxs-lookup"><span data-stu-id="9b99f-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="9b99f-109">Uygulama ve bileşenlerin bölgeleri için erişim kuralları.</span><span class="sxs-lookup"><span data-stu-id="9b99f-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="9b99f-110">:::no-loc(Blazor WebAssembly)::: uygulamalar istemcide çalışır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-110">:::no-loc(Blazor WebAssembly)::: apps run on the client.</span></span> <span data-ttu-id="9b99f-111">Yetkilendirme *yalnızca* hangi kullanıcı arabirimi seçeneklerinin gösterileceğini belirlemede kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="9b99f-112">İstemci tarafı denetimleri bir kullanıcı tarafından değiştirililerek veya atlandığından, bir :::no-loc(Blazor WebAssembly)::: uygulama yetkilendirme erişim kurallarını zorunlu kılamaz.</span><span class="sxs-lookup"><span data-stu-id="9b99f-112">Since client-side checks can be modified or bypassed by a user, a :::no-loc(Blazor WebAssembly)::: app can't enforce authorization access rules.</span></span>

<span data-ttu-id="9b99f-113">[ :::no-loc(Razor)::: Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization) yönlendirilebilir :::no-loc(Razor)::: bileşenlere uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="9b99f-113">[:::no-loc(Razor)::: Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable :::no-loc(Razor)::: components.</span></span> <span data-ttu-id="9b99f-114">Bir sayfada yönlendirilemeyen bir :::no-loc(Razor)::: bileşen [gömüliyorsa](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), sayfanın yetkilendirme kuralları, :::no-loc(Razor)::: sayfanın geri kalanı ile birlikte, bileşeni dolaylı olarak etkiler.</span><span class="sxs-lookup"><span data-stu-id="9b99f-114">If a non-routable :::no-loc(Razor)::: component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the :::no-loc(Razor)::: component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="9b99f-115"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601> ve <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> :::no-loc(Razor)::: bileşenlerde desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="9b99f-115"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> aren't supported in :::no-loc(Razor)::: components.</span></span>

## <a name="authentication"></a><span data-ttu-id="9b99f-116">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="9b99f-116">Authentication</span></span>

<span data-ttu-id="9b99f-117">:::no-loc(Blazor)::: , kullanıcının kimliğini kurmak için mevcut ASP.NET Core kimlik doğrulama mekanizmalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-117">:::no-loc(Blazor)::: uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="9b99f-118">Tam mekanizma, :::no-loc(Blazor)::: uygulamanın nasıl barındırıldığını :::no-loc(Blazor WebAssembly)::: veya :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-118">The exact mechanism depends on how the :::no-loc(Blazor)::: app is hosted, :::no-loc(Blazor WebAssembly)::: or :::no-loc(Blazor Server):::.</span></span>

### <a name="no-locblazor-webassembly-authentication"></a><span data-ttu-id="9b99f-119">:::no-loc(Blazor WebAssembly)::: yetkilendirmesi</span><span class="sxs-lookup"><span data-stu-id="9b99f-119">:::no-loc(Blazor WebAssembly)::: authentication</span></span>

<span data-ttu-id="9b99f-120">:::no-loc(Blazor WebAssembly):::Uygulamalarda, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için, kimlik doğrulama denetimleri atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-120">In :::no-loc(Blazor WebAssembly)::: apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="9b99f-121">Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="9b99f-122">Aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9b99f-122">Add the following:</span></span>

* <span data-ttu-id="9b99f-123">Uygulamanın proje dosyasına yönelik bir paket başvurusu [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) .</span><span class="sxs-lookup"><span data-stu-id="9b99f-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="9b99f-124">`Microsoft.AspNetCore.Components.Authorization`Uygulama dosyasının ad alanı `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="9b99f-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="9b99f-125">Kimlik doğrulamasını işlemek için, yerleşik veya özel bir <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> hizmetin kullanımı aşağıdaki bölümlerde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="9b99f-126">Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:blazor/security/webassembly/index> ..</span><span class="sxs-lookup"><span data-stu-id="9b99f-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="no-locblazor-server-authentication"></a><span data-ttu-id="9b99f-127">:::no-loc(Blazor Server)::: yetkilendirmesi</span><span class="sxs-lookup"><span data-stu-id="9b99f-127">:::no-loc(Blazor Server)::: authentication</span></span>

<span data-ttu-id="9b99f-128">:::no-loc(Blazor Server)::: uygulamalar kullanılarak oluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-128">:::no-loc(Blazor Server)::: apps operate over a real-time connection that's created using :::no-loc(SignalR):::.</span></span> <span data-ttu-id="9b99f-129">Bağlantı kurulurken [kimlik doğrulaması :::no-loc(SignalR)::: tabanlı uygulamalar](xref:signalr/authn-and-authz) işlenir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-129">[Authentication in :::no-loc(SignalR):::-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="9b99f-130">Kimlik doğrulaması bir :::no-loc(cookie)::: veya başka bir taşıyıcı belirtecini temel alabilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-130">Authentication can be based on a :::no-loc(cookie)::: or some other bearer token.</span></span>

<span data-ttu-id="9b99f-131"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Uygulamalar için yerleşik hizmet, :::no-loc(Blazor Server)::: ASP.NET Core 'dan kimlik doğrulama durumu verilerini alır `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="9b99f-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for :::no-loc(Blazor Server)::: apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="9b99f-132">Kimlik doğrulama durumu, mevcut ASP.NET Core kimlik doğrulama mekanizmalarıyla tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="9b99f-133">Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:blazor/security/server/index> ..</span><span class="sxs-lookup"><span data-stu-id="9b99f-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="9b99f-134">AuthenticationStateProvider hizmeti</span><span class="sxs-lookup"><span data-stu-id="9b99f-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="9b99f-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> , <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bileşen ve <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> bileşen tarafından kimlik doğrulama durumunu almak için kullanılan temel hizmettir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="9b99f-136">Genellikle doğrudan kullanmazsınız <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .</span><span class="sxs-lookup"><span data-stu-id="9b99f-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="9b99f-137">[ `AuthorizeView` ](#authorizeview-component) [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) Bu makalenin ilerleyen kısımlarında açıklanan bileşeni veya yaklaşımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="9b99f-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="9b99f-138">Doğrudan kullanmanın ana dezavantajı, <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> temeldeki kimlik doğrulama durumu verileri değişirse bileşen tarafından otomatik olarak bildirilmemektedir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="9b99f-139"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Hizmet, <xref:System.Security.Claims.ClaimsPrincipal> Aşağıdaki örnekte gösterildiği gibi geçerli kullanıcının verilerini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="9b99f-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            _authMessage = $"{user.:::no-loc(Identity):::.Name} is authenticated.";
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

<span data-ttu-id="9b99f-140">`user.:::no-loc(Identity):::.IsAuthenticated`İse `true` ve Kullanıcı bir ise <xref:System.Security.Claims.ClaimsPrincipal> , talepler numaralandırılabilir ve değerlendirilen rollerde üyelik yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-140">If `user.:::no-loc(Identity):::.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="9b99f-141">Bağımlılık ekleme (dı) ve hizmetleri hakkında daha fazla bilgi için bkz <xref:blazor/fundamentals/dependency-injection> <xref:fundamentals/dependency-injection> . ve.</span><span class="sxs-lookup"><span data-stu-id="9b99f-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="9b99f-142">Özel bir AuthenticationStateProvider uygulama</span><span class="sxs-lookup"><span data-stu-id="9b99f-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="9b99f-143">Uygulama için özel bir sağlayıcı gerekiyorsa, uygulayın <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ve geçersiz kılın `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new Claims:::no-loc(Identity):::(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="9b99f-144">Bir :::no-loc(Blazor WebAssembly)::: uygulamada, `CustomAuthStateProvider` hizmet şu şekilde kaydedilir `Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-144">In a :::no-loc(Blazor WebAssembly)::: app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="9b99f-145">Bir :::no-loc(Blazor Server)::: uygulamada, `CustomAuthStateProvider` hizmet şu şekilde kaydedilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-145">In a :::no-loc(Blazor Server)::: app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="9b99f-146">`CustomAuthStateProvider`Önceki örnekte kullanarak, tüm kullanıcıların Kullanıcı adı ile kimliği doğrulanır `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="9b99f-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="9b99f-147">Kimlik doğrulama durumunu basamaklı bir parametre olarak kullanıma sunma</span><span class="sxs-lookup"><span data-stu-id="9b99f-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="9b99f-148">Kullanıcı tarafından tetiklenen bir eylem gerçekleştirilirken gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekliyse, türü geçişli bir parametre tanımlayarak kimlik doğrulama durumu verilerini alın `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

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

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            _authMessage = $"{user.:::no-loc(Identity):::.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="9b99f-149">`user.:::no-loc(Identity):::.IsAuthenticated`İse `true` , talepler, değerlendirilen roller içinde numaralandırılabilir ve üyelenebilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-149">If `user.:::no-loc(Identity):::.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="9b99f-150">`Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> Bileşen () içindeki ve bileşenlerini kullanarak basamaklı parametreyi ayarlayın `App` `App.razor` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

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

<span data-ttu-id="9b99f-151">Bir :::no-loc(Blazor WebAssembly)::: uygulamada, Seçenekler ve yetkilendirme için Hizmetleri şu şekilde ekleyin `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-151">In a :::no-loc(Blazor WebAssembly)::: App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="9b99f-152">Bir :::no-loc(Blazor Server)::: uygulamada, Seçenekler ve yetkilendirme hizmetleri zaten mevcuttur, bu nedenle başka bir eylem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9b99f-152">In a :::no-loc(Blazor Server)::: app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="9b99f-153">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="9b99f-153">Authorization</span></span>

<span data-ttu-id="9b99f-154">Bir kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *Yetkilendirme* kuralları uygulanır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="9b99f-155">Erişim, genellikle aşağıdakileri yapıp verilmeksizin verilir veya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="9b99f-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="9b99f-156">Bir kullanıcının kimliği doğrulanır (oturum açıldı).</span><span class="sxs-lookup"><span data-stu-id="9b99f-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="9b99f-157">Bir Kullanıcı bir *roldür* .</span><span class="sxs-lookup"><span data-stu-id="9b99f-157">A user is in a *role* .</span></span>
* <span data-ttu-id="9b99f-158">Bir kullanıcının *talebi* vardır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-158">A user has a *claim* .</span></span>
* <span data-ttu-id="9b99f-159">Bir *ilke* karşılandı.</span><span class="sxs-lookup"><span data-stu-id="9b99f-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="9b99f-160">Bu kavramların her biri, ASP.NET Core MVC veya Pages uygulamasındaki ile aynıdır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-160">Each of these concepts is the same as in an ASP.NET Core MVC or :::no-loc(Razor)::: Pages app.</span></span> <span data-ttu-id="9b99f-161">ASP.NET Core güvenliği hakkında daha fazla bilgi için [ASP.NET Core güvenlik ve :::no-loc(Identity)::: ](xref:security/index)altındaki makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="9b99f-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and :::no-loc(Identity):::](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="9b99f-162">AuthorizeView bileşeni</span><span class="sxs-lookup"><span data-stu-id="9b99f-162">AuthorizeView component</span></span>

<span data-ttu-id="9b99f-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen Kullanıcı tarafından görme yetkisine sahip olup olmadığına bağlı olarak Kullanıcı arabirimini seçmeli olarak görüntüler.</span><span class="sxs-lookup"><span data-stu-id="9b99f-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="9b99f-164">Bu yaklaşım yalnızca Kullanıcı için veri *görüntülemesi* gerektiğinde ve Kullanıcı kimliğini yordamsal mantığda kullanmanıza gerek olmadığında yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="9b99f-165">Bileşeni, `context` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> oturum açmış kullanıcıyla ilgili bilgilere erişmek için kullanabileceğiniz türünde bir değişken kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="9b99f-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="9b99f-166">Kullanıcının kimliği doğrulanmadıysa, görüntülenmek üzere farklı içerikler de sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="9b99f-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="9b99f-167">Bileşen, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> `NavMenu` `Shared/NavMenu.razor` bir bileşen () için bir liste öğesi () göstermek üzere bileşende () kullanılabilir `<li>...</li>` [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> , ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığına unutmayın.</span><span class="sxs-lookup"><span data-stu-id="9b99f-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="9b99f-168">Kullanıcının bileşene gitmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="9b99f-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="9b99f-169">`<Authorized>`Ve `<NotAuthorized>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="9b99f-170">Kullanıcı arabirimi seçeneklerini veya erişimini denetleyen roller veya ilkeler gibi yetkilendirme koşulları, [Yetkilendirme](#authorization) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="9b99f-171">Yetkilendirme koşulları belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> varsayılan bir ilke kullanır ve şu şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="9b99f-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="9b99f-172">Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.</span><span class="sxs-lookup"><span data-stu-id="9b99f-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="9b99f-173">Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="9b99f-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="9b99f-174">Rol tabanlı ve ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="9b99f-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="9b99f-175"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="9b99f-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="9b99f-176">Rol tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9b99f-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="9b99f-177">Daha fazla bilgi için bkz. <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="9b99f-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="9b99f-178">İlke tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9b99f-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="9b99f-179">Talep tabanlı yetkilendirme, ilke tabanlı yetkilendirme için özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="9b99f-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="9b99f-180">Örneğin, kullanıcıların belirli bir talebe sahip olmasını gerektiren bir ilke tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9b99f-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="9b99f-181">Daha fazla bilgi için bkz. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="9b99f-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="9b99f-182">Bu API 'Ler, ya da :::no-loc(Blazor Server)::: uygulamalarında kullanılabilir :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-182">These APIs can be used in either :::no-loc(Blazor Server)::: or :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="9b99f-183">Ne <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> de <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> varsayılan ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="9b99f-184">Zaman uyumsuz kimlik doğrulaması sırasında görünen içerik</span><span class="sxs-lookup"><span data-stu-id="9b99f-184">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="9b99f-185">:::no-loc(Blazor)::: kimlik doğrulaması durumunun *zaman uyumsuz* olarak belirlenmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-185">:::no-loc(Blazor)::: allows for authentication state to be determined *asynchronously* .</span></span> <span data-ttu-id="9b99f-186">Bu yaklaşım için birincil senaryo, :::no-loc(Blazor WebAssembly)::: kimlik doğrulaması için bir dış uç noktaya istek yapan uygulamalarda yer alan uygulamalardır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-186">The primary scenario for this approach is in :::no-loc(Blazor WebAssembly)::: apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="9b99f-187">Kimlik doğrulama devam ederken, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> Varsayılan olarak içerik görüntülemez.</span><span class="sxs-lookup"><span data-stu-id="9b99f-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="9b99f-188">Kimlik doğrulama sırasında içeriği göstermek için `<Authorizing>` etiketini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9b99f-188">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.:::no-loc(Identity):::.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="9b99f-189">Bu yaklaşım normalde uygulamalar için geçerli değildir :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-189">This approach isn't normally applicable to :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="9b99f-190">:::no-loc(Blazor Server)::: uygulamalar, durum belirlenir oluşturmaz kimlik doğrulama durumunu bilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-190">:::no-loc(Blazor Server)::: apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="9b99f-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> içerik bir uygulamanın bileşeni içinde bulunabilir :::no-loc(Blazor Server)::: <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , ancak içerik hiçbir şekilde gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="9b99f-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a :::no-loc(Blazor Server)::: app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="9b99f-192">[Yetkilendir] özniteliği</span><span class="sxs-lookup"><span data-stu-id="9b99f-192">[Authorize] attribute</span></span>

<span data-ttu-id="9b99f-193">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Özniteliği, :::no-loc(Razor)::: bileşenlerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="9b99f-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="9b99f-194">Yalnızca [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) `@page` yönlendirici üzerinden ulaşılan bileşenlerde kullanın :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the :::no-loc(Blazor)::: Router.</span></span> <span data-ttu-id="9b99f-195">Yetkilendirme yalnızca, bir sayfada işlenen alt bileşenler için *değil* , yönlendirmenin bir yönü olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="9b99f-196">Bir sayfa içindeki belirli parçaların görüntülenmesini yetkilendirmek için, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bunun yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="9b99f-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="9b99f-197">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Özniteliği rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="9b99f-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="9b99f-198">Rol tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9b99f-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="9b99f-199">İlke tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametresini kullanın:</span><span class="sxs-lookup"><span data-stu-id="9b99f-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="9b99f-200"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>Ya da <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> belirtilmemişse [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) varsayılan ilkeyi kullanır, varsayılan olarak şu şekilde değerlendirilir:</span><span class="sxs-lookup"><span data-stu-id="9b99f-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="9b99f-201">Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.</span><span class="sxs-lookup"><span data-stu-id="9b99f-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="9b99f-202">Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="9b99f-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="9b99f-203">Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme</span><span class="sxs-lookup"><span data-stu-id="9b99f-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="9b99f-204">Bileşen <xref:Microsoft.AspNetCore.Components.Routing.Router> ile birlikte bileşeni, uygulamanın şu <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> durumlarda özel içerik belirtmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="9b99f-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="9b99f-205">İçerik bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="9b99f-205">Content isn't found.</span></span>
* <span data-ttu-id="9b99f-206">Kullanıcı, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşene uygulanan bir koşulla başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b99f-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="9b99f-207">Özniteliği, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="9b99f-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="9b99f-208">Zaman uyumsuz kimlik doğrulama devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="9b99f-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="9b99f-209">Varsayılan :::no-loc(Blazor Server)::: Proje şablonunda, `App` bileşen ( `App.razor` ) özel içeriği nasıl ayarlayabileceğinizi gösterir:</span><span class="sxs-lookup"><span data-stu-id="9b99f-209">In the default :::no-loc(Blazor Server)::: project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="9b99f-210">`<NotFound>`, `<NotAuthorized>` Ve etiketlerinin içeriği, `<Authorizing>` diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="9b99f-211">`<NotAuthorized>`Etiket belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> aşağıdaki geri dönüş iletisini kullanır:</span><span class="sxs-lookup"><span data-stu-id="9b99f-211">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="9b99f-212">Kimlik doğrulama durumu değişiklikleri hakkında bildirim</span><span class="sxs-lookup"><span data-stu-id="9b99f-212">Notification about authentication state changes</span></span>

<span data-ttu-id="9b99f-213">Uygulama, temeldeki kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, Kullanıcı oturumu kapattığından veya başka bir kullanıcı rollerini değiştirse), [özel `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) olarak <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> temel sınıfta yöntemi çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="9b99f-214">Bu, yeni verileri kullanarak yeniden kimlik doğrulama durumu verilerini (örneğin,) tüketicilere bildirir <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> .</span><span class="sxs-lookup"><span data-stu-id="9b99f-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="9b99f-215">Yordamsal mantık</span><span class="sxs-lookup"><span data-stu-id="9b99f-215">Procedural logic</span></span>

<span data-ttu-id="9b99f-216">Uygulama, yordamsal mantığın bir parçası olarak yetkilendirme kurallarını denetmek için gerekliyse, `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Kullanıcı tarafından elde etmek için türünde basamaklı bir parametre kullanın <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="9b99f-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="9b99f-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>``IAuthorizationService`, ilkeleri değerlendirmek için gibi diğer hizmetlerle birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="9b99f-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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

        if (user.:::no-loc(Identity):::.IsAuthenticated)
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
> <span data-ttu-id="9b99f-218">Bir :::no-loc(Blazor WebAssembly)::: uygulama bileşeninde, <xref:Microsoft.AspNetCore.Authorization> ve <xref:Microsoft.AspNetCore.Components.Authorization> ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9b99f-218">In a :::no-loc(Blazor WebAssembly)::: app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="9b99f-219">Bu ad alanları, uygulamanın dosyasına eklenerek Global olarak temin edilebilir `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="9b99f-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="9b99f-220">Sorun giderme hataları</span><span class="sxs-lookup"><span data-stu-id="9b99f-220">Troubleshoot errors</span></span>

<span data-ttu-id="9b99f-221">Sık karşılaşılan hatalar:</span><span class="sxs-lookup"><span data-stu-id="9b99f-221">Common errors:</span></span>

* <span data-ttu-id="9b99f-222">**Yetkilendirme, türünde basamaklı bir parametre gerektirir `Task\<AuthenticationState>` . `CascadingAuthenticationState` Bunu sağlamak için kullanmayı düşünün.**</span><span class="sxs-lookup"><span data-stu-id="9b99f-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="9b99f-223">**`null` için değer alındı `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="9b99f-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="9b99f-224">Projenin kimlik doğrulaması etkin bir şablon kullanılarak oluşturulmamış olması olasıdır :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="9b99f-224">It's likely that the project wasn't created using a :::no-loc(Blazor Server)::: template with authentication enabled.</span></span> <span data-ttu-id="9b99f-225">`<CascadingAuthenticationState>`Kullanıcı arabirimi ağacının bir bölümünü (örneğin, `App` bileşen () içinde aşağıdaki gibi sarmalayın `App.razor` :</span><span class="sxs-lookup"><span data-stu-id="9b99f-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="9b99f-226">, <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Temel alınan dı hizmetinden aldığı geçişli parametreyi sağlar <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .</span><span class="sxs-lookup"><span data-stu-id="9b99f-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b99f-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9b99f-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="9b99f-228">[Başar :::no-loc(Blazor)::: : kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluğu örnek bağlantıları</span><span class="sxs-lookup"><span data-stu-id="9b99f-228">[Awesome :::no-loc(Blazor):::: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
