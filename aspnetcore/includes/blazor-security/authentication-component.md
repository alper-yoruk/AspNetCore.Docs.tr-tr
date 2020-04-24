<span data-ttu-id="0b9cf-101">`Authentication` Bileşen tarafından üretilen sayfa (*Sayfalar/Authentication. Razor*), farklı kimlik doğrulama aşamalarını işlemek için gereken yolları tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0b9cf-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="0b9cf-102">`RemoteAuthenticatorView` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="0b9cf-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="0b9cf-103">, [Microsoft. AspNetCore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="0b9cf-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="0b9cf-104">Her kimlik doğrulama aşamasında uygun eylemlerin gerçekleştirilmesini yönetir.</span><span class="sxs-lookup"><span data-stu-id="0b9cf-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
