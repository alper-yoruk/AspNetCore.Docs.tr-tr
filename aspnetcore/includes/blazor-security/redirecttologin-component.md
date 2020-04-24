`RedirectToLogin` Bileşen (*Shared/redirecttologin. Razor*):

* Yetkisiz kullanıcıların oturum açma sayfasına yeniden yönlendirildiğini yönetir.
* Kimlik doğrulaması başarılı olursa bu sayfaya döndürülmeleri için kullanıcının erişmeye çalışan geçerli URL 'YI korur.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
