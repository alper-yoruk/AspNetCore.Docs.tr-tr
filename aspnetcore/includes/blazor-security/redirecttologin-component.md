<span data-ttu-id="0b0ff-101">`RedirectToLogin` Bileşen (*Shared/redirecttologin. Razor*):</span><span class="sxs-lookup"><span data-stu-id="0b0ff-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="0b0ff-102">Yetkisiz kullanıcıların oturum açma sayfasına yeniden yönlendirildiğini yönetir.</span><span class="sxs-lookup"><span data-stu-id="0b0ff-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="0b0ff-103">Kimlik doğrulaması başarılı olursa bu sayfaya döndürülmeleri için kullanıcının erişmeye çalışan geçerli URL 'YI korur.</span><span class="sxs-lookup"><span data-stu-id="0b0ff-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri));
    }
}
```
