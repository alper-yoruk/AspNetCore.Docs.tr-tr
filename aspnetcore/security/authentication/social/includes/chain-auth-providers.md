## <a name="multiple-authentication-providers"></a><span data-ttu-id="bdf84-101">Birden çok kimlik doğrulama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="bdf84-101">Multiple authentication providers</span></span>

<span data-ttu-id="bdf84-102">Uygulama birden çok sağlayıcı gerektirdiğinde, sağlayıcı uzantısı yöntemlerini [Addaduthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication)arkasında zincirle:</span><span class="sxs-lookup"><span data-stu-id="bdf84-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
