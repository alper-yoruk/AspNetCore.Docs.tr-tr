# <a name="aspnet-core-distributed-cache-sample"></a>Dağıtılmış önbellek örneğini ASP.NET Core

Bu örnek, dağıtılmış bir önbelleğin kullanımını gösterir. Bu örnek, ASP.NET Core konu başlığında [Dağıtılmış önbellek Ile çalışma](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) bölümünde açıklanan senaryoyu gösterir.

Üretim ortamında, örnek uygulama dağıtılmış bir SQL Server önbelleği kullanacak şekilde yapılandırılmıştır. Uygulamayı dağıtılmış Redsıs önbelleği kullanmak üzere yeniden yapılandırmak için, *Startup.cs* dosyasının en üstündeki Önişlemci yönergesini redsıs () kullanmak üzere değiştirin `#define Redis // SQLServer` . Daha fazla bilgi için bkz. [örnek kodda Önişlemci yönergeleri](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
