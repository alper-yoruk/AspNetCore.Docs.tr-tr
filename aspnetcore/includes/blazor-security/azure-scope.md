> [!NOTE]
> <span data-ttu-id="34f78-101">Azure portal, uygulamanın kapsam URI 'sini sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında işlenmeyen bir özel durum oluşturursa, düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="34f78-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="34f78-102">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="34f78-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="34f78-103">Kapsam URI 'sini, şema ve ana bilgisayar olmadan sağlamayı deneyin:</span><span class="sxs-lookup"><span data-stu-id="34f78-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
