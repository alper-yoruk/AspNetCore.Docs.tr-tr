---
title: ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama
author: rick-anderson
description: ASP.NET Core uygulamasının geliştirilmesi sırasında hassas bilgileri nasıl depolayacağınızı ve alabileceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486206"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk larkabağı](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bu belgede, bir geliştirme makinesindeki ASP.NET Core uygulaması için hassas verilerin nasıl yönetileceği açıklanmaktadır. Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin. Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır. Gizli dizileri uygulamayla birlikte dağıtılmamalıdır. Bunun yerine, üretim gizli dizileri, ortam değişkenleri veya Azure Key Vault gibi denetimli bir yöntemle erişilmelidir. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.

## <a name="environment-variables"></a>Ortam değişkenleri

Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır. Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.

**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün. Varsayılan bir veritabanı bağlantı dizesi, *appsettings.json* anahtar ile projenin dosyasına dahildir `DefaultConnection` . Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir. Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir. Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.

> [!WARNING]
> Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır. Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir. Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gizli dizi Yöneticisi

Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar. Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi. Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır. Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır. Uygulama gizli dizileri kaynak denetimine iade edilmedi.

> [!WARNING]
> Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir. Yalnızca geliştirme amaçlıdır. Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.

## <a name="how-the-secret-manager-tool-works"></a>Gizli dizi Yöneticisi aracı nasıl kullanılır?

Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını gizler. Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz. Değerler yerel makinenin Kullanıcı profili klasöründe bir JSON dosyasında depolanır:

# <a name="windows"></a>[Windows](#tab/windows)

Dosya sistemi yolu:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dosya sistemi yolu:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Yukarıdaki dosya yollarında, öğesini `<user_secrets_id>` `UserSecretsId` Proje dosyasında belirtilen değerle değiştirin.

Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma. Bu uygulama ayrıntıları değişebilir. Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.

## <a name="enable-secret-storage"></a>Gizli depolamayı etkinleştir

Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.

Gizli dizi Yöneticisi Aracı, `init` .NET Core SDK 3.0.100 veya sonraki sürümlerde bir komut içerir. Kullanıcı gizli dizilerini kullanmak için, proje dizininde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets init
```

Önceki komut, `UserSecretsId` Proje dosyasının içindeki bir öğesi ekler `PropertyGroup` . Varsayılan olarak, iç metni `UserSecretsId` BIR GUID 'dir. İç metin rastgele, ancak proje için benzersizdir.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin. Bu hareket `UserSecretsId` , proje dosyasına BIR GUID ile doldurulmuş bir öğe ekler.

## <a name="set-a-secret"></a>Gizli dizi ayarla

Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın. Gizli dizi, projenin değeri ile ilişkilendirilir `UserSecretsId` . Örneğin, proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Yukarıdaki örnekte, iki nokta üst üste `Movies` bir özelliği olan bir nesne sabit değeri olduğunu gösterir `ServiceApiKey` .

Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir. `--project`Proje dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın. Örneğin:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 'da JSON yapısı düzleştirme

Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *secrets.js* dosya açar. *Üzerindesecrets.js* içeriğini, saklanacak anahtar-değer çiftleriyle değiştirin. Örneğin:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON yapısı, veya ile yapılan değişikliklerden sonra düzleştirilir `dotnet user-secrets remove` `dotnet user-secrets set` . Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır. Değiştirilen dosya aşağıdaki JSON 'a benzer:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Birden çok gizli dizi ayarla

Bir dizi gizli dizi, JSON ile komutuna ayırarak ayarlanabilir `set` . Aşağıdaki örnekte, dosyanın içeriğindeki *input.js* `set` komutuna verilmez.

# <a name="windows"></a>[Windows](#tab/windows)

Bir komut kabuğu açın ve şu komutu yürütün:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Bir komut kabuğu açın ve şu komutu yürütün:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Gizli dizi erişimi

Gizli dizi erişmek için aşağıdaki adımları izleyin:

1. [Kullanıcı parolaları yapılandırma kaynağını Kaydet](#register-the-user-secrets-configuration-source)
1. [Yapılandırma API 'SI aracılığıyla gizli anahtarı okuyun](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a>Kullanıcı parolaları yapılandırma kaynağını Kaydet

Kullanıcı gizli dizileri [yapılandırma sağlayıcısı](/dotnet/core/extensions/configuration-providers) uygun yapılandırma kaynağını .net [Yapılandırma API 'sine](xref:fundamentals/configuration/index)kaydeder.

Kullanıcı gizli dizileri yapılandırma kaynağı, proje çağırdığında geliştirme moduna otomatik olarak eklenir <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> . `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Şu olduğunda çağırır <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

`CreateDefaultBuilder`Çağrılmıyorsa, ' de çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> . `AddUserSecrets`Aşağıdaki örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a>Yapılandırma API 'SI aracılığıyla gizli anahtarı okuyun

Kullanıcı gizli dizileri yapılandırma kaynağı kayıtlıysa, .NET yapılandırma API 'SI gizli dizileri okuyabilir. [Oluşturucu Ekleme](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) .NET yapılandırma API 'sine erişim kazanmak için kullanılabilir. Anahtarı okurken aşağıdaki örnekleri göz önünde bulundurun `Movies:ServiceApiKey` :

**Başlangıç sınıfı:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

**Razor Sayfa sayfası modeli:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

Daha fazla bilgi için bkz. sayfalarda [erişim yapılandırması](xref:fundamentals/configuration/index#access-configuration-in-startup) ve [erişim yapılandırması. Razor ](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)

## <a name="map-secrets-to-a-poco"></a>Gizli dizileri bir POCO ile eşleme

Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Önceki gizli dizileri bir POCO 'ya eşlemek için .NET yapılandırma API 'sinin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın. Aşağıdaki kod, özel bir `MovieSettings` poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`Ve gizli dizileri, `Movies:ServiceApiKey` içindeki ilgili özelliklerle eşlenir `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Gizli dizileri olan dize değiştirme

Parolaların düz metin olarak depolanması güvenli değildir. Örneğin, içinde depolanan bir veritabanı bağlantı dizesi, *appsettings.json* belirtilen kullanıcı için bir parola içerebilir:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır. Örneğin:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`Anahtar-değer çiftini içindeki bağlantı dizesinden kaldırın *appsettings.json* . Örneğin:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Gizli dizi değeri, <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> bağlantı dizesinin tamamlanabilmesi için bir nesnenin özelliğinde ayarlanabilir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Gizli dizileri listeleyin

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets list
```

Aşağıdaki çıkış görüntülenir:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *secrets.js* içindeki nesne hiyerarşisini gösterir.

## <a name="remove-a-single-secret"></a>Tek bir parolayı kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Uygulamanın dosyada *secrets.js* , anahtarla ilişkili anahtar-değer çiftini kaldırmak için değiştirilmiştir `MoviesConnectionString` :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` Aşağıdaki iletiyi görüntüler:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Tüm gizli dizileri kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets clear
```

Uygulama için tüm Kullanıcı gizli dizileri, dosyadaki *secrets.js* silindi:

```json
{}
```

Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ek kaynaklar

* IIS 'deki Kullanıcı gizli bilgilerine erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bu belgede, bir geliştirme makinesindeki ASP.NET Core uygulaması için hassas verilerin nasıl yönetileceği açıklanmaktadır. Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin. Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır. Gizli dizileri uygulamayla birlikte dağıtılmamalıdır. Bunun yerine, üretim gizli dizileri, ortam değişkenleri veya Azure Key Vault gibi denetimli bir yöntemle erişilmelidir. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.

## <a name="environment-variables"></a>Ortam değişkenleri

Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır. Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.

**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün. Varsayılan bir veritabanı bağlantı dizesi, *appsettings.json* anahtar ile projenin dosyasına dahildir `DefaultConnection` . Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir. Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir. Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.

> [!WARNING]
> Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır. Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir. Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gizli dizi Yöneticisi

Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar. Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi. Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır. Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır. Uygulama gizli dizileri kaynak denetimine iade edilmedi.

> [!WARNING]
> Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir. Yalnızca geliştirme amaçlıdır. Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.

## <a name="how-the-secret-manager-tool-works"></a>Gizli dizi Yöneticisi aracı nasıl kullanılır?

Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını gizler. Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz. Değerler yerel makinenin Kullanıcı profili klasöründe bir JSON dosyasında depolanır:

# <a name="windows"></a>[Windows](#tab/windows)

Dosya sistemi yolu:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dosya sistemi yolu:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Yukarıdaki dosya yollarında, öğesini `<user_secrets_id>` `UserSecretsId` Proje dosyasında belirtilen değerle değiştirin.

Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma. Bu uygulama ayrıntıları değişebilir. Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.

## <a name="enable-secret-storage"></a>Gizli depolamayı etkinleştir

Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.

Kullanıcı gizli dizilerini kullanmak için `UserSecretsId` Proje dosyasının içindeki bir öğesi tanımlayın `PropertyGroup` . İç metni `UserSecretsId` rastgele, ancak proje için benzersizdir. Geliştiriciler, genellikle için bir GUID oluşturur `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin. Bu hareket `UserSecretsId` , proje dosyasına BIR GUID ile doldurulmuş bir öğe ekler.

## <a name="set-a-secret"></a>Gizli dizi ayarla

Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın. Gizli dizi, projenin değeri ile ilişkilendirilir `UserSecretsId` . Örneğin, proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Yukarıdaki örnekte, iki nokta üst üste `Movies` bir özelliği olan bir nesne sabit değeri olduğunu gösterir `ServiceApiKey` .

Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir. `--project`Proje dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın. Örneğin:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 'da JSON yapısı düzleştirme

Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *secrets.js* dosya açar. *Üzerindesecrets.js* içeriğini, saklanacak anahtar-değer çiftleriyle değiştirin. Örneğin:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON yapısı, veya ile yapılan değişikliklerden sonra düzleştirilir `dotnet user-secrets remove` `dotnet user-secrets set` . Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır. Değiştirilen dosya aşağıdaki JSON 'a benzer:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Birden çok gizli dizi ayarla

Bir dizi gizli dizi, JSON ile komutuna ayırarak ayarlanabilir `set` . Aşağıdaki örnekte, dosyanın içeriğindeki *input.js* `set` komutuna verilmez.

# <a name="windows"></a>[Windows](#tab/windows)

Bir komut kabuğu açın ve şu komutu yürütün:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Bir komut kabuğu açın ve şu komutu yürütün:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Gizli dizi erişimi

[Yapılandırma API 'si](xref:fundamentals/configuration/index) Kullanıcı gizli dizileri için erişim sağlar.

Projeniz .NET Framework hedefliyorsa [Microsoft.Extensions.Configbir para birimi kullanın. Usergizlilikler](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketi.

ASP.NET Core 2,0 veya sonraki sürümlerde, Kullanıcı gizli dizileri yapılandırma kaynağı, proje çağırdığında geliştirme moduna otomatik olarak eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> . `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Şu olduğunda çağırır <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

`CreateDefaultBuilder`Çağrılmıyorsa, oluşturucuda çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` . `AddUserSecrets`Aşağıdaki örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Kullanıcı gizli dizileri .NET yapılandırma API 'SI aracılığıyla alınabilir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Gizli dizileri bir POCO ile eşleme

Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Önceki gizli dizileri bir POCO 'ya eşlemek için .NET yapılandırma API 'sinin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın. Aşağıdaki kod, özel bir `MovieSettings` poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`Ve gizli dizileri, `Movies:ServiceApiKey` içindeki ilgili özelliklerle eşlenir `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Gizli dizileri olan dize değiştirme

Parolaların düz metin olarak depolanması güvenli değildir. Örneğin, içinde depolanan bir veritabanı bağlantı dizesi, *appsettings.json* belirtilen kullanıcı için bir parola içerebilir:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır. Örneğin:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`Anahtar-değer çiftini içindeki bağlantı dizesinden kaldırın *appsettings.json* . Örneğin:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Gizli dizi değeri, <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> bağlantı dizesinin tamamlanabilmesi için bir nesnenin özelliğinde ayarlanabilir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Gizli dizileri listeleyin

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets list
```

Aşağıdaki çıkış görüntülenir:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *secrets.js* içindeki nesne hiyerarşisini gösterir.

## <a name="remove-a-single-secret"></a>Tek bir parolayı kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Uygulamanın dosyada *secrets.js* , anahtarla ilişkili anahtar-değer çiftini kaldırmak için değiştirilmiştir `MoviesConnectionString` :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Tüm gizli dizileri kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Proje dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets clear
```

Uygulama için tüm Kullanıcı gizli dizileri, dosyadaki *secrets.js* silindi:

```json
{}
```

Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ek kaynaklar

* IIS 'deki Kullanıcı gizli bilgilerine erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end