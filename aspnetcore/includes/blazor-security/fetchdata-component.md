`FetchData`Bileşen şunları gösterir:

* Erişim belirteci sağlayın.
* *Sunucu* uygulamasında korumalı bir kaynak API 'si çağırmak için erişim belirtecini kullanın.

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)Yönerge, kullanıcının bu bileşeni ziyaret etmek için yetkilendirilmiş olması gereken Blazor WebAssembly yetkilendirme sistemine işaret ediyor. Uygulamadaki özniteliğinin varlığı, `Client` SUNUCUDAKI API 'nin doğru kimlik bilgileri olmadan çağrılmasına engel olmaz. `Server`Ayrıca, uygulamanın `[Authorize]` doğru şekilde korunması için uygun uç noktalar üzerinde kullanılması gerekir.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>API 'yi çağırmak için isteğe eklenebilen bir erişim belirteci isteme işlemini gerçekleştirir. Belirteç önbelleğe alınmışsa veya hizmet Kullanıcı etkileşimi olmadan yeni bir erişim belirteci sağlayabiliyor ise, belirteç isteği başarılı olur. Aksi takdirde, belirteç isteği bir bildirimde yakalanan bir ile başarısız olur <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) .

İsteğe dahil edilecek gerçek belirteci almak için, uygulamanın çağırarak isteğin başarılı olduğunu denetlemesi gerekir [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) .

İstek başarılı olduysa, belirteç değişkeni erişim belirteciyle doldurulur. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>Belirtecin özelliği, istek üstbilgisine dahil etmek için sabit dizeyi gösterir `Authorization` .

Belirteç Kullanıcı etkileşimi olmadan sağlanamadığından istek başarısız olduysa, belirteç sonucu bir yeniden yönlendirme URL 'SI içerir. Bu URL 'ye gidildiğinde, Kullanıcı oturum açma sayfasına geçer ve başarılı bir kimlik doğrulamasından sonra geçerli sayfaya geri dönün.

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
