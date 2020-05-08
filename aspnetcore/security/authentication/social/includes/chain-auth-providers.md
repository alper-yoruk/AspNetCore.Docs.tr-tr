## <a name="multiple-authentication-providers"></a>Birden çok kimlik doğrulama sağlayıcısı

Uygulama birden çok sağlayıcı gerektirdiğinde, sağlayıcı uzantısı yöntemlerini [Addaduthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication)arkasında zincirle:

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
