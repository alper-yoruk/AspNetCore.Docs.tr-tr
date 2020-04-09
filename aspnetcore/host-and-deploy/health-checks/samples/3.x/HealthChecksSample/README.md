# <a name="aspnet-core-health-check-sample"></a>ASP.NET Çekirdek Sağlık Kontrol Örneği

Bu örnek, Sağlık Denetimi Middleware ve hizmetlerinin kullanımını göstermektedir. Bu örnek, Temel ASP.NET konusu sağlık [denetimlerinde](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) açıklanan senaryoyu gösterir.

Konuyla açıklanan bir senaryo için örnek uygulamayı çalıştırmak için, proje klasöründeki [dotnet çalıştır](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) komutunu komut kabuğunda kullanın. Keşfetmekte olduğunuz senaryo için bir geçiş geçirin. Bir anahtar sağlanmadığında uygulama `basic` yapılandırmaya `dotnet run`varsayılan olarak

| Senaryo                                               | Örnek uygulama komutu               | Açıklama |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Temel sistem durumu sondası (varsayılan)                           | `dotnet run --scenario basic`    | Uygulamanın HTTP isteklerini işleyebileceğini onaylar. |
| Veritabanı sondası                                         | `dotnet run --scenario db`       | SQL Server veritabanı bağlantısını denetler. Talimatlar için konunun [Veritabanı sondası](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) bölümüne bakın. |
| Hazırlık/canlılık probları                              | `dotnet run --scenario liveness` | Canlı bir uygulama durumu *(canlılık)* için kontroller gerçekleştirir ve uygulamaya karşı canlı olmaya hazırlanır *(hazır olma).* |
| Metrik tabanlı sonda (bellek)/<br>özel yanıt yazar | `dotnet run --scenario writer`   | Bellek kullanımına karşı denetler ve sistem durumu bitiş noktası kontrol edildiğinde özel JSON'u yazar. |
| Bağlantı noktasına göre filtrele                                         | `dotnet run --scenario port`     | Sistem durumu denetimlerini belirli bir bağlantı noktasına filtreler. Talimatlar için konunun bağlantı noktası bölümüne [göre Filtre'ye](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) bakın. |

Veritabanı sondası ve bağlantı noktası filtresi senaryoları ek yapılandırma gerektirir. Ayrıntılar için [Sağlık denetimleri](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) konusuna bakın.
