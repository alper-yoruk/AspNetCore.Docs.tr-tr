AAD ile kayıtlı bir sunucu API 'siyle birlikte çalışırken ve uygulamanın AAD kaydı, [doğrulanmamış bir yayımcı etki alanını](/azure/active-directory/develop/howto-configure-publisher-domain)kullanan bir kiracıda yer aldığından, sunucu API UYGULAMANıZıN uygulama kimliği URI 'si, `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ancak bunun yerine biçimindedir `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` . Bu durumda, uygulamanın () varsayılan erişim belirteci kapsamı aşağıdakine `Program.Main` `Program.cs` *`Client`* benzer şekilde görünür:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Sunucu API uygulamasını eşleşen bir hedef kitle için yapılandırmak için, `Audience` *`Server`* API uygulama ayarları dosyasında ( `appsettings.json` ), Azure Portal tarafından belirtilen uygulama kitleleriyle eşleşecek şekilde ayarlayın:

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

Önceki yapılandırmada, `Audience` değerin sonu varsayılan kapsamı  içermez `/{DEFAULT SCOPE}` .

Örnek:

`Program.Main` ( `Program.cs` ) *`Client`* uygulamanın:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

*`Server`* API App settings dosyasını ( `appsettings.json` ) eşleşen bir seyirci () ile yapılandırın `Audience` :

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

Yukarıdaki örnekte, `Audience` değerin sonu varsayılan kapsamı  içermez `/API.Access` .
