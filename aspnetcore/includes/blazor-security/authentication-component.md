Bileşen tarafından üretilen sayfa `Authentication` (*Sayfalar/Authentication. Razor*), farklı kimlik doğrulama aşamalarını işlemek için gereken yolları tanımlar.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Bileşen:

* , [Microsoft. AspNetCore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi tarafından sağlanır.
* Her kimlik doğrulama aşamasında uygun eylemlerin gerçekleştirilmesini yönetir.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
