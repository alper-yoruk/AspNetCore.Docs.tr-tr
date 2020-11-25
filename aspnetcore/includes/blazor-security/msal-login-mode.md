<span data-ttu-id="f75d1-101">Çerçeve, açılır oturum açma modunu varsayılan olarak alır ve bir açılır pencere açılamadıklarında oturum açma moduna geri döner.</span><span class="sxs-lookup"><span data-stu-id="f75d1-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="f75d1-102">' İ özelliğini olarak ayarlayarak MSAL 'i yeniden yönlendirme modunu kullanacak şekilde yapılandırın `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` :</span><span class="sxs-lookup"><span data-stu-id="f75d1-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="f75d1-103">Varsayılan ayar `popup` ve dize değeri büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="f75d1-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
