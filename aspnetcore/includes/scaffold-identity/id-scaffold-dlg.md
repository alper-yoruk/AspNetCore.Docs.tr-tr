Identity scaffolder öğesini çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, projeye sağ tıklayıp **Add**  >  **Yeni iskli öğe**Ekle >.
* **Yeni yapı iskelesi öğesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik**  >  **Ekle**' yi seçin.
* **Kimlik Ekle** iletişim kutusunda istediğiniz seçenekleri belirleyin.
  * Var olan düzen sayfanızı seçin veya Düzen dosyanızın üzerine yanlış biçimlendirme uygulanır:
    * `~/Pages/Shared/_Layout.cshtml`Razor Pages için
    * `~/Views/Shared/_Layout.cshtml`MVC projeleri için
    * Blazor Server şablonundan oluşturulan Blazor Server uygulamaları, `blazorserver` Varsayılan olarak Razor Pages veya MVC için yapılandırılmamıştır. Düzen sayfası girişini boş bırakın.
  * **+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin. Varsayılan değeri kabul edin veya bir sınıf belirtin (örneğin, `MyApplication.Data.ApplicationDbContext` ).
* **Add (Ekle)** seçeneğini belirleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Proje dosyasına (*. csproj*) gerekli NuGet paket başvurularını ekleyin. Proje dizininde aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Identity desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Proje klasöründe, kimlik desteği ' ı istediğiniz seçeneklerle çalıştırın. Örneğin, kimliği varsayılan UI ve minimum dosya sayısı ile ayarlamak için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
