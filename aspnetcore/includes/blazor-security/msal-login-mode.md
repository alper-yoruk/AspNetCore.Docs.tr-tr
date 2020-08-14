Çerçeve, açılır oturum açma modunu varsayılan olarak alır ve bir açılır pencere açılamadıklarında oturum açma moduna geri döner. ' İ özelliğini olarak ayarlayarak MSAL 'i yeniden yönlendirme modunu kullanacak şekilde yapılandırın `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Varsayılan ayar `popup` ve dize değeri büyük/küçük harfe duyarlı değildir.
