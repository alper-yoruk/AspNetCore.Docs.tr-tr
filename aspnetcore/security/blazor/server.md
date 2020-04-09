---
title: Core Blazor Server uygulamalarını güvenli ASP.NET
author: guardrex
description: Sunucu uygulamalarına yönelik Blazor güvenlik tehditlerini nasıl azaltılamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626017"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="89cdc-103">Core Blazor Server uygulamaları ASP.NET güvenli</span><span class="sxs-lookup"><span data-stu-id="89cdc-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="89cdc-104">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="89cdc-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="89cdc-105">Blazor Server uygulamaları, sunucu ve istemcinin uzun süreli bir ilişki sürdürdüğü *durumlu* bir veri işleme modelini benimser.</span><span class="sxs-lookup"><span data-stu-id="89cdc-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="89cdc-106">Kalıcı durum, potansiyel olarak uzun ömürlü olan bağlantıları kaplayabilen bir [devre](xref:blazor/state-management)tarafından korunur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="89cdc-107">Bir kullanıcı Blazor Server sitesini ziyaret ettiğinde, sunucu sunucunun belleğinde bir devre oluşturur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="89cdc-108">Devre, kullanıcının Kullanıcı Aracı'nda bir düğme seçmesi gibi olaylara hangi içeriğin işleneceğini ve bunlara tepki verir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="89cdc-109">Bu eylemleri gerçekleştirmek için bir devre, kullanıcının tarayıcısında javascript işlevlerini ve sunucudaki .NET yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="89cdc-110">Bu iki yönlü JavaScript tabanlı etkileşim [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="89cdc-111">JS interop Internet üzerinden oluşur ve istemci uzak bir tarayıcı kullandığından, Blazor Server uygulamaları çoğu web uygulaması güvenlik endişelerini paylaşır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="89cdc-112">Bu konu, Blazor Server uygulamalarına yönelik yaygın tehditleri açıklar ve Internet'e dönük uygulamalara odaklanmış tehdit azaltma kılavuzu sağlar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="89cdc-113">Şirket ağları veya intranetler gibi kısıtlı ortamlarda, bazı azaltma kılavuzu aşağıdakilerden biri:</span><span class="sxs-lookup"><span data-stu-id="89cdc-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="89cdc-114">Kısıtlı ortamda geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="89cdc-115">Güvenlik riski kısıtlı bir ortamda düşük olduğundan, uygulanması gereken maliyete değmez.</span><span class="sxs-lookup"><span data-stu-id="89cdc-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="89cdc-116">Blazor Server proje şablonu</span><span class="sxs-lookup"><span data-stu-id="89cdc-116">Blazor Server project template</span></span>

<span data-ttu-id="89cdc-117">Blazor Server proje şablonu, proje oluşturulduğunda kimlik doğrulaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="89cdc-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89cdc-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89cdc-119">Kimlik doğrulama mekanizmasına <xref:blazor/get-started> sahip yeni bir Blazor Server projesi oluşturmak için makaledeki Visual Studio kılavuzunu izleyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="89cdc-120">**Yeni bir ASP.NET Çekirdek Web Uygulaması Oluştur** iletişim kutusunda **Blazor Server App** şablonunu seçtikten sonra Kimlik **Doğrulama**altında **Değiştir'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="89cdc-121">Diğer ASP.NET Core projeleri için kullanılabilir aynı kimlik doğrulama mekanizmaları kümesini sunmak için bir iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="89cdc-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="89cdc-122">**Kimlik Doğrulaması Yok**</span><span class="sxs-lookup"><span data-stu-id="89cdc-122">**No Authentication**</span></span>
* <span data-ttu-id="89cdc-123">**Bireysel Kullanıcı Hesapları** &ndash; Kullanıcı hesapları saklanabilir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="89cdc-124">ASP.NET Core'un [Kimlik](xref:security/authentication/identity) sistemini kullanarak uygulama içinde.</span><span class="sxs-lookup"><span data-stu-id="89cdc-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="89cdc-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)ile .</span><span class="sxs-lookup"><span data-stu-id="89cdc-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="89cdc-126">**İş veya Okul Hesapları**</span><span class="sxs-lookup"><span data-stu-id="89cdc-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="89cdc-127">**Windows Kimlik Doğrulama**</span><span class="sxs-lookup"><span data-stu-id="89cdc-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="89cdc-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89cdc-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="89cdc-129">Kimlik doğrulama mekanizmasına sahip <xref:blazor/get-started> yeni bir Blazor Server projesi oluşturmak için makaledeki Visual Studio Code kılavuzunu izleyin:</span><span class="sxs-lookup"><span data-stu-id="89cdc-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="89cdc-130">İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri ( ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="89cdc-131">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="89cdc-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="89cdc-132">`{AUTHENTICATION}`Değer</span><span class="sxs-lookup"><span data-stu-id="89cdc-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="89cdc-133">Kimlik Doğrulaması Yok</span><span class="sxs-lookup"><span data-stu-id="89cdc-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="89cdc-134">Bireysel</span><span class="sxs-lookup"><span data-stu-id="89cdc-134">Individual</span></span><br><span data-ttu-id="89cdc-135">Uygulamada ASP.NET Çekirdek Kimliği ile saklanan kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="89cdc-136">Bireysel</span><span class="sxs-lookup"><span data-stu-id="89cdc-136">Individual</span></span><br><span data-ttu-id="89cdc-137">Azure AD [B2C'de](xref:security/authentication/azure-ad-b2c)depolanan kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="89cdc-138">İş veya Okul Hesapları</span><span class="sxs-lookup"><span data-stu-id="89cdc-138">Work or School Accounts</span></span><br><span data-ttu-id="89cdc-139">Tek bir kiracı için kuruluş kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="89cdc-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="89cdc-140">İş veya Okul Hesapları</span><span class="sxs-lookup"><span data-stu-id="89cdc-140">Work or School Accounts</span></span><br><span data-ttu-id="89cdc-141">Birden çok kiracı için kuruluş kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="89cdc-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="89cdc-142">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="89cdc-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="89cdc-143">Komut, `{APP NAME}` yer tutucuiçin sağlanan değeri içeren bir klasör oluşturur ve klasör adını uygulamanın adı olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="89cdc-144">Daha fazla bilgi için .NET Core Guide'daki [dotnet yeni](/dotnet/core/tools/dotnet-new) komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="89cdc-145">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89cdc-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="89cdc-146">Makaledeki Mac kılavuzu için <xref:blazor/get-started> Visual Studio'yu izleyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="89cdc-147">Yeni **Blazor Server App adımınızı Yapılandır'da,** **Kimlik Doğrulama** açılır tarihinden Bireysel **Kimlik Doğrulama'yı (uygulama içi)** seçin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="89cdc-148">Uygulama, ASP.NET Çekirdek Kimliği ile uygulamada saklanan bireysel kullanıcılar için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="89cdc-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="89cdc-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="89cdc-150">Kimlik doğrulama mekanizmasına sahip yeni <xref:blazor/get-started> bir Blazor Server projesi oluşturmak için makaledeki .NET Core CLI kılavuzunu izleyin:</span><span class="sxs-lookup"><span data-stu-id="89cdc-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="89cdc-151">İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri ( ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="89cdc-152">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="89cdc-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="89cdc-153">`{AUTHENTICATION}`Değer</span><span class="sxs-lookup"><span data-stu-id="89cdc-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="89cdc-154">Kimlik Doğrulaması Yok</span><span class="sxs-lookup"><span data-stu-id="89cdc-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="89cdc-155">Bireysel</span><span class="sxs-lookup"><span data-stu-id="89cdc-155">Individual</span></span><br><span data-ttu-id="89cdc-156">Uygulamada ASP.NET Çekirdek Kimliği ile saklanan kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="89cdc-157">Bireysel</span><span class="sxs-lookup"><span data-stu-id="89cdc-157">Individual</span></span><br><span data-ttu-id="89cdc-158">Azure AD [B2C'de](xref:security/authentication/azure-ad-b2c)depolanan kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="89cdc-159">İş veya Okul Hesapları</span><span class="sxs-lookup"><span data-stu-id="89cdc-159">Work or School Accounts</span></span><br><span data-ttu-id="89cdc-160">Tek bir kiracı için kuruluş kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="89cdc-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="89cdc-161">İş veya Okul Hesapları</span><span class="sxs-lookup"><span data-stu-id="89cdc-161">Work or School Accounts</span></span><br><span data-ttu-id="89cdc-162">Birden çok kiracı için kuruluş kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="89cdc-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="89cdc-163">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="89cdc-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="89cdc-164">Komut, `{APP NAME}` yer tutucuiçin sağlanan değeri içeren bir klasör oluşturur ve klasör adını uygulamanın adı olarak kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="89cdc-165">Daha fazla bilgi için .NET Core Guide'daki [dotnet yeni](/dotnet/core/tools/dotnet-new) komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="89cdc-166">Jetonları Blazor Server uygulamasına aktarın</span><span class="sxs-lookup"><span data-stu-id="89cdc-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="89cdc-167">Blazor Server uygulamasını normal bir Razor Pages veya MVC uygulamasıyla doğrulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="89cdc-168">Belirteçleri kimlik doğrulama çerezine sağlama ve kaydetme.</span><span class="sxs-lookup"><span data-stu-id="89cdc-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="89cdc-169">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="89cdc-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="89cdc-170">Tam `Startup.ConfigureServices` bir örnek de dahil olmak üzere örnek kod [için, sunucu tarafındaki Blazor uygulamasına geçen belirteçlere](https://github.com/javiercn/blazor-server-aad-sample)bakın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="89cdc-171">Başlangıç uygulama durumunda erişim ve yenileme belirteçleriyle geçmek için bir sınıf tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="89cdc-172">DI'deki belirteçleri çözmek için Blazor uygulaması içinde kullanılabilecek **kapsamlı** bir belirteç sağlayıcısı hizmeti tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="89cdc-173">In `Startup.ConfigureServices`, için hizmet ekle:</span><span class="sxs-lookup"><span data-stu-id="89cdc-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="89cdc-174">*_Host.cshtml* dosyasında, uygulamanın `InitialApplicationState` parametresi olarak oluşturun ve örnek lendirin ve aktarın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="89cdc-175">`App` Bileşende *(App.razor),* hizmeti çözün ve parametredeki verilerle başharfe alet edin:</span><span class="sxs-lookup"><span data-stu-id="89cdc-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="89cdc-176">Güvenli bir API isteği nde bulunan hizmette, belirteç sağlayıcısına enjekte edin ve API'yi aramak için belirteci alın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="89cdc-177">Kaynak tükenmesi</span><span class="sxs-lookup"><span data-stu-id="89cdc-177">Resource exhaustion</span></span>

<span data-ttu-id="89cdc-178">Bir istemci sunucuyla etkileşimde ve sunucunun aşırı kaynak tüketmesine neden olduğunda kaynak tükenmesi oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="89cdc-179">Aşırı kaynak tüketimi öncelikle etkiler:</span><span class="sxs-lookup"><span data-stu-id="89cdc-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="89cdc-180">CPU</span><span class="sxs-lookup"><span data-stu-id="89cdc-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="89cdc-181">Bellek</span><span class="sxs-lookup"><span data-stu-id="89cdc-181">Memory</span></span>](#memory)
* [<span data-ttu-id="89cdc-182">İstemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="89cdc-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="89cdc-183">Hizmet reddi (DoS) saldırıları genellikle bir uygulamanın veya sunucunun kaynaklarını tüketmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="89cdc-184">Ancak, kaynak tükenmesi mutlaka sisteme yapılan bir saldırının sonucu değildir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="89cdc-185">Örneğin, sonlu kaynaklar yüksek kullanıcı talebi nedeniyle tükenebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="89cdc-186">DoS, [Hizmet Reddi (DoS) saldırıları](#denial-of-service-dos-attacks) bölümünde daha fazla yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="89cdc-187">Veritabanları ve dosya tutamaçları (dosyaları okumak ve yazmak için kullanılır) gibi Blazor çerçevesi dışındaki kaynaklar da kaynak tükenmesi yaşayabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="89cdc-188">Daha fazla bilgi için bkz. <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="89cdc-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="89cdc-189">CPU</span><span class="sxs-lookup"><span data-stu-id="89cdc-189">CPU</span></span>

<span data-ttu-id="89cdc-190">CPU tükenmesi, bir veya daha fazla istemci sunucuyu yoğun CPU çalışması yapmaya zorladığında oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="89cdc-191">Örneğin, Bir *Fibonnacci numarası*hesaplayan bir Blazor Server uygulaması düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="89cdc-192">Fibonnacci numarası, sıradaki her sayının önceki iki sayının toplamı olduğu bir Fibonnacci dizisinden üretilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="89cdc-193">Yanıta ulaşmak için gereken çalışma miktarı dizinin uzunluğuna ve başlangıç değerinin boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="89cdc-194">Uygulama bir istemcinin isteğine sınır koymazsa, CPU yoğun hesaplamalar CPU'nun zamanına hakim olabilir ve diğer görevlerin performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="89cdc-195">Aşırı kaynak tüketimi kullanılabilirliği etkileyen bir güvenlik sorunudur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="89cdc-196">CPU tükenmesi, genel kullanıma açık tüm uygulamalar için bir endişe kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="89cdc-197">Normal web uygulamalarında, istekler ve bağlantılar güvenlik önlemi olarak zaman alar, ancak Blazor Server uygulamaları aynı güvenlik önlemlerini sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="89cdc-198">Blazor Server uygulamaları, CPU yoğun olabilecek bir çalışma gerçekleştirmeden önce uygun denetimleri ve sınırları içermelidir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="89cdc-199">Bellek</span><span class="sxs-lookup"><span data-stu-id="89cdc-199">Memory</span></span>

<span data-ttu-id="89cdc-200">Bir veya daha fazla istemci sunucuyu büyük miktarda bellek tüketmeye zorladığında bellek tükenmesi oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="89cdc-201">Örneğin, öğelerin listesini kabul eden ve görüntüleyen bir bileşene sahip blazor sunucusu yan uygulamasını düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="89cdc-202">Blazor uygulaması, izin verilen öğe sayısına veya istemciye geri işlenen öğe sayısına sınırlama lar koymuyorsa, bellek yoğun işlem ve işleme sunucunun performansının zarar gördüğü noktaya kadar sunucunun belleğinde hakim olabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="89cdc-203">Sunucu çökebilir veya çöktüğı gibi görünen noktaya kadar yavaşlayabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="89cdc-204">Sunucuda olası bir bellek tükenmesenaryosuyla ilgili öğelerin listesini korumak ve görüntülemek için aşağıdaki senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="89cdc-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="89cdc-205">Bir `List<MyItem>` özellik veya alandaki öğeler sunucunun belleği kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="89cdc-206">Uygulama, öğelerin listesinin sınırsız büyümesine izin veriyorsa, sunucunun belleği tükenebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="89cdc-207">Belleği bitmek üzere olan bu oturum, geçerli oturumun sona erdirilmesine (kilitlenme) neden olur ve bu sunucu örneğindeki eşzamanlı oturumların tümü bellek dışı bir özel durum alır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="89cdc-208">Bu senaryonun oluşmasını önlemek için, uygulamanın eşzamanlı kullanıcılara madde sınırı getiren bir veri yapısı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="89cdc-209">Görüntüleme için bir sayfalama düzeni kullanılmazsa, sunucu UI'de görünmeyen nesneler için ek bellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="89cdc-210">Öğe sayısında bir sınırlama olmadan, bellek talepleri kullanılabilir sunucu belleği egzoz olabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="89cdc-211">Bu senaryoyu önlemek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="89cdc-212">İşleme yaparken paginated listeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="89cdc-213">Yalnızca ilk 100 ila 1.000 öğeyi görüntüleyin ve görüntülenen öğelerin ötesinde öğeleri bulmak için kullanıcının arama ölçütlerini girmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="89cdc-214">Daha gelişmiş bir işleme senaryosu için, sanallaştırmayı destekleyen listeler veya ızgaralar *uygulayın.*</span><span class="sxs-lookup"><span data-stu-id="89cdc-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="89cdc-215">Sanallaştırma yı kullanarak, listeler yalnızca kullanıcı tarafından görülebilen öğelerin bir alt kümesini işler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="89cdc-216">Kullanıcı Kullanıcı Arabirimi'ndeki kaydırma çubuğuyla etkileşimde olduğunda, bileşen yalnızca görüntülenmek için gereken öğeleri işler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="89cdc-217">Şu anda görüntülenmek için gerekli olmayan öğeler, ideal bir yaklaşım olan ikincil depolama da tutulabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="89cdc-218">Görüntülenmeyen öğeler de daha az ideal bellekte tutulabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="89cdc-219">Blazor Server uygulamaları, WPF, Windows Forms veya Blazor WebAssembly gibi özel uygulamalar için diğer web hizmeti arabirimi çerçevelerine benzer bir programlama modeli sunar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="89cdc-220">Temel fark, ui çerçeveleri birkaç uygulama tarafından tüketilen bellek istemciye ait ve sadece bu bireysel istemci etkiler olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="89cdc-221">Örneğin, blazor WebAssembly uygulaması tamamen istemciüzerinde çalışır ve yalnızca istemci bellek kaynaklarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="89cdc-222">Blazor Server senaryosunda, uygulama tarafından tüketilen bellek sunucuya aittir ve sunucu örneğindeki istemciler arasında paylaşılır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="89cdc-223">Sunucu tarafı bellek talepleri tüm Blazor Server uygulamaları için göz önünde bulundurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="89cdc-224">Ancak, web uygulamalarının çoğu durum dilemezdir ve yanıt döndürüldüğünde isteği işlerken kullanılan bellek serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="89cdc-225">Genel bir öneri olarak, istemci bağlantılarını devam eden diğer sunucu tarafındaki uygulamalarda olduğu gibi, istemcilerin sınırsız miktarda bellek ayırmasına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="89cdc-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="89cdc-226">Blazor Server uygulaması tarafından tüketilen bellek, tek bir istekten daha uzun süre kalır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="89cdc-227">Geliştirme sırasında, bir profil oluşturucu kullanılabilir veya istemcilerin bellek taleplerini değerlendirmek için bir izleme yakalanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="89cdc-228">Bir profil oluşturucu veya izleme, belirli bir istemciye ayrılan belleği yakalamaz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="89cdc-229">Geliştirme sırasında belirli bir istemcinin bellek kullanımını yakalamak için, bir dökümü yakalayın ve kullanıcının devresine kök salan tüm nesnelerin bellek talebini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="89cdc-230">İstemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="89cdc-230">Client connections</span></span>

<span data-ttu-id="89cdc-231">Bir veya daha fazla istemci sunucuya çok fazla eşzamanlı bağlantı açtığında bağlantı tükenmesi oluşabilir ve diğer istemcilerin yeni bağlantılar kurmasını engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="89cdc-232">Blazor istemcileri oturum başına tek bir bağlantı kurar ve tarayıcı penceresi açık olduğu sürece bağlantıyı açık tutar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="89cdc-233">Sunucudaki tüm bağlantıların bakımı yla ilgili talepler Blazor uygulamalarına özgü değildir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="89cdc-234">Bağlantıların kalıcı yapısı ve Blazor Server uygulamalarının durum daki doğası göz önüne alındığında, bağlantı yorgunluğu uygulamanın kullanılabilirliği açısından daha büyük bir risktir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="89cdc-235">Varsayılan olarak, blazor server uygulaması için kullanıcı başına bağlantı sayısında bir sınır yoktur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="89cdc-236">Uygulama bir bağlantı sınırı gerektiriyorsa, aşağıdaki yaklaşımlardan birini veya birkaçını alın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="89cdc-237">Yetkisiz kullanıcıların uygulamaya bağlanma yeteneğini doğal olarak sınırlayan kimlik doğrulaması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="89cdc-238">Bu senaryonun etkili olabilmesi için, kullanıcıların yeni kullanıcılar alabilmeli şekilde sağlaması engellenmelidir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="89cdc-239">Kullanıcı başına bağlantı sayısını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-239">Limit the number of connections per user.</span></span> <span data-ttu-id="89cdc-240">Bağlantıları sınırlandırma aşağıdaki yaklaşımlarla gerçekleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="89cdc-241">Yasal kullanıcıların uygulamaya erişmesine izin vermek için dikkatli olun (örneğin, istemcinin IP adresine göre bir bağlantı sınırı oluşturulduğunda).</span><span class="sxs-lookup"><span data-stu-id="89cdc-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="89cdc-242">Uygulama düzeyinde:</span><span class="sxs-lookup"><span data-stu-id="89cdc-242">At the app level:</span></span>
    * <span data-ttu-id="89cdc-243">Endpoint yönlendirme genişletilebilirlik.</span><span class="sxs-lookup"><span data-stu-id="89cdc-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="89cdc-244">Uygulamaya bağlanmak ve kullanıcı başına etkin oturumları izlemek için kimlik doğrulaması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="89cdc-245">Bir sınıra ulaştıktan sonra yeni oturumları reddedin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="89cdc-246">Proxy WebSocket bağlantıları, istemcilerden bir uygulamaya bağlantıları birden fazlaya çarpan [Azure SinyalR Hizmeti](/azure/azure-signalr/signalr-overview) gibi bir proxy aracılığıyla bir uygulamaya bağlanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="89cdc-247">Bu, tek bir istemcinin kurabileceğinden daha fazla bağlantı kapasitesine sahip bir uygulama sağlayarak istemcinin sunucuya olan bağlantıları tüketmesini önler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="89cdc-248">Sunucu düzeyinde: Uygulamanın önünde bir proxy/ağ geçidi kullanın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="89cdc-249">Örneğin, [Azure Ön Kapı,](/azure/frontdoor/front-door-overview) web trafiğinin bir uygulamaya genel yönlendirmesini tanımlamanızı, yönetmenize ve izlemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="89cdc-250">Hizmet reddi (DoS) saldırıları</span><span class="sxs-lookup"><span data-stu-id="89cdc-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="89cdc-251">Hizmet reddi (DoS) saldırıları, sunucunun bir veya daha fazla kaynağının tüketmesine ve uygulamanın kullanılamamasına neden olmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="89cdc-252">Blazor Server uygulamaları bazı varsayılan sınırlar içerir ve DoS saldırılarına karşı korunmak için diğer ASP.NET Core ve SignalR sınırlarına güvenir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="89cdc-253">Blazor Server uygulama sınırı</span><span class="sxs-lookup"><span data-stu-id="89cdc-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="89cdc-254">Açıklama</span><span class="sxs-lookup"><span data-stu-id="89cdc-254">Description</span></span> | <span data-ttu-id="89cdc-255">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="89cdc-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="89cdc-256">Belirli bir sunucunun bir anda bellekte tuttuğu maksimum bağlantısız devre sayısı.</span><span class="sxs-lookup"><span data-stu-id="89cdc-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="89cdc-257">100</span><span class="sxs-lookup"><span data-stu-id="89cdc-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="89cdc-258">Bağlantısı kesilen bir devre nin parçalanmadan önce bellekte tutulduğu maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="89cdc-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="89cdc-259">3 dakika</span><span class="sxs-lookup"><span data-stu-id="89cdc-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="89cdc-260">Sunucunun, eşzamanlı bir JavaScript işlev çağırması zamanlamasını zamanlamadan önce beklediği maksimum süre.</span><span class="sxs-lookup"><span data-stu-id="89cdc-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="89cdc-261">1 dakika</span><span class="sxs-lookup"><span data-stu-id="89cdc-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="89cdc-262">Sağlam yeniden bağlantıyı desteklemek için sunucunun belirli bir zamanda devre başına bellekte tuttuğu maksimum onaylanmamış render toplu iş sayısı.</span><span class="sxs-lookup"><span data-stu-id="89cdc-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="89cdc-263">Sınıra ulaştıktan sonra, sunucu istemci tarafından bir veya daha fazla toplu iş kabul edilene kadar yeni işişleme toplu oluşturmayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="89cdc-264">10</span><span class="sxs-lookup"><span data-stu-id="89cdc-264">10</span></span> |


| <span data-ttu-id="89cdc-265">SignalR ve ASP.NET Çekirdek sınırı</span><span class="sxs-lookup"><span data-stu-id="89cdc-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="89cdc-266">Açıklama</span><span class="sxs-lookup"><span data-stu-id="89cdc-266">Description</span></span> | <span data-ttu-id="89cdc-267">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="89cdc-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="89cdc-268">Tek bir ileti için ileti boyutu.</span><span class="sxs-lookup"><span data-stu-id="89cdc-268">Message size for an individual message.</span></span> | <span data-ttu-id="89cdc-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="89cdc-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="89cdc-270">Tarayıcı ile etkileşimler (istemci)</span><span class="sxs-lookup"><span data-stu-id="89cdc-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="89cdc-271">İstemci, JS interop olay gönderme ve tamamlama render aracılığıyla sunucu ile etkileşim.</span><span class="sxs-lookup"><span data-stu-id="89cdc-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="89cdc-272">JS interop iletişim JavaScript ve .NET arasında her iki yönde gider:</span><span class="sxs-lookup"><span data-stu-id="89cdc-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="89cdc-273">Tarayıcı olayları istemciden sunucuya eşzamanlı bir şekilde gönderilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="89cdc-274">Sunucu, kullanıcı ayını gerektiği gibi eşzamanlı olarak yeniden işlemeye yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="89cdc-275">.NET'ten çağrılan JavaScript işlevleri</span><span class="sxs-lookup"><span data-stu-id="89cdc-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="89cdc-276">.NET yöntemlerinden JavaScript'e yapılan aramalar için:</span><span class="sxs-lookup"><span data-stu-id="89cdc-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="89cdc-277">Tüm çağrıların, bir arayanın döndürülmesi, <xref:System.OperationCanceledException> başarısız oldukları yapılandırılabilir bir zaman acısı vardır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="89cdc-278">Aramalar için varsayılan bir zaman ası vardır (`CircuitOptions.JSInteropDefaultCallTimeout`) bir dakika.</span><span class="sxs-lookup"><span data-stu-id="89cdc-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="89cdc-279">Bu sınırı yapılandırmak <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>için bkz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="89cdc-280">İptal iamasını her çağrı başına kontrol etmek için bir iptal belirteci sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="89cdc-281">İptal belirteci sağlandığı takdirde, istemciye yapılan olası ve zamana bağlı herhangi bir çağrının mümkün olduğu durumlarda varsayılan arama zaman ayarı'na güvenin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="89cdc-282">JavaScript aramasının sonucuna güvenilemez.</span><span class="sxs-lookup"><span data-stu-id="89cdc-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="89cdc-283">Tarayıcıda çalışan Blazor uygulama istemcisi, javascript işlevini çağırmak için arar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="89cdc-284">İşlev çağrılır ve sonuç veya hata üretilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="89cdc-285">Kötü amaçlı bir istemci şunları yapmaya çalışabilir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="89cdc-286">JavaScript işlevinden bir hata döndürerek uygulamada bir soruna neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="89cdc-287">JavaScript işlevinden beklenmeyen bir sonuç döndürerek sunucuda istenmeyen bir davranış alıbın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="89cdc-288">Önceki senaryolara karşı korunmak için aşağıdaki önlemleri alın:</span><span class="sxs-lookup"><span data-stu-id="89cdc-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="89cdc-289">Çağrılar sırasında oluşabilecek hataları hesaba katmak için [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadeleri içindeki JS interop çağrılarını sarın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="89cdc-290">Daha fazla bilgi için bkz. <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="89cdc-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="89cdc-291">Herhangi bir işlem yapmadan önce hata iletileri de dahil olmak üzere JS interop çağrılarından döndürülen verileri doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="89cdc-292">.TARAYıCıDAN çağrılan NET yöntemleri</span><span class="sxs-lookup"><span data-stu-id="89cdc-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="89cdc-293">JavaScript'ten .NET yöntemlerine yapılan çağrılara güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="89cdc-294">Bir .NET yöntemi JavaScript'e maruz kaldığında, .NET yönteminin nasıl çağrıldığını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="89cdc-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="89cdc-295">JavaScript'e maruz kalan herhangi bir .NET yöntemini, uygulamanın genel bitiş noktası olarak ele alın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="89cdc-296">Girişi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-296">Validate input.</span></span>
    * <span data-ttu-id="89cdc-297">Değerlerin beklenen aralıklarda olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="89cdc-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="89cdc-298">Kullanıcının istenen eylemi gerçekleştirmek için izni olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="89cdc-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="89cdc-299">.NET yöntemi çağırmasının bir parçası olarak aşırı miktarda kaynak ayırmayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="89cdc-300">Örneğin, CPU ve bellek kullanımı için denetimler gerçekleştirin ve sınırlar yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="89cdc-301">Statik ve örnek yöntemlerinin JavaScript istemcilerine maruz kabileceğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="89cdc-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="89cdc-302">Tasarım, durumu uygun kısıtlamalarla paylaşmayı gerektiriyorsa, oturumlar arasında durum paylaşımından kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="89cdc-303">Örneğin, başlangıçta `DotNetReference` bağımlılık enjeksiyonu (DI) yoluyla oluşturulan nesneler aracılığıyla ortaya çıkarılan yöntemler, nesnelerin kapsamlı nesneler olarak kaydedilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="89cdc-304">Bu, Sunucu uygulamasının Blazor kullandığı herhangi bir DI hizmeti için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="89cdc-305">Statik yöntemler için, uygulama bir sunucu örneğindeki tüm kullanıcılar arasında durum by-design açıkça paylaşım olmadığı sürece istemciye kapsamlandırılamaz durum oluşturmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="89cdc-306">Kullanıcı tarafından sağlanan verileri parametrelerdeki JavaScript çağrılarına aktarmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="89cdc-307">Parametrelerde veri aktarılması kesinlikle gerekiyorsa, JavaScript kodunun siteleri arası [komut dosyası (XSS)](#cross-site-scripting-xss) güvenlik açıklarını tanıtmadan verileri geçirmeyi işlediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="89cdc-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="89cdc-308">Örneğin, bir öğenin özelliğini `innerHTML` ayarlayarak kullanıcı tarafından sağlanan verileri Belge Nesnesi Modeline (DOM) yazmayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="89cdc-309">Devre dışı ve diğer güvenli olmayan JavaScript `eval` ilkellerini devre dışı katmak için [İçerik Güvenlik İlkesi 'ni (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="89cdc-310">Çerçevenin gönderme uygulamasının üstüne .NET çağrılarının özel olarak gönderilmesini uygulamaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="89cdc-311">.NET yöntemlerinin tarayıcıya teşhir edilmesi, genel Blazor geliştirme için önerilmeyen gelişmiş bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="89cdc-312">Olaylar</span><span class="sxs-lookup"><span data-stu-id="89cdc-312">Events</span></span>

<span data-ttu-id="89cdc-313">Olaylar, sunucu Blazor uygulamasına bir giriş noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="89cdc-314">Web uygulamalarında uç noktaları korumak için aynı kurallar Blazor Sunucu uygulamalarında olay işleme için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="89cdc-315">Kötü niyetli bir istemci, bir olay için yük olarak göndermek istediği verileri gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="89cdc-316">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="89cdc-316">For example:</span></span>

* <span data-ttu-id="89cdc-317">Bir değişiklik `<select>` olayı, uygulamanın istemciye sunduğu seçenekler arasında olmayan bir değer gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="89cdc-318">İstemci tarafı doğrulaması atlayarak sunucuya herhangi bir `<input>` metin verisi gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="89cdc-319">Uygulama, uygulamanın işlediği herhangi bir olay için verileri doğrulamalıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="89cdc-320">Çerçeve Blazor [formları bileşenleri](xref:blazor/forms-validation) temel doğrulamaları gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="89cdc-321">Uygulama özel form bileşenleri kullanıyorsa, olay verilerini uygun şekilde doğrulamak için özel kod yazılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="89cdc-322">Sunucu olayları eşzamanlıdır, bu nedenle uygulama yeni bir render oluşturarak tepki vermek için zaman bulamadan birden çok olay sunucuya gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="89cdc-323">Bu göz önünde bulundurulması gereken bazı güvenlik etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-323">This has some security implications to consider.</span></span> <span data-ttu-id="89cdc-324">Uygulamadaki istemci eylemlerini sınırlama, olay işleyicileri içinde gerçekleştirilmeli ve geçerli işlenen görünüm durumuna bağlı olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="89cdc-325">Bir kullanıcının sayaç sayacını en fazla üç kez artmasını sağlayacak bir sayaç bileşeni düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="89cdc-326">Sayacı nitremi leştirmek için düğme aşağıdakilerin `count`değerine bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="89cdc-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="89cdc-327">Bir istemci, çerçeve bu bileşenin yeni bir işlemesini oluşturmadan önce bir veya daha fazla artış olayı gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="89cdc-328">Sonuç, düğme `count` Kullanıcı Arabirimi tarafından yeterince hızlı bir şekilde kaldırılmadığı ndan kullanıcı tarafından *üç kat* daha fazla artımlanolabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="89cdc-329">Üç `count` artış sınırına ulaşmanın doğru yolu aşağıdaki örnekte gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="89cdc-330">Denetimi `if (count < 3) { ... }` işleyicinin içine ekleyerek, artış `count` kararı geçerli uygulama durumuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="89cdc-331">Karar, önceki örnekte olduğu gibi geçici olarak bayat olabilecek Kişisel BirA'nın durumuna dayalı değildir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="89cdc-332">Birden fazla sevkiyata karşı koruma</span><span class="sxs-lookup"><span data-stu-id="89cdc-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="89cdc-333">Bir olay geri araması, harici bir hizmetten veya veritabanından veri alma gibi uzun süren bir işlemi eşit olarak çağırırsa, bir koruma kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="89cdc-334">Koruma, işlem görsel geri bildirimle devam ederken kullanıcının birden çok işlemi sıraya girmesini engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="89cdc-335">Aşağıdaki bileşen kodu, `true` `GetForecastAsync` sunucudan veri elde ederken ayarlar. `isLoading`</span><span class="sxs-lookup"><span data-stu-id="89cdc-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="89cdc-336">`isLoading` Ise, `true`düğme UI devre dışı bırakılır:</span><span class="sxs-lookup"><span data-stu-id="89cdc-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="89cdc-337">Önceki örnekte gösterilen koruma deseni, arka plan işlemi `async` - `await` desenle eşzamanlı olarak yürütülürse çalışır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="89cdc-338">Erken iptal edin ve kullanım sonrası elden çıkarmadan kaçının</span><span class="sxs-lookup"><span data-stu-id="89cdc-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="89cdc-339">Birden fazla sevkiyat bölümüne [karşı Guard'da](#guard-against-multiple-dispatches) açıklandığı gibi bir <xref:System.Threading.CancellationToken> koruma kullanmanın yanı sıra, bileşen imha edildiğinde uzun süren işlemleri iptal etmek için bir koruma kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="89cdc-340">Bu yaklaşım, bileşenlerde *kullanım sonrası elden çıkarmaktan* kaçınmanın ek yararına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="89cdc-341">Büyük miktarda veri üreten olaylardan kaçının</span><span class="sxs-lookup"><span data-stu-id="89cdc-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="89cdc-342">Gibi bazı DOM `oninput` olayları, büyük `onscroll`miktarda veri üretebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="89cdc-343">Sunucu uygulamalarında Blazor bu olayları kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="89cdc-344">Ek güvenlik kılavuzu</span><span class="sxs-lookup"><span data-stu-id="89cdc-344">Additional security guidance</span></span>

<span data-ttu-id="89cdc-345">Core uygulamaları ASP.NET güvenliğini sağlama Blazor kılavuzu Server uygulamalarına uygulanır ve aşağıdaki bölümlerde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="89cdc-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="89cdc-346">Günlüğe kaydetme ve hassas veriler</span><span class="sxs-lookup"><span data-stu-id="89cdc-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="89cdc-347">HTTPS ile geçiş sırasındaki bilgileri koruyun</span><span class="sxs-lookup"><span data-stu-id="89cdc-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="89cdc-348">[Site arası komut dosyası (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="89cdc-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="89cdc-349">Çapraz orijin koruması</span><span class="sxs-lookup"><span data-stu-id="89cdc-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="89cdc-350">Tıklama kaçırma</span><span class="sxs-lookup"><span data-stu-id="89cdc-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="89cdc-351">Açık yönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="89cdc-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="89cdc-352">Günlüğe kaydetme ve hassas veriler</span><span class="sxs-lookup"><span data-stu-id="89cdc-352">Logging and sensitive data</span></span>

<span data-ttu-id="89cdc-353">İstemci ve sunucu arasındaki JS interop etkileşimleri sunucunun <xref:Microsoft.Extensions.Logging.ILogger> günlüklerinde örneklerle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="89cdc-354">gerçek olaylar veya JS interop giriş ve çıkışları gibi hassas bilgilerin günlüğe kaydedilmesini önler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="89cdc-355">Sunucuda bir hata oluştuğunda, çerçeve istemciyi bildirer ve oturumu yıkın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="89cdc-356">Varsayılan olarak, istemci tarayıcının geliştirici araçlarında görülebilen genel bir hata iletisi alır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="89cdc-357">İstemci tarafı hatası çağrı yığınını içermez ve hatanın nedeni hakkında ayrıntı sağlamaz, ancak sunucu günlükleri bu tür bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="89cdc-358">Geliştirme amacıyla, hassas hata bilgileri ayrıntılı hataları etkinleştirerek istemcinin kullanımına sunulabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="89cdc-359">Aşağıdakilerle ayrıntılı hataları etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="89cdc-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="89cdc-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="89cdc-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="89cdc-361">`DetailedErrors`yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="89cdc-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="89cdc-362">Örneğin, ortam `ASPNETCORE_DETAILEDERRORS` değişkenini bir `true`değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="89cdc-363">Hata bilgilerinin Internet'te istemcilere teşhir edilmesi her zaman kaçınılması gereken bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="89cdc-364">HTTPS ile geçiş sırasındaki bilgileri koruyun</span><span class="sxs-lookup"><span data-stu-id="89cdc-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="89cdc-365">Sunucu SignalR istemci ve sunucu arasındaki iletişim için kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="89cdc-366">Sunucu normalde SignalR genellikle WebSockets olan anlaşmayapan aktarım kullanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="89cdc-367">Sunucu, sunucu ve istemci arasında gönderilen verilerin bütünlüğünü ve gizliliğini sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="89cdc-368">Her zaman HTTPS kullanın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="89cdc-369">Site arası komut dosyası (XSS)</span><span class="sxs-lookup"><span data-stu-id="89cdc-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="89cdc-370">Site arası komut dosyası (XSS), yetkisiz bir tarafın tarayıcı bağlamında rasgele mantık yürütmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="89cdc-371">Gizliliği ihlal edilen bir uygulama istemcide rasgele kod çalıştırabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="89cdc-372">Güvenlik açığı, sunucuya karşı bir dizi kötü amaçlı eylem gerçekleştirme potansiyeline sahip olarak kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="89cdc-373">Sunucuya sahte/geçersiz olaylar gönder.</span><span class="sxs-lookup"><span data-stu-id="89cdc-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="89cdc-374">Gönderme başarısız /geçersiz render tamamlamaları.</span><span class="sxs-lookup"><span data-stu-id="89cdc-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="89cdc-375">Render tamamlamalarını göndermekten kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="89cdc-376">JavaScript'ten .NET'e interop çağrıları gönder.</span><span class="sxs-lookup"><span data-stu-id="89cdc-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="89cdc-377">.NET'ten JavaScript'e interop çağrılarının yanıtını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="89cdc-378">.NET'i JS interop sonuçlarına göndermekten kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="89cdc-379">Blazor Sunucu çerçevesi, önceki tehditlere karşı koruma sağlamak için adımlar atsA:</span><span class="sxs-lookup"><span data-stu-id="89cdc-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="89cdc-380">İstemci toplu iş oluşturmayı kabul etmiyorsa yeni UI güncelleştirmeleri oluşturmayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="89cdc-381">' ile `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`yapılandırılan.</span><span class="sxs-lookup"><span data-stu-id="89cdc-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="89cdc-382">İstemciden yanıt almadan bir dakika sonra herhangi bir .NET'i JavaScript aramasına zaman sızdırın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="89cdc-383">' ile `CircuitOptions.JSInteropDefaultCallTimeout`yapılandırılan.</span><span class="sxs-lookup"><span data-stu-id="89cdc-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="89cdc-384">JS interop sırasında tarayıcıdan gelen tüm girişlerde temel doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="89cdc-385">.NET başvuruları geçerlidir ve .NET yöntemi tarafından beklenen türdendir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="89cdc-386">Veriler yanlış biçimlendirilmiş değil.</span><span class="sxs-lookup"><span data-stu-id="89cdc-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="89cdc-387">Yöntem için doğru sayıda bağımsız değişken yükü nde bulunur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="89cdc-388">Bağımsız değişkenler veya sonuç, yöntemi aramadan önce doğru şekilde deserialed edilebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="89cdc-389">Gönderilen olaylardan tarayıcıdan gelen tüm girişlerde temel doğrulama yı gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="89cdc-390">Olayın geçerli bir türü vardır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-390">The event has a valid type.</span></span>
  * <span data-ttu-id="89cdc-391">Olay için veriler deserialized olabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="89cdc-392">Olayla ilişkili bir olay işleyicisi vardır.</span><span class="sxs-lookup"><span data-stu-id="89cdc-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="89cdc-393">Çerçevenin uyguladığı güvenlik önlemlerine ek olarak, uygulama nın tehditlere karşı korunmak ve uygun eylemleri uygulamak için geliştirici tarafından kodlanmalıdır:</span><span class="sxs-lookup"><span data-stu-id="89cdc-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="89cdc-394">Olayları işlerken verileri her zaman doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="89cdc-395">Geçersiz veri aldıktan sonra uygun eylemi gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="89cdc-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="89cdc-396">Verileri yoksayın ve döndürün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-396">Ignore the data and return.</span></span> <span data-ttu-id="89cdc-397">Bu, uygulamanın istekleri işlemeye devam etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="89cdc-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="89cdc-398">Uygulama girişin gayrimeşru olduğunu ve yasal istemci tarafından üretilemediğini belirlerse, bir özel durum atın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="89cdc-399">Bir istisna atma devre aşağı gözyaşları ve oturumu sona erer.</span><span class="sxs-lookup"><span data-stu-id="89cdc-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="89cdc-400">Günlüklerde yer alan toplu iş tamamlanmalarını işlemek tarafından sağlanan hata iletisi güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="89cdc-401">Hata istemci tarafından sağlanır ve istemci tehlikeye olabilir gibi genellikle güvenilir olamaz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="89cdc-402">JavaScript ve .NET yöntemleri arasında her iki yönde de JS interop çağrıları nın girişine güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="89cdc-403">Uygulama, bağımsız değişkenler veya sonuçlar doğru şekilde deserialized olsa bile, bağımsız değişkenler ve sonuçların içeriğigeçerli olduğunu doğrulamak için sorumludur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="89cdc-404">Bir XSS açığının mevcut olması için uygulamanın işlenen sayfaya kullanıcı girişi dahil etmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="89cdc-405">Sunucu bileşenleri, *.jilet* dosyasındaki biçimlendirmenin yordamsal C# mantığına dönüştürüldüğü bir derleme zamanı adımı yürütür.</span><span class="sxs-lookup"><span data-stu-id="89cdc-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="89cdc-406">Çalışma zamanında, C# mantığı öğeleri, metni ve alt bileşenleri açıklayan bir *render ağacı* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="89cdc-407">Bu JavaScript yönergeleri bir dizi ile tarayıcının DOM uygulanır (veya önceden işleme durumunda HTML serileştirilmiş):</span><span class="sxs-lookup"><span data-stu-id="89cdc-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="89cdc-408">Normal Razor sözdizimi (örneğin,) aracılığıyla `@someStringValue`işlenen kullanıcı girişi, yalnızca metin yazabilen komutlarla DOM'a eklenmiştir, çünkü XSS güvenlik açığını ortaya çıkarmaz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="89cdc-409">Değer HTML biçimlendirmesi içerse bile, değer statik metin olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="89cdc-410">Ön işleme yapılırken, çıktı HTML kodlanır ve bu da içeriği statik metin olarak görüntüler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="89cdc-411">Komut dosyası etiketlerine izin verilmez ve uygulamanın bileşen oluşturma ağacına dahil edilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="89cdc-412">Bir komut dosyası etiketi bileşenin biçimlendirmesinde yer alırsa, derleme zamanı hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="89cdc-413">Bileşen yazarları, Razor kullanmadan C# bileşenlerini yazabilirler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="89cdc-414">Bileşen yazarı çıktı yayırken doğru API'leri kullanmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="89cdc-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="89cdc-415">Örneğin, ikincisi `builder.AddContent(0, someUserSuppliedString)` bir XSS güvenlik açığı oluşturabileceğinden, kullanmayın ve *kullanmayın.* `builder.AddMarkupContent(0, someUserSuppliedString)`</span><span class="sxs-lookup"><span data-stu-id="89cdc-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="89cdc-416">XSS saldırılarına karşı korumanın bir parçası olarak, İçerik Güvenlik [Politikası (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)gibi XSS azaltıcı etkenleri uygulamayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="89cdc-417">Daha fazla bilgi için bkz. <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="89cdc-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="89cdc-418">Çapraz orijin koruması</span><span class="sxs-lookup"><span data-stu-id="89cdc-418">Cross-origin protection</span></span>

<span data-ttu-id="89cdc-419">Çapraz kaynak saldırıları, farklı bir kökenden gelen bir istemcinin sunucuya karşı bir eylem gerçekleştirmesini içerir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="89cdc-420">Kötü amaçlı eylem genellikle bir GET isteği veya bir form POST (Çapraz Site İstek Sahteciliği, CSRF), ancak kötü amaçlı bir WebSocket açılması da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="89cdc-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="89cdc-421">Sunucu [uygulamaları, hub protokolü'nü SignalR kullanan diğer uygulamaların sunduğu garantilerle aynı garantiyi](xref:signalr/security)sunar:</span><span class="sxs-lookup"><span data-stu-id="89cdc-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="89cdc-422">Sunucu uygulamalarına, bunu önlemek için ek önlemler alınmadığı sürece çapraz orijinerişilebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="89cdc-423">Çapraz orijin erişimini devre dışı kalarak, cors ara yazılımını boru hattına ekleyerek ve `DisableCorsAttribute` Blazor bitiş noktası meta verilerine ekleyerek veya [başlangıçlar SignalR arası kaynak paylaşımı için yapılandırarak](xref:signalr/security#cross-origin-resource-sharing)izin verilen kaynak kümesini sınırlandırarak CORS'i bitiş noktasında devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="89cdc-424">CORS etkinse, CORS yapılandırmasına bağlı olarak uygulamayı korumak için ek adımlar gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="89cdc-425">CORS genel olarak etkinse, çağrıdan Blazor `DisableCorsAttribute` `hub.MapBlazorHub()`sonra meta verileri uç nokta meta verilerine ekleyerek Sunucu hub'ı için CORS devre dışı edilebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="89cdc-426">Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="89cdc-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="89cdc-427">Tıklama kaçırma</span><span class="sxs-lookup"><span data-stu-id="89cdc-427">Click-jacking</span></span>

<span data-ttu-id="89cdc-428">Tıklama kaçırma, kullanıcıyı saldırı altındaki `<iframe>` sitede eylemler gerçekleştirmesi için kandırmak için bir siteyi farklı bir kaynaktan gelen bir sitenin içinde olarak görüntülemeyi içerir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="89cdc-429">Bir uygulamayı bir `<iframe>`, İçerik Güvenlik [İlkesi (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `X-Frame-Options` ve üstbilginin içinde işlemekten korumak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="89cdc-430">Daha fazla bilgi için [MDN web dokümanlarına bakın: X-Frame-Options.](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)</span><span class="sxs-lookup"><span data-stu-id="89cdc-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="89cdc-431">Açık yönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="89cdc-431">Open redirects</span></span>

<span data-ttu-id="89cdc-432">Sunucu Blazor uygulaması oturumu başladığında, sunucu oturumu başlatmanın bir parçası olarak gönderilen URL'lerin temel doğrulanmasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="89cdc-433">Çerçeve, devreyi kurmadan önce temel URL'nin geçerli URL'nin bir üst öğesi olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="89cdc-434">Çerçeve tarafından ek denetimler yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="89cdc-435">Bir kullanıcı istemcide bir bağlantı seçtiğinde, bağlantının URL'si sunucuya gönderilir ve bu da hangi eylemin gerçekleşip gerçekleşsüreceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="89cdc-436">Örneğin, uygulama istemci tarafında gezinme gerçekleştirebilir veya tarayıcıya yeni konuma gitmesini gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="89cdc-437">Bileşenler ayrıca, '' kullanımı yoluyla navigasyon `NavigationManager`isteklerini programlı olarak tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="89cdc-438">Bu tür senaryolarda, uygulama istemci tarafında gezinme gerçekleştirebilir veya tarayıcıya yeni konuma gitmesini gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="89cdc-439">Bileşenler şunları yapmalı:</span><span class="sxs-lookup"><span data-stu-id="89cdc-439">Components must:</span></span>

* <span data-ttu-id="89cdc-440">Gezinti çağrısı bağımsız değişkenlerinin bir parçası olarak kullanıcı girişi kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="89cdc-441">Hedefin uygulama tarafından izin verildiğinden emin olmak için bağımsız değişkenleri doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="89cdc-442">Aksi takdirde, kötü amaçlı bir kullanıcı tarayıcıyı saldırgan kontrolündeki bir siteye gitmeye zorlayabilir.</span><span class="sxs-lookup"><span data-stu-id="89cdc-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="89cdc-443">Bu senaryoda, saldırgan `NavigationManager.Navigate` yöntemin çağrılması bir parçası olarak bazı kullanıcı girişi kullanarak uygulama hileler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="89cdc-444">Bu öneri, uygulamanın bir parçası olarak bağlantıları işlerken de geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="89cdc-445">Mümkünse, göreli bağlantılar kullanın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-445">If possible, use relative links.</span></span>
* <span data-ttu-id="89cdc-446">Mutlak bağlantı hedeflerinin bir sayfaya dahil etmeden önce geçerli olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="89cdc-447">Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="89cdc-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="89cdc-448">Kimlik doğrulama ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="89cdc-448">Authentication and authorization</span></span>

<span data-ttu-id="89cdc-449">Kimlik doğrulama ve yetkilendirme ile <xref:security/blazor/index>ilgili kılavuz için bkz.</span><span class="sxs-lookup"><span data-stu-id="89cdc-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="89cdc-450">Güvenlik denetim listesi</span><span class="sxs-lookup"><span data-stu-id="89cdc-450">Security checklist</span></span>

<span data-ttu-id="89cdc-451">Aşağıdaki güvenlik konuları listesi kapsamlı değildir:</span><span class="sxs-lookup"><span data-stu-id="89cdc-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="89cdc-452">Olaylardan bağımsız değişkenleri doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-452">Validate arguments from events.</span></span>
* <span data-ttu-id="89cdc-453">JS interop aramalarından gelen girişleri ve sonuçları doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="89cdc-454">.NET to JS interop aramaları için kullanıcı girişini kullanmaktan (veya önceden doğrulamaktan) kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="89cdc-455">İstemcinin bağlanmamış miktarda bellek ayırmasını engelleyin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="89cdc-456">Bileşen içindeki veriler.</span><span class="sxs-lookup"><span data-stu-id="89cdc-456">Data within the component.</span></span>
  * <span data-ttu-id="89cdc-457">`DotNetObject`başvurular istemciye döndürülür.</span><span class="sxs-lookup"><span data-stu-id="89cdc-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="89cdc-458">Birden fazla sevkiyata karşı korunun.</span><span class="sxs-lookup"><span data-stu-id="89cdc-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="89cdc-459">Bileşen imha edildiğinde uzun süren işlemleri iptal edin.</span><span class="sxs-lookup"><span data-stu-id="89cdc-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="89cdc-460">Büyük miktarda veri üreten olaylardan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="89cdc-461">Url'ler `NavigationManager.Navigate` için kullanıcı girdisini, kaçınılmaz sayılmaması durumunda önce izin verilen bir dizi menşee karşı kullanıcı girişinin bir parçası olarak kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="89cdc-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="89cdc-462">Yetkilendirme kararlarını Kullanıcı G'ın durumuna göre değil, yalnızca bileşen durumundan alın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="89cdc-463">XSS saldırılarına karşı korumak için [İçerik Güvenlik Politikası 'nı (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="89cdc-464">Tıklama kaçırmaya karşı korumak için CSP ve [X-Frame Seçenekleri](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="89cdc-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="89cdc-465">CORS'i etkinleştirirken veya uygulamalar için Blazor CORS'i açıkça devre dışı kalırken CORS ayarlarının uygun olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="89cdc-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="89cdc-466">Uygulamanın sunucu tarafı sınırlarının kabul Blazor edilemez risk düzeyleri olmadan kabul edilebilir bir kullanıcı deneyimi sağladığından emin olmak için sınayın.</span><span class="sxs-lookup"><span data-stu-id="89cdc-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
