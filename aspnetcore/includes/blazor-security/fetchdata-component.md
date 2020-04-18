<span data-ttu-id="f2f64-101">Bileşen `FetchData` nasıl yapılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f2f64-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="f2f64-102">Erişim jetonu sağlama.</span><span class="sxs-lookup"><span data-stu-id="f2f64-102">Provision an access token.</span></span>
* <span data-ttu-id="f2f64-103">*Sunucu* uygulamasında korumalı kaynak API'sini aramak için erişim belirteci'ni kullanın.</span><span class="sxs-lookup"><span data-stu-id="f2f64-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="f2f64-104">Yönerge, `@attribute [Authorize]` Blazor WebAssembly yetkilendirme sistemine kullanıcının bu bileşeni ziyaret etmek için yetkilendirilmesi gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f2f64-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="f2f64-105">*Müşteri* uygulamasında özniteliğin bulunması, sunucudaki API'nin uygun kimlik bilgileri olmadan çağrılmasını engellemez.</span><span class="sxs-lookup"><span data-stu-id="f2f64-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="f2f64-106">*Sunucu* uygulaması da `[Authorize]` doğru korumak için uygun uç noktalarda kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="f2f64-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="f2f64-107">`AuthenticationService.RequestAccessToken();`API'yi arama isteğine eklenebilecek bir erişim belirteci istemeye özen diner.</span><span class="sxs-lookup"><span data-stu-id="f2f64-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="f2f64-108">Belirteç önbelleğe alınmışsa veya hizmet kullanıcı etkileşimi olmadan yeni bir erişim belirteci sağlanabilirse, belirteç isteği başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="f2f64-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="f2f64-109">Aksi takdirde, belirteç isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f2f64-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="f2f64-110">İstekte yer alacak gerçek belirteci elde etmek için, uygulamanın isteğin `tokenResult.TryGetToken(out var token)`başarılı olup olmadığını kontrol etmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f2f64-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="f2f64-111">İstek başarılı olduysa, belirteç değişkeni erişim belirteciyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f2f64-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="f2f64-112">Belirteç `Value` özelliği, istek üstbilgisine dahil edilmesi `Authorization` gereken gerçek dizeyi ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="f2f64-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="f2f64-113">Belirteç kullanıcı etkileşimi olmadan sağlanamadığı için istek başarısız olduysa, belirteç sonucu yeniden yönlendirme URL'si içerir.</span><span class="sxs-lookup"><span data-stu-id="f2f64-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="f2f64-114">Bu URL'de gezinmek, kullanıcıyı oturum açma sayfasına ve başarılı bir kimlik doğrulamadan sonra geçerli sayfaya geri götürür.</span><span class="sxs-lookup"><span data-stu-id="f2f64-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="f2f64-115">Daha fazla bilgi için, [kimlik doğrulama işleminden önce uygulama durumunu kaydet'e](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)bakın.</span><span class="sxs-lookup"><span data-stu-id="f2f64-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
