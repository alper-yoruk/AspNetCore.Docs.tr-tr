## <a name="multiple-authentication-providers"></a><span data-ttu-id="50dde-101">Birden çok kimlik doğrulama sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="50dde-101">Multiple authentication providers</span></span>

<span data-ttu-id="50dde-102">Uygulama birden fazla sağlayıcı gerektirdiğinde, sağlayıcı uzantısı [yöntemlerini AddAuthentication'ın](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication)arkasına zincirle:</span><span class="sxs-lookup"><span data-stu-id="50dde-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
