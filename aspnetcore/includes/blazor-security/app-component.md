<span data-ttu-id="fd159-101">Bileşen (*app. Razor*) Blazor Server uygulamalarında bulunan `App` bileşene benzerdir: `App`</span><span class="sxs-lookup"><span data-stu-id="fd159-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="fd159-102">`CascadingAuthenticationState` Bileşen, `AuthenticationState` uygulamanın geri kalanına yönelik olarak ortaya çıkarmayı yönetir.</span><span class="sxs-lookup"><span data-stu-id="fd159-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="fd159-103">`AuthorizeRouteView` Bileşen, geçerli kullanıcının belirli bir sayfaya erişmek için yetkilendirilmiş olduğundan emin olur veya başka bir şekilde `RedirectToLogin` bileşeni işler.</span><span class="sxs-lookup"><span data-stu-id="fd159-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="fd159-104">`RedirectToLogin` Bileşen, yetkisiz kullanıcıların oturum açma sayfasına yeniden yönlendirildiğini yönetir.</span><span class="sxs-lookup"><span data-stu-id="fd159-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
