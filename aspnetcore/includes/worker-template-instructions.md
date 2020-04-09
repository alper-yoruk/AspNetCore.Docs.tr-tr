# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.
1. **İşçi Hizmeti'ni**seçin. **Sonraki'ni**seçin.
1. **Proje adı** alanında bir proje adı sağlayın veya varsayılan proje adını kabul edin. **Oluştur'u**seçin.
1. Yeni **Bir İşçi Hizmeti Oluştur** iletişim kutusunda **Oluştur'u**seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Yeni bir proje oluşturma.
1. Kenar çubuğunda **.NET Core** altında **Uygulama'yı** seçin.
1. **ASP.NET Çekirdek**altında **İşçi'yi** seçin. **Sonraki'ni**seçin.
1. **Hedef Çerçeve**için **.NET Core 3.0** veya sonraki leri seçin. **Sonraki'ni**seçin.
1. **Proje Adı** alanında bir ad sağlayın. **Oluştur'u**seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Bir komut kabuğundan`worker` [dotnet yeni](/dotnet/core/tools/dotnet-new) komutu ile İşçi Hizmeti ( ) şablonunu kullanın. Aşağıdaki örnekte, Bir İşçi Hizmeti `ContosoWorker`uygulaması adlı oluşturulur. Komut yürütüldüğünde `ContosoWorker` uygulama için bir klasör otomatik olarak oluşturulur.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
