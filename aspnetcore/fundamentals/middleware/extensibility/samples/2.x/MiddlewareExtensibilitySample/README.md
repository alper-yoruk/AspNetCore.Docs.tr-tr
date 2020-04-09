# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Çekirdek Ara Yazılım Ekstensibilite Örneği

Bu örnek, [ASP.NET Core'da Fabrika tabanlı ara yazılım etkinleştirmesinde](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)açıklanan senaryoları gösterir.

Örnek uygulama, aşağıdakiler tarafından etkinleştirilen ara yazılımları gösterir:

* Kongre. Geleneksel ara yazılım etkinleştirme hakkında daha fazla bilgi için [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) konusuna bakın.
* Bir [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) uygulaması. Varsayılan [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) sınıfı ara yazılımı etkinleştirir.

Ara yazılım uygulamaları aynı şekilde çalışır ve sorgu dize parametresi ( tarafından`key`sağlanan değeri kaydeder. Ara yazılımlar, sorgu dize değerini bellek içi bir veritabanına kaydetmek için enjekte edilmiş bir veritabanı bağlamı (kapsamlı bir hizmet) kullanır.
