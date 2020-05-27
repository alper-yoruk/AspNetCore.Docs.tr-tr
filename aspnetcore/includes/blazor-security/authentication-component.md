<span data-ttu-id="97574-101">Bileşen tarafından üretilen sayfa `Authentication` (*Sayfalar/Authentication. Razor*), farklı kimlik doğrulama aşamalarını işlemek için gereken yolları tanımlar.</span><span class="sxs-lookup"><span data-stu-id="97574-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="97574-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Bileşen:</span><span class="sxs-lookup"><span data-stu-id="97574-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="97574-103">, [Microsoft. AspNetCore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="97574-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="97574-104">Her kimlik doğrulama aşamasında uygun eylemlerin gerçekleştirilmesini yönetir.</span><span class="sxs-lookup"><span data-stu-id="97574-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
