`App`Bileşen (*app. Razor*) `App` Blazor Server uygulamalarında bulunan bileşene benzerdir:

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>Bileşen, <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> uygulamanın geri kalanına yönelik olarak ortaya çıkarmayı yönetir.
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>Bileşen, geçerli kullanıcının belirli bir sayfaya erişmek için yetkilendirilmiş olduğundan emin olur veya başka bir şekilde bileşeni işler `RedirectToLogin` .
* `RedirectToLogin`Bileşen, yetkisiz kullanıcıların oturum açma sayfasına yeniden yönlendirildiğini yönetir.

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
