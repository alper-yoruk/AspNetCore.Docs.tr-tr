---
title: ASP.NET Blazor Çekirdek kimlik doğrulaması ve yetkilendirme
author: guardrex
description: Kimlik Blazor doğrulama ve yetkilendirme senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501321"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="ab7bf-103">ASP.NET Blazor Çekirdek kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="ab7bf-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="ab7bf-104">Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ab7bf-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="ab7bf-105">WebAssembly için geçerli olan bu Blazor makaledeki kılavuz Blazor için, ASP.NET Core WebAssembly şablonu sürüm 3.2 veya sonraki gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="ab7bf-106">Visual Studio sürüm 16.6 Preview 2 veya sonraki sürümlerini Blazor kullanmıyorsanız, 'deki <xref:blazor/get-started>kılavuzu izleyerek en son WebAssembly şablonuna kavuşun.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="ab7bf-107">ASP.NET Core, uygulamalardaki Blazor güvenliğin yapılandırmasını ve yönetimini destekler.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="ab7bf-108">Güvenlik senaryoları Blazor Sunucu Blazor ve WebAssembly uygulamaları arasında farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="ab7bf-109">Blazor Sunucu uygulamaları sunucuda çalıştığı için, yetkilendirme denetimleri aşağıdakileri belirleyebilir:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="ab7bf-110">Kullanıcıya sunulan Kullanıcı Arabirimi seçenekleri (örneğin, hangi menü girişlerinin bir kullanıcı tarafından kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="ab7bf-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="ab7bf-111">Uygulama nın ve bileşenlerin alanları için erişim kuralları.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="ab7bf-112">WebAssembly uygulamaları istemcide çalışır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="ab7bf-113">Yetkilendirme *yalnızca* hangi Kullanıcı Arama Birimi seçeneklerinin gösterilen belirlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="ab7bf-114">İstemci tarafı denetimleri bir kullanıcı tarafından değiştirilebildiği veya atlanabildiği için, Bir Blazor WebAssembly uygulaması yetkilendirme erişim kurallarını uygulayamaz.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="ab7bf-115">[Razor Pages yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization) routable Razor bileşenleri için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="ab7bf-116">Bir [sayfaya routable](xref:blazor/integrate-components#render-components-from-a-page-or-view)olmayan bir Razor bileşeni gömülüyse, sayfanın yetkilendirme kuralları sayfanın içeriğinin geri kalanıyla birlikte Razor bileşenini dolaylı olarak etkiler.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="ab7bf-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>ve <xref:Microsoft.AspNetCore.Identity.UserManager%601> Razor bileşenleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="ab7bf-118">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="ab7bf-118">Authentication</span></span>

Blazor<span data-ttu-id="ab7bf-119">kullanıcının kimliğini oluşturmak için varolan ASP.NET Core kimlik doğrulama mekanizmalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="ab7bf-120">Tam mekanizma, uygulamanın Blazor nasıl barındırılan, Blazor Blazor WebAssembly veya Sunucu'ya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="ab7bf-121">WebAssembly kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="ab7bf-121"> WebAssembly authentication</span></span>

<span data-ttu-id="ab7bf-122">WebAssembly uygulamalarında Blazor kimlik doğrulama denetimleri atlanabilir, çünkü tüm istemci tarafı kodu kullanıcılar tarafından değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="ab7bf-123">Aynı durum, javascript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar da dahil olmak üzere tüm istemci tarafındaki uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="ab7bf-124">Aşağıdakileri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-124">Add the following:</span></span>

* <span data-ttu-id="ab7bf-125">[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) için uygulamanın proje dosyasına bir paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="ab7bf-126">Uygulamanın `Microsoft.AspNetCore.Components.Authorization` *_Imports.jilet* dosyasının ad alanı.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="ab7bf-127">Kimlik doğrulamayı işlemek için, yerleşik veya `AuthenticationStateProvider` özel bir hizmetin uygulanması aşağıdaki bölümlerde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="ab7bf-128">Uygulama ve yapılandırma oluşturma hakkında <xref:security/blazor/webassembly/index>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="ab7bf-129">Sunucu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="ab7bf-129"> Server authentication</span></span>

Blazor<span data-ttu-id="ab7bf-130">Sunucu uygulamaları, SignalR'' kullanılarak oluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="ab7bf-131">Bağlantı kurulduğunda [tabanlı uygulamalarda SignalRkimlik doğrulama](xref:signalr/authn-and-authz) işlenir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="ab7bf-132">Kimlik doğrulama, bir çerez veya başka bir taşıyıcı belirteci temel olabilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="ab7bf-133">Uygulama ve yapılandırma oluşturma hakkında <xref:security/blazor/server>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="ab7bf-134">Kimlik DoğrulamaStateProvider hizmeti</span><span class="sxs-lookup"><span data-stu-id="ab7bf-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="ab7bf-135">Yerleşik `AuthenticationStateProvider` hizmet, ASP.NET Core'un `HttpContext.User`kimlik doğrulama durumu verilerini elde eder.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="ab7bf-136">Kimlik doğrulama durumu, varolan ASP.NET Kimlik Doğrulama mekanizmalarıyla nasıl bütünleşir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="ab7bf-137">`AuthenticationStateProvider`kimlik doğrulama durumunu almak `AuthorizeView` için `CascadingAuthenticationState` bileşen ve bileşen tarafından kullanılan temel hizmettir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="ab7bf-138">Genellikle doğrudan kullanmayın. `AuthenticationStateProvider`</span><span class="sxs-lookup"><span data-stu-id="ab7bf-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="ab7bf-139">Bu makalede daha sonra açıklanan [>Yetkilendirme Görünümü bileşenini](#authorizeview-component) veya [Görev\<Kimlik Doğrulama durumunu](#expose-the-authentication-state-as-a-cascading-parameter) kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="ab7bf-140">Doğrudan kullanmanın `AuthenticationStateProvider` ana dezavantajı, temel kimlik doğrulama durumu verileri değişirse bileşenin otomatik olarak bilgilendirilmemesidir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="ab7bf-141">Hizmet, `AuthenticationStateProvider` aşağıdaki örnekte gösterildiği <xref:System.Security.Claims.ClaimsPrincipal> gibi geçerli kullanıcıverilerini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="ab7bf-142">Eğer `user.Identity.IsAuthenticated` `true` ve kullanıcı bir, <xref:System.Security.Claims.ClaimsPrincipal>iddiaları numaralandırılmış ve rollerde üyelik değerlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="ab7bf-143">Bağımlılık enjeksiyonu (DI) ve hizmetleri hakkında <xref:blazor/dependency-injection> <xref:fundamentals/dependency-injection>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="ab7bf-144">Özel bir AuthenticationStateProvider uygulama</span><span class="sxs-lookup"><span data-stu-id="ab7bf-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="ab7bf-145">Uygulama özel bir sağlayıcı gerektiriyorsa, uygulayın `AuthenticationStateProvider` ve geçersiz kılın: `GetAuthenticationStateAsync`</span><span class="sxs-lookup"><span data-stu-id="ab7bf-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="ab7bf-146">Bir Blazor WebAssembly uygulamasında, `CustomAuthStateProvider` hizmet `Main` *Program.cs*kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="ab7bf-147">Bir Blazor Server `CustomAuthStateProvider` uygulamasında, hizmet şu `Startup.ConfigureServices`şekilde kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="ab7bf-148">Önceki `CustomAuthStateProvider` örnekte kullanarak, tüm kullanıcılar kullanıcı adı `mrfibuli`ile kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="ab7bf-149">Kimlik doğrulama durumunu basamaklı bir parametre olarak ortaya çıkarma</span><span class="sxs-lookup"><span data-stu-id="ab7bf-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="ab7bf-150">Kullanıcı tarafından tetiklenen bir eylemi gerçekleştirirken olduğu gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekiyorsa, basamaklı bir `Task<AuthenticationState>`tür parametresi tanımlayarak kimlik doğrulama durumu verilerini elde edin:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="ab7bf-151">`user.Identity.IsAuthenticated` Ise, `true`iddiaları numaralandırılabilir ve rollere üyelik değerlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="ab7bf-152">Bileşendeki `Task<AuthenticationState>` `AuthorizeRouteView` ve `CascadingAuthenticationState` bileşenlerdeki bileşenleri kullanarak basamaklı `App` parametreyi ayarlama *(App.razor):*</span><span class="sxs-lookup"><span data-stu-id="ab7bf-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="ab7bf-153">Bir Blazor WebAssembly Uygulamasında, seçenekler ve yetkilendirme `Program.Main`için hizmetler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="ab7bf-154">Sunucu Blazor uygulamasında seçenekler ve yetkilendirme hizmetleri zaten mevcut olduğundan başka bir işlem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="ab7bf-155">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="ab7bf-155">Authorization</span></span>

<span data-ttu-id="ab7bf-156">Kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *yetkilendirme* kuralları uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="ab7bf-157">Erişim genellikle şu nedenlere bağlı olarak verilir veya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="ab7bf-158">Bir kullanıcının kimliği doğrulandı (oturum açmış).</span><span class="sxs-lookup"><span data-stu-id="ab7bf-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="ab7bf-159">Bir kullanıcı bir *role*roldedir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-159">A user is in a *role*.</span></span>
* <span data-ttu-id="ab7bf-160">Bir kullanıcının *bir talebi*vardır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-160">A user has a *claim*.</span></span>
* <span data-ttu-id="ab7bf-161">Bir *ilke* memnun.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="ab7bf-162">Bu kavramların her biri ASP.NET Core MVC veya Razor Pages uygulamasındakiyle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="ab7bf-163">ASP.NET Core güvenliği hakkında daha fazla bilgi için, ASP.NET Core Security ve Identity altındaki [makalelere](xref:security/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="ab7bf-164">AuthorizeView bileşeni</span><span class="sxs-lookup"><span data-stu-id="ab7bf-164">AuthorizeView component</span></span>

<span data-ttu-id="ab7bf-165">Bileşen, `AuthorizeView` kullanıcının görmeye yetkili olup olmadığına bağlı olarak seçici olarak Kullanıcı Arası Bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="ab7bf-166">Bu yaklaşım, yalnızca kullanıcı için veri *görüntülemeniz* gerektiğinde ve kullanıcının kimliğini yordam mantığıyla kullanmanız gerektiğinde yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="ab7bf-167">Bileşen, oturum `context` açmış kullanıcı `AuthenticationState`hakkındaki bilgilere erişmek için kullanabileceğiniz bir tür değişkenini ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="ab7bf-168">Kullanıcının kimliği doğrulanmıyorsa görüntülenmek için farklı içerik de sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="ab7bf-169">`AuthorizeView` Bileşen, bir liste `NavMenu` öğesini görüntülemek için `NavLink`bileşende *(Paylaşılan/NavMenu.razor)* kullanılabilir ,`<li>...</li>`ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="ab7bf-170">Kullanıcının bileşene gezinmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="ab7bf-171">İçeriği `<Authorized>` ve `<NotAuthorized>` etiketleri, diğer etkileşimli bileşenler gibi rasgele öğeler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="ab7bf-172">Kullanıcı Arabirimi seçeneklerini veya erişimi kontrol eden roller veya ilkeler gibi yetkilendirme koşulları [Yetkilendirme](#authorization) bölümünde ele alınır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="ab7bf-173">Yetkilendirme koşulları belirtilmemişse, `AuthorizeView` varsayılan bir ilke kullanır ve şu şekilde davranır:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="ab7bf-174">Yetkili olarak kimlik doğrulaması (oturum açmış) kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="ab7bf-175">Kimliği doğrulanmamış (oturum dışı) kullanıcılar yetkisiz olarak.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="ab7bf-176">Rol tabanlı ve ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="ab7bf-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="ab7bf-177">Bileşen `AuthorizeView` *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="ab7bf-178">Rol tabanlı yetkilendirme için `Roles` parametreyi kullanın:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="ab7bf-179">Daha fazla bilgi için bkz. <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="ab7bf-180">İlke tabanlı yetkilendirme için `Policy` parametreyi kullanın:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="ab7bf-181">Taleplere dayalı yetkilendirme, özel bir ilke tabanlı yetkilendirme örneğidir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="ab7bf-182">Örneğin, kullanıcıların belirli bir hak talebinde bulunmalarını gerektiren bir ilke tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="ab7bf-183">Daha fazla bilgi için bkz. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="ab7bf-184">Bu API'ler Blazor Sunucu veya Blazor WebAssembly uygulamalarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ab7bf-185">Ne `Roles` de `Policy` belirtilmişse, `AuthorizeView` varsayılan ilkeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="ab7bf-186">Eşzamanlı kimlik doğrulama sırasında görüntülenen içerik</span><span class="sxs-lookup"><span data-stu-id="ab7bf-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="ab7bf-187">kimlik doğrulama *durumunun eşzamanlı olarak*belirlenmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="ab7bf-188">Bu yaklaşım için birincil Blazor senaryo, kimlik doğrulaması için harici bir bitiş noktasına istekte bulunan WebAssembly uygulamalarıdır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="ab7bf-189">Kimlik doğrulama devam ederken, `AuthorizeView` varsayılan olarak hiçbir içerik görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="ab7bf-190">Kimlik doğrulaması gerçekleşirken içeriği görüntülemek `<Authorizing>` için aşağıdaki öğeyi kullanın:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="ab7bf-191">Bu yaklaşım normalde Sunucu uygulamaları Blazor için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="ab7bf-192">Sunucu uygulamaları, durum kurulur kurulmaz kimlik doğrulama durumunu bilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="ab7bf-193">`Authorizing`içerik bir Blazor Server uygulamasının `AuthorizeView` bileşeninde sağlanabilir, ancak içerik hiçbir zaman görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="ab7bf-194">[Yetki] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ab7bf-194">[Authorize] attribute</span></span>

<span data-ttu-id="ab7bf-195">Öznitelik `[Authorize]` Razor bileşenlerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="ab7bf-196">Yalnızca `[Authorize]` Blazor Yönlendirici `@page` üzerinden ulaşılan bileşenlerde kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="ab7bf-197">Yetkilendirme, yalnızca bir sayfa içinde işlenen alt bileşenler için *değil,* yönlendirmenin bir yönü olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="ab7bf-198">Bir sayfadaki belirli parçaların görüntülenmesini `AuthorizeView` yetkilendirmek için bunun yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="ab7bf-199">Öznitelik, `[Authorize]` rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="ab7bf-200">Rol tabanlı yetkilendirme için `Roles` parametreyi kullanın:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="ab7bf-201">İlke tabanlı yetkilendirme için `Policy` parametreyi kullanın:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="ab7bf-202">Ne `Roles` de `Policy` belirtilmişse, `[Authorize]` varsayılan olarak tedavi etmek için varsayılan ilke kullanır:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="ab7bf-203">Yetkili olarak kimlik doğrulaması (oturum açmış) kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="ab7bf-204">Kimliği doğrulanmamış (oturum dışı) kullanıcılar yetkisiz olarak.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="ab7bf-205">Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ab7bf-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="ab7bf-206">Bileşen, `Router` `AuthorizeRouteView` bileşenle birlikte, aşağıdaki durumlarda uygulamanın özel içerik belirtmesine olanak tanır:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="ab7bf-207">İçerik bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-207">Content isn't found.</span></span>
* <span data-ttu-id="ab7bf-208">Kullanıcı bileşene `[Authorize]` uygulanan bir koşul başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="ab7bf-209">Öznitelik `[Authorize]` [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="ab7bf-210">Eşkron kimlik doğrulama devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="ab7bf-211">Varsayılan Blazor Sunucu proje şablonunda, `App` bileşen *(App.razor)* özel içeriğin nasıl ayarlangerektiğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="ab7bf-212">`<NotFound>`İçeriği , `<NotAuthorized>`ve `<Authorizing>` etiketleri, diğer etkileşimli bileşenler gibi rasgele öğeleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="ab7bf-213">`<NotAuthorized>` Öğe belirtilmemişse, aşağıdaki `AuthorizeRouteView` geri dönüş iletisini kullanır:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="ab7bf-214">Kimlik doğrulama durumu değişiklikleri hakkında bildirim</span><span class="sxs-lookup"><span data-stu-id="ab7bf-214">Notification about authentication state changes</span></span>

<span data-ttu-id="ab7bf-215">Uygulama, temel kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, kullanıcı oturumaçtığı veya başka bir kullanıcı rollerini değiştirdiği için), `AuthenticationStateProvider` özel bir [AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) isteğe bağlı olarak temel sınıftaki yöntemi `NotifyAuthenticationStateChanged` çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="ab7bf-216">Bu, tüketicilere kimlik doğrulama durumu verilerini (örneğin,) `AuthorizeView`yeni verileri kullanarak yeniden işlemeleri için haber verar.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="ab7bf-217">Usul mantığı</span><span class="sxs-lookup"><span data-stu-id="ab7bf-217">Procedural logic</span></span>

<span data-ttu-id="ab7bf-218">Uygulamanın yordam mantığının bir parçası olarak yetkilendirme kurallarını denetlemesi gerekiyorsa, `Task<AuthenticationState>` kullanıcının. <xref:System.Security.Claims.ClaimsPrincipal></span><span class="sxs-lookup"><span data-stu-id="ab7bf-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="ab7bf-219">`Task<AuthenticationState>`ilkeleri değerlendirmek için diğer hizmetlerle `IAuthorizationService`birleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
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
> <span data-ttu-id="ab7bf-220">Bir Blazor WebAssembly uygulama bileşeninde, aşağıdakileri `Microsoft.AspNetCore.Authorization` ve `Microsoft.AspNetCore.Components.Authorization` ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="ab7bf-221">Bu ad alanları, uygulamanın *_Imports.razor* dosyasına eklenerek genel olarak sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="ab7bf-222">WebAssembly Blazor uygulamalarında yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="ab7bf-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="ab7bf-223">WebAssembly uygulamalarında, Blazor istemci tarafı kodu kullanıcılar tarafından değiştirilebildiği için yetkilendirme denetimleri atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="ab7bf-224">Aynı durum, javascript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar da dahil olmak üzere tüm istemci tarafındaki uygulama teknolojileri için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="ab7bf-225">**İstemci tarafındaki uygulamanız tarafından erişilen api uç noktaları içinde her zaman sunucuda yetkilendirme denetimleri gerçekleştirin.**</span><span class="sxs-lookup"><span data-stu-id="ab7bf-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="ab7bf-226">Daha fazla bilgi için <xref:security/blazor/webassembly/index>aşağıdaki makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="ab7bf-227">Sorun giderme hataları</span><span class="sxs-lookup"><span data-stu-id="ab7bf-227">Troubleshoot errors</span></span>

<span data-ttu-id="ab7bf-228">Sık karşılaşılan hatalar:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-228">Common errors:</span></span>

* <span data-ttu-id="ab7bf-229">**Yetkilendirme türü Görev\<Kimlik DoğrulamaDevlet> basamaklı bir parametre gerektirir. Bunu sağlamak için CascadingAuthenticationState'i kullanmayı düşünün.**</span><span class="sxs-lookup"><span data-stu-id="ab7bf-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="ab7bf-230">**`null`değer için alınır`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="ab7bf-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="ab7bf-231">Büyük olasılıkla proje kimlik doğrulama sıetkin Blazor bir Sunucu şablonu kullanılarak oluşturulmadı.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="ab7bf-232">Örneğin `<CascadingAuthenticationState>` bileşen *(App.razor)* gibi UI `App` ağacının bir bölümünü aşağıdaki gibi sarın:</span><span class="sxs-lookup"><span data-stu-id="ab7bf-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="ab7bf-233">Basamaklı `CascadingAuthenticationState` parametreyi sağlar `Task<AuthenticationState>` ve bu parametre de `AuthenticationStateProvider` temel DI hizmetinden alır.</span><span class="sxs-lookup"><span data-stu-id="ab7bf-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab7bf-234">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ab7bf-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="ab7bf-235">[Awesome Blazor: Kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluk örnek bağlantılar</span><span class="sxs-lookup"><span data-stu-id="ab7bf-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
