<span data-ttu-id="7b7c7-101">`FetchData` Bileşen şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="7b7c7-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="7b7c7-102">Erişim belirteci sağlayın.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-102">Provision an access token.</span></span>
* <span data-ttu-id="7b7c7-103">*Sunucu* uygulamasında korumalı bir kaynak API 'si çağırmak için erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="7b7c7-104">`@attribute [Authorize]` Yönerge, kullanıcının bu bileşeni ziyaret etmek için yetkilendirilmiş olması gereken Blazor WebAssembly yetkilendirme sistemine işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="7b7c7-105">*İstemci* uygulamasındaki özniteliğin varlığı, sunucudaki API 'nin doğru kimlik bilgileri olmadan çağrılmasına engel olmaz.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="7b7c7-106">*Sunucu* uygulamasının aynı zamanda uygun uç `[Authorize]` noktalar üzerinde doğru korunması için kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="7b7c7-107">`IAccessTokenProvider.RequestAccessToken();`API 'yi çağırmak için isteğe eklenebilen bir erişim belirteci isteme işlemini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-107">`IAccessTokenProvider.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="7b7c7-108">Belirteç önbelleğe alınmışsa veya hizmet Kullanıcı etkileşimi olmadan yeni bir erişim belirteci sağlayabiliyor ise, belirteç isteği başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="7b7c7-109">Aksi takdirde, belirteç isteği bir `AccessTokenNotAvailableException` `try-catch` bildirimde yakalanabilecek bir hata ile başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-109">Otherwise, the token request fails with an `AccessTokenNotAvailableException` error, which is caught in a `try-catch` statement.</span></span>

<span data-ttu-id="7b7c7-110">İsteğe dahil edilecek gerçek belirteci almak için, uygulamanın çağırarak `tokenResult.TryGetToken(out var token)`isteğin başarılı olduğunu denetlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="7b7c7-111">İstek başarılı olduysa, belirteç değişkeni erişim belirteciyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="7b7c7-112">Belirtecin `Value` özelliği, `Authorization` istek üstbilgisine dahil etmek için sabit dizeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="7b7c7-113">Belirteç Kullanıcı etkileşimi olmadan sağlanamadığından istek başarısız olduysa, belirteç sonucu bir yeniden yönlendirme URL 'SI içerir.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="7b7c7-114">Bu URL 'ye gidildiğinde, Kullanıcı oturum açma sayfasına geçer ve başarılı bir kimlik doğrulamasından sonra geçerli sayfaya geri dönün.</span><span class="sxs-lookup"><span data-stu-id="7b7c7-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
