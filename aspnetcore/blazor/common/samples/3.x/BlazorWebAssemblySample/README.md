# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly örnek uygulaması

Bu örnek, Blazor belgelerinde açıklanan Blazor senaryolarının kullanımını gösterir.

## <a name="call-web-api-example"></a>Web API 'SI çağırma örneği

Web API örneği, varsayılan olarak Blazor örnek uygulaması olarak aynı HTTPS bağlantı noktasını (5001) kullanan <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">ASP.NET Core konusuna sahip Web API 'Si oluşturma</a> için örnek uygulamayı temel alan çalışan BIR Web API 'si gerektirir. Aynı makinede her iki uygulamayı da kullanmak için, Web API 'sinin bağlantı noktasını değiştirin (örneğin, 10000 numaralı bağlantı noktasını kullanın). Örnek uygulama, ' de Web API 'sine istek yapar `https://localhost:10000/api/TodoItems` . Farklı bir Web API adresi kullanılırsa, `ServiceEndpoint` Razor bileşeni bloğundaki sabit değeri güncelleştirin `@code` .</p>

Örnek uygulama, veya Web API 'sinden bir <a href="https://docs.microsoft.com/aspnet/core/security/cors">çıkış noktaları arası kaynak paylaşımı (CORS)</a> isteği oluşturur `http://localhost:5000` `https://localhost:5001` . Kimlik bilgilerine (yetkilendirme tanımlama bilgilerine/üstbilgilere) izin verilir. Aşağıdaki CORS ara yazılım yapılandırmasını Web API 'sinin `Startup.Configure` yöntemine ekleyin:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor uygulaması için gerektiği gibi etki alanlarını ve bağlantı noktalarını ayarlayın `WithOrigins` .

Web API 'SI, istemci kodundan yetkilendirme tanımlama bilgilerine/üstbilgilere ve isteklere izin vermek üzere CORS için yapılandırılmıştır, ancak öğretici tarafından oluşturulan Web API 'SI istekleri yetkilendirmez. Uygulama Kılavuzu için bkz. <a href="https://docs.microsoft.com/aspnet/core/security/">güvenlik ve kimlik makaleleri ASP.NET Core</a> .
