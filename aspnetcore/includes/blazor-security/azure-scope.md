> [!NOTE]
> Azure portal, uygulamanın kapsam URI 'sini sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında işlenmeyen bir özel durum oluşturursa, düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin. Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Kapsam URI 'sini, şema ve ana bilgisayar olmadan sağlamayı deneyin:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
