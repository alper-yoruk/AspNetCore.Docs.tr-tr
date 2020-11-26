<span data-ttu-id="69ffc-101">`Authentication`Bileşen () tarafından üretilen sayfa, `Pages/Authentication.razor` farklı kimlik doğrulama aşamalarını işlemek için gerekli olan yolları tanımlar.</span><span class="sxs-lookup"><span data-stu-id="69ffc-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="69ffc-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Bileşen:</span><span class="sxs-lookup"><span data-stu-id="69ffc-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="69ffc-103">, Paket tarafından sağlanır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="69ffc-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="69ffc-104">Her kimlik doğrulama aşamasında uygun eylemlerin gerçekleştirilmesini yönetir.</span><span class="sxs-lookup"><span data-stu-id="69ffc-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
