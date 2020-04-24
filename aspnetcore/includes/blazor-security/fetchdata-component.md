`FetchData` Bileşen şunları gösterir:

* Erişim belirteci sağlayın.
* *Sunucu* uygulamasında korumalı bir kaynak API 'si çağırmak için erişim belirtecini kullanın.

`@attribute [Authorize]` Yönerge, kullanıcının bu bileşeni ziyaret etmek için yetkilendirilmiş olması gereken Blazor WebAssembly yetkilendirme sistemine işaret ediyor. *İstemci* uygulamasındaki özniteliğin varlığı, sunucudaki API 'nin doğru kimlik bilgileri olmadan çağrılmasına engel olmaz. *Sunucu* uygulamasının aynı zamanda uygun uç `[Authorize]` noktalar üzerinde doğru korunması için kullanması gerekir.

`AuthenticationService.RequestAccessToken();`API 'yi çağırmak için isteğe eklenebilen bir erişim belirteci isteme işlemini gerçekleştirir. Belirteç önbelleğe alınmışsa veya hizmet Kullanıcı etkileşimi olmadan yeni bir erişim belirteci sağlayabiliyor ise, belirteç isteği başarılı olur. Aksi takdirde, belirteç isteği başarısız olur.

İsteğe dahil edilecek gerçek belirteci almak için, uygulamanın çağırarak `tokenResult.TryGetToken(out var token)`isteğin başarılı olduğunu denetlemesi gerekir. 

İstek başarılı olduysa, belirteç değişkeni erişim belirteciyle doldurulur. Belirtecin `Value` özelliği, `Authorization` istek üstbilgisine dahil etmek için sabit dizeyi gösterir.

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

Daha fazla bilgi için bkz. [bir kimlik doğrulama işleminden önce uygulama durumunu kaydetme](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
