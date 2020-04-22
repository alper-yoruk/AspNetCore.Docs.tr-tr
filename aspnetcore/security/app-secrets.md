---
title: ASP.NET Core'da uygulama sırlarınıgeliştirmede güvenli depolama
author: rick-anderson
description: bir ASP.NET Core uygulaması nın geliştirilmesi sırasında hassas bilgileri uygulama sırları olarak nasıl depolayıp edineceklerini öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791608"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core'da uygulama sırlarınıgeliştirmede güvenli depolama

::: moniker range=">= aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), ve [Scott Addie](https://github.com/scottaddie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Bu belge, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması tekniklerini açıklar. Parolaları veya diğer hassas verileri asla kaynak kodunda depolamayın. Üretim sırları geliştirme veya test için kullanılmamalıdır. Sırlar uygulamayla birlikte dağıtılmamalıdır. Bunun yerine, sırlar çevre değişkenleri, Azure Anahtar Kasası vb. gibi kontrollü bir yolla üretim ortamında kullanıma sunulmalıdır. Azure Test ve üretim sırlarını Azure [Anahtar Kasası yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)saklayabilir ve koruyabilirsiniz.

## <a name="environment-variables"></a>Ortam değişkenleri

Ortam değişkenleri, uygulama sırlarını kodda veya yerel yapılandırma dosyalarında depolamaktan kaçınmak için kullanılır. Ortam değişkenleri, önceden belirtilmiş tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.

**Bireysel Kullanıcı Hesapları** güvenliğinin etkin olduğu bir ASP.NET Core web uygulaması düşünün. Varsayılan veritabanı bağlantı dizesi, projenin *appsettings.json* dosyasında `DefaultConnection`anahtarla birlikte yer alır. Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir. Uygulama dağıtımı `DefaultConnection` sırasında, anahtar değeri bir ortam değişkeninin değeriyle geçersiz kılınabilir. Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle depolayabilir.

> [!WARNING]
> Ortam değişkenleri genellikle düz, şifrelenmemiş metinde depolanır. Makine veya işlem tehlikeye girerse, ortam değişkenlerine güvenilmeyen taraflarca erişilebilir. Kullanıcı sırlarının açıklanmasını önlemek için ek önlemler gerekebilir.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gizli Müdür

Gizli Yönetici aracı, bir ASP.NET Core projesinin geliştirilmesi sırasında hassas verileri depolar. Bu bağlamda, hassas verilerin bir parçası bir uygulama gizlidir. Uygulama sırları proje ağacından ayrı bir konumda depolanır. Uygulama sırları belirli bir projeyle ilişkilendirilir veya çeşitli projelerde paylaşılır. Uygulama sırları kaynak denetimine girmez.

> [!WARNING]
> Gizli Yönetici aracı depolanan sırları şifrelemez ve güvenilir bir mağaza olarak ele alınamamalıdır. Sadece geliştirme amaçlı. Anahtarlar ve değerler, kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.

## <a name="how-the-secret-manager-tool-works"></a>Gizli Yönetici aracı nasıl çalışır?

Gizli Yönetici aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar. Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz. Değerler, yerel makinedeki sistem korumalı kullanıcı profili klasöründe JSON yapılandırma dosyasında depolanır:

# <a name="windows"></a>[Windows](#tab/windows)

Dosya sistemi yolu:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Dosya sistemi yolu:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Önceki dosya `<user_secrets_id>` yollarında,.csproj dosyasında belirtilen `UserSecretsId` değerle değiştirin. *.csproj*

Gizli Yönetici aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazmayın. Bu uygulama ayrıntıları değişebilir. Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.

## <a name="enable-secret-storage"></a>Gizli depolamayı etkinleştirme

Gizli Yönetici aracı, kullanıcı profilinizde depolanan projeye özel yapılandırma ayarlarında çalışır.

Gizli Yönetici aracı `init` .NET Core SDK 3.0.100 veya sonrası bir komut içerir. Kullanıcı sırlarını kullanmak için proje dizininde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets init
```

Önceki komut `UserSecretsId` *.csproj* dosyasının içinde bir `PropertyGroup` öğe ekler. Varsayılan olarak, iç `UserSecretsId` metin bir GUID'dir. İç metin rasgele, ancak projeye özgüdür.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio'da, Solution Explorer'da projeyi sağ tıklatın ve bağlam menüsünden **Kullanıcı Sırlarını Yönet'i** seçin. Bu hareket, `UserSecretsId` *.csproj* dosyasına GUID ile doldurulan bir öğe ekler.

## <a name="set-a-secret"></a>Bir sır ayarlayın

Bir anahtar ve değerinden oluşan bir uygulama sırrı tanımlayın. Gizli projenin `UserSecretsId` değeri ile ilişkilidir. Örneğin, *.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Önceki örnekte, iki nokta üst `Movies` üste bir `ServiceApiKey` özelliği olan bir nesne nin gerçek olduğunu gösterir.

Gizli Yönetici aracı diğer dizinlerden de kullanılabilir. `--project` *.csproj* dosyasının bulunduğu dosya sistemi yolunu sağlama seçeneğini kullanın. Örneğin:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio'da JSON yapısı düzleme

Visual Studio'nun **Kullanıcı Sırlarını Yönet** hareketi metin düzenleyicisinde bir *secrets.json* dosyasını açar. *Secrets.json'ın* içeriğini depolanacak anahtar değeri çiftleri ile değiştirin. Örneğin:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON yapısı üzerinden değişiklikler den sonra `dotnet user-secrets remove` `dotnet user-secrets set`düzleştirilmiş veya . Örneğin, çalışan `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne nin gerçek olarak daraltıyor. Değiştirilen dosya aşağıdakilere benzer:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Birden çok sır ayarlama

JSON'un `set` komutasına boru lanmasıyla bir dizi sır belirlenebilir. Aşağıdaki örnekte, *input.json* dosyasının içeriği `set` komuta aktarılır.

# <a name="windows"></a>[Windows](#tab/windows)

Komut kabuğunu açın ve aşağıdaki komutu uygulayın:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Komut kabuğunu açın ve aşağıdaki komutu uygulayın:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Bir gizliye erişin

[ASP.NET Core Configuration API,](xref:fundamentals/configuration/index) Gizli Yönetici sırlarına erişim sağlar.

Proje, önceden yapılandırılmış varsayılanlara sahip ana bilgisayaryeni <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> bir örneğini başlatmaya çağırdığında, kullanıcı sırları yapılandırma kaynağı otomatik olarak geliştirme moduna eklenir. `CreateDefaultBuilder`olduğunda <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>çağırır:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Çağrılmadığı zaman, `CreateDefaultBuilder` kullanıcı sırları yapılandırma kaynağını açıkça <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>arayarak ekleyin. Aşağıdaki `AddUserSecrets` örnekte gösterildiği gibi, yalnızca uygulama Geliştirme ortamında çalıştığında arama:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Kullanıcı sırları `Configuration` API üzerinden alınabilir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Bir POCO'ya harita sırları

Tüm nesnenin gerçek bir nesnesini bir POCO (özellikleri olan basit bir .NET sınıfı) ile eşleme, ilgili özellikleri bir araya getirmek için yararlıdır.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Önceki sırları bir POCO ile eşlemek `Configuration` için API'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın. Aşağıdaki kod özel `MovieSettings` bir POCO'ya bağlanır `ServiceApiKey` ve özellik değerine erişir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Ve sırlar ilgili özelliklere `MovieSettings`eşlenir: `Movies:ServiceApiKey` `Movies:ConnectionString`

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Sırlar ile string değiştirme

Parolaları düz metinde depolamak güvenli değildir. Örneğin, *appsettings.json'da* depolanan bir veritabanı bağlantı dizesi, belirtilen kullanıcı için bir parola içerebilir:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Daha güvenli bir yaklaşım, parolayı bir sır olarak depolamaktır. Örneğin:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` *appsettings.json'daki*bağlantı dizesinden anahtar değeri çiftini kaldırın. Örneğin:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Gizlinin değeri, bağlantı dizesini tamamlamak için bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> özelliğine ayarlanabilir:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Sırları listele

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets list
```

Şu çıktı görünür:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Önceki örnekte, anahtar adlarında bir üst üste *secrets.json*içindeki nesne hiyerarşisini gösterir.

## <a name="remove-a-single-secret"></a>Tek bir sırrı kaldırma

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Uygulamanın *secrets.json* `MoviesConnectionString` dosyası, anahtarla ilişkili anahtar değeri çiftini kaldırmak için değiştirildi:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`aşağıdaki iletiyi görüntüler:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Tüm sırları kaldırın

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets clear
```

Uygulamanın tüm kullanıcı sırları *secrets.json* dosyasından silindi:

```json
{}
```

Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ek kaynaklar

* IIS'den Gizli Yönetici'ye erişim hakkında bilgi için [bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), ve [Scott Addie](https://github.com/scottaddie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Bu belge, bir geliştirme makinesinde ASP.NET Core uygulamasının geliştirilmesi sırasında hassas verilerin depolanması ve alınması tekniklerini açıklar. Parolaları veya diğer hassas verileri asla kaynak kodunda depolamayın. Üretim sırları geliştirme veya test için kullanılmamalıdır. Sırlar uygulamayla birlikte dağıtılmamalıdır. Bunun yerine, sırlar çevre değişkenleri, Azure Anahtar Kasası vb. gibi kontrollü bir yolla üretim ortamında kullanıma sunulmalıdır. Azure Test ve üretim sırlarını Azure [Anahtar Kasası yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)saklayabilir ve koruyabilirsiniz.

## <a name="environment-variables"></a>Ortam değişkenleri

Ortam değişkenleri, uygulama sırlarını kodda veya yerel yapılandırma dosyalarında depolamaktan kaçınmak için kullanılır. Ortam değişkenleri, önceden belirtilmiş tüm yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.

**Bireysel Kullanıcı Hesapları** güvenliğinin etkin olduğu bir ASP.NET Core web uygulaması düşünün. Varsayılan veritabanı bağlantı dizesi, projenin *appsettings.json* dosyasında `DefaultConnection`anahtarla birlikte yer alır. Varsayılan bağlantı dizesi, kullanıcı modunda çalışan ve parola gerektirmeyen LocalDB içindir. Uygulama dağıtımı `DefaultConnection` sırasında, anahtar değeri bir ortam değişkeninin değeriyle geçersiz kılınabilir. Ortam değişkeni, tüm bağlantı dizesini hassas kimlik bilgileriyle depolayabilir.

> [!WARNING]
> Ortam değişkenleri genellikle düz, şifrelenmemiş metinde depolanır. Makine veya işlem tehlikeye girerse, ortam değişkenlerine güvenilmeyen taraflarca erişilebilir. Kullanıcı sırlarının açıklanmasını önlemek için ek önlemler gerekebilir.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gizli Müdür

Gizli Yönetici aracı, bir ASP.NET Core projesinin geliştirilmesi sırasında hassas verileri depolar. Bu bağlamda, hassas verilerin bir parçası bir uygulama gizlidir. Uygulama sırları proje ağacından ayrı bir konumda depolanır. Uygulama sırları belirli bir projeyle ilişkilendirilir veya çeşitli projelerde paylaşılır. Uygulama sırları kaynak denetimine girmez.

> [!WARNING]
> Gizli Yönetici aracı depolanan sırları şifrelemez ve güvenilir bir mağaza olarak ele alınamamalıdır. Sadece geliştirme amaçlı. Anahtarlar ve değerler, kullanıcı profili dizinindeki bir JSON yapılandırma dosyasında depolanır.

## <a name="how-the-secret-manager-tool-works"></a>Gizli Yönetici aracı nasıl çalışır?

Gizli Yönetici aracı, değerlerin nerede ve nasıl depolandığı gibi uygulama ayrıntılarını soyutlar. Bu uygulama ayrıntılarını bilmeden aracı kullanabilirsiniz. Değerler, yerel makinedeki sistem korumalı kullanıcı profili klasöründe JSON yapılandırma dosyasında depolanır:

# <a name="windows"></a>[Windows](#tab/windows)

Dosya sistemi yolu:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Dosya sistemi yolu:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Önceki dosya `<user_secrets_id>` yollarında,.csproj dosyasında belirtilen `UserSecretsId` değerle değiştirin. *.csproj*

Gizli Yönetici aracıyla kaydedilen verilerin konumuna veya biçimine bağlı olarak kod yazmayın. Bu uygulama ayrıntıları değişebilir. Örneğin, gizli değerler şifrelenmez, ancak gelecekte olabilir.

## <a name="enable-secret-storage"></a>Gizli depolamayı etkinleştirme

Gizli Yönetici aracı, kullanıcı profilinizde depolanan projeye özel yapılandırma ayarlarında çalışır.

Kullanıcı sırlarını kullanmak için `UserSecretsId` `PropertyGroup` *.csproj* dosyasının içindeki bir öğeyi tanımlayın. İç metin `UserSecretsId` rasgele, ancak projeye özgüdür. Geliştiriciler genellikle `UserSecretsId`bir GUID oluşturmak için .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio'da, Solution Explorer'da projeyi sağ tıklatın ve bağlam menüsünden **Kullanıcı Sırlarını Yönet'i** seçin. Bu hareket, `UserSecretsId` *.csproj* dosyasına GUID ile doldurulan bir öğe ekler.

## <a name="set-a-secret"></a>Bir sır ayarlayın

Bir anahtar ve değerinden oluşan bir uygulama sırrı tanımlayın. Gizli projenin `UserSecretsId` değeri ile ilişkilidir. Örneğin, *.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Önceki örnekte, iki nokta üst `Movies` üste bir `ServiceApiKey` özelliği olan bir nesne nin gerçek olduğunu gösterir.

Gizli Yönetici aracı diğer dizinlerden de kullanılabilir. `--project` *.csproj* dosyasının bulunduğu dosya sistemi yolunu sağlama seçeneğini kullanın. Örneğin:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio'da JSON yapısı düzleme

Visual Studio'nun **Kullanıcı Sırlarını Yönet** hareketi metin düzenleyicisinde bir *secrets.json* dosyasını açar. *Secrets.json'ın* içeriğini depolanacak anahtar değeri çiftleri ile değiştirin. Örneğin:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON yapısı üzerinden değişiklikler den sonra `dotnet user-secrets remove` `dotnet user-secrets set`düzleştirilmiş veya . Örneğin, çalışan `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` nesne nin gerçek olarak daraltıyor. Değiştirilen dosya aşağıdakilere benzer:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Birden çok sır ayarlama

JSON'un `set` komutasına boru lanmasıyla bir dizi sır belirlenebilir. Aşağıdaki örnekte, *input.json* dosyasının içeriği `set` komuta aktarılır.

# <a name="windows"></a>[Windows](#tab/windows)

Komut kabuğunu açın ve aşağıdaki komutu uygulayın:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Komut kabuğunu açın ve aşağıdaki komutu uygulayın:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Bir gizliye erişin

[ASP.NET Core Configuration API,](xref:fundamentals/configuration/index) Gizli Yönetici sırlarına erişim sağlar.

Projeniz .NET Framework'u [hedefliyorsa, Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketini yükleyin.

Core 2.0 veya daha sonra ASP.NET, proje önceden yapılandırılmış varsayılanları ile <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> ana bilgisayar yeni bir örnek başlatmaya çağırır kullanıcı sırları yapılandırma kaynağı otomatik olarak geliştirme moduna eklenir. `CreateDefaultBuilder`olduğunda <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>çağırır:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Çağrılmadığı zaman, `CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` oluşturucuyu çağırarak kullanıcı sırları yapılandırma kaynağını açıkça ekleyin. Aşağıdaki `AddUserSecrets` örnekte gösterildiği gibi, yalnızca uygulama Geliştirme ortamında çalıştığında arama:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Kullanıcı sırları `Configuration` API üzerinden alınabilir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Bir POCO'ya harita sırları

Tüm nesnenin gerçek bir nesnesini bir POCO (özellikleri olan basit bir .NET sınıfı) ile eşleme, ilgili özellikleri bir araya getirmek için yararlıdır.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Önceki sırları bir POCO ile eşlemek `Configuration` için API'nin [nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliğini kullanın. Aşağıdaki kod özel `MovieSettings` bir POCO'ya bağlanır `ServiceApiKey` ve özellik değerine erişir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Ve sırlar ilgili özelliklere `MovieSettings`eşlenir: `Movies:ServiceApiKey` `Movies:ConnectionString`

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Sırlar ile string değiştirme

Parolaları düz metinde depolamak güvenli değildir. Örneğin, *appsettings.json'da* depolanan bir veritabanı bağlantı dizesi, belirtilen kullanıcı için bir parola içerebilir:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Daha güvenli bir yaklaşım, parolayı bir sır olarak depolamaktır. Örneğin:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` *appsettings.json'daki*bağlantı dizesinden anahtar değeri çiftini kaldırın. Örneğin:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Gizlinin değeri, bağlantı dizesini tamamlamak için bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> özelliğine ayarlanabilir:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Sırları listele

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets list
```

Şu çıktı görünür:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Önceki örnekte, anahtar adlarında bir üst üste *secrets.json*içindeki nesne hiyerarşisini gösterir.

## <a name="remove-a-single-secret"></a>Tek bir sırrı kaldırma

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Uygulamanın *secrets.json* `MoviesConnectionString` dosyası, anahtarla ilişkili anahtar değeri çiftini kaldırmak için değiştirildi:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Tüm sırları kaldırın

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* dosyasının bulunduğu dizinden aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet user-secrets clear
```

Uygulamanın tüm kullanıcı sırları *secrets.json* dosyasından silindi:

```json
{}
```

Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Ek kaynaklar

* IIS'den Gizli Yönetici'ye erişim hakkında bilgi için [bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/16328) bakın.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end