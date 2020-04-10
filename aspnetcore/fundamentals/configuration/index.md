---
title: ASP.NET Core'da Yapılandırma
author: rick-anderson
description: ASP.NET Core uygulamasını yapılandırmak için Yapılandırma API'sini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007164"
---
# <a name="configuration-in-aspnet-core"></a>ASP.NET Core'da Yapılandırma

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core'daki yapılandırma bir veya daha fazla [yapılandırma sağlayıcısı](#cp)kullanılarak gerçekleştirilir. Yapılandırma sağlayıcıları, çeşitli yapılandırma kaynaklarını kullanarak anahtar değer çiftlerinden gelen yapılandırma verilerini okur:

* *settings.json* gibi ayarlar dosyaları
* Ortam değişkenleri
* Azure Key Vault
* Azure Uygulama Yapılandırması
* Komut satırı bağımsız değişkenleri
* Özel sağlayıcılar, yüklü veya oluşturulmuş
* Dizin dosyaları
* Bellek içi .NET nesneleri

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Varsayılan yapılandırma

ASP.NET Core web uygulamaları [dotnet yeni](/dotnet/core/tools/dotnet-new) veya Visual Studio ile oluşturulan aşağıdaki kodu oluşturmak:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>aşağıdaki sırada uygulama için varsayılan yapılandırma sağlar:

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Kaynak `IConfiguration` olarak var olan bir kuruluşu ekler. Varsayılan yapılandırma durumunda, [ana bilgisayar](#hvac) yapılandırmasını ekler ve _uygulama_ yapılandırmasının ilk kaynağı olarak ayarlar.
1. [appsettings.json](#appsettingsjson) [JSON yapılandırma sağlayıcısı](#file-configuration-provider)kullanarak .
1. *ayarları.* `Environment` *.json* [JSON yapılandırma sağlayıcısını](#file-configuration-provider)kullanarak . Örneğin, *uygulama ayarları*. ***Üretim***. *json* ve *appsettings*. ***Geliştirme***. *json*.
1. [Uygulama](xref:security/app-secrets) `Development` çevrede çalıştığında uygulama sırları.
1. [Çevre Değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.
1. Komut satırı yapılandırma [sağlayıcısını](#command-line)kullanarak komut satırı bağımsız değişkenleri.

Daha sonra eklenen yapılandırma sağlayıcıları önceki anahtar ayarlarını geçersiz kılar. Örneğin, `MyKey` hem *appsettings.json* hem de ortamda ayarlanmışsa, ortam değeri kullanılır. [Komut satırı yapılandırma sağlayıcısı,](#command-line-configuration-provider) varsayılan yapılandırma sağlayıcılarını kullanarak diğer tüm sağlayıcıları geçersiz kılar.

Hakkında daha `CreateDefaultBuilder`fazla bilgi için Varsayılan [oluşturucu ayarlarına](xref:fundamentals/host/generic-host#default-builder-settings)bakın.

Aşağıdaki kod, etkin yapılandırma sağlayıcılarını eklendikleri sırada görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a>appsettings.json

Aşağıdaki *appsettings.json* dosyasını göz önünde bulundurun:

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Varsayılan <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yükler yapılandırması aşağıdaki sırayla:

1. *appsettings.json*
1. *ayarları.* `Environment` *.json* : Örneğin, *uygulama ayarları*. ***Üretim***. *json* ve *appsettings*. ***Geliştirme***. *json* dosyaları. Dosyanın ortam sürümü [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)dayalı yüklenir. Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

*appsettings*. `Environment`. *json* değerleri *appsettings.json*tuşları geçersiz kılar. Örneğin, varsayılan olarak:

* Geliştirme, *appsettings*. ***Geliştirme***. *json* yapılandırma *appsettings.json*bulunan değerleri üzerine yazar.
* Üretimde, *uygulama ayarları*. ***Üretim***. *json* yapılandırma *appsettings.json*bulunan değerleri üzerine yazar. Örneğin, uygulamayı Azure'a dağıtırken.

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Seçenekler deseni kullanarak hiyerarşik yapılandırma verilerini bağlama

İlgili yapılandırma değerlerini okumanın tercih edilen yolu [seçenekler deseni](xref:fundamentals/configuration/options)kullanmaktır. Örneğin, aşağıdaki yapılandırma değerlerini okumak için:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Aşağıdaki `PositionOptions` sınıfı oluşturun:

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Türünün tüm ortak okuma-yazma özellikleri bağlanır. Alanlar bağlı ***değildir.***

Aşağıdaki kod:

* `PositionOptions` Sınıfı `Position` bölüme bağlamak için [ConfigurationBinder.Bind'i](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) çağırır.
* Yapılandırma `Position` verilerini görüntüler.

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)biner ve belirtilen türü döndürür. `ConfigurationBinder.Get<T>`kullanmaktan `ConfigurationBinder.Bind`daha uygun olabilir. Aşağıdaki kod, sınıfla `ConfigurationBinder.Get<T>` nasıl `PositionOptions` kullanılacağını gösterir:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

***Seçenekler deseni*** kullanırken alternatif bir yaklaşım `Position` bölümü bağlamak ve [bağımlılık enjeksiyon servis konteyner](xref:fundamentals/dependency-injection)eklemektir. Aşağıdaki kodda, `PositionOptions` yapılandırmaile ve <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> yapılandırmaya bağlı servis kapsayıcısına eklenir:

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Yukarıdaki kodu kullanarak, aşağıdaki kod konum seçeneklerini okur:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

[Varsayılan](#default) yapılandırmayı, *appsettings.json* ve *uygulama ayarlarını kullanarak.* `Environment` *.json* dosyaları [reloadOnChange](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)ile etkinleştirilir: true . *appsettings.json* ve *appsettings'te* yapılan değişiklikler. `Environment` *.json* dosyası uygulama ***başladıktan sonra*** [JSON yapılandırma sağlayıcısı](#jcp)tarafından okunur.

Ek JSON yapılandırma dosyaları ekleme hakkında bilgi için bu belgede [JSON yapılandırma sağlayıcısına](#jcp) bakın.

<a name="security"></a>

## <a name="security-and-secret-manager"></a>Güvenlik ve gizli müdür

Yapılandırma veri yönergeleri:

* Parolaları veya diğer hassas verileri asla yapılandırma sağlayıcı kodunda veya düz metin yapılandırma dosyalarında depolamayın. [Gizli yönetici](xref:security/app-secrets) geliştirme sırlarını saklamak için kullanılabilir.
* Üretim sırlarını geliştirme veya test ortamlarında kullanmayın.
* Yanlışlıkla bir kaynak kodu deposuna işlenmeyecek şekilde projenin dışındaki sırları belirtin.

[Varsayılan olarak,](#default) [Secret yöneticisi](xref:security/app-secrets) *appsettings.json* ve uygulama ayarlarını sonra yapılandırma ayarlarını *okur.* `Environment` *.json*.

Parolaları veya diğer hassas verileri depolama hakkında daha fazla bilgi için:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Hassas verileri depolamak için ortam değişkenlerinin kullanılmasına ilişkin tavsiyeleri içerir. Gizli Yönetici, kullanıcı sırlarını yerel sistemdeki bir JSON dosyasında depolamak için [Dosya yapılandırma sağlayıcısını](#fcp) kullanır.

[Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamaları için uygulama sırlarını güvenle saklar. Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Ortam değişkenleri

[Varsayılan](#default) yapılandırmayı kullanarak, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> *appsettings.json, appsettings*okuduktan sonra ortam değişken anahtar değeri çiftleri yükler *yapılandırma.* `Environment` *.json*ve [Gizli müdür.](xref:security/app-secrets) Bu nedenle, ortamdan okunan temel değerler *appsettings.json*, appsettings okunan değerleri geçersiz *kılar.* `Environment` *.json*ve gizli müdür.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Aşağıdaki `set` komutlar:

* Windows'da [önceki örneğin](#appsettingsjson) ortam anahtarlarını ve değerlerini ayarlayın.
* [Örnek indirmeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)kullanırken ayarları test edin. Komut `dotnet run` proje dizininde çalıştırılmalıdır.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Önceki ortam ayarları:

* Yalnızca ayarlandıkları komut penceresinden başlatılan işlemlerde ayarlanır.
* Visual Studio ile başlatılan tarayıcılar tarafından okunmaz.

Aşağıdaki [setx](/windows-server/administration/windows-commands/setx) komutları Windows'daki ortam anahtarlarını ve değerlerini ayarlamak için kullanılabilir. `set`Aksine, `setx` ayarlar kalıcıdır. `/M`değişkeni sistem ortamında ayarlar. Anahtar `/M` kullanılmazsa, bir kullanıcı ortamı değişkeni ayarlanır.

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Önceki komutların *appsettings.json* ve appsettings geçersiz kılınmasını test etmek *için.* `Environment` *.json*:

* Visual Studio ile: Visual Studio'dan çıkın ve yeniden başlatın.
* CLI ile: Yeni bir komut `dotnet run`penceresi başlatın ve girin.

Ortam <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> değişkenleri için bir önek belirtmek için dizeyle arama:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

Yukarıdaki kodda:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`varsayılan yapılandırma [sağlayıcılarından](#default)sonra eklenir. Yapılandırma sağlayıcılarını sıralamak için [Bkz. JSON yapılandırma sağlayıcısı.](#jcp)
* Öneki ile ayarlanan ortam değişkenleri [varsayılan yapılandırma sağlayıcılarını](#default)geçersiz kılar. `MyCustomPrefix_` Bu önek olmadan ortam değişkenleri içerir.

Yapılandırma anahtar değeri çiftleri okunduğunda önek çıkarılır.

Aşağıdaki komutlar özel önek test:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

[Varsayılan yapılandırma,](#default) ortam değişkenlerini ve komut `DOTNET_` satırı `ASPNETCORE_`bağımsız değişkenlerini önceden belirlenmiş ve . `DOTNET_` Önekleri `ASPNETCORE_` ve önekleri [ana bilgisayar ve uygulama yapılandırması](xref:fundamentals/host/generic-host#host-configuration)için ASP.NET Core tarafından kullanılır, ancak kullanıcı yapılandırması için kullanılmaz. Ana bilgisayar ve uygulama yapılandırması hakkında daha fazla bilgi için [.NET Genel Ana Bilgisayar'a](xref:fundamentals/host/generic-host)bakın.

[Azure Uygulama Hizmeti'nde](https://azure.microsoft.com/services/app-service/)Ayarlar **> Yapılandırma** sayfasında Yeni **uygulama ayarını** seçin. Azure Uygulama Hizmeti uygulama ayarları şunlardır:

* Istirahatte şifrelenir ve şifreli bir kanal üzerinden iletilir.
* Çevre değişkenleri olarak ortaya çıktı.

Daha fazla bilgi için Azure [Uygulamaları: Azure Portalı'nı kullanarak uygulama yapılandırmasını geçersiz kılın.](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)

Azure veritabanı bağlantı dizeleri hakkında bilgi için [Bağlantı dizelerine](#constr) bakın.

<a name="clcp"></a>

## <a name="command-line"></a>Komut Satırı

[Varsayılan](#default) yapılandırmayı kullanarak, aşağıdaki yapılandırma kaynaklarından sonra komut satırı bağımsız değişken anahtar değeri çiftlerinden <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> yükler yapılandırması:

* *appsettings.json* ve *appsettings*. `Environment`. *json* dosyaları.
* Geliştirme ortamında [uygulama sırları (Gizli Yönetici).](xref:security/app-secrets)
* Ortam değişkenleri.

[Varsayılan olarak,](#default)komut satırında ayarlanan yapılandırma değerleri, diğer tüm yapılandırma sağlayıcılarıyla birlikte ayarlanan yapılandırma değerlerini geçersiz kılar.

### <a name="command-line-arguments"></a>Komut satırı bağımsız değişkenleri

Aşağıdaki komut tuşları ve `=`değerleri kullanarak ayarlar:

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

Aşağıdaki komut tuşları ve `/`değerleri kullanarak ayarlar:

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

Aşağıdaki komut tuşları ve `--`değerleri kullanarak ayarlar:

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Anahtar değeri:

* Takip `=`etmeli veya anahtar da bir `--` öneki ye sahip olmalıdır veya `/` değer bir alanı takip ettiğinde.
* Kullanılıyorsa `=` gerekli değildir. Örneğin, `MySetting=`.

Aynı komut içinde, boşluk kullanan anahtar değer çiftleri ile `=` kullanılan komut satırı bağımsız değişken anahtar değeri çiftleri karıştırmayın.

### <a name="switch-mappings"></a>Anahtar eşlemeleri

Anahtar eşlemeleri **anahtar** adı değiştirme mantığına izin verir. <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> Yönteme geçiş değiştirmesözlüğü sağlayın.

Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük komut satırı bağımsız değişkeni tarafından sağlanan anahtarla eşleşen bir anahtar için işaretlenir. Komut satırı anahtarı sözlükte bulunursa, anahtar değeri çiftini uygulamanın yapılandırmasına ayarlamak için sözlük değeri geri aktarılır. Tek bir çizgi ile önceden belirlenmiş herhangi bir komut`-`satırı tuşu için bir anahtar eşlemesi gereklidir ( ).

Haritalama ları sözlük anahtar kurallarını değiştirin:

* Anahtarlar ile `-` başlamalı veya `--`.
* Anahtar eşlemeler sözlüğü yinelenen anahtarları içermemelidir.

Bir anahtar eşleme sözlüğü kullanmak için, `AddCommandLine`çağrı içine geçirin:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

Aşağıdaki kod değiştirilen anahtarların anahtar değerlerini gösterir:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Anahtar değiştirmesini test etmek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Not: Şu `=` anda, tek bir çizgi `-`ile anahtar değiştirme değerleri ayarlamak için kullanılamaz. [Bu GitHub sorununa](https://github.com/dotnet/extensions/issues/3059)bakın.

Anahtar değiştirmesini test etmek için aşağıdaki komut çalışır:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Anahtar eşlemeleri kullanan uygulamalar için, `CreateDefaultBuilder` çağrı bağımsız değişkenleri geçmemelidir. Yöntemin `CreateDefaultBuilder` `AddCommandLine` araması eşlenen anahtarları içermez ve anahtar eşleme sözlüğünden `CreateDefaultBuilder`''ye geçmenin bir yolu yoktur. Çözüm bağımsız değişkenleri geçirmek `CreateDefaultBuilder` için değil, bunun `ConfigurationBuilder` yerine `AddCommandLine` yöntemin yöntemihem bağımsız değişkenleri hem de anahtar eşleme sözlüğü işlemek için izin vermektir.

## <a name="hierarchical-configuration-data"></a>Hiyerarşik yapılandırma verileri

Yapılandırma API'si, hiyerarşik verileri yapılandırma tuşlarında bir sınırlayıcı kullanarak düzleştirmek tarafından hiyerarşik yapılandırma verilerini okur.

[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *appsettings.json* dosyasını içerir:

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

[Örnek indirmedeki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, yapılandırma ayarlarından birkaçını görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Hiyerarşik yapılandırma verilerini okumanın tercih edilen yolu seçenekler deseni kullanmaktır. Daha fazla bilgi için bu belgedeki [Bind hiyerarşik yapılandırma verilerine](#optpat) bakın.

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verilerindeki bir bölümün bölümlerini ve altlarını yalıtmak için yöntemler kullanılabilir. Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection)'de açıklanmıştır.

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Yapılandırma anahtarları ve değerleri

Yapılandırma tuşları:

* Büyük/küçük harf duyarsız. Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak kabul edilir.
* Bir anahtar ve değer birden fazla yapılandırma sağlayıcısında ayarlanırsa, eklenen son sağlayıcının değeri kullanılır. Daha fazla bilgi için [Varsayılan yapılandırmaya](#default)bakın.
* Hiyerarşik anahtarlar
  * Yapılandırma API'si içinde,`:`bir iki nokta üst üste ayırıcı ( ) tüm platformlarda çalışır.
  * Ortam değişkenlerinde, bir kolon ayırıcısı tüm platformlarda çalışmayabilir. Bir çift alt `__`çizgi, , tüm platformlar tarafından desteklenen ve `:`otomatik olarak bir üst üste dönüştürülür.
  * Azure Anahtar Kasası'nda `--` hiyerarşik tuşlar ayırıcı olarak kullanılır. [Azure Key Vault yapılandırma sağlayıcısı,](xref:security/key-vault-configuration) sırların uygulamanın yapılandırmasına yüklendiği zaman otomatik olarak bir `--` `:` anahtarla değiştirir.
* Yapılandırma <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler. Dizi bağlama, [Bind dizisinde bir sınıf bölümüne](#boa) açıklanır.

Yapılandırma değerleri:

* İpler vardır.
* Null değerleri yapılandırmada depolanamaz veya nesnelere bağlanabilir.

<a name="cp"></a>

## <a name="configuration-providers"></a>Yapılandırma sağlayıcıları

Aşağıdaki tablo, ASP.NET Core uygulamalarının kullanabileceği yapılandırma sağlayıcılarını gösterir.

| Sağlayıcı | Yapılandırma sağlar |
| -------- | ----------------------------------- |
| [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration) | Azure Key Vault |
| [Azure App yapılandırma sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure Uygulama Yapılandırması |
| [Komut satırı yapılandırma sağlayıcısı](#clcp) | Komut satırı parametreleri |
| [Özel yapılandırma sağlayıcısı](#custom-configuration-provider) | Özel kaynak |
| [Çevre Değişkenleri yapılandırma sağlayıcısı](#evcp) | Ortam değişkenleri |
| [Dosya yapılandırma sağlayıcısı](#file-configuration-provider) | INI, JSON ve XML dosyaları |
| [Dosya başına anahtar yapılandırma sağlayıcısı](#key-per-file-configuration-provider) | Dizin dosyaları |
| [Bellek yapılandırma sağlayıcısı](#memory-configuration-provider) | Bellek içi koleksiyonlar |
| [Gizli Müdür](xref:security/app-secrets)  | Kullanıcı profili dizinindeki dosya |

Yapılandırma kaynakları, yapılandırma sağlayıcılarının belirtildiği şekilde okunur. Yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynaklarının önceliklerine uyacak şekilde kodolarak sipariş edin.

Yapılandırma sağlayıcılarının tipik bir sırası:

1. *appsettings.json*
1. *appsettings*. `Environment`. *json*
1. [Gizli Müdür](xref:security/app-secrets)
1. [Çevre Değişkenleri yapılandırma sağlayıcısını](#evcp)kullanarak ortam değişkenleri.
1. Komut satırı yapılandırma [sağlayıcısını](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.

Yaygın bir uygulama komut satırı yapılandırma sağlayıcısı diğer sağlayıcılar tarafından ayarlanan yapılandırma geçersiz kılmak için komut satırı bağımsız değişkenleri sağlamak için bir dizi sağlayıcılar son eklemektir.

Sağlayıcıların önceki sırası [varsayılan yapılandırmada](#default)kullanılır.

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Bağlantı dizeleri önekleri

Yapılandırma API'sinin dört bağlantı dizesi ortamı değişkeni için özel işleme kuralları vardır. Bu bağlantı dizeleri, uygulama ortamı için Azure bağlantı dizeleri yapılandırmada yer almaktadır. Tabloda gösterilen öneklerle ortam değişkenleri [varsayılan yapılandırmayla](#default) veya önek sağıldığında uygulamaya `AddEnvironmentVariables`yüklenir.

| Bağlantı dize öneki | Sağlayıcı |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Özel sağlayıcı |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Veritabanı](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önek ile yapılandırmaya yüklendiğinde:

* Yapılandırma anahtarı, ortam değişkeni önekikaldırılarak ve yapılandırma anahtar`ConnectionStrings`bölümü eklenerek oluşturulur ( ).
* Veritabanı bağlantı sağlayıcısını temsil eden (belirtilen sağlayıcı bulunmayanlar `CUSTOMCONNSTR_`hariç) yeni bir yapılandırma anahtar değeri çifti oluşturulur.

| Ortam değişken anahtarı | Dönüştürülmüş yapılandırma anahtarı | Sağlayıcı yapılandırma girişi                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Yapılandırma girişi oluşturulmamadı.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName`:<br>Değer:`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName`:<br>Değer:`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName`:<br>Değer:`System.Data.SqlClient`  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>JSON yapılandırma sağlayıcısı

JSON dosya anahtar değeri çiftlerinden <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> yükler yapılandırması.

Aşırı yüklemeler şunları belirtebilir:

* Dosyanın isteğe bağlı olup olmadığı.
* Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.

Aşağıdaki kodu inceleyin:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Yukarıdaki kod:

* *MyConfig.json* dosyasını aşağıdaki seçeneklerle yüklemek için JSON yapılandırma sağlayıcısını yapılandırır:
  * `optional: true`: Dosya isteğe bağlıdır.
  * `reloadOnChange: true`: Değişiklikler kaydedildiğinde dosya yeniden yüklenir.
* *MyConfig.json* dosyasından önce [varsayılan yapılandırma sağlayıcılarını](#default) okur. *MyConfig.json* dosyasındaki ayarlar, [Çevre değişkenleri yapılandırma sağlayıcısı](#evcp) ve Komut satırı yapılandırma [sağlayıcısı](#clcp)da dahil olmak üzere varsayılan yapılandırma sağlayıcılarındaki ayarları geçersiz kılar.

Genellikle, [Çevre değişkenleri yapılandırma sağlayıcısında](#evcp) ve [Komut satırı yapılandırma sağlayıcısında](#clcp)ayarlanan değerleri geçersiz kılan özel bir JSON dosyası ***istemezsinüz.***

Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve birkaç yapılandırma sağlayıcısı ekler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

Önceki kodda, *MyConfig.json* ve *MyConfig*ayarları . `Environment`. *json* dosyaları:

* *appsettings.json* ve *uygulama ayarları*geçersiz kılın. `Environment`. *json* dosyaları.
* [Çevre değişkenleri yapılandırma sağlayıcısı](#evcp) ve Komut satırı [yapılandırma sağlayıcısındaki](#clcp)ayarlar tarafından geçersiz kılınmıştır.

[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *MyConfig.json* dosyasını içerir:

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Dosya yapılandırma sağlayıcısı

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>dosya sisteminden yapılandırma yükleme için taban sınıftır. Aşağıdaki yapılandırma sağlayıcıları ndan `FileConfigurationProvider`kaynaklanır:

* [INI yapılandırma sağlayıcısı](#ini-configuration-provider)
* [JSON yapılandırma sağlayıcısı](#jcp)
* [XML yapılandırma sağlayıcısı](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI yapılandırma sağlayıcısı

Çalışma <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> zamanında INI dosya anahtar değeri çiftlerinden yükler yapılandırması.

Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve birkaç yapılandırma sağlayıcısı ekler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

Önceki kodda, *MyIniConfig.ini* ve *MyIniConfig*ayarları . `Environment`. *ini* dosyaları aşağıdaki ayarlartarafından geçersiz kılınmaktadır:

* [Çevre değişkenleri yapılandırma sağlayıcısı](#evcp)
* [Komut satırı yapılandırma sağlayıcısı.](#clcp)

[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *MyIniConfig.ini* dosyasını içerir:

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>XML yapılandırma sağlayıcısı

XML <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> dosya anahtar değeri çiftlerinden gelen yükler yapılandırması çalışma zamanında.

Aşağıdaki kod tüm yapılandırma sağlayıcılarını temizler ve birkaç yapılandırma sağlayıcısı ekler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

Önceki kodda, *MyXMLFile.xml* ve *MyXMLFile*ayarları . `Environment`. *xml* dosyaları aşağıdaki ayarlarla geçersiz kılınır:

* [Çevre değişkenleri yapılandırma sağlayıcısı](#evcp)
* [Komut satırı yapılandırma sağlayıcısı.](#clcp)

[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki *MyXMLFile.xml* dosyasını içerir:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

[Örnek karşıdan yüklemeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod, önceki yapılandırma ayarlarından birkaçını görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

`name` Öznitelik öğeleri ayırt etmek için kullanılırsa aynı öğe adı kullanan yinelenen öğeler çalışır:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Aşağıdaki kod önceki yapılandırma dosyasını okur ve anahtarları ve değerleri görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Öznitelikler değerleri sağlamak için kullanılabilir:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:

* key:öznitelik
* bölüm:key:öznitelik

## <a name="key-per-file-configuration-provider"></a>Dosya başına anahtar yapılandırma sağlayıcısı

Yapılandırma <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> anahtar değeri çiftleri olarak bir dizin dosyaları kullanır. Anahtar dosya adıdır. Değer, dosyanın içeriğini içerir. Dosya başına Anahtar yapılandırma sağlayıcısı Docker barındırma senaryolarında kullanılır.

Dosya başına anahtar yapılandırmasını etkinleştirmek <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> için, uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini bir .' `directoryPath` Dosyalara mutlak bir yol olmalıdır.

Aşırı yükleme izni belirterek:

* Kaynağı `Action<KeyPerFileConfigurationSource>` yapılandıran bir temsilci.
* Dizinin isteğe bağlı olup olmadığı ve dizine giden yol.

Çift alt çizgi`__`( ) dosya adlarında yapılandırma anahtarı delimiter olarak kullanılır. Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını `Logging:LogLevel:System`üretir.

Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Bellek yapılandırma sağlayıcısı

Yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> anahtar değeri çiftleri olarak bellek içi bir koleksiyon kullanır.

Aşağıdaki kod yapılandırma sistemine bir bellek koleksiyonu ekler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) aşağıdaki kod önceki yapılandırma ayarlarını görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Önceki kodda, `config.AddInMemoryCollection(Dict)` varsayılan yapılandırma [sağlayıcıları](#default)sonra eklenir. Yapılandırma sağlayıcılarını sıralamak için [Bkz. JSON yapılandırma sağlayıcısı.](#jcp)

Yapılandırma sağlayıcılarını sıralamak için [Bkz. JSON yapılandırma sağlayıcısı.](#jcp)

Bkz. Başka bir örnek `MemoryConfigurationProvider`için [bind bir dizi](#boa) kullanarak.

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirtilen bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen türe dönüştürür:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

Önceki kodda, yapılandırmada `NumberKey` bulunmazsa, varsayılan değeri `99` kullanılır.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren ve Var

Aşağıdaki örnekler için aşağıdaki *MyAlt.json* dosyasını göz önünde bulundurun:

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Aşağıdaki kod yapılandırma sağlayıcılarına *MyAlt.json* ekler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) belirtilen alt bölüm tuşu ile bir yapılandırma alt bölümü döndürür.

Aşağıdaki kod için `section1`değerleri döndürür:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Aşağıdaki kod için `section2:subsection0`değerleri döndürür:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection`asla `null`dönmez. Eşleşen bir bölüm bulunamazsa, `IConfigurationSection` boş döndürülür.

Eşleşen `GetSection` bir bölüm <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> döndürdüğünde doldurulmaz. A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> ve bölüm olduğunda döndürülür.

### <a name="getchildren-and-exists"></a>GetChildren ve Var

Aşağıdaki kod [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) çağırır ve `section2:subsection0`değerleri döndürür:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Önceki kod [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) bölümünün var olduğunu doğrulamak için çağırır:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Bir diziyi bağlama

[ConfigurationBinder.Bind,](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) yapılandırma anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler. Sayısal anahtar kesimini ortaya çıkaran herhangi bir dizi [biçimi, bir POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) sınıf dizisine dizi bağlama yeteneğine sahiptir.

[Örnek indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) *MyArray.json* düşünün:

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Aşağıdaki kod yapılandırma sağlayıcılarına *MyArray.json* ekler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

Aşağıdaki kod yapılandırmayı okur ve değerleri görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Önceki kod aşağıdaki çıktıyı döndürür:

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

Önceki çıktıda, Dizin 3 `value40`değeri `"4": "value40",` vardır , *MyArray.json*karşılık gelen . Bağlı dizi endeksleri süreklidir ve yapılandırma anahtar dizini bağlı değildir. Yapılandırma bağlayıcısı null değerlerini bağlama veya bağlı nesnelerde null girişleri oluşturma yeteneğine sahip değildir

Aşağıdaki <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> kod, `array:entries` yapılandırmayı uzantı yöntemiyle yükler:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Aşağıdaki kod yapılandırmayı `arrayDict` `Dictionary` okur ve değerleri görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Önceki kod aşağıdaki çıktıyı döndürür:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

Bağlı &num;nesnedeki Dizin `array:4` 3, yapılandırma anahtarı ve değeri `value4`için yapılandırma verilerini tutar. Bir dizi içeren yapılandırma verileri bağlandığında, nesne oluşturulurken yapılandırma verilerini yinelemek için yapılandırma anahtarlarındaki dizi dizinleri kullanılır. Yapılandırma verilerinde null değeri tutulamaz ve yapılandırma anahtarlarındaki bir dizi bir veya daha fazla endeksi atladığında bağlı bir nesnede null değerli bir giriş oluşturulmaz.

Dizin &num;3 için eksik yapılandırma öğesi, dizin `ArrayExample` &num;3 anahtar/değer çiftini okuyan herhangi bir yapılandırma sağlayıcısı tarafından örneğine bağlanmadan önce sağlanabilir. Örnek indirmeden aşağıdaki *Value3.json* dosyasını göz önünde bulundurun:

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Aşağıdaki kod *Value3.json* ve yapılandırma `arrayDict` `Dictionary`içerir:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

Aşağıdaki kod önceki yapılandırmayı okur ve değerleri görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Önceki kod aşağıdaki çıktıyı döndürür:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcılarının gerekli değildir.

## <a name="custom-configuration-provider"></a>Özel yapılandırma sağlayıcısı

Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar değeri çiftleri okuyan temel bir yapılandırma sağlayıcısının nasıl oluşturulturolduğunu gösterir.

Sağlayıcı aşağıdaki özelliklere sahiptir:

* EF bellek veritabanı gösteri amacıyla kullanılır. Bağlantı dizesi gerektiren bir veritabanı kullanmak `ConfigurationBuilder` için, bağlantı dizesini başka bir yapılandırma sağlayıcısından sağlamak için ikincil bir uygulama uygulayın.
* Sağlayıcı başlangıçta yapılandırma içine bir veritabanı tablosu okur. Sağlayıcı, veritabanını anahtar başına sorgulamıyor.
* Yeniden yükleme-on-change uygulanmaz, bu nedenle uygulama başladıktan sonra veritabanını güncelleştirmenin uygulamanın yapılandırması üzerinde hiçbir etkisi yoktur.

Yapılandırma `EFConfigurationValue` değerlerini veritabanında depolamak için bir varlık tanımlayın.

*Modeller/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Yapılandırılan `EFConfigurationContext` değerleri depolamak ve erişmek için bir ekleyin.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Uygulayan bir sınıf <xref:Microsoft.Extensions.Configuration.IConfigurationSource>oluşturun.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

'den <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>devralarak özel yapılandırma sağlayıcısı oluşturun. Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatılmasını sağlar. [Yapılandırma anahtarları büyük/küçük harf duyarsız olduğundan,](#keys)veritabanını başlatmak için kullanılan sözlük büyük/küçük harf duyarlı karşılaştırıcı[(StringComparer.OrdinalIgnoreCase)](xref:System.StringComparer.OrdinalIgnoreCase)ile oluşturulur.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Uzatma `AddEFConfiguration` yöntemi, yapılandırma kaynağını bir `ConfigurationBuilder`.'ye eklemeye izin verir.

*Uzantılar/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Aşağıdaki `EFConfigurationProvider` *kod, Program.cs*özel in nasıl kullanılacağını gösterir:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Başlangıç'ta erişim yapılandırması

Aşağıdaki kod yapılandırma verilerini `Startup` yöntemlerle görüntüler:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişim örneği için Bkz. [Uygulama başlangıç: Kolaylık yöntemleri.](xref:fundamentals/startup#convenience-methods)

## <a name="access-configuration-in-razor-pages"></a>Razor Pages'de erişim yapılandırması

Aşağıdaki kod, bir Jilet Sayfasında yapılandırma verilerini görüntüler:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>MVC görünüm dosyasındaki yapılandırmaya erişim

Aşağıdaki kod, yapılandırma verilerini MVC görünümünde görüntüler:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Ana bilgisayar ve uygulama yapılandırması

Uygulama yapılandırılmadan ve başlatılmadan önce, bir *ana bilgisayar* yapılandırılır ve başlatılır. Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur. Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır. Ana bilgisayar yapılandırma anahtar değeri çiftleri de uygulamanın yapılandırmasında yer alıyor. Ana bilgisayar oluşturulurken yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının ana <xref:fundamentals/index#host>bilgisayar yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz.

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Varsayılan ana bilgisayar yapılandırması

[Web Ana Bilgisayar'ı](xref:fundamentals/host/web-host)kullanırken varsayılan yapılandırma yla ilgili ayrıntılar [için, bu konunun ASP.NET Core 2.2 sürümüne](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)bakın.

* Ana bilgisayar yapılandırması şu andan sağlanır:
  * Çevre Değişkenleri yapılandırma `DOTNET_` sağlayıcısını `DOTNET_ENVIRONMENT`kullanarak (örneğin, ) önceden belirlenmiş ortam [değişkenleri.](#environment-variables-configuration-provider) Yapılandırma anahtar`DOTNET_`değeri çiftleri yüklendiğinde önek ( ) çıkarılır.
  * Komut satırı yapılandırma [sağlayıcısını](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.
* Web Host varsayılan yapılandırma`ConfigureWebHostDefaults`kuruldu ( ):
  * Kerkenez web sunucusu olarak kullanılır ve uygulamanın yapılandırma sağlayıcıları kullanılarak yapılandırılır.
  * Ana Bilgisayar Filtreleme Ara Ware ekleyin.
  * Çevre değişkeni `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true`' ne ayarlanmışsa, İlezli Üstbilgi Aralığı ekle.
  * IIS tümleştirmesini etkinleştirin.

## <a name="other-configuration"></a>Diğer yapılandırma

Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir. Core uygulamalarını çalıştırmanın ve ASP.NET barındırmanın diğer yönleri, bu başlıkta yer almayan yapılandırma dosyaları kullanılarak yapılandırılır:

* *launch.json*/*launchSettings.json* Geliştirme ortamı için yapılandırma dosyaları araç, açıklanan:
  * In <xref:fundamentals/environments#development>.
  * Dosyaların Geliştirme senaryoları için ASP.NET Temel uygulamaları yapılandırmak için kullanıldığı belgeler kümesi boyunca.
* *web.config* bir sunucu yapılandırma dosyası, aşağıdaki konularda açıklanan:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

ASP.NET önceki sürümlerinden uygulama yapılandırmasının geçirilmesi <xref:migration/proper-to-2x/index#store-configurations>hakkında daha fazla bilgi için bkz.

## <a name="add-configuration-from-an-external-assembly"></a>Harici bir montajdan yapılandırma ekleme

Uygulama, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> uygulamanın sınıfının dışındaki harici bir derlemeden başlangıçta bir `Startup` uygulamaya geliştirme eklemeye olanak tanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Yapılandırma kaynak kodu](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core'daki uygulama yapılandırması, *yapılandırma sağlayıcıları*tarafından kurulan anahtar değer çiftlerini temel adamıştır. Yapılandırma sağlayıcıları yapılandırma verilerini çeşitli yapılandırma kaynaklarından anahtar değer çiftleri halinde okur:

* Azure Key Vault
* Azure Uygulama Yapılandırması
* Komut satırı bağımsız değişkenleri
* Özel sağlayıcılar (yüklü veya oluşturulmuş)
* Dizin dosyaları
* Ortam değişkenleri
* Bellek içi .NET nesneleri
* Ayarlar dosyaları

Yaygın yapılandırma sağlayıcısı senaryoları için yapılandırma paketleri[(Microsoft.Extensions.Configuration)](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app)dahildir.

Örnek uygulamada ve örnek uygulamada yer <xref:Microsoft.Extensions.Configuration> alan kod örnekleri ad alanını kullanır:

```csharp
using Microsoft.Extensions.Configuration;
```

*Seçenekler deseni,* bu konuda açıklanan yapılandırma kavramlarının bir uzantısıdır. Seçenekler, ilgili ayar gruplarını temsil etmek için sınıfları kullanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Ana bilgisayar ve uygulama yapılandırması

Uygulama yapılandırılmadan ve başlatılmadan önce, bir *ana bilgisayar* yapılandırılır ve başlatılır. Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur. Hem uygulama hem de ana bilgisayar, bu konuda açıklanan yapılandırma sağlayıcıları kullanılarak yapılandırılır. Ana bilgisayar yapılandırma anahtar değeri çiftleri de uygulamanın yapılandırmasında yer alıyor. Ana bilgisayar oluşturulurken yapılandırma sağlayıcılarının nasıl kullanıldığı ve yapılandırma kaynaklarının ana <xref:fundamentals/index#host>bilgisayar yapılandırmasını nasıl etkilediği hakkında daha fazla bilgi için bkz.

## <a name="other-configuration"></a>Diğer yapılandırma

Bu konu yalnızca *uygulama yapılandırması*ile ilgilidir. Core uygulamalarını çalıştırmanın ve ASP.NET barındırmanın diğer yönleri, bu başlıkta yer almayan yapılandırma dosyaları kullanılarak yapılandırılır:

* *launch.json*/*launchSettings.json* Geliştirme ortamı için yapılandırma dosyaları araç, açıklanan:
  * In <xref:fundamentals/environments#development>.
  * Dosyaların Geliştirme senaryoları için ASP.NET Temel uygulamaları yapılandırmak için kullanıldığı belgeler kümesi boyunca.
* *web.config* bir sunucu yapılandırma dosyası, aşağıdaki konularda açıklanan:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

ASP.NET önceki sürümlerinden uygulama yapılandırmasının geçirilmesi <xref:migration/proper-to-2x/index#store-configurations>hakkında daha fazla bilgi için bkz.

## <a name="default-configuration"></a>Varsayılan yapılandırma

Core [dotnet ASP.NET](/dotnet/core/tools/dotnet-new) yeni şablonları temel <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> alan web uygulamaları, bir ana bilgisayar inşa ederken çağrı yapar. `CreateDefaultBuilder`aşağıdaki sırada uygulama için varsayılan yapılandırma sağlar:

Aşağıdakiler [Web Barındırma'nı](xref:fundamentals/host/web-host)kullanan uygulamalar için geçerlidir. [Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanırken varsayılan yapılandırmayla ilgili ayrıntılar için [bu konunun en son sürümüne](xref:fundamentals/configuration/index)bakın.

* Ana bilgisayar yapılandırması şu andan sağlanır:
  * Çevre Değişkenleri Yapılandırma `ASPNETCORE_` Sağlayıcısı'nı `ASPNETCORE_ENVIRONMENT`kullanarak (örneğin, ) önceden belirlenmiş ortam [değişkenleri.](#environment-variables-configuration-provider) Yapılandırma anahtar`ASPNETCORE_`değeri çiftleri yüklendiğinde önek ( ) çıkarılır.
  * Komut satırı Yapılandırma [Sağlayıcısı'nı](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.
* Uygulama yapılandırması şu andan sağlanır:
  * [dosya yapılandırma sağlayıcısı](#file-configuration-provider)kullanarak *appsettings.json* .
  * *ayarları. {Environment}.json* [Dosya Yapılandırma Sağlayıcısı'nı](#file-configuration-provider)kullanarak.
  * [Uygulama](xref:security/app-secrets) giriş montajını `Development` kullanarak ortamda çalıştığında Gizli Yönetici.
  * Çevre Değişkenleri Yapılandırma Sağlayıcısı'nı kullanarak ortam [değişkenleri.](#environment-variables-configuration-provider)
  * Komut satırı Yapılandırma [Sağlayıcısı'nı](#command-line-configuration-provider)kullanarak komut satırı bağımsız değişkenleri.

## <a name="security"></a>Güvenlik

Hassas yapılandırma verilerini güvence altına almak için aşağıdaki uygulamaları benimseyin:

* Parolaları veya diğer hassas verileri asla yapılandırma sağlayıcı kodunda veya düz metin yapılandırma dosyalarında depolamayın.
* Üretim sırlarını geliştirme veya test ortamlarında kullanmayın.
* Yanlışlıkla bir kaynak kodu deposuna işlenmeyecek şekilde projenin dışındaki sırları belirtin.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>&ndash; Hassas verileri depolamak için ortam değişkenlerini kullanma konusunda öneriler içerir. Gizli Yönetici, kullanıcı sırlarını yerel sistemdeki bir JSON dosyasında depolamak için Dosya Yapılandırma Sağlayıcısı'nı kullanır. Dosya Yapılandırma Sağlayıcısı daha sonra bu konuda açıklanmıştır.

[Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) ASP.NET Core uygulamaları için uygulama sırlarını güvenle saklar. Daha fazla bilgi için bkz. <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Hiyerarşik yapılandırma verileri

Yapılandırma API'si, hiyerarşik verileri yapılandırma tuşlarında bir sınırlayıcı kullanarak düzleştirmek le hiyerarşik yapılandırma verilerini koruyabilme yeteneğine sahiptir.

Aşağıdaki JSON dosyasında, dört anahtar iki bölümden oluşan yapılandırılmış bir hiyerarşide bulunur:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

Dosya yapılandırmaya okunduğunda, yapılandırma kaynağının özgün hiyerarşik veri yapısını korumak için benzersiz anahtarlar oluşturulur. Bölümler ve anahtarlar, orijinal yapıyı korumak`:`için bir kolon () kullanımı ile düzleştirilmiştir:

* bölüm0:key0
* bölüm0:key1
* bölüm1:key0
* bölüm1:key1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>ve <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> yapılandırma verilerindeki bir bölümün bölümlerini ve altlarını yalıtmak için yöntemler kullanılabilir. Bu yöntemler daha sonra [GetSection, GetChildren ve Exists](#getsection-getchildren-and-exists)'de açıklanmıştır.

## <a name="conventions"></a>Kurallar

### <a name="sources-and-providers"></a>Kaynaklar ve sağlayıcılar

Uygulama başlatmada, yapılandırma kaynakları yapılandırma sağlayıcılarının belirtildiği şekilde okunur.

Değişiklik algılamasını uygulayan yapılandırma sağlayıcıları, temel bir ayar değiştirildiğinde yapılandırmayı yeniden yükleme yeteneğine sahiptir. Örneğin, Dosya Yapılandırma Sağlayıcısı (bu konuda daha sonra açıklanmıştır) ve [Azure Anahtar Kasası Yapılandırma Sağlayıcısı](xref:security/key-vault-configuration) değişiklik algılaması uygular.

<xref:Microsoft.Extensions.Configuration.IConfiguration>uygulamanın [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection) konteynerinde kullanılabilir. <xref:Microsoft.Extensions.Configuration.IConfiguration>sınıf için yapılandırma elde <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> etmek için <xref:Microsoft.AspNetCore.Mvc.Controller> bir Razor Pages veya MVC içine enjekte edilebilir.

Aşağıdaki örneklerde, `_config` alan yapılandırma değerlerine erişmek için kullanılır:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Yapılandırma sağlayıcıları, ana bilgisayar tarafından ayarlandığında kullanılamadığından, DI'den yararlanamaz.

### <a name="keys"></a>Anahtarlar

Yapılandırma anahtarları aşağıdaki kuralları benimser:

* Anahtarlar büyük/küçük harf duyarsızdır. Örneğin, `ConnectionString` ve `connectionstring` eşdeğer anahtarlar olarak kabul edilir.
* Aynı anahtar için bir değer aynı veya farklı yapılandırma sağlayıcıları tarafından ayarlanırsa, anahtar üzerinde ayarlanan son değer kullanılan değerdir.
* Hiyerarşik anahtarlar
  * Yapılandırma API'si içinde,`:`bir iki nokta üst üste ayırıcı ( ) tüm platformlarda çalışır.
  * Ortam değişkenlerinde, bir kolon ayırıcısı tüm platformlarda çalışmayabilir. Bir çift alt`__`çizgi ( ) tüm platformlar tarafından desteklenir ve otomatik olarak bir üst üste dönüştürülür.
  * Azure Anahtar Kasası'nda `--` hiyerarşik tuşlar ayırıcı olarak (iki tire) kullanır. Sırlar uygulamanın yapılandırmasına yüklendiğinde tireleri bir üst nokta ile değiştirmek için kod yazın.
* Yapılandırma <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler. Dizi bağlama, [Bind dizisinde bir sınıf bölümüne](#bind-an-array-to-a-class) açıklanır.

### <a name="values"></a>Değerler

Yapılandırma değerleri aşağıdaki kuralları benimser:

* Değerler dizeleri vardır.
* Null değerleri yapılandırmada depolanamaz veya nesnelere bağlanabilir.

## <a name="providers"></a>Sağlayıcılar

Aşağıdaki tablo, ASP.NET Core uygulamalarının kullanabileceği yapılandırma sağlayıcılarını gösterir.

| Sağlayıcı | Yapılandırma sağlar&hellip; |
| -------- | ----------------------------------- |
| [Azure Key Vault Yapılandırma Sağlayıcısı](xref:security/key-vault-configuration) (*Güvenlik* konuları) | Azure Key Vault |
| [Azure Uygulama Yapılandırma Sağlayıcısı](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure belgeleri) | Azure Uygulama Yapılandırması |
| [Komut Satırı Yapılandırma Sağlayıcısı](#command-line-configuration-provider) | Komut satırı parametreleri |
| [Özel yapılandırma sağlayıcısı](#custom-configuration-provider) | Özel kaynak |
| [Çevre Değişkenleri Yapılandırma Sağlayıcısı](#environment-variables-configuration-provider) | Ortam değişkenleri |
| [Dosya Yapılandırma Sağlayıcısı](#file-configuration-provider) | Dosyalar (INI, JSON, XML) |
| [Dosya başına anahtar Yapılandırma Sağlayıcısı](#key-per-file-configuration-provider) | Dizin dosyaları |
| [Bellek Yapılandırma Sağlayıcısı](#memory-configuration-provider) | Bellek içi koleksiyonlar |
| [Kullanıcı sırları (Secret Manager)](xref:security/app-secrets) *(Güvenlik* konuları) | Kullanıcı profili dizinindeki dosya |

Yapılandırma kaynakları, yapılandırma sağlayıcılarının başlangıçta belirtilmiş olması sırasına göre okunur. Bu konuda açıklanan yapılandırma sağlayıcıları, kodun düzenlediği sırada değil, alfabetik sırada açıklanır. Yapılandırma sağlayıcılarını, uygulamanın gerektirdiği temel yapılandırma kaynaklarının önceliklerine uyacak şekilde kodolarak sipariş edin.

Yapılandırma sağlayıcılarının tipik bir sırası:

1. Dosyalar (*appsettings.json*, *appsettings.{ Environment}.json*, `{Environment}` uygulamanın geçerli barındırma ortamı nerededir)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Kullanıcı sırları (Secret Manager)](xref:security/app-secrets) (Yalnızca geliştirme ortamı)
1. Ortam değişkenleri
1. Komut satırı bağımsız değişkenleri

Komut satırı yapılandırma sağlayıcısını, komut satırı bağımsız değişkenlerinin diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılmasına izin vermek için bir dizi sağlayıcıya son olarak konumlandırmak yaygın bir uygulamadır.

Yeni bir ana bilgisayar oluşturucu ile `CreateDefaultBuilder`başharfe geçtiğinde, önceki sağlayıcı sıraları kullanılır. Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.

## <a name="configure-the-host-builder-with-useconfiguration"></a>Ev sahibi oluşturucuyu UseConfiguration ile yapılandırın

Ana bilgisayar oluşturucuyu <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> yapılandırmak için, yapılandırmaile birlikte ana bilgisayar oluşturucuyu çağırın.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>YapılandırmaAppConfiguration

Aşağıdakiler tarafından otomatik olarak eklenenlere ek olarak uygulamanın yapılandırma `CreateDefaultBuilder`sağlayıcılarını belirtmek için ana bilgisayarını kurarken arayın: `ConfigureAppConfiguration`

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Komut satırı bağımsız değişkenleriyle önceki yapılandırmayı geçersiz kılma

Komut satırı bağımsız değişkenleriyle geçersiz kılınabilecek uygulama yapılandırmasını sağlamak için son çağrıyı arayın: `AddCommandLine`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>CreateDefaultBuilder tarafından eklenen sağlayıcıları kaldırma

Tarafından eklenen sağlayıcıları `CreateDefaultBuilder`kaldırmak için, [önce IConfigurationBuilder.Sources'da](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) [Clear'i](/dotnet/api/system.collections.generic.icollection-1.clear) arayın:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Uygulama başlatma sırasında yapılandırmayı tüketin

Uygulamada sağlanan `ConfigureAppConfiguration` yapılandırma, uygulamanın başlatılması sırasında kullanılabilir. `Startup.ConfigureServices` Daha fazla bilgi için başlangıç bölümünde [Access yapılandırması](#access-configuration-during-startup) bölümüne bakın.

## <a name="command-line-configuration-provider"></a>Komut Satırı Yapılandırma Sağlayıcısı

Çalışma <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> zamanında komut satırı bağımsız değişken anahtar değeri çiftlerinden yükler yapılandırması.

Komut satırı yapılandırmasını <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> etkinleştirmek için, uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemi .

`AddCommandLine`çağrıldığında `CreateDefaultBuilder(string [])` otomatik olarak çağrılır. Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.

`CreateDefaultBuilder`ayrıca yükler:

* *Appsettings.json* ve appsettings'ten isteğe bağlı *yapılandırma.{ Çevre}.json* dosyaları.
* Geliştirme ortamında [kullanıcı sırları (Gizli Yönetici).](xref:security/app-secrets)
* Ortam değişkenleri.

`CreateDefaultBuilder`Komut satırı Yapılandırma Sağlayıcısı son ekler. Çalışma zamanında geçirilen komut satırı bağımsız değişkenleri, diğer sağlayıcılar tarafından ayarlanan yapılandırmayı geçersiz kılar.

`CreateDefaultBuilder`ev sahibi inşa edildiğinde hareket eder. Bu nedenle, komut satırı `CreateDefaultBuilder` yapılandırması tarafından etkinleştirilen ana bilgisayar yapısının nasıl yapılandırıldığı etkileyebilir.

ASP.NET Core şablonlarına dayalı uygulamalar `AddCommandLine` için, zaten `CreateDefaultBuilder`. Ek yapılandırma sağlayıcıları eklemek ve komut satırı bağımsız değişkenleri olan bu sağlayıcılardan yapılandırmayı geçersiz `ConfigureAppConfiguration` kılma `AddCommandLine` yeteneğini korumak için, uygulamanın ek sağlayıcılarını çağırın ve en son çağırın.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Örnek**

Örnek uygulama, ana bilgisayarı oluşturmak `CreateDefaultBuilder` için <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>statik kolaylık yönteminden yararlanır ve bu yöntem .

1. Projenin dizininde bir komut istemi açın.
1. Komuta bir komut satırı `dotnet run` bağımsız `dotnet run CommandLineKey=CommandLineValue`değişkeni ver.
1. Uygulama çalışmaya devam ettikten sonra, uygulamanın `http://localhost:5000`bir tarayıcısını .
1. Çıktının, ''ye sağlanan yapılandırma komut satırı bağımsız değişkeni için anahtar değer çiftini `dotnet run`içerdiğini gözlemleyin.

### <a name="arguments"></a>Bağımsız Değişkenler

Değer, eşitler işaretini`=`(), veya değer bir alanı`--` `/`takip ettiğinde anahtarın bir önek (veya ) olması gerekir. Eşitler işareti kullanılıyorsa (örneğin, `CommandLineKey=`değer gerekli değildir).

| Anahtar öneki               | Örnek                                                |
| ------------------------ | ------------------------------------------------------ |
| Önek yok                | `CommandLineKey1=value1`                               |
| İki tire (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| İleri eğik çizgi (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

Aynı komut içinde, eşit işareti kullanan komut satırı bağımsız değişken anahtar değer çiftlerini boşluk kullanan anahtar değer çiftleri ile karıştırmayın.

Örnek komutlar:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Anahtar eşlemeleri

Anahtar eşlemeleri anahtar adı değiştirme mantığına izin verir. Bir ile el ile <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yapılandırma inşa ederken, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> yöntemiçin anahtar değiştirmeleri bir sözlük sağlayın.

Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük komut satırı bağımsız değişkeni tarafından sağlanan anahtarla eşleşen bir anahtar için işaretlenir. Komut satırı anahtarı sözlükte bulunursa, anahtar değeri çiftini uygulamanın yapılandırmasına ayarlamak için sözlük değeri (anahtar değiştirme) geri aktarılır. Tek bir çizgi ile önceden belirlenmiş herhangi bir komut`-`satırı tuşu için bir anahtar eşlemesi gereklidir ( ).

Haritalama ları sözlük anahtar kurallarını değiştirin:

* Anahtarlar bir çizgi (`-`) veya çift`--`çizgi ( ile başlamalıdır).
* Anahtar eşlemeler sözlüğü yinelenen anahtarları içermemelidir.

Anahtar eşlemesözlüğü oluşturun. Aşağıdaki örnekte, iki anahtar eşleme oluşturulur:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Ana bilgisayar oluşturulduğunda, anahtar eşlemeleri sözlüğüyle arayın: `AddCommandLine`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Anahtar eşlemeleri kullanan uygulamalar için, `CreateDefaultBuilder` çağrı bağımsız değişkenleri geçmemelidir. Yöntemin `CreateDefaultBuilder` `AddCommandLine` araması eşlenen anahtarları içermez ve anahtar eşleme sözlüğünden `CreateDefaultBuilder`''ye geçmenin bir yolu yoktur. Çözüm bağımsız değişkenleri geçirmek `CreateDefaultBuilder` için değil, bunun `ConfigurationBuilder` yerine `AddCommandLine` yöntemin yöntemihem bağımsız değişkenleri hem de anahtar eşleme sözlüğü işlemek için izin vermektir.

Anahtar eşlemeleri sözlüğü oluşturulduktan sonra, aşağıdaki tabloda gösterilen verileri içerir.

| Anahtar       | Değer             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Uygulamayı başlatırken anahtar eşlenen tuşlar kullanılırsa, yapılandırma sözlük tarafından sağlanan anahtardaki yapılandırma değerini alır:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Önceki komutu çalıştırdıktan sonra yapılandırma aşağıdaki tabloda gösterilen değerleri içerir.

| Anahtar               | Değer    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Çevre Değişkenleri Yapılandırma Sağlayıcısı

Çalışma <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> zamanında ortam değişken anahtar değeri çiftlerinden yükler yapılandırması.

Ortam değişkenleri yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> bir .'

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure Uygulama Hizmeti,](https://azure.microsoft.com/services/app-service/) Azure Portalı'nda Çevre Değişkenleri Yapılandırma Sağlayıcısı'nı kullanarak uygulama yapılandırmasını geçersiz kılabilecek ortam değişkenleri ayarlamaya izin verir. Daha fazla bilgi için Azure [Uygulamaları: Azure Portalı'nı kullanarak uygulama yapılandırmasını geçersiz kılın.](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)

`AddEnvironmentVariables`yeni bir ana bilgisayar [oluşturucu Web Host](xref:fundamentals/host/web-host) ile baş harfe döndüğünde ve `CreateDefaultBuilder` çağrıldığında ana bilgisayar [host configuration](#host-versus-app-configuration) `ASPNETCORE_` yapılandırması için önceden belirlenmiş ortam değişkenlerini yüklemek için kullanılır. Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.

`CreateDefaultBuilder`ayrıca yükler:

* Önek olmadan arayarak `AddEnvironmentVariables` önceden ayarlanmamış ortam değişkenlerinden uygulama yapılandırması.
* *Appsettings.json* ve appsettings'ten isteğe bağlı *yapılandırma.{ Çevre}.json* dosyaları.
* Geliştirme ortamında [kullanıcı sırları (Gizli Yönetici).](xref:security/app-secrets)
* Komut satırı bağımsız değişkenleri.

Yapılandırma kullanıcı sırları ve *uygulama* dosyalarından oluşturulduktan sonra Çevre Değişkenleri Yapılandırma Sağlayıcısı çağrılır. Sağlayıcıyı bu konumda çağırmak, çalışma zamanında okunan ortam değişkenlerinin kullanıcı sırları ve *uygulama* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılmasına olanak tanır.

Ek ortam değişkenlerinden uygulama yapılandırması sağlamak için, uygulamanın ek sağlayıcılarını arayın `ConfigureAppConfiguration` ve önek ile arayın: `AddEnvironmentVariables`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Diğer `AddEnvironmentVariables` sağlayıcılardan gelen değerleri geçersiz kılmak için verilen önek içeren ortam değişkenlerine izin vermek için son çağrıyı verin.

**Örnek**

Örnek uygulama, ana bilgisayarı oluşturmak `CreateDefaultBuilder` için `AddEnvironmentVariables`statik kolaylık yönteminden yararlanır ve bu yöntem .

1. Örnek uygulamayı çalıştırın. Uygulamanın tarayıcısını ' `http://localhost:5000`dan aç.
1. Çıktının ortam değişkeni `ENVIRONMENT`için anahtar değer çiftini içerdiğini gözlemleyin. Değer, genellikle `Development` yerel olarak çalışırken uygulamanın çalıştırıldığı ortamı yansıtır.

Uygulama tarafından işlenen ortam değişkenlerinin listesini kısa tutmak için uygulama ortam değişkenlerini filtreler. Örnek uygulamanın *Pages/Index.cshtml.cs* dosyasına bakın.

Uygulamanın kullanabileceği tüm ortam değişkenlerini ortaya çıkarmak `FilteredConfiguration` için *Pages/Index.cshtml.cs'deki* leri aşağıdaki lerle değiştirin:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Ön Ekler

`AddEnvironmentVariables` Yönteme bir önek verilirken uygulamanın yapılandırmasına yüklenen ortam değişkenleri filtrelenir. Örneğin, önekteki `CUSTOM_`ortam değişkenlerini filtrelemek için önek'i yapılandırma sağlayıcısına sağlayın:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Yapılandırma anahtar değeri çiftleri oluşturulduğunda önek çıkarılır.

Ana bilgisayar oluşturucu oluşturulduğunda, ana bilgisayar yapılandırması ortam değişkenleri tarafından sağlanır. Bu ortam değişkenleri için kullanılan önek hakkında daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.

**Bağlantı dizeleri önekleri**

Yapılandırma API'si, uygulama ortamı için Azure bağlantı dizelerini yapılandırmada yer alan dört bağlantı dize skalası değişkeni için özel işleme kurallarına sahiptir. Tabloda gösterilen öneklerle ortam değişkenleri, ''ye önek sağedilmezse uygulamaya `AddEnvironmentVariables`yüklenir.

| Bağlantı dize öneki | Sağlayıcı |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Özel sağlayıcı |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Veritabanı](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Bir ortam değişkeni keşfedildiğinde ve tabloda gösterilen dört önek ile yapılandırmaya yüklendiğinde:

* Yapılandırma anahtarı, ortam değişkeni önekikaldırılarak ve yapılandırma anahtar`ConnectionStrings`bölümü eklenerek oluşturulur ( ).
* Veritabanı bağlantı sağlayıcısını temsil eden (belirtilen sağlayıcı bulunmayanlar `CUSTOMCONNSTR_`hariç) yeni bir yapılandırma anahtar değeri çifti oluşturulur.

| Ortam değişken anahtarı | Dönüştürülmüş yapılandırma anahtarı | Sağlayıcı yapılandırma girişi                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Yapılandırma girişi oluşturulmamadı.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName`:<br>Değer:`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName`:<br>Değer:`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Anahtar: `ConnectionStrings:{KEY}_ProviderName`:<br>Değer:`System.Data.SqlClient`  |

**Örnek**

Sunucuda özel bir bağlantı dizesi ortamı değişkeni oluşturulur:

* İsim &ndash;`CUSTOMCONNSTR_ReleaseDB`
* Değer &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Enjekte `IConfiguration` edilip adlı `_config`bir alana atanmışsa, değeri okuyun:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Dosya Yapılandırma Sağlayıcısı

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>dosya sisteminden yapılandırma yükleme için taban sınıftır. Aşağıdaki yapılandırma sağlayıcıları belirli dosya türlerine adanmıştır:

* [INI Yapılandırma Sağlayıcısı](#ini-configuration-provider)
* [JSON Yapılandırma Sağlayıcısı](#json-configuration-provider)
* [XML Yapılandırma Sağlayıcısı](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI Yapılandırma Sağlayıcısı

Çalışma <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> zamanında INI dosya anahtar değeri çiftlerinden yükler yapılandırması.

INI dosya yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> bir örnekte çağırın.

Üst üste INI dosya yapılandırmasında bir kesit sınırlayıcı olarak kullanılabilir.

Aşırı yükleme izni belirterek:

* Dosyanın isteğe bağlı olup olmadığı.
* Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.
* Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.

Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

INI yapılandırma dosyasının genel bir örneği:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:

* bölüm0:key0
* bölüm0:key1
* bölüm1:alt bölüm:anahtar
* bölüm2:altbölüm0:anahtar
* bölüm2:altbölüm1:anahtar

### <a name="json-configuration-provider"></a>JSON Yapılandırma Sağlayıcısı

Çalışma <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> süresi sırasında JSON dosya anahtar değeri çiftlerinden gelen yükler yapılandırması.

JSON dosya yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> bir örnekte arayın.

Aşırı yükleme izni belirterek:

* Dosyanın isteğe bağlı olup olmadığı.
* Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.
* Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.

`AddJsonFile`yeni bir ana bilgisayar oluşturucu ile `CreateDefaultBuilder`baş harfe döndüğünde otomatik olarak iki kez çağrılır. Yöntem, yapılandırmayı yüklemek için aşağıdakilerden çağrılır:

* *appsettings.json* &ndash; Bu dosya ilk okunur. Dosyanın ortam sürümü *appsettings.json* dosyası tarafından sağlanan değerleri geçersiz kılabilir.
* *ayarları. {Environment}.json* &ndash; Dosyanın ortam sürümü [IHostingEnvironment.EnvironmentName'e](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)göre yüklenir.

Daha fazla bilgi için [Varsayılan yapılandırma](#default-configuration) bölümüne bakın.

`CreateDefaultBuilder`ayrıca yükler:

* Ortam değişkenleri.
* Geliştirme ortamında [kullanıcı sırları (Gizli Yönetici).](xref:security/app-secrets)
* Komut satırı bağımsız değişkenleri.

Önce JSON Yapılandırma Sağlayıcısı kurulur. Bu nedenle, kullanıcı sırları, ortam değişkenleri ve komut satırı bağımsız *değişkenleri, uygulama ayarları* dosyaları tarafından ayarlanan yapılandırmayı geçersiz kılar.

`ConfigureAppConfiguration` *Appsettings.json* ve appsettings dışındaki dosyalar için uygulamanın yapılandırmasını belirtmek için ana bilgisayarını oluştururken *arayın.{ Çevre}.json*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Örnek**

Örnek uygulama, iki çağrı içeren `CreateDefaultBuilder` ana bilgisayar oluşturmak için `AddJsonFile`statik kolaylık yönteminden yararlanır:

* `AddJsonFile` *appsettings.json*gelen yükler yapılandırma için ilk çağrı :

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Uygulama ayarlarından `AddJsonFile` yükler yapılandırmasına ikinci *çağrı.{ Çevre}.json*. *Uygulama ayarları için. Development.json* örnek uygulamasında aşağıdaki dosya yüklenir:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Örnek uygulamayı çalıştırın. Uygulamanın tarayıcısını ' `http://localhost:5000`dan aç.
1. Çıktı, uygulamanın ortamına göre yapılandırma için anahtar değer çiftleri içerir. Anahtarın günlük `Logging:LogLevel:Default` düzeyi, `Debug` uygulamayı Geliştirme ortamında çalıştırırken olmasıdır.
1. Örnek uygulamayı Üretim ortamında yeniden çalıştırın:
   1. *Özellikler/launchSettings.json* dosyasını açın.
   1. `ConfigurationSample` Profilde, `ASPNETCORE_ENVIRONMENT` ortam değişkeninin değerini ' den ' e `Production`değiştirin.
   1. Dosyayı kaydedin ve `dotnet run` uygulamayı komut kabuğunda çalıştırın.
1. *Uygulama ayarlarındaki ayarlar. Development.json* artık *appsettings.json'daki*ayarları geçersiz kılamaz. Anahtarın `Logging:LogLevel:Default` günlük düzeyi `Warning`.

### <a name="xml-configuration-provider"></a>XML Yapılandırma Sağlayıcısı

XML <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> dosya anahtar değeri çiftlerinden gelen yükler yapılandırması çalışma zamanında.

XML dosya yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> bir örnekte çağırın.

Aşırı yükleme izni belirterek:

* Dosyanın isteğe bağlı olup olmadığı.
* Dosya değişirse yapılandırmanın yeniden yüklenip yeniden yüklenmediği.
* Dosyaya <xref:Microsoft.Extensions.FileProviders.IFileProvider> erişmek için kullanılır.

Yapılandırma anahtar değeri çiftleri oluşturulduğunda yapılandırma dosyasının kök düğümü yoksayılır. Dosyada Belge Türü Tanımı (DTD) veya ad alanı belirtmeyin.

Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

XML yapılandırma dosyaları bölümleri yinelemek için farklı öğe adları kullanabilirsiniz:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:

* bölüm0:key0
* bölüm0:key1
* bölüm1:key0
* bölüm1:key1

`name` Öznitelik öğeleri ayırt etmek için kullanılırsa aynı öğe adı kullanan yinelenen öğeler çalışır:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:

* bölüm:bölüm0:anahtar:key0
* bölüm:bölüm0:anahtar:key1
* bölüm:bölüm1:key:key0
* bölüm:bölüm1:key:key1

Öznitelikler değerleri sağlamak için kullanılabilir:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Önceki yapılandırma dosyası aşağıdaki tuşları `value`aşağıdaki tuşlarla yükler:

* key:öznitelik
* bölüm:key:öznitelik

## <a name="key-per-file-configuration-provider"></a>Dosya başına anahtar Yapılandırma Sağlayıcısı

Yapılandırma <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> anahtar değeri çiftleri olarak bir dizin dosyaları kullanır. Anahtar dosya adıdır. Değer, dosyanın içeriğini içerir. Anahtar başına dosya Yapılandırma Sağlayıcısı Docker barındırma senaryolarında kullanılır.

Dosya başına anahtar yapılandırmasını etkinleştirmek <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> için, uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini bir .' `directoryPath` Dosyalara mutlak bir yol olmalıdır.

Aşırı yükleme izni belirterek:

* Kaynağı `Action<KeyPerFileConfigurationSource>` yapılandıran bir temsilci.
* Dizinin isteğe bağlı olup olmadığı ve dizine giden yol.

Çift alt çizgi`__`( ) dosya adlarında yapılandırma anahtarı delimiter olarak kullanılır. Örneğin, dosya adı `Logging__LogLevel__System` yapılandırma anahtarını `Logging:LogLevel:System`üretir.

Uygulamanın yapılandırmasını belirtmek için ana bilgisayar ını kurarken arayın: `ConfigureAppConfiguration`

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Bellek Yapılandırma Sağlayıcısı

Yapılandırma <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> anahtar değeri çiftleri olarak bellek içi bir koleksiyon kullanır.

Bellek içi koleksiyon yapılandırmasını etkinleştirmek için uzantı <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>yöntemini <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> bir örnekte çağırın.

Yapılandırma sağlayıcısı bir `IEnumerable<KeyValuePair<String,String>>`.

Uygulamanın yapılandırmasını belirtmek için ana bilgisayarı kurarken arayın. `ConfigureAppConfiguration`

Aşağıdaki örnekte, bir yapılandırma sözlüğü oluşturulur:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Sözlük yapılandırmasağlamak için `AddInMemoryCollection` bir çağrı ile kullanılır:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)belirtilen bir anahtarla yapılandırmadan tek bir değer ayıklar ve belirtilen noncollection türüne dönüştürür. Aşırı yükleme varsayılan bir değer kabul eder.

Aşağıdaki örnek:

* Anahtarla `NumberKey`yapılandırmadan dize değerini ayıklar. Yapılandırma `NumberKey` anahtarlarında bulunmazsa, varsayılan değeri `99` kullanılır.
* Değeri bir `int`.
* Sayfanın kullandığı `NumberConfig` değer deki değeri depolar.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren ve Var

İzleyen örnekler için aşağıdaki JSON dosyasını göz önünde bulundurun. Biri bir çift alt bölüm içeren iki bölüm arasında dört anahtar bulunur:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Dosya yapılandırmaya okunduğunda, yapılandırma değerlerini tutmak için aşağıdaki benzersiz hiyerarşik anahtarlar oluşturulur:

* bölüm0:key0
* bölüm0:key1
* bölüm1:key0
* bölüm1:key1
* bölüm2:altbölüm0:key0
* bölüm2:altbölüm0:key1
* bölüm2:altbölüm1:key0
* bölüm2:altbölüm1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) belirtilen alt bölüm tuşu ile bir yapılandırma alt bölümü ayıklar.

Yalnızca anahtar <xref:Microsoft.Extensions.Configuration.IConfigurationSection> değeri çiftlerini `section1`içeren bir bölümü `GetSection` çağırmak ve tedarik etmek için:

```csharp
var configSection = _config.GetSection("section1");
```

Bir `configSection` değeri yok, sadece bir anahtar ve bir yol var.

Benzer şekilde, bölüm yolunu aramak `section2:subsection0` `GetSection` ve tedarik etmek için anahtarlar için değerleri elde etmek için:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection`asla `null`dönmez. Eşleşen bir bölüm bulunamazsa, `IConfigurationSection` boş döndürülür.

Eşleşen `GetSection` bir bölüm <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> döndürdüğünde doldurulmaz. A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> ve bölüm olduğunda döndürülür.

### <a name="getchildren"></a>GetChildren

[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) için `section2` bir çağrı `IEnumerable<IConfigurationSection>` içerir:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Var

Yapılandırma bölümünün var olup olmadığını belirlemek için [ConfigurationExtensions.Exists'ı](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) kullanın:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Örnek veriler göz `sectionExists` `false` önüne alındığında, yapılandırma `section2:subsection2` verilerinde bir bölüm olmamasıdır.

## <a name="bind-to-an-object-graph"></a>Nesne grafiğine bağlama

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>tüm POCO nesne grafiğini bağlama yeteneğine sahiptir. Basit bir nesneyi bağlamada olduğu gibi, yalnızca ortak okuma/yazma özellikleri bağlanır.

Örnek, nesne `TvShow` grafiği ve `Metadata` `Actors` sınıfları içeren bir model içerir *(Modeller/TvShow.cs):*

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Örnek uygulamanın yapılandırma verilerini içeren bir *tvshow.xml* dosyası vardır:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Yapılandırma `Bind` yöntemi ile `TvShow` tüm nesne grafiğine bağlıdır. Bağlı örnek işleme için bir özelliğe atanır:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)biner ve belirtilen türü döndürür. `Get<T>`kullanmaktan `Bind`daha uygundur. Aşağıdaki kod, önceki `Get<T>` örnekle nasıl kullanılacağını gösterir:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Bir diziyi sınıfa bağlama

*Örnek uygulama bu bölümde açıklanan kavramları gösterir.*

Yapılandırma <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> anahtarlarında dizi dizinlerini kullanarak nesnelere bağlama dizilerini destekler. Sayısal anahtar kesimini ortaya çıkaran herhangi`:0:`bir `:1:` &hellip; `:{n}:`dizi biçimi ( , , ) bir POCO sınıf dizisine dizi bağlama yeteneğine sahiptir.

> [!NOTE]
> Bağlama sözleşme ile sağlanır. Dizi bağlamayı uygulamak için özel yapılandırma sağlayıcılarının gerekli değildir.

**Bellek içi dizi işleme**

Aşağıdaki tabloda gösterilen yapılandırma anahtarlarını ve değerlerini göz önünde bulundurun.

| Anahtar             | Değer  |
| :-------------: | :----: |
| dizi:girişler:0 | değeri0 |
| dizi:girişler:1 | değer1 |
| dizi:girişler:2 | değeri2 |
| dizi:girişler:4 | değer4 |
| dizi:girişler:5 | değeri5 |

Bu anahtarlar ve değerler, Bellek Yapılandırma Sağlayıcısı kullanılarak örnek uygulamaya yüklenir:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Dizi dizin &num;3 için bir değer atlar. Yapılandırma bağlayıcısı, bu diziyi bir nesneye bağlama sonucunun gösterildiği bir anda netleşen, bağlı nesnelerde null değerleri bağlama veya null girişleri oluşturma yeteneğine sahip değildir.

Örnek uygulamada, bağlı yapılandırma verilerini tutmak için bir POCO sınıfı kullanılabilir:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Yapılandırma verileri nesneye bağlıdır:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)sözdizimi de kullanılabilir, bu da daha kompakt kodla sonuçlanır:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Bağlı nesne, bir `ArrayExample`örnek , yapılandırmadan dizi verilerini alır.

| `ArrayExample.Entries`Dizin | `ArrayExample.Entries`Değer |
| :--------------------------: | :--------------------------: |
| 0                            | değeri0                       |
| 1                            | değer1                       |
| 2                            | değeri2                       |
| 3                            | değer4                       |
| 4                            | değeri5                       |

Bağlı &num;nesnedeki Dizin `array:4` 3, yapılandırma anahtarı ve değeri `value4`için yapılandırma verilerini tutar. Bir dizi içeren yapılandırma verileri bağlandığında, yapılandırma anahtarlarındaki dizi dizileri yalnızca nesneyi oluştururken yapılandırma verilerini doğrulamak için kullanılır. Yapılandırma verilerinde null değeri tutulamaz ve yapılandırma anahtarlarındaki bir dizi bir veya daha fazla endeksi atladığında bağlı bir nesnede null değerli bir giriş oluşturulmaz.

Dizin &num;3 için eksik yapılandırma öğesi, yapılandırmada doğru anahtar değeri çiftini üreten herhangi bir yapılandırma sağlayıcısı tarafından `ArrayExample` örneğine bağlanmadan önce sağlanabilir. Örnekte eksik anahtar değeri çifti olan ek bir JSON `ArrayExample.Entries` Yapılandırma Sağlayıcısı varsa, tam yapılandırma dizisi yle eşleşir:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

`ConfigureAppConfiguration` içinde:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

Tabloda gösterilen anahtar değeri çifti yapılandırmaya yüklenir.

| Anahtar             | Değer  |
| :-------------: | :----: |
| dizi:girişler:3 | değeri3 |

`ArrayExample` Sınıf örneği JSON Yapılandırma Sağlayıcısı dizin &num;3 için giriş içerir sonra bağlı ise, `ArrayExample.Entries` dizi değeri içerir.

| `ArrayExample.Entries`Dizin | `ArrayExample.Entries`Değer |
| :--------------------------: | :--------------------------: |
| 0                            | değeri0                       |
| 1                            | değer1                       |
| 2                            | değeri2                       |
| 3                            | değeri3                       |
| 4                            | değer4                       |
| 5                            | değeri5                       |

**JSON dizi işleme**

Bir JSON dosyası nda bir dizi varsa, sıfır tabanlı bölüm dizilimi olan dizi öğeleri için yapılandırma anahtarları oluşturulur. Aşağıdaki yapılandırma dosyasında, `subsection` bir dizidir:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

JSON Yapılandırma Sağlayıcısı yapılandırma verilerini aşağıdaki anahtar değer çiftlerine okur:

| Anahtar                     | Değer  |
| ----------------------- | :----: |
| json_array:anahtar          | değerA |
| json_array:alt bölüm:0 | değerB |
| json_array:alt bölüm:1 | valueC |
| json_array:alt bölüm:2 | Değerli |

Örnek uygulamada, yapılandırma anahtar değeri çiftlerini bağlamak için aşağıdaki POCO sınıfı kullanılabilir:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Bağlama sonra, `JsonArrayExample.Key` değeri `valueA`tutar. Alt bölüm değerleri POCO dizi özelliğinde `Subsection`depolanır.

| `JsonArrayExample.Subsection`Dizin | `JsonArrayExample.Subsection`Değer |
| :---------------------------------: | :---------------------------------: |
| 0                                   | değerB                              |
| 1                                   | valueC                              |
| 2                                   | Değerli                              |

## <a name="custom-configuration-provider"></a>Özel yapılandırma sağlayıcısı

Örnek uygulama, [Entity Framework (EF)](/ef/core/)kullanarak bir veritabanından yapılandırma anahtar değeri çiftleri okuyan temel bir yapılandırma sağlayıcısının nasıl oluşturulturolduğunu gösterir.

Sağlayıcı aşağıdaki özelliklere sahiptir:

* EF bellek veritabanı gösteri amacıyla kullanılır. Bağlantı dizesi gerektiren bir veritabanı kullanmak `ConfigurationBuilder` için, bağlantı dizesini başka bir yapılandırma sağlayıcısından sağlamak için ikincil bir uygulama uygulayın.
* Sağlayıcı başlangıçta yapılandırma içine bir veritabanı tablosu okur. Sağlayıcı, veritabanını anahtar başına sorgulamıyor.
* Yeniden yükleme-on-change uygulanmaz, bu nedenle uygulama başladıktan sonra veritabanını güncelleştirmenin uygulamanın yapılandırması üzerinde hiçbir etkisi yoktur.

Yapılandırma `EFConfigurationValue` değerlerini veritabanında depolamak için bir varlık tanımlayın.

*Modeller/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Yapılandırılan `EFConfigurationContext` değerleri depolamak ve erişmek için bir ekleyin.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Uygulayan bir sınıf <xref:Microsoft.Extensions.Configuration.IConfigurationSource>oluşturun.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

'den <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>devralarak özel yapılandırma sağlayıcısı oluşturun. Yapılandırma sağlayıcısı boş olduğunda veritabanını başlatılmasını sağlar.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Uzatma `AddEFConfiguration` yöntemi, yapılandırma kaynağını bir `ConfigurationBuilder`.'ye eklemeye izin verir.

*Uzantılar/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Aşağıdaki `EFConfigurationProvider` *kod, Program.cs*özel in nasıl kullanılacağını gösterir:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Başlangıç sırasında yapılandırmaya erişim

'deki `Startup` yapılandırma değerlerine erişmek için oluşturucuya enjekte edin. `IConfiguration` `Startup.ConfigureServices` Yapılandırmaya `Startup.Configure`erişmek için `IConfiguration` doğrudan yönteme enjekte edin veya oluşturucudan örneği kullanın:

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Başlangıç kolaylığı yöntemlerini kullanarak yapılandırmaya erişim örneği için Bkz. [Uygulama başlangıç: Kolaylık yöntemleri.](xref:fundamentals/startup#convenience-methods)

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Razor Pages sayfasında veya MVC görünümünde yapılandırmaya erişin

Razor Pages sayfasındaveya MVC görünümünde yapılandırma ayarlarına erişmek [için, Microsoft.Extensions.Configuration ad alanı](xref:Microsoft.Extensions.Configuration) için bir kullanma <xref:Microsoft.Extensions.Configuration.IConfiguration> [yönergesi](xref:mvc/views/razor#using) [(C# başvurusu: yönergeyi kullanma)](/dotnet/csharp/language-reference/keywords/using-directive)ekleyin ve sayfaya veya görünüme enjekte edin.

Jilet Sayfaları sayfasında:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

MVC görünümünde:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Harici bir montajdan yapılandırma ekleme

Uygulama, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> uygulamanın sınıfının dışındaki harici bir derlemeden başlangıçta bir `Startup` uygulamaya geliştirme eklemeye olanak tanır. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/options>

::: moniker-end
