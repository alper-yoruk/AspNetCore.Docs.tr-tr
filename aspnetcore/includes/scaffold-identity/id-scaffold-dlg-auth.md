::: moniker range=">= aspnetcore-3.0"

Identity scaffolder öğesini çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, projeye sağ tıklayıp **Add** > **Yeni iskli öğe**Ekle >.
* **Yapı iskelesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik** > **Ekle**' yi seçin.
* **Kimlik Ekle** iletişim kutusunda istediğiniz seçenekleri belirleyin.
  * Düzen dosyanızın üzerine yazılmaması için, var olan düzen sayfanızı seçin. Var olan bir * \_ Layout. cshtml* dosyası **seçildiğinde, üzerine yazılmaz.** Örneğin:
    * `~/Pages/Shared/_Layout.cshtml`Mevcut Razor Pages altyapısına sahip Razor Pages veya Blazor Server projeleri için
    * `~/Views/Shared/_Layout.cshtml`Mevcut MVC altyapısına sahip MVC projeleri veya Blazor Server projeleri için
* Mevcut veri bağlamınızı kullanmak için, geçersiz kılmak üzere en az bir dosya seçin. Veri bağlamınızı eklemek için en az bir dosya seçmeniz gerekir.
  * Veri bağlamı sınıfınızı seçin.
  * **Add (Ekle)** seçeneğini belirleyin.
* Yeni bir kullanıcı bağlamı oluşturmak ve muhtemelen kimlik için özel bir Kullanıcı sınıfı oluşturmak için:
  * **+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin. Varsayılan değeri kabul edin veya bir sınıf belirtin (örneğin, `MyApplication.Data.ApplicationDbContext` ).
  * **Add (Ekle)** seçeneğini belirleyin.

Note: yeni bir kullanıcı bağlamı oluşturuyorsanız, geçersiz kılmak için bir dosya seçmeniz gerekmez.

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

Proje klasöründe, kimlik desteği ' ı istediğiniz seçeneklerle çalıştırın. Örneğin, kimliği varsayılan UI ve minimum dosya sayısı ile ayarlamak için aşağıdaki komutu çalıştırın. DB içeriğiniz için doğru tam adı kullanın:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır. PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini çift tırnak içine koyun. Örneğin:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Identity desteği bayrağını veya bayrağını belirtmeden çalıştırırsanız `--files` `--useDefaultUI` , tüm kullanılabilir kimlik Kullanıcı arabirimi sayfaları projenizde oluşturulur.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Identity scaffolder öğesini çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, projeye sağ tıklayıp **Add** > **Yeni iskli öğe**Ekle >.
* **Yapı iskelesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik** > **Ekle**' yi seçin.
* **Kimlik Ekle** iletişim kutusunda istediğiniz seçenekleri belirleyin.
  * Var olan düzen sayfanızı seçin veya Düzen dosyanızın üzerine yanlış biçimlendirme uygulanır. Var olan bir * \_ Layout. cshtml* dosyası **seçildiğinde, üzerine yazılmaz.** Örneğin:
    * `~/Pages/Shared/_Layout.cshtml`Razor Pages için
    * `~/Views/Shared/_Layout.cshtml`MVC projeleri için
* Mevcut veri bağlamınızı kullanmak için, geçersiz kılmak üzere en az bir dosya seçin. Veri bağlamınızı eklemek için en az bir dosya seçmeniz gerekir.
  * Veri bağlamı sınıfınızı seçin.
  * **Add (Ekle)** seçeneğini belirleyin.
* Yeni bir kullanıcı bağlamı oluşturmak ve muhtemelen kimlik için özel bir Kullanıcı sınıfı oluşturmak için:
  * **+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin. Varsayılan değeri kabul edin veya bir sınıf belirtin (örneğin, `MyApplication.Data.ApplicationDbContext` ).
  * **Add (Ekle)** seçeneğini belirleyin.

Note: yeni bir kullanıcı bağlamı oluşturuyorsanız, geçersiz kılmak için bir dosya seçmeniz gerekmez.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Proje dosyasına (*. csproj*) [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) öğesine bir paket başvurusu ekleyin. Proje dizininde aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Proje klasöründe, kimlik desteği ' ı istediğiniz seçeneklerle çalıştırın. Örneğin, kimliği varsayılan UI ve minimum dosya sayısı ile ayarlamak için aşağıdaki komutu çalıştırın. DB içeriğiniz için doğru tam adı kullanın:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır. PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini çift tırnak içine koyun. Örneğin:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Identity desteği bayrağını veya bayrağını belirtmeden çalıştırırsanız `--files` `--useDefaultUI` , tüm kullanılabilir kimlik Kullanıcı arabirimi sayfaları projenizde oluşturulur.

---

::: moniker-end
