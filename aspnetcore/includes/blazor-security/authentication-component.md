`Authentication`Bileşen () tarafından üretilen sayfa, `Pages/Authentication.razor` farklı kimlik doğrulama aşamalarını işlemek için gerekli olan yolları tanımlar.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Bileşen:

* , Paket tarafından sağlanır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
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
