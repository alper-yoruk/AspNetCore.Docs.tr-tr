## <a name="multiple-authentication-providers"></a>Birden çok kimlik doğrulama sağlayıcısı

Uygulama birden fazla sağlayıcı gerektirdiğinde, sağlayıcı uzantısı [yöntemlerini AddAuthentication'ın](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication)arkasına zincirle:

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
