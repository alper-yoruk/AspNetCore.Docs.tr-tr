<span data-ttu-id="87ebb-101">Azure portal, AAD kiracısının [doğrulanmış veya doğrulanmamış bir yayımcı etki alanına](/azure/active-directory/develop/howto-configure-publisher-domain)sahip olup olmadığına bağlı olarak AŞAĞıDAKI uygulama kimliği URI biçimlerinden birini sağlar:</span><span class="sxs-lookup"><span data-stu-id="87ebb-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="87ebb-102">İstemci uygulamasında kapsam URI 'sini oluşturmak için yukarıdaki iki uygulama KIMLIĞI URI 'sinin ikinci değeri kullanıldığında ve yetkilendirme başarılı olmazsa, kapsam URI 'sini düzen ve konak olmadan sağlamayı deneyin:</span><span class="sxs-lookup"><span data-stu-id="87ebb-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="87ebb-103">Örnek:</span><span class="sxs-lookup"><span data-stu-id="87ebb-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
