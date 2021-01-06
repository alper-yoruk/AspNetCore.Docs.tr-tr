# <a name="aspnet-core-health-check-sample"></a>ASP.NET Core sistem durumu denetimi örneği

Bu örnek, sistem durumu denetimi ara yazılımı ve hizmetlerinin kullanımını gösterir. Bu örnek, [ASP.NET Core konusunun durum denetimleri](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) bölümünde açıklanan senaryoyu gösterir.

Konu başlığı altında açıklanan bir senaryoya örnek uygulamayı çalıştırmak için, bir komut kabuğunda projenin klasöründen [DotNet Run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) komutunu kullanın. Araştırırken senaryo için bir anahtar geçirin. Uygulamasına `basic` bir anahtar sağlanmıyorsa Uygulamanın yapılandırması varsayılan olarak olur `dotnet run` .

| Senaryo                                               | Örnek uygulama komutu               | Açıklama |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Temel sistem durumu araştırması (varsayılan)                           | `dotnet run --scenario basic`    | Uygulamanın HTTP isteklerini işleyebileceğinizi onaylar. |
| Veritabanı araştırması                                         | `dotnet run --scenario db`       | SQL Server veritabanı bağlantısını denetler. Yönergeler için konusunun [veritabanı araştırması](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) bölümüne bakın. |
| Hazırlık/lizlilik araştırmaları                              | `dotnet run --scenario liveness` | Canlı uygulama durumu (*Lisine**) için* denetim gerçekleştirir. |
| Ölçüm tabanlı araştırma (bellek)/<br>Özel yanıt yazıcısı | `dotnet run --scenario writer`   | Bellek kullanımına karşı denetler ve sistem durumu uç noktası işaretlendiğinde özel JSON yazar. |
| Bağlantı noktasına göre filtrele                                         | `dotnet run --scenario port`     | Durum denetimlerini belirli bir bağlantı noktasına filtreler. Yönergeler için konusunun [bağlantı noktasına göre filtrele](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) bölümüne bakın. |

Veritabanı araştırması ve bağlantı noktası filtresi senaryoları için ek yapılandırma gerekir. Ayrıntılar için [sistem durumu denetimleri](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) konusuna bakın.
