# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly Örnek Uygulaması

Bu örnek, Blazor belgelerinde açıklanan Blazor senaryolarının kullanımını göstermektedir.

## <a name="call-web-api-example"></a>Web API örneğini arayın

Web API örneği, varsayılan olarak Blazor örnek uygulamasıyla aynı HTTPS bağlantı noktasını (5001) kullanan <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">ASP.NET Temel konuiçeren bir web API'si oluşturma</a> için örnek uygulamaya dayalı çalışan bir web API'si gerektirir. Her iki uygulamayı da aynı makinede aynı anda kullanmak için web API'sının bağlantı noktasını değiştirin (örneğin, 10000 bağlantı noktasını kullanın). Örnek uygulama, web API'sine `https://localhost:10000/api/TodoItems`. Farklı bir web API adresi kullanılırsa, Razor `ServiceEndpoint` bileşeninin `@code` bloğundaki sabit değeri güncelleştirin.</p>

Örnek uygulama, web API'sinden veya `https://localhost:5001` web `http://localhost:5000` API'sinden <a href="https://docs.microsoft.com/aspnet/core/security/cors">bir çapraz kaynak paylaşımı (CORS)</a> isteğinde bulunarak. Kimlik bilgilerine (yetkilendirme çerezleri/üstbilgileri) izin verilir. Web API `Startup.Configure` yöntemine aşağıdaki CORS ara yazılım yapılandırmasını ekleyin:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Blazor uygulaması için `WithOrigins` gerektiğinde etki alanlarını ve bağlantı noktalarını ayarlayın.

Web API' si CORS için yetkilendirme tanımlama bilgilerine/üstbilgilerine ve istemci kodundan gelen isteklere izin vermek üzere yapılandırılmıştır, ancak öğretici tarafından oluşturulan web API'si aslında istekleri yetkilendirmez. Uygulama kılavuzu için <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Temel Güvenlik ve Kimlik makalelerini</a> görün.
