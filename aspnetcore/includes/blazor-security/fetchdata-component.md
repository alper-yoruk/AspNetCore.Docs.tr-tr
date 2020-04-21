Bileşen `FetchData` nasıl yapılacağını gösterir:

* Erişim jetonu sağlama.
* *Sunucu* uygulamasında korumalı kaynak API'sini aramak için erişim belirteci'ni kullanın.

Yönerge, `@attribute [Authorize]` Blazor WebAssembly yetkilendirme sistemine kullanıcının bu bileşeni ziyaret etmek için yetkilendirilmesi gerektiğini belirtir. *Müşteri* uygulamasında özniteliğin bulunması, sunucudaki API'nin uygun kimlik bilgileri olmadan çağrılmasını engellemez. *Sunucu* uygulaması da `[Authorize]` doğru korumak için uygun uç noktalarda kullanmanız gerekir.

`AuthenticationService.RequestAccessToken();`API'yi arama isteğine eklenebilecek bir erişim belirteci istemeye özen diner. Belirteç önbelleğe alınmışsa veya hizmet kullanıcı etkileşimi olmadan yeni bir erişim belirteci sağlanabilirse, belirteç isteği başarılı olur. Aksi takdirde, belirteç isteği başarısız olur.

İstekte yer alacak gerçek belirteci elde etmek için, uygulamanın isteğin `tokenResult.TryGetToken(out var token)`başarılı olup olmadığını kontrol etmesi gerekir. 

İstek başarılı olduysa, belirteç değişkeni erişim belirteciyle doldurulur. Belirteç `Value` özelliği, istek üstbilgisine dahil edilmesi `Authorization` gereken gerçek dizeyi ortaya çıkarır.

Belirteç kullanıcı etkileşimi olmadan sağlanamadığı için istek başarısız olduysa, belirteç sonucu yeniden yönlendirme URL'si içerir. Bu URL'de gezinmek, kullanıcıyı oturum açma sayfasına ve başarılı bir kimlik doğrulamadan sonra geçerli sayfaya geri götürür.

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
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

Daha fazla bilgi için, [kimlik doğrulama işleminden önce uygulama durumunu kaydet'e](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)bakın.
