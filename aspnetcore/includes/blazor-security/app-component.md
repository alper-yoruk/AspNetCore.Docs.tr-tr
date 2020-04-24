Bileşen (*app. Razor*) Blazor Server uygulamalarında bulunan `App` bileşene benzerdir: `App`

* `CascadingAuthenticationState` Bileşen, `AuthenticationState` uygulamanın geri kalanına yönelik olarak ortaya çıkarmayı yönetir.
* `AuthorizeRouteView` Bileşen, geçerli kullanıcının belirli bir sayfaya erişmek için yetkilendirilmiş olduğundan emin olur veya başka bir şekilde `RedirectToLogin` bileşeni işler.
* `RedirectToLogin` Bileşen, yetkisiz kullanıcıların oturum açma sayfasına yeniden yönlendirildiğini yönetir.

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
