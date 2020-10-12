<span data-ttu-id="88bd6-101">AAD ile kayıtlı bir sunucu API 'siyle birlikte çalışırken ve uygulamanın AAD kaydı, [doğrulanmamış bir yayımcı etki alanını](/azure/active-directory/develop/howto-configure-publisher-domain)kullanan bir kiracıda yer aldığından, sunucu API UYGULAMANıZıN uygulama kimliği URI 'si, `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ancak bunun yerine biçimindedir `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` .</span><span class="sxs-lookup"><span data-stu-id="88bd6-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="88bd6-102">Bu durumda, uygulamanın () varsayılan erişim belirteci kapsamı aşağıdakine `Program.Main` `Program.cs` *`Client`* benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="88bd6-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="88bd6-103">Sunucu API uygulamasını eşleşen bir hedef kitle için yapılandırmak için, `Audience` *`Server`* API uygulama ayarları dosyasında ( `appsettings.json` ), Azure Portal tarafından belirtilen uygulama kitleleriyle eşleşecek şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="88bd6-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="88bd6-104">Önceki yapılandırmada, `Audience` değerin sonu varsayılan kapsamı **not** içermez `/{DEFAULT SCOPE}` .</span><span class="sxs-lookup"><span data-stu-id="88bd6-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="88bd6-105">Örnek:</span><span class="sxs-lookup"><span data-stu-id="88bd6-105">Example:</span></span>

<span data-ttu-id="88bd6-106">`Program.Main` ( `Program.cs` ) *`Client`* uygulamanın:</span><span class="sxs-lookup"><span data-stu-id="88bd6-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="88bd6-107">*`Server`* API App settings dosyasını ( `appsettings.json` ) eşleşen bir seyirci () ile yapılandırın `Audience` :</span><span class="sxs-lookup"><span data-stu-id="88bd6-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="88bd6-108">Yukarıdaki örnekte, `Audience` değerin sonu varsayılan kapsamı **not** içermez `/API.Access` .</span><span class="sxs-lookup"><span data-stu-id="88bd6-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
