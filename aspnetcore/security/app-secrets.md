---
title: ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama
author: rick-anderson
description: ASP.NET Core uygulamasının geliştirilmesi sırasında gizli bilgileri uygulama gizli dizileri olarak depolamayı ve almayı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 7508aebcda4e14812140f13ece635428908a4abb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776688"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core sürümünde geliştirme sırasında uygulama gizli dizileri güvenli depolama

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk larkabağı](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bu belgede, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması için teknikler açıklanmaktadır. Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin. Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır. Gizli dizileri uygulamayla birlikte dağıtılmamalıdır. Bunun yerine, ortamlar, ortam değişkenleri, Azure Key Vault vb. gibi denetimli bir yöntemle üretim ortamında kullanılabilir hale gelmelidir. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.

## <a name="environment-variables"></a>Ortam değişkenleri

Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır. Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.

**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün. Varsayılan bir veritabanı bağlantı dizesi, anahtarına `DefaultConnection`sahip projenin *appSettings. JSON* dosyasına dahildir. Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir. Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir. Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.

> [!WARNING]
> Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır. Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir. Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gizli dizi Yöneticisi

Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar. Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi. Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır. Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır. Uygulama gizli dizileri kaynak denetimine iade edilmedi.

> [!WARNING]
> Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir. Yalnızca geliştirme amaçlıdır. Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.

## <a name="how-the-secret-manager-tool-works"></a>Gizli dizi Yöneticisi aracı nasıl kullanılır?

Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar. Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz. Değerler, yerel makinedeki sistem korumalı bir kullanıcı profili klasöründe bir JSON yapılandırma dosyasında depolanır:

# <a name="windows"></a>[Windows](#tab/windows)

Dosya sistemi yolu:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dosya sistemi yolu:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Yukarıdaki dosya yollarında, *. csproj* dosyasında `<user_secrets_id>` belirtilen `UserSecretsId` değerle değiştirin.

Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma. Bu uygulama ayrıntıları değişebilir. Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.

## <a name="enable-secret-storage"></a>Gizli depolamayı etkinleştir

Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.

Gizli dizi Yöneticisi Aracı, .NET Core SDK `init` 3.0.100 veya sonraki sürümlerde bir komut içerir. Kullanıcı gizli dizilerini kullanmak için, proje dizininde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets init
```

Önceki komut, `UserSecretsId` *. csproj* dosyasının içindeki bir `PropertyGroup` öğesi ekler. Varsayılan olarak, iç metni bir GUID `UserSecretsId` 'dir. İç metin rastgele, ancak proje için benzersizdir.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin. Bu hareket, `UserSecretsId` *. csproj* dosyasına bir GUID ile doldurulmuş bir öğe ekler.

## <a name="set-a-secret"></a>Gizli dizi ayarla

Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın. Gizli dizi, projenin `UserSecretsId` değeri ile ilişkilendirilir. Örneğin, *. csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Yukarıdaki örnekte, iki nokta üst üste bir `Movies` `ServiceApiKey` özelliği olan bir nesne sabit değeri olduğunu gösterir.

Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir. `--project` *. Csproj* dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın. Örneğin:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 'da JSON yapısı düzleştirme

Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *gizli dizi. JSON* dosyası açar. *Gizlilikler. JSON* içeriğini depolanacak anahtar-değer çiftleriyle değiştirin. Örneğin:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON yapısı, veya `dotnet user-secrets remove` `dotnet user-secrets set`ile yapılan değişikliklerden sonra düzleştirilir. Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır. Değiştirilen dosya şuna benzer:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Birden çok gizli dizi ayarla

Bir dizi gizli dizi, `set` JSON ile komutuna ayırarak ayarlanabilir. Aşağıdaki örnekte, *input. JSON* dosyasının içeriği `set` komutuna yöneldir.

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

[ASP.NET Core Configuration API 'si](xref:fundamentals/configuration/index) , gizli yönetici sırları için erişim sağlar.

Kullanıcı gizli dizileri yapılandırma kaynağı, proje, önceden yapılandırılmış varsayılanlar ile konağın yeni bir <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> örneğini başlatmak için çağırdığında geliştirme moduna otomatik olarak eklenir. `CreateDefaultBuilder`<xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> şu <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>olduğunda çağırır:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

`CreateDefaultBuilder` Çağrılmıyorsa, çağırarak <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin. Aşağıdaki `AddUserSecrets` örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Kullanıcı gizli dizileri `Configuration` API aracılığıyla alınabilir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Gizli dizileri bir POCO ile eşleme

Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Önceki gizli dizileri bir POCO 'ya eşlemek için, `Configuration` API 'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın. Aşağıdaki kod, özel `MovieSettings` bir poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Ve `Movies:ConnectionString` `Movies:ServiceApiKey` gizli dizileri, içindeki `MovieSettings`ilgili özelliklerle eşlenir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Gizli dizileri olan dize değiştirme

Parolaların düz metin olarak depolanması güvenli değildir. Örneğin, *appSettings. JSON* içinde depolanan bir veritabanı bağlantı dizesi, belirtilen kullanıcı için bir parola içerebilir:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır. Örneğin:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` Anahtar-değer çiftini *appSettings. JSON*içindeki bağlantı dizesinden kaldırın. Örneğin:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Gizli dizi değeri, bağlantı dizesinin tamamlanabilmesi için bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> özelliğinde ayarlanabilir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Gizli dizileri listeleyin

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets list
```

Şu çıktı görünür:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *gizli dizi. JSON*içindeki nesne hiyerarşisini gösterir.

## <a name="remove-a-single-secret"></a>Tek bir parolayı kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Bu `MoviesConnectionString` anahtarla ilişkili anahtar-değer çiftini kaldırmak için uygulamanın *gizli dizi. JSON* dosyası değiştirildi:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`Aşağıdaki iletiyi görüntüler:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Tüm gizli dizileri kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets clear
```

Uygulamanın tüm Kullanıcı gizli dizileri, *gizlilikler. JSON* dosyasından silindi:

```json
{}
```

Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ek kaynaklar

* IIS 'den gizli yöneticiye erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)ve [Scott Ade](https://github.com/scottaddie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bu belgede, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması için teknikler açıklanmaktadır. Kaynak kodunda parolaları veya diğer hassas verileri hiçbir şekilde depolamayin. Üretim gizli dizileri geliştirme veya test için kullanılmamalıdır. Gizli dizileri uygulamayla birlikte dağıtılmamalıdır. Bunun yerine, ortamlar, ortam değişkenleri, Azure Key Vault vb. gibi denetimli bir yöntemle üretim ortamında kullanılabilir hale gelmelidir. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.

## <a name="environment-variables"></a>Ortam değişkenleri

Ortam değişkenleri, kodda veya yerel yapılandırma dosyalarında uygulama gizli dizileri depolanmasını önlemek için kullanılır. Ortam değişkenleri, daha önce belirtilen tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.

**Bireysel kullanıcı hesapları** güvenliğinin etkinleştirildiği bir ASP.NET Core Web uygulaması düşünün. Varsayılan bir veritabanı bağlantı dizesi, anahtarına `DefaultConnection`sahip projenin *appSettings. JSON* dosyasına dahildir. Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir. Uygulama dağıtımı sırasında, `DefaultConnection` anahtar değeri bir ortam değişkeninin değeri ile geçersiz kılınabilir. Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle saklayabilir.

> [!WARNING]
> Ortam değişkenleri genellikle düz, şifresiz metin olarak depolanır. Makinenin veya işlemin güvenliği tehlikeye atılırsa, ortam değişkenlerine güvenilmeyen taraflar tarafından erişilebilir. Kullanıcı gizliliklerinin açıklanmasını önlemeye yönelik ek ölçüler gerekebilir.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gizli dizi Yöneticisi

Gizli verileri bir ASP.NET Core projesi geliştirme sırasında depolar. Bu bağlamda, önemli bir veri parçası bir uygulama gizli dizisi. Uygulama gizli dizileri, proje ağacından ayrı bir konumda depolanır. Uygulama gizli dizileri belirli bir projeyle ilişkilendirilir veya çeşitli projeler arasında paylaşılır. Uygulama gizli dizileri kaynak denetimine iade edilmedi.

> [!WARNING]
> Gizli dizi Yöneticisi aracı depolanan gizli dizileri şifrelemez ve güvenilir bir depo olarak değerlendirilmemelidir. Yalnızca geliştirme amaçlıdır. Anahtarlar ve değerler, Kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.

## <a name="how-the-secret-manager-tool-works"></a>Gizli dizi Yöneticisi aracı nasıl kullanılır?

Gizli dizi Yöneticisi Aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar. Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz. Değerler, yerel makinedeki sistem korumalı bir kullanıcı profili klasöründe bir JSON yapılandırma dosyasında depolanır:

# <a name="windows"></a>[Windows](#tab/windows)

Dosya sistemi yolu:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dosya sistemi yolu:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Yukarıdaki dosya yollarında, *. csproj* dosyasında `<user_secrets_id>` belirtilen `UserSecretsId` değerle değiştirin.

Gizli dizi yöneticisi aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazma. Bu uygulama ayrıntıları değişebilir. Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.

## <a name="enable-secret-storage"></a>Gizli depolamayı etkinleştir

Gizli dizi Yöneticisi Aracı, Kullanıcı profilinizde depolanan projeye özgü yapılandırma ayarları üzerinde çalışır.

Kullanıcı gizli dizilerini kullanmak için `UserSecretsId` *. csproj* dosyasının içindeki `PropertyGroup` bir öğesi tanımlayın. İç metni `UserSecretsId` rastgele, ancak proje için benzersizdir. Geliştiriciler, `UserSecretsId`genellikle IÇIN bir GUID oluşturur.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio 'da Çözüm Gezgini projeye sağ tıklayın ve bağlam menüsünden **Kullanıcı gizli dizilerini Yönet** ' i seçin. Bu hareket, `UserSecretsId` *. csproj* dosyasına bir GUID ile doldurulmuş bir öğe ekler.

## <a name="set-a-secret"></a>Gizli dizi ayarla

Anahtar ve değerini içeren bir uygulama gizli anahtarı tanımlayın. Gizli dizi, projenin `UserSecretsId` değeri ile ilişkilendirilir. Örneğin, *. csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Yukarıdaki örnekte, iki nokta üst üste bir `Movies` `ServiceApiKey` özelliği olan bir nesne sabit değeri olduğunu gösterir.

Gizli dizi Yöneticisi Aracı diğer dizinlerden de kullanılabilir. `--project` *. Csproj* dosyasının bulunduğu dosya sistemi yolunu sağlamak için seçeneğini kullanın. Örneğin:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 'da JSON yapısı düzleştirme

Visual Studio 'nun **Kullanıcı gizli dizilerini Yönet** hareketi metin düzenleyicisinde bir *gizli dizi. JSON* dosyası açar. *Gizlilikler. JSON* içeriğini depolanacak anahtar-değer çiftleriyle değiştirin. Örneğin:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON yapısı, veya `dotnet user-secrets remove` `dotnet user-secrets set`ile yapılan değişikliklerden sonra düzleştirilir. Örneğin, çalışıyor `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne değişmez değerini daraltır. Değiştirilen dosya şuna benzer:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Birden çok gizli dizi ayarla

Bir dizi gizli dizi, `set` JSON ile komutuna ayırarak ayarlanabilir. Aşağıdaki örnekte, *input. JSON* dosyasının içeriği `set` komutuna yöneldir.

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

[ASP.NET Core Configuration API 'si](xref:fundamentals/configuration/index) , gizli yönetici sırları için erişim sağlar.

Projeniz .NET Framework hedefliyorsa [Microsoft. Extensions. Configuration. Usergizlilikler](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketini yükler.

ASP.NET Core 2,0 veya sonraki bir sürümde, proje önceden yapılandırılmış varsayılanlar ile konağın yeni bir örneğini başlatmak için çağırdığında <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , Kullanıcı gizli dizileri yapılandırma kaynağı geliştirme moduna otomatik olarak eklenir. `CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> şu <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>olduğunda çağırır:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

`CreateDefaultBuilder` Çağrılmıyorsa, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` oluşturucuda çağırarak Kullanıcı gizli dizi yapılandırma kaynağını açıkça ekleyin. Aşağıdaki `AddUserSecrets` örnekte gösterildiği gibi, yalnızca uygulama geliştirme ortamında çalıştırıldığında çağırın:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Kullanıcı gizli dizileri `Configuration` API aracılığıyla alınabilir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Gizli dizileri bir POCO ile eşleme

Bir nesne sabit değerinin tamamını bir POCO 'ya eşleme (özelliklerle basit bir .NET sınıfı) ilgili özellikleri toplamak için faydalıdır.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Önceki gizli dizileri bir POCO 'ya eşlemek için, `Configuration` API 'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın. Aşağıdaki kod, özel `MovieSettings` bir poco 'a bağlanır ve `ServiceApiKey` özellik değerine erişir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Ve `Movies:ConnectionString` `Movies:ServiceApiKey` gizli dizileri, içindeki `MovieSettings`ilgili özelliklerle eşlenir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Gizli dizileri olan dize değiştirme

Parolaların düz metin olarak depolanması güvenli değildir. Örneğin, *appSettings. JSON* içinde depolanan bir veritabanı bağlantı dizesi, belirtilen kullanıcı için bir parola içerebilir:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Daha güvenli bir yaklaşım, parolayı gizli olarak depolanmalıdır. Örneğin:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` Anahtar-değer çiftini *appSettings. JSON*içindeki bağlantı dizesinden kaldırın. Örneğin:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Gizli dizi değeri, bağlantı dizesinin tamamlanabilmesi için bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> özelliğinde ayarlanabilir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Gizli dizileri listeleyin

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets list
```

Şu çıktı görünür:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Yukarıdaki örnekte, anahtar adlarındaki bir iki nokta üst üste *gizli dizi. JSON*içindeki nesne hiyerarşisini gösterir.

## <a name="remove-a-single-secret"></a>Tek bir parolayı kaldır

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Bu `MoviesConnectionString` anahtarla ilişkili anahtar-değer çiftini kaldırmak için uygulamanın *gizli dizi. JSON* dosyası değiştirildi:

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

*. Csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets clear
```

Uygulamanın tüm Kullanıcı gizli dizileri, *gizlilikler. JSON* dosyasından silindi:

```json
{}
```

Çalıştıran `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ek kaynaklar

* IIS 'den gizli yöneticiye erişme hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end