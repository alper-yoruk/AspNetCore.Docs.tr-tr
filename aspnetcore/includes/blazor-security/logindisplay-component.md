<span data-ttu-id="47b5e-101">Bileşen (*paylaşılan/LoginDisplay.* Razor) `MainLayout` , bileşende (*paylaşılan/mainlayout. Razor*) işlenir ve aşağıdaki davranışları yönetir: `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="47b5e-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="47b5e-102">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="47b5e-102">For authenticated users:</span></span>
  * <span data-ttu-id="47b5e-103">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="47b5e-103">Displays the current username.</span></span>
  * <span data-ttu-id="47b5e-104">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="47b5e-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="47b5e-105">Anonim kullanıcılar için oturum açma seçeneğini sunar.</span><span class="sxs-lookup"><span data-stu-id="47b5e-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
