---
title: ASP.NET Core 'de barındırma başlangıç derlemelerini kullanın
author: rick-anderson
description: Bir ASP.NET Core uygulamasının bir dış derlemeden bir ıhostingstartup uygulaması kullanarak nasıl geliştirileceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
no-loc:
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: fadd93cf28603653e20ed6c7dceadcabf0dfb9a5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627526"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>ASP.NET Core 'de barındırma başlangıç derlemelerini kullanın

[Palete kroni](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(Barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler. Örneğin, bir dış kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetler sağlamak için barındırma başlangıç uygulamasını kullanabilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup özniteliği

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.

Giriş derlemesi veya sınıfı içeren derleme `Startup` öznitelik için otomatik olarak taranır `HostingStartup` . Öznitelikleri aramak için derlemelerin listesi `HostingStartup` [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir. Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi.

Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement` . Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup`Özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Yüklü barındırma başlangıç derlemelerini bul

Yüklü barındırma başlangıç derlemelerini öğrenmek için, günlüğü etkinleştirin ve uygulamanın günlüklerini denetleyin. Derlemeler yüklenirken oluşan hatalar günlüğe kaydedilir. Yüklenen barındırma başlangıç derlemeleri hata ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırak

Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:

* Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini veya olarak ayarlayın `true` `1` :

  * Barındırma başlangıç ana bilgisayar yapılandırma ayarını önle:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` ortam değişkeni.

* Belirli barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini, başlangıçta dışlamak üzere, bir barındırma başlangıç bütünleştirilmiş kodlarının noktalı virgülle ayrılmış dizesine ayarlayın:

  * Barındırma başlatma derlemeleri dışlama ana bilgisayar yapılandırma ayarı:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` ortam değişkeni.

Ana bilgisayar yapılandırma ayarı ve ortam değişkeni ayarlanırsa, konak ayarı davranışı denetler.

Konak ayarını veya ortam değişkenini kullanarak başlatma derlemelerinin barındırılmasını devre dışı bırakmak, derlemeyi küresel olarak devre dışı bırakabilir ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.

## <a name="project"></a>Project

Aşağıdaki proje türlerinden birini kullanarak bir barındırma başlatması oluşturun:

* [Sınıf kitaplığı](#class-library)
* [Giriş noktası olmayan konsol uygulaması](#console-app-without-an-entry-point)

### <a name="class-library"></a>Sınıf kitaplığı

Bir barındırma başlatma geliştirmesi, bir sınıf kitaplığında bulunabilir. Kitaplık bir özniteliği içerir `HostingStartup` .

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) , bir Razor Pages uygulaması, *Hostingstartupapp*ve bir sınıf kitaplığı, *hostingstartuplibrary*içerir. Sınıf kitaplığı:

* Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` . `ServiceKeyInjection` bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.
* `HostingStartup`Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir özniteliği içerir.

`ServiceKeyInjection`Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.

*Hostingstartuplibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın dizin sayfası, sınıf kitaplığının barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage*sağlayan bir NuGet paket projesi içerir. Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir. Paket:

* Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` . `ServiceKeyInjection` uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.
* Bir `HostingStartup` özniteliği içerir.

*Hostingstartuppackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Giriş noktası olmayan konsol uygulaması

*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*

Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir öznitelik içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi `HostingStartup` . Konsol uygulamasını yayımlamak, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi oluşturur.

Bu işlemde giriş noktası olmayan bir konsol uygulaması kullanılmıştır çünkü:

* Barındırma başlangıç derlemesinde barındırma başlangıcını kullanmak için bir bağımlılıklar dosyası gereklidir. Bağımlılıklar dosyası, bir kitaplık değil bir uygulama yayımlamayla üretilen çalıştırılabilir bir uygulama varlıktır.
* Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.

Dinamik barındırma başlatma oluşturma bölümünde:

* Bir barındırma başlangıç derlemesi, konsol uygulamasından bir giriş noktası olmadan oluşturulur:
  * , Uygulamayı içeren bir sınıf içerir `IHostingStartup` .
  * Uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir `IHostingStartup` .
* Konsol uygulaması, barındırma başlatmasının bağımlılıklarını almak için yayımlanır. Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpıllarıdır.
* Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak için değiştirilir.
* Barındırma başlangıç derlemesi ve onun bağımlılıklar dosyası çalışma zamanı paket deposuna yerleştirilir. Barındırma başlangıç derlemesini ve onun bağımlılıklar dosyasını öğrenmek için, ortam değişkenleri çiftinde listelenir.

Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfını `IHostingStartup` oluştururken yükleme ve yürütme için uygulamasının bir uygulamasını tanımlar <xref:Microsoft.AspNetCore.Hosting.IWebHost> . Aşağıdaki örnekte, ad alanı `StartupEnhancement` ve sınıfı `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Bir sınıf uygular `IHostingStartup` . Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır. `IHostingStartup.Configure` barındırma başlangıç derlemesinde kullanıcı kodundan önce çalışma zamanı tarafından çağrılır `Startup.Configure` , bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına olanak tanır.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Bir proje oluştururken `IHostingStartup` , bağımlılıklar dosyası (*.deps.js*), `runtime` derlemenin konumunu *bin* klasörüne ayarlar:

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Yalnızca dosyanın bir kısmı gösterilir. Örnekteki derleme adı `StartupEnhancement` .

## <a name="configuration-provided-by-the-hosting-startup"></a>Barındırma başlatma tarafından belirtilen yapılandırma

Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:

1. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>Uygulamanın temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> . Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.
1. <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>Uygulamanın temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> . Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Barındırma başlangıç derlemesini belirtin

Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` . Ortam değişkeni, derlemelerin noktalı virgülle ayrılmış listesidir.

Özniteliği için yalnızca barındırma başlangıç derlemeleri taranır `HostingStartup` . Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp*örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Barındırma başlangıç bütünleştirilmiş kodları, barındırma başlangıç derlemeleri ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.

## <a name="activation"></a>Etkinleştirme

Başlatma başlangıç etkinleştirme seçenekleri şunlardır:

* [Çalışma zamanı deposu](#runtime-store): etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez. Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına*koyar. *Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.
* Etkinleştirme için derleme zamanı başvurusu gerekiyor
  * [NuGet paketi](#nuget-package)
  * [Proje bin klasörü](#project-bin-folder)

### <a name="runtime-store"></a>Çalışma zamanı deposu

Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi. Derleme zamanı başvurusu, Gelişmiş uygulama için gerekli değildir.

Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.

**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**

Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, ile çalışma zamanına ek bağımlılıklar belirtin `additionalDeps` . `additionalDeps` şunları yapmanıza olanak sağlar:

* Dosya başlangıcında uygulamanın kendi *.deps.js* birlikte birleştirilecek dosyalara bir dizi ek *.deps.js* sağlayarak uygulamanın kitaplık grafiğini genişletin.
* Barındırma başlangıç derlemesini bulunabilir ve yüklenebilir hale getirin.

Ek bağımlılıklar dosyası oluşturmak için önerilen yaklaşım şunlardır:

 1. `dotnet publish`Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında yürütün.
 1. Bildirimlerin bildirim başvurusunu ve `runtime` sonuçta elde edilen *.deps.js* dosyanın bölümünü kaldırın.

Örnek projede, `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

*.deps.js* dosyasını aşağıdaki konuma yerleştirin:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Ortam değişkenine konum eklendi `DOTNET_ADDITIONAL_DEPS` .
* `{SHARED FRAMEWORK NAME}`: Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.
* `{SHARED FRAMEWORK VERSION}`: En düşük paylaşılan çerçeve sürümü.
* `{ENHANCEMENT ASSEMBLY NAME}`: Geliştirmesinin derleme adı.

Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.

Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.

Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).

**Dağıtım**

Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:

* Barındırma başlangıç çalışma zamanı deposu.
* Barındırma başlangıç bağımlılıkları dosyası.
* `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE` `DOTNET_ADDITIONAL_DEPS` Barındırma başlangıcını etkinleştirmeyi desteklemek için, ve ' ı oluşturan veya değiştiren bir PowerShell betiği. Betiği dağıtım sistemindeki bir yönetim PowerShell komut isteminden çalıştırın.

### <a name="nuget-package"></a>NuGet paketi

Bir NuGet paketinde barındırma başlatma geliştirmesi sağlayabilirsiniz. Pakette bir öznitelik vardır `HostingStartup` . Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:

* Gelişmiş uygulamanın proje dosyası, uygulamanın proje dosyasında (derleme zamanı başvurusu) barındırma başlatması için bir paket başvurusu yapar. Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir. Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).

NuGet paketleri ve çalışma zamanı deposu hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Platformlar arası araçlarla bir NuGet paketi oluşturma](/dotnet/core/deploying/creating-nuget-packages)
* [Paketler yayımlanıyor](/nuget/create-packages/publish-a-package)
* [Çalışma zamanı paket deposu](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Proje bin klasörü

Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin*ile dağıtılan bir derleme tarafından sağlanıyor. Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:

* Gelişmiş uygulamanın proje dosyası, barındırma başlatmaya bir derleme başvurusu yapar (derleme zamanı başvurusu). Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir. Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine (*. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:
  * Tüketen proje.
  * Tüketim Projesi tarafından erişilebilen bir konum.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).
* .NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:
  * Uygulama temel yolu: uygulamanın yürütülebilir dosyasının (*. exe*) bulunduğu *bin* klasörü.
  * Genel bütünleştirilmiş kod önbelleği (GAC): GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar. Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.

## <a name="sample-code"></a>Örnek kod

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:

* İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar-değer çiftleri çiftini ayarlar:
  * NuGet paketi (*Hostingstartuppackage*)
  * Sınıf kitaplığı (*Hostingstartuplibrary*)
* Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir. Derleme, üzerinde tanılama bilgileri sağlayan, başlangıçta uygulamaya iki middlewares ekler:
  * Kayıtlı hizmetler
  * Adres (düzen, ana bilgisayar, yol tabanı, yol, sorgu dizesi)
  * Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)
  * İstek üst bilgileri
  * Ortam değişkenleri

Örneği çalıştırmak için:

**NuGet paketinden etkinleştirme**

1. ,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.
1. Paketin *Hostingstartuppackage* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. Uygulamayı derleyin ve çalıştırın. Gelişmiş uygulamada bir paket başvurusu vardır (derleme zamanı başvurusu). `<PropertyGroup>`Uygulamanın proje dosyasındaki bir paket projenin çıkışını belirtir (*.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak. Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .

*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin. Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:

```dotnetcli
dotnet nuget locals all --clear
```

**Bir sınıf kitaplığından etkinleştirme**

1. , [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.
1. Sınıf kitaplığının *Hostingstartuplibrary* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. *bin*- *HostingStartupLibrary.dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak, sınıf kitaplığının derlemesini uygulamaya dağıtın.
1. Uygulamayı derleyin ve çalıştırın. `<ItemGroup>`Uygulamanın proje dosyasındaki bir, sınıf kitaplığının derlemesine (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (derleme zamanı başvurusu) başvurur. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .

**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**

1. *Startupdiagnostics* projesi dosyada *StartupDiagnostics.deps.js* değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır. PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak varsayılan olarak yüklüdür. Diğer platformlarda PowerShell 'i almak için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](/powershell/scripting/install/installing-powershell).
1. *Runtimesyürüme* klasöründe *build.ps1* betiğini yürütün. Betik şunları yapar:
   * , `StartupDiagnostics` *Obj\packages* klasöründe paketi oluşturur.
   * İçin çalışma zamanı deposunu `StartupDiagnostics` *Mağaza* klasöründe oluşturur. `dotnet store`Betikteki komut, `win7-x64` Windows 'a dağıtılan bir barındırma başlatması için [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır. Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun. Çalışma zamanı deposu `StartupDiagnostics` daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sisteminin çalışma zamanı deposuna taşınır. Derlemenin Kullanıcı çalışma zamanı deposu yüklemesi konumu `StartupDiagnostics` *. DotNet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.
   * , `additionalDeps` `StartupDiagnostics` *Additionaldeps* klasöründe için öğesini oluşturur. Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır. Kullanıcı `StartupDiagnostics` ek bağımlılıkları yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. netcore. app/3.0.0/StartupDiagnostics.deps.json*.
   * *deploy.ps1* dosyasını *dağıtım* klasörüne koyar.
1. *Dağıtım* klasöründe *deploy.ps1* betiğini çalıştırın. Betik şunu ekler:
   * `StartupDiagnostics``ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`ortam değişkenine.
   * Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.
   * Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.
1. Örnek uygulamayı çalıştırın.
1. `/services`Uygulamanın kayıtlı hizmetlerini görmek için uç nokta isteyin. `/diag`Tanılama bilgilerini görmek için uç nokta isteyin.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(Barındırma başlatma) uygulaması, bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler ekler. Örneğin, bir dış kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetler sağlamak için barındırma başlangıç uygulamasını kullanabilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingStartup özniteliği

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirilecek bir barındırma başlangıç derlemesinin varlığını gösterir.

Giriş derlemesi veya sınıfı içeren derleme `Startup` öznitelik için otomatik olarak taranır `HostingStartup` . Öznitelikleri aramak için derlemelerin listesi `HostingStartup` [Webhostdefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)içindeki yapılandırmadan çalışma zamanında yüklenir. Bulmadan dışlanacak derlemelerin listesi [Webhostdefaults. Hostingstartupexcludederlemelieskey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)öğesinden yüklendi. Daha fazla bilgi için bkz. [Web Konağı: barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ve [Web Konağı: barındırma başlatma derlemeleri çıkarma](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad alanı `StartupEnhancement` . Barındırma başlangıç kodunu içeren sınıf `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup`Özniteliği genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında bulunur.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Yüklü barındırma başlangıç derlemelerini bul

Yüklü barındırma başlangıç derlemelerini öğrenmek için, günlüğü etkinleştirin ve uygulamanın günlüklerini denetleyin. Derlemeler yüklenirken oluşan hatalar günlüğe kaydedilir. Yüklenen barındırma başlangıç derlemeleri hata ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırak

Barındırma başlangıç derlemelerinin otomatik yüklenmesini devre dışı bırakmak için aşağıdaki yaklaşımlardan birini kullanın:

* Tüm barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini veya olarak ayarlayın `true` `1` :
  * [Barındırma başlangıç](xref:fundamentals/host/web-host#prevent-hosting-startup) ana bilgisayar yapılandırma ayarını önleyin.
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` ortam değişkeni.
* Belirli barındırma başlangıç derlemelerinin yüklenmesini engellemek için aşağıdakilerden birini, başlangıçta dışlamak üzere, bir barındırma başlangıç bütünleştirilmiş kodlarının noktalı virgülle ayrılmış dizesine ayarlayın:
  * [Barındırma başlatma derlemeleri](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ana bilgisayar yapılandırma ayarı.
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` ortam değişkeni.

Ana bilgisayar yapılandırma ayarı ve ortam değişkeni ayarlanırsa, konak ayarı davranışı denetler.

Konak ayarını veya ortam değişkenini kullanarak başlatma derlemelerinin barındırılmasını devre dışı bırakmak, derlemeyi küresel olarak devre dışı bırakabilir ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.

## <a name="project"></a>Project

Aşağıdaki proje türlerinden birini kullanarak bir barındırma başlatması oluşturun:

* [Sınıf kitaplığı](#class-library)
* [Giriş noktası olmayan konsol uygulaması](#console-app-without-an-entry-point)

### <a name="class-library"></a>Sınıf kitaplığı

Bir barındırma başlatma geliştirmesi, bir sınıf kitaplığında bulunabilir. Kitaplık bir özniteliği içerir `HostingStartup` .

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) , bir Razor Pages uygulaması, *Hostingstartupapp*ve bir sınıf kitaplığı, *hostingstartuplibrary*içerir. Sınıf kitaplığı:

* Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` . `ServiceKeyInjection` bellek içi yapılandırma sağlayıcısını ([Addınmemorycollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) kullanarak uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.
* `HostingStartup`Barındırma başlatmasının ad alanını ve sınıfını tanımlayan bir özniteliği içerir.

`ServiceKeyInjection`Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır.

*Hostingstartuplibrary/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın dizin sayfası, sınıf kitaplığının barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ayrıca ayrı bir barındırma başlatma, *Hostingstartuppackage*sağlayan bir NuGet paket projesi içerir. Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir. Paket:

* Uygulayan bir barındırma başlangıç sınıfı içerir `ServiceKeyInjection` `IHostingStartup` . `ServiceKeyInjection` uygulamanın yapılandırmasına bir hizmet dizesi çifti ekler.
* Bir `HostingStartup` özniteliği içerir.

*Hostingstartuppackage/ServiceKeyInjection. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtarın yapılandırma değerlerini okur ve işler:

*Hostingstartupapp/Pages/Index. cshtml. cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Giriş noktası olmayan konsol uygulaması

*Bu yaklaşım, .NET Framework değil yalnızca .NET Core uygulamaları için kullanılabilir.*

Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik barındırma başlatma geliştirmesi, bir öznitelik içeren giriş noktası olmadan bir konsol uygulamasında sağlanabilmesi `HostingStartup` . Konsol uygulamasını yayımlamak, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi oluşturur.

Bu işlemde giriş noktası olmayan bir konsol uygulaması kullanılmıştır çünkü:

* Barındırma başlangıç derlemesinde barındırma başlangıcını kullanmak için bir bağımlılıklar dosyası gereklidir. Bağımlılıklar dosyası, bir kitaplık değil bir uygulama yayımlamayla üretilen çalıştırılabilir bir uygulama varlıktır.
* Bir kitaplık, paylaşılan çalışma zamanını hedefleyen bir çalıştırılabilir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)eklenemez.

Dinamik barındırma başlatma oluşturma bölümünde:

* Bir barındırma başlangıç derlemesi, konsol uygulamasından bir giriş noktası olmadan oluşturulur:
  * , Uygulamayı içeren bir sınıf içerir `IHostingStartup` .
  * Uygulama sınıfını tanımlamak için bir [Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir `IHostingStartup` .
* Konsol uygulaması, barındırma başlatmasının bağımlılıklarını almak için yayımlanır. Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpıllarıdır.
* Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak için değiştirilir.
* Barındırma başlangıç derlemesi ve onun bağımlılıklar dosyası çalışma zamanı paket deposuna yerleştirilir. Barındırma başlangıç derlemesini ve onun bağımlılıklar dosyasını öğrenmek için, ortam değişkenleri çiftinde listelenir.

Konsol uygulaması [Microsoft. AspNetCore. Hosting. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

[Hostingstartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfını `IHostingStartup` oluştururken yükleme ve yürütme için uygulamasının bir uygulamasını tanımlar <xref:Microsoft.AspNetCore.Hosting.IWebHost> . Aşağıdaki örnekte, ad alanı `StartupEnhancement` ve sınıfı `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Bir sınıf uygular `IHostingStartup` . Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirmeler eklemek için bir kullanır. `IHostingStartup.Configure` barındırma başlangıç derlemesinde kullanıcı kodundan önce çalışma zamanı tarafından çağrılır `Startup.Configure` , bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından verilen yapılandırmanın üzerine yazılmasına olanak tanır.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Bir proje oluştururken `IHostingStartup` , bağımlılıklar dosyası (*.deps.js*), `runtime` derlemenin konumunu *bin* klasörüne ayarlar:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Yalnızca dosyanın bir kısmı gösterilir. Örnekteki derleme adı `StartupEnhancement` .

## <a name="configuration-provided-by-the-hosting-startup"></a>Barındırma başlatma tarafından belirtilen yapılandırma

Yapılandırma işlemi, barındırma başlatmasının yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak iki yaklaşımdan yararlanabilir:

1. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>Uygulamanın temsilcileri çalıştırıldıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> . Barındırma başlangıç yapılandırması, uygulamanın yapılandırmasına bu yaklaşımı kullanarak öncelik kazanır.
1. <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>Uygulamanın temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> . Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Barındırma başlangıç derlemesini belirtin

Bir sınıf kitaplığı ya da konsol uygulaması tarafından sağlanan bir barındırma başlatması için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` . Ortam değişkeni, derlemelerin noktalı virgülle ayrılmış listesidir.

Özniteliği için yalnızca barındırma başlangıç derlemeleri taranır `HostingStartup` . Daha önce açıklanan barındırma başlangıç pencerelerini öğrenmek için *Hostingstartupapp*örnek uygulaması için, ortam değişkeni aşağıdaki değere ayarlanır:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Barındırma başlangıç bütünleştirilmiş [kodları barındırma başlangıç derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir.

Birden çok barındırma başlatması varsa, <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemleri derlemelerin listelendiği sırada yürütülür.

## <a name="activation"></a>Etkinleştirme

Başlatma başlangıç etkinleştirme seçenekleri şunlardır:

* [Çalışma zamanı deposu](#runtime-store): etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez. Örnek uygulama, çok makineli bir ortamda barındırma başlatmasının dağıtımını kolaylaştırmak için barındırma başlangıç derlemesini ve bağımlılıklar dosyalarını bir klasöre, *dağıtımına*koyar. *Dağıtım* klasörü Ayrıca, barındırma başlangıcını etkinleştirmek için dağıtım sistemindeki ortam değişkenlerini oluşturan veya değiştiren bir PowerShell betiği içerir.
* Etkinleştirme için derleme zamanı başvurusu gerekiyor
  * [NuGet paketi](#nuget-package)
  * [Proje bin klasörü](#project-bin-folder)

### <a name="runtime-store"></a>Çalışma zamanı deposu

Barındırma başlangıç uygulamasının [çalışma zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilmesi. Derleme zamanı başvurusu, Gelişmiş uygulama için gerekli değildir.

Barındırma başlatma oluşturulduktan sonra, bildirim proje dosyası ve [DotNet Store](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir çalışma zamanı deposu oluşturulur.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Örnek uygulamada (*Runtimesstore* Projesi) aşağıdaki komut kullanılır:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Çalışma zamanının çalışma zamanı deposunu bulması için, çalışma zamanı deposunun konumu `DOTNET_SHARED_STORE` ortam değişkenine eklenir.

**Barındırma başlatmasının bağımlılıklar dosyasını değiştirme ve yerleştirme**

Geliştirmede bir paket başvurusu olmadan geliştirmeyi etkinleştirmek için, ile çalışma zamanına ek bağımlılıklar belirtin `additionalDeps` . `additionalDeps` şunları yapmanıza olanak sağlar:

* Dosya başlangıcında uygulamanın kendi *.deps.js* birlikte birleştirilecek dosyalara bir dizi ek *.deps.js* sağlayarak uygulamanın kitaplık grafiğini genişletin.
* Barındırma başlangıç derlemesini bulunabilir ve yüklenebilir hale getirin.

Ek bağımlılıklar dosyası oluşturmak için önerilen yaklaşım şunlardır:

 1. `dotnet publish`Önceki bölümde başvurulan çalışma zamanı deposu bildirim dosyasında yürütün.
 1. Bildirimlerin bildirim başvurusunu ve `runtime` sonuçta elde edilen *.deps.js* dosyanın bölümünü kaldırın.

Örnek projede, `store.manifest/1.0.0` özelliği `targets` ve `libraries` bölümünden kaldırılır:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

*.deps.js* dosyasını aşağıdaki konuma yerleştirin:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Ortam değişkenine konum eklendi `DOTNET_ADDITIONAL_DEPS` .
* `{SHARED FRAMEWORK NAME}`: Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.
* `{SHARED FRAMEWORK VERSION}`: En düşük paylaşılan çerçeve sürümü.
* `{ENHANCEMENT ASSEMBLY NAME}`: Geliştirmesinin derleme adı.

Örnek uygulamada (*Runtimesbir* proje), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Çalışma zamanı depo konumunu bulması için, ek bağımlılıklar dosya konumu `DOTNET_ADDITIONAL_DEPS` ortam değişkenine eklenir.

Örnek uygulamada (*Runtimesbir* proje), çalışma zamanı deposunun oluşturulması ve ek bağımlılıklar dosyası oluşturulması bir [PowerShell](/powershell/scripting/powershell-scripting) betiği kullanılarak gerçekleştirilir.

Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanbileceğine ilişkin örnekler için, bkz. [birden çok ortam kullanma](xref:fundamentals/environments).

**Dağıtım**

Çoklu makine ortamında bir barındırma başlatmasının dağıtımını kolaylaştırmak için, örnek uygulama, yayımlanan çıktıda şunları içeren bir *dağıtım* klasörü oluşturur:

* Barındırma başlangıç çalışma zamanı deposu.
* Barındırma başlangıç bağımlılıkları dosyası.
* `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE` `DOTNET_ADDITIONAL_DEPS` Barındırma başlangıcını etkinleştirmeyi desteklemek için, ve ' ı oluşturan veya değiştiren bir PowerShell betiği. Betiği dağıtım sistemindeki bir yönetim PowerShell komut isteminden çalıştırın.

### <a name="nuget-package"></a>NuGet paketi

Bir NuGet paketinde barındırma başlatma geliştirmesi sağlayabilirsiniz. Pakette bir öznitelik vardır `HostingStartup` . Paket tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:

* Gelişmiş uygulamanın proje dosyası, uygulamanın proje dosyasında (derleme zamanı başvurusu) barındırma başlatması için bir paket başvurusu yapar. Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir. Bu yaklaşım, [NuGet.org](https://www.nuget.org/)'e yayınlanan bir barındırma başlangıç derleme paketi için geçerlidir.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).

NuGet paketleri ve çalışma zamanı deposu hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Platformlar arası araçlarla bir NuGet paketi oluşturma](/dotnet/core/deploying/creating-nuget-packages)
* [Paketler yayımlanıyor](/nuget/create-packages/publish-a-package)
* [Çalışma zamanı paket deposu](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Proje bin klasörü

Bir barındırma başlatma geliştirmesi, gelişmiş uygulamada, *bin*ile dağıtılan bir derleme tarafından sağlanıyor. Derleme tarafından sağlanan barındırma başlangıç türleri, aşağıdaki yaklaşımlardan biri kullanılarak uygulama için kullanılabilir hale getirilir:

* Gelişmiş uygulamanın proje dosyası, barındırma başlatmaya bir derleme başvurusu yapar (derleme zamanı başvurusu). Derleme zamanı başvurusuyla birlikte, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına (*.deps.js*) eklenir. Bu yaklaşım, dağıtım senaryosu barındırma başlatmasının derlemesine (*. dll* dosyası) bir derleme zamanı başvurusu yapmak ve derlemeyi şu şekilde taşımak için çağırdığında geçerlidir:
  * Tüketen proje.
  * Tüketim Projesi tarafından erişilebilen bir konum.
* Barındırma başlatmasının bağımlılıklar dosyası, [çalışma zamanı deposu](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir hale getirilir (derleme zamanı başvurusu olmadan).
* .NET Framework hedeflenirken, derleme varsayılan yükleme bağlamında yüklenebilir olur; bu, .NET Framework, derlemenin aşağıdaki konumlardan birinde bulunduğu anlamına gelir:
  * Uygulama temel yolu: uygulamanın yürütülebilir dosyasının (*. exe*) bulunduğu *bin* klasörü.
  * Genel bütünleştirilmiş kod önbelleği (GAC): GAC, birkaç .NET Framework uygulamanın paylaştığı derlemeleri depolar. Daha fazla bilgi için, bkz. [nasıl yapılır: bir derlemeyi genel derleme önbelleğine yüklemek](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) .NET Framework belgeleri.

## <a name="sample-code"></a>Örnek kod

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample)), başlangıç uygulama senaryolarını barındırma gösterir:

* İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar-değer çiftleri çiftini ayarlar:
  * NuGet paketi (*Hostingstartuppackage*)
  * Sınıf kitaplığı (*Hostingstartuplibrary*)
* Bir barındırma başlatması, çalışma zamanı deposu tarafından dağıtılan bir derlemeden (*Startupdiagnostics*) etkinleştirilir. Derleme, üzerinde tanılama bilgileri sağlayan, başlangıçta uygulamaya iki middlewares ekler:
  * Kayıtlı hizmetler
  * Adres (düzen, ana bilgisayar, yol tabanı, yol, sorgu dizesi)
  * Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)
  * İstek üst bilgileri
  * Ortam değişkenleri

Örneği çalıştırmak için:

**NuGet paketinden etkinleştirme**

1. ,, [DotNet paketi](/dotnet/core/tools/dotnet-pack) komutuyla *hostingstartuppackage* paketini derleyin.
1. Paketin *Hostingstartuppackage* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. Uygulamayı derleyin ve çalıştırın. Gelişmiş uygulamada bir paket başvurusu vardır (derleme zamanı başvurusu). `<PropertyGroup>`Uygulamanın proje dosyasındaki bir paket projenin çıkışını belirtir (*.. /HostingStartupPackage/bin/Debug*) bir paket kaynağı olarak. Bu, uygulamanın paketi [NuGet.org](https://www.nuget.org/)'e yüklemeden paketi kullanmasına izin verir. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, paketin yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .

*Hostingstartuppackage* projesinde değişiklik yaparsanız ve yeniden derleyseniz, *Hostingstartupapp* ' ın yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin. Yerel NuGet önbelleklerini temizlemek için aşağıdaki [DotNet NuGet Yereller](/dotnet/core/tools/dotnet-nuget-locals) komutunu yürütün:

```dotnetcli
dotnet nuget locals all --clear
```

**Bir sınıf kitaplığından etkinleştirme**

1. , [DotNet Build](/dotnet/core/tools/dotnet-build) komutuyla *hostingstartuplibrary* sınıf kitaplığını derleyin.
1. Sınıf kitaplığının *Hostingstartuplibrary* derleme adını `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ortam değişkenine ekleyin.
1. *bin*- *HostingStartupLibrary.dll* dosyasını sınıf kitaplığının derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne kopyalayarak, sınıf kitaplığının derlemesini uygulamaya dağıtın.
1. Uygulamayı derleyin ve çalıştırın. `<ItemGroup>`Uygulamanın proje dosyasındaki bir, sınıf kitaplığının derlemesine (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (derleme zamanı başvurusu) başvurur. Daha fazla bilgi için HostingStartupApp öğesinin proje dosyasındaki notlara bakın.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Dizin sayfası tarafından oluşturulan hizmet yapılandırma anahtarı değerlerinin, sınıf kitaplığının yöntemi tarafından ayarlanan değerlerle eşleştiğini gözlemleyin `ServiceKeyInjection.Configure` .

**Çalışma zamanı deposu tarafından dağıtılan bir derlemeden etkinleştirme**

1. *Startupdiagnostics* projesi dosyada *StartupDiagnostics.deps.js* değiştirmek için [PowerShell](/powershell/scripting/powershell-scripting) kullanır. PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak varsayılan olarak yüklüdür. Diğer platformlarda PowerShell 'i almak için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](/powershell/scripting/install/installing-powershell).
1. *Runtimesyürüme* klasöründe *build.ps1* betiğini yürütün. Betik şunları yapar:
   * , `StartupDiagnostics` *Obj\packages* klasöründe paketi oluşturur.
   * İçin çalışma zamanı deposunu `StartupDiagnostics` *Mağaza* klasöründe oluşturur. `dotnet store`Betikteki komut, `win7-x64` Windows 'a dağıtılan bir barındırma başlatması için [çalışma zamanı tanımlayıcısı 'nı (RID)](/dotnet/core/rid-catalog) kullanır. Farklı bir çalışma zamanı için barındırma başlangıcını sağlarken, betiğin 37. satırındaki doğru RID 'yi yerine koyun. Çalışma zamanı deposu `StartupDiagnostics` daha sonra derlemenin tüketilebileceği makinede kullanıcının veya sisteminin çalışma zamanı deposuna taşınır. Derlemenin Kullanıcı çalışma zamanı deposu yüklemesi konumu `StartupDiagnostics` *. DotNet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.
   * , `additionalDeps` `StartupDiagnostics` *Additionaldeps* klasöründe için öğesini oluşturur. Ek bağımlılıklar daha sonra kullanıcının veya sistem ek bağımlılıklarına taşınır. Kullanıcı `StartupDiagnostics` ek bağımlılıkları yüklemesi konumu *. DotNet/x64/additionalDeps/startupdiagnostics/Shared/Microsoft. netcore. App/2.2.0/StartupDiagnostics.deps.json*.
   * *deploy.ps1* dosyasını *dağıtım* klasörüne koyar.
1. *Dağıtım* klasöründe *deploy.ps1* betiğini çalıştırın. Betik şunu ekler:
   * `StartupDiagnostics``ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`ortam değişkenine.
   * Barındırma başlangıç bağımlılıkları yolu (Runtimessımında projenin *dağıtım* klasöründe) `DOTNET_ADDITIONAL_DEPS` ortam değişkenine.
   * Çalışma zamanı depolama yolu (Runtimes, projenin *dağıtım* klasöründe) `DOTNET_SHARED_STORE` ortam değişkenine.
1. Örnek uygulamayı çalıştırın.
1. `/services`Uygulamanın kayıtlı hizmetlerini görmek için uç nokta isteyin. `/diag`Tanılama bilgilerini görmek için uç nokta isteyin.

::: moniker-end
