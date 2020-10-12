Azure portal, AAD kiracısının [doğrulanmış veya doğrulanmamış bir yayımcı etki alanına](/azure/active-directory/develop/howto-configure-publisher-domain)sahip olup olmadığına bağlı olarak AŞAĞıDAKI uygulama kimliği URI biçimlerinden birini sağlar:

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

İstemci uygulamasında kapsam URI 'sini oluşturmak için yukarıdaki iki uygulama KIMLIĞI URI 'sinin ikinci değeri kullanıldığında ve yetkilendirme başarılı olmazsa, kapsam URI 'sini düzen ve konak olmadan sağlamayı deneyin:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Örnek:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
