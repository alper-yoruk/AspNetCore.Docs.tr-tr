# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core ara yazılım genişletilebilirlik örneği

Bu örnek, [ASP.NET Core ' de fabrika tabanlı ara yazılım etkinleştirme](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)bölümünde açıklanan senaryoları gösterir.

Örnek uygulama, tarafından etkinleştirilen ara yazılımı gösterir:

* Kuralıyla. Geleneksel ara yazılım etkinleştirmesi hakkında daha fazla bilgi için bkz. [Ara yazılım](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) konusu.
* [Iıddliware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) uygulamasının. Varsayılan [ımıddliwarefactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) sınıfı, ara yazılımı etkinleştirir.

Ara yazılım uygulamaları aynı şekilde çalışır ve sorgu dizesi parametresi () tarafından belirtilen değeri kaydeder `key` . Middlewares, sorgu dizesi değerini bellek içi bir veritabanına kaydetmek için eklenen bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.
