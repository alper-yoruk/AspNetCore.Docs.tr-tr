---
title: ASP.NET Core'da barındırma başlangıç derlemelerini kullanma
author: rick-anderson
description: IHostingStartup uygulamasını kullanarak harici bir derlemeden ASP.NET Core uygulamasını nasıl geliştireceğimi keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417616"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>ASP.NET Core'da barındırma başlangıç derlemelerini kullanma

Yazar: [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

(Barındırma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> başlangıç) uygulaması, harici bir derlemeden başlangıçta bir uygulamaya geliştirmeler ekler. Örneğin, harici bir kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetleri sağlamak için barındırma başlangıç uygulamasını kullanabilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingBaşlangıç özniteliği

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirmek üzere bir barındırma başlangıç derlemesinin varlığını gösterir.

Giriş derlemesi veya `Startup` sınıfı içeren derleme öznitelik `HostingStartup` için otomatik olarak taranır. Öznitelikleri aramak için `HostingStartup` derlemeler listesi [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)yapılandırmadan çalışma zamanında yüklenir. Keşif hariç tutmak için derlemeler listesi [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)yüklenir.

Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad `StartupEnhancement`alanı . Barındırma başlangıç kodunu içeren `StartupEnhancementHostingStartup`sınıf:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Öznitelik `HostingStartup` genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında yer alır.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Yüklü barındırma başlangıç derlemelerini keşfedin

Yüklü barındırma başlangıç derlemelerini keşfetmek için günlüğe kaydetmeyi etkinleştirin ve uygulamanın günlüklerini kontrol edin. Derlemeler günlüğe yüklenirken oluşan hatalar. Yüklü barındırma başlangıç derlemeleri Hata Ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Barındırma başlangıç montajlarının otomatik yüklemesini devre dışı

Barındırma başlangıç derlemelerinin otomatik yüklemesini devre dışı kullanabilirsiniz, aşağıdaki yaklaşımlardan birini kullanın:

* Tüm barındırma başlangıç derlemelerinin yüklenmesini önlemek için `true` `1`aşağıdakilerden birini ayarlayın veya:

  * Hosting Başlangıç ana bilgisayar yapılandırma ayarını önleyin:

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

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`çevre değişkenidir.

* Belirli barındırma başlangıç derlemelerinin yüklenmesinden korunmak için, başlangıçta hariç tutmak için aşağıdakilerden birini yarı sütunlu sınırlı sayıda barındırma başlangıç derlemesi dizesine ayarlayın:

  * Barındırma Başlangıç Dışlama Derlemeleri ana bilgisayar yapılandırma ayarı:

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

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`çevre değişkenidir.

Hem ana bilgisayar yapılandırma ayarı hem de ortam değişkeni ayarlanırsa, ana bilgisayar ayarı davranışı denetler.

Ana bilgisayar ayarı veya ortam değişkenini kullanarak barındırma başlangıç derlemelerini devre dışı bırakmak, genel olarak montajı devre dışı bırakırsa ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.

## <a name="project"></a>Project

Aşağıdaki proje türlerinden birini içeren bir barındırma başlatma oluşturma:

* [Sınıf kitaplığı](#class-library)
* [Giriş noktası olmayan konsol uygulaması](#console-app-without-an-entry-point)

### <a name="class-library"></a>Sınıf kitaplığı

Bir barındırma başlangıç geliştirme bir sınıf kitaplığı sağlanabilir. Kitaplık bir `HostingStartup` öznitelik içerir.

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) bir Razor Pages uygulaması, *HostingStartupApp*ve bir sınıf kütüphanesi, *HostingStartupLibrary*içerir. Sınıf kitaplığı:

* Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular. `ServiceKeyInjection`bellek içi yapılandırma sağlayıcısını[(AddInMemoryCollection)](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)kullanarak uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.
* Barındırma `HostingStartup` başlangıç adı alanını ve sınıfını tanımlayan bir öznitelik içerir.

Sınıfın `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın Dizin sayfası, sınıf kitaplığı barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:

*HostingStartupApp/Sayfalar/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) da ayrı bir hosting başlangıç sağlayan bir NuGet paket projesi içerir, *HostingStartupPackage*. Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir. Paket:

* Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular. `ServiceKeyInjection`uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.
* Bir `HostingStartup` öznitelik içerir.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın Dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:

*HostingStartupApp/Sayfalar/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Giriş noktası olmayan konsol uygulaması

*Bu yaklaşım ,.NET Framework için değil, yalnızca .NET Core uygulamaları için kullanılabilir.*

Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik bir `HostingStartup` barındırma başlatma geliştirmesi, öznitelik içeren bir giriş noktası olmayan bir konsol uygulamasında sağlanabilir. Konsol uygulamasının yayımedilmesi, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi üretir.

Giriş noktası olmayan bir konsol uygulaması bu işlemde kullanılır, çünkü:

* Barındırma başlangıç derlemesinde barındırma başlangıç ını tüketmek için bir bağımlılık lar dosyası gereklidir. Bağımlılıklar dosyası, kitaplık değil, bir uygulamayayımlayarak üretilen çalıştırılabilir bir uygulama varlığıdır.
* Kitaplık, paylaşılan çalışma süresini hedefleyen çalıştırılabilir bir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)doğrudan ekilemez.

Dinamik bir barındırma başlangıç oluşturulmasında:

* Konsol uygulamasından bir giriş noktası olmadan bir barındırma başlangıç derlemesi oluşturulur:
  * `IHostingStartup` Uygulamayı içeren bir sınıf içerir.
  * Uygulama sınıfını `IHostingStartup` tanımlamak için bir [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir.
* Konsol uygulaması, barındırma başlatmanın bağımlılıklarını elde etmek için yayınlanır. Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpılmış olmasıdır.
* Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak üzere değiştirilir.
* Barındırma başlangıç derlemesi ve bağımlılıkları dosyası çalışma zamanı paket deposuna yerleştirilir. Barındırma başlangıç derlemesini ve bağımlılıkları dosyasını keşfetmek için, bunlar bir çift ortam değişkeninde listelenir.

Konsol uygulaması [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfı yükleme `IHostingStartup` ve yürütme için <xref:Microsoft.AspNetCore.Hosting.IWebHost>bir uygulama olarak tanımlar. Aşağıdaki örnekte, ad alanı `StartupEnhancement`ve sınıf: `StartupEnhancementHostingStartup`

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Bir sınıf `IHostingStartup`uygular. Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır. `IHostingStartup.Configure`barındırma başlangıç derlemesinde, kullanıcı `Startup.Configure` kodunda önceki çalışma süresine göre çağrılır ve bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından sağlanan herhangi bir yapılandırmanın üzerine yazmasına olanak tanır.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Bir `IHostingStartup` proje inşa ederken, bağımlılıklar dosyası (*.deps.json*) derlemenin `runtime` konumunu *çöp* kutusu klasörüne ayarlar:

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Dosyanın yalnızca bir bölümü gösterilir. Örnekteki montaj `StartupEnhancement`adı.

## <a name="configuration-provided-by-the-hosting-startup"></a>Barındırma başlatma tarafından sağlanan yapılandırma

Barındırma başlangıç yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak yapılandırmayı işlemek için iki yaklaşım vardır:

1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri çalıştırdıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın. Başlangıç yapılandırmasını barındırma, bu yaklaşımı kullanarak uygulamanın yapılandırması üzerinde öncelik alır.
1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın. Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.

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

Sınıf kitaplığı veya konsol uygulaması yla sağlanan barındırma başlatma için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` Ortam değişkeni, yarı kolonlu sınırlı bir derleme listesidir.

Öznitelik için `HostingStartup` yalnızca barındırma başlangıç derlemeleri taranır. Örnek uygulama, *HostingStartupApp*için, daha önce açıklanan barındırma başlangıçları keşfetmek için, ortam değişkeni aşağıdaki değere ayarlanır:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Barındırma başlangıç derlemesi, Barındırma Başlangıç Derlemeleri ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir:

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

Birden çok barındırma başlangıç derlemesi olduğunda, yöntemleri <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> derlemelerin listelenme sırasına göre yürütülür.

## <a name="activation"></a>Etkinleştirme

Başlangıç etkinleştirme barındırma seçenekleri şunlardır:

* [Runtime store](#runtime-store) &ndash; Etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez. Örnek uygulama, barındırma başlatma başlatma ve bağımlılık dosyalarını çok makineli bir ortamda barındırma başlatmanın dağıtımını kolaylaştırmak için bir klasöre, *dağıtıma*yerleştirir. *Dağıtım* klasörü, barındırma başlatmayı etkinleştirmek için dağıtım sisteminde ortam değişkenleri oluşturan veya değiştiren bir PowerShell komut dosyası da içerir.
* Etkinleştirme için gerekli zaman başvurularını derleme
  * [NuGet paketi](#nuget-package)
  * [Proje bin klasörü](#project-bin-folder)

### <a name="runtime-store"></a>Runtime deposu

Barındırma başlangıç uygulaması çalışma [zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilir. Gelişmiş uygulama tarafından derleme zamanı derleme süresi başvurusu gerekmez.

Barındırma başlatma oluşturulduktan sonra, manifest proje dosyası ve [dotnet deposu](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir runtime deposu oluşturulur.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Örnek uygulamada *(RuntimeStore* projesi) aşağıdaki komut kullanılır:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Çalışma zamanı deposunu keşfetmek için, çalışma zamanı mağazasının konumu ortam `DOTNET_SHARED_STORE` değişkenine eklenir.

**Barındırma başlatmanın bağımlılıklar dosyasını değiştirin ve yerleştirin**

Geliştirmeye paket başvurusu yapmadan geliştirmeyi etkinleştirmek için, çalışma süresine ek bağımlılıklar belirtin. `additionalDeps` `additionalDeps`şunları yapmanızı sağlar:

* Başlangıçta uygulamanın kendi *.deps.json* dosyasıyla birleştirmek için bir dizi ek *.deps.json* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.
* Barındırma başlangıç tertibatını kullanılabilir ve yüklenebilir hale getirin.

Ek bağımlılıklar dosyasını oluşturmak için önerilen yaklaşım:

 1. Önceki `dotnet publish` bölümde başvurulan çalışma zamanı deposu bildirimi dosyasında yürütün.
 1. Açıktan başvuruyu kitaplıklardan ve ortaya çıkan `runtime` *.deps.json* dosyasının bölümünden kaldırın.

Örnek projede, `store.manifest/1.0.0` özellik `targets` ve `libraries` bölüm kaldırılır:

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

*.deps.json* dosyasını aşağıdaki konuma yerleştirin:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`&ndash; Ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenen konum.
* `{SHARED FRAMEWORK NAME}`&ndash; Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.
* `{SHARED FRAMEWORK VERSION}`&ndash; Minimum paylaşılan çerçeve sürümü.
* `{ENHANCEMENT ASSEMBLY NAME}`&ndash; Geliştirmenin montaj adı.

Örnek uygulamada *(RuntimeStore* projesi), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Çalışma zamanı deposu konumunu bulmak için çalışma zamanı için ek bağımlılıklar dosya konumu ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenir.

Örnek uygulamada *(RuntimeStore* projesi), çalışma zamanı deposunu oluşturmak ve ek bağımlılıklar dosyası oluşturmak [powershell](/powershell/scripting/powershell-scripting) komut dosyası kullanılarak gerçekleştirilir.

Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanabildiğini örnekler için [bkz.](xref:fundamentals/environments)

**Dağıtım**

Çok makineli bir ortamda barındırma başlatma dağıtımını kolaylaştırmak için, örnek uygulama yayımlanmış çıktıda aşağıdakileri içeren bir *dağıtım* klasörü oluşturur:

* Barındırma başlangıç çalışma zamanı mağazası.
* Barındırma başlangıç bağımlılıkları dosyası.
* Bir PowerShell komut dosyası oluşturur veya `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE`değiştirir `DOTNET_ADDITIONAL_DEPS` , , , ve barındırma başlangıç etkinleştirme desteklemek için. Komut dosyasını dağıtım sistemindeki yönetimpowershell komut isteminden çalıştırın.

### <a name="nuget-package"></a>NuGet paketi

Bir barındırma başlangıç geliştirme bir NuGet paketinde sağlanabilir. Paketin bir `HostingStartup` özelliği vardır. Paket tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmaktadır:

* Geliştirilmiş uygulamanın proje dosyası, uygulamanın proje dosyasındaki barındırma başlatma için bir paket başvurusu yapar (derleme zamanı başvurusu). Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir. Bu yaklaşım, [nuget.org](https://www.nuget.org/)için yayınlanan bir barındırma başlangıç montaj paketi için geçerlidir.
* Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).

NuGet paketleri ve çalışma zamanı mağazası hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Çapraz Platform Araçları ile NuGet Paketi Nasıl Oluşturulur?](/dotnet/core/deploying/creating-nuget-packages)
* [Yayımlama paketleri](/nuget/create-packages/publish-a-package)
* [Çalışma zamanı paket deposu](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Proje bin klasörü

Barındırma başlatma geliştirmesi, geliştirilmiş uygulamada depo *gözü*tarafından dağıtılan bir derleme tarafından sağlanabilir. Derleme tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmuştur:

* Geliştirilmiş uygulamanın proje dosyası, barındırma başlatma (derleme zamanı başvurusu) için bir derleme başvurusu yapar. Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir. Bu yaklaşım, dağıtım senaryosu barındırma başlangıç derlemesi *(.dll* dosyası) için derleme zamanı başvurusu yapılması ve derlemenin aşağıdakilerden herhangi biri için taşınması gerektiğinde geçerlidir:
  * Tüketen proje.
  * Tüketen proje tarafından erişilebilen bir konum.
* Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).
* .NET Framework hedeflenirken, derleme varsayılan yük bağlamında yüklenebilir, bu da .NET Framework'de derlemenin aşağıdaki konumlardan herhangi birinde bulunduğu anlamına gelir:
  * Uygulama temel &ndash; yolu Uygulamanın yürütülebileceği kutu *klasörü* (*.exe*) bulunur.
  * Küresel Derleme Önbelleği &ndash; (GAC) GAC, birkaç .NET Framework uygulamasının paylaştığı derlemeleri depolar. Daha fazla bilgi için bkz: .NET Framework belgelerindeki [genel derleme önbelleğine montaj yükleme.](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)

## <a name="sample-code"></a>Örnek kod

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)başlangıç uygulama senaryolarını barındırmayı gösterir:

* İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar değeri çiftleri kümesi:
  * NuGet paketi (*HostingStartupPackage*)
  * Sınıf kitaplığı (*HostingStartupLibrary*)
* Barındırma başlatma, depotarafından dağıtılan bir derlemeden *(Başlangıç Tanılama)* etkinleştirilir. Derleme, başlangıçta uygulamaya tanılama bilgileri sağlayan iki ara yazılım ekler:
  * Kayıtlı hizmetler
  * Adres (şema, ana bilgisayar, yol tabanı, yol, sorgu dizesi)
  * Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)
  * İstek üst bilgileri
  * Ortam değişkenleri

Örneği çalıştırmak için:

**NuGet paketinden etkinleştirme**

1. *HostingStartupPackage* paketini [dotnet paketi](/dotnet/core/tools/dotnet-pack) komutu ile derle.
1. *HostingStartupPackage* paketinin montaj adını ortam değişkenine `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ekleyin.
1. Uygulamayı derle ve çalıştır. Geliştirilmiş uygulamada bir paket başvurusu bulunur (derleme zamanı başvurusu). Uygulamanın proje dosyasında a, `<PropertyGroup>` paket projenin çıktısını belirtir (*... /HostingStartupPackage/bin/Debug*) paket kaynağı olarak. Bu, uygulamanın paketi [nuget.org](https://www.nuget.org/)yüklemeden paketini kullanmasına olanak tanır. Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin paket `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.

*HostingStartupPackage* projesinde değişiklik yapar ve yeniden derlerseniz, *HostingStartupApp'ın* yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin. Yerel NuGet önbelleklerini temizlemek için aşağıdaki [dotnet nuget yerel komutunu](/dotnet/core/tools/dotnet-nuget-locals) uygulayın:

```dotnetcli
dotnet nuget locals all --clear
```

**Sınıf kitaplığından etkinleştirme**

1. *HostingStartupLibrary* sınıf kitaplığını [dotnet build](/dotnet/core/tools/dotnet-build) komutuyla derle.
1. Sınıf kitaplığı *derleme* sinin derleme adını çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine ekleyin.
1. *bin*-sınıf kitaplığı derlemesi'nin derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne *HostingStartupLibrary.dll* dosyasını kopyalayarak sınıf kitaplığı derlemesini uygulamaya dağıtın.
1. Uygulamayı derle ve çalıştır. Uygulamanın proje `<ItemGroup>` dosyasında sınıf kitaplığı derlemesi *(.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll)*(derleme zamanı başvurusu) başvurur. Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin sınıf kitaplığı `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.

**Çalışma zamanı depotarafından dağıtılan bir derlemeden etkinleştirme**

1. *StartupDiagnostics* *projesi, StartupDiagnostics.deps.json* dosyasını değiştirmek için [PowerShell'i](/powershell/scripting/powershell-scripting) kullanır. PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak Windows'da varsayılan olarak yüklenir. PowerShell'i diğer platformlarda elde etmek için [PowerShell'in çeşitli sürümlerini yükleme bölümüne](/powershell/scripting/install/installing-powershell)bakın.
1. *RuntimeStore* klasöründe *build.ps1* komut dosyasını çalıştırın. Komut dosyası:
   * `StartupDiagnostics` Paketi *obj\packages* klasöründe oluşturur.
   * `StartupDiagnostics` *Mağaza* klasöründe çalışma zamanı deposunu oluşturur. Komut `dotnet store` dosyasındaki komut, `win7-x64` Windows'a dağıtılan bir barındırma başlatma için [çalışma zamanı tanımlayıcısını (RID)](/dotnet/core/rid-catalog) kullanır. Barındırma başlatmayı farklı bir çalışma süresi için sağlarken, komut dosyasının 37. Çalışma zamanı deposu `StartupDiagnostics` daha sonra montajın tüketileceği makinede kullanıcının veya sistemin çalışma zamanı deposuna taşınır. Montaj için `StartupDiagnostics` kullanıcı runtime mağaza yükleme yeri *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.
   * *Ek Deps* klasöründe `additionalDeps` for `StartupDiagnostics` oluşturur. Ek bağımlılıklar daha sonra kullanıcının veya sistemin ek bağımlılıklarına taşınır. Kullanıcı `StartupDiagnostics` ek bağımlılıkları yükleme konumu *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.
   * *deploy.ps1* dosyasını *dağıtım* klasörüne yerleştirir.
1. *Dağıtım* klasöründe *deploy.ps1* komut dosyasını çalıştırın. Komut dosyası ekler:
   * `StartupDiagnostics`çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine.
   * Barındırma başlangıç bağımlılıkları yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_ADDITIONAL_DEPS`
   * Çalışma zamanı deposu yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_SHARED_STORE`
1. Örnek uygulamayı çalıştırın.
1. Uygulamanın `/services` kayıtlı hizmetlerini görmek için bitiş noktasını isteyin. Tanılama `/diag` bilgilerini görmek için bitiş noktasını isteyin.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

(Barındırma <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> başlangıç) uygulaması, harici bir derlemeden başlangıçta bir uygulamaya geliştirmeler ekler. Örneğin, harici bir kitaplık, bir uygulamaya ek yapılandırma sağlayıcıları veya hizmetleri sağlamak için barındırma başlangıç uygulamasını kullanabilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>HostingBaşlangıç özniteliği

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, çalışma zamanında etkinleştirmek üzere bir barındırma başlangıç derlemesinin varlığını gösterir.

Giriş derlemesi veya `Startup` sınıfı içeren derleme öznitelik `HostingStartup` için otomatik olarak taranır. Öznitelikleri aramak için `HostingStartup` derlemeler listesi [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)yapılandırmadan çalışma zamanında yüklenir. Keşif hariç tutmak için derlemeler listesi [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)yüklenir. Daha fazla bilgi için [Bkz. Web Host: Başlangıç Derlemelerini Barındırma](xref:fundamentals/host/web-host#hosting-startup-assemblies) ve [Web Barındırma: Başlangıç Hariç Derlemeleri Barındırma.](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)

Aşağıdaki örnekte, barındırma başlangıç derlemesinin ad `StartupEnhancement`alanı . Barındırma başlangıç kodunu içeren `StartupEnhancementHostingStartup`sınıf:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Öznitelik `HostingStartup` genellikle barındırma başlangıç derlemesinin `IHostingStartup` uygulama sınıfı dosyasında yer alır.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Yüklü barındırma başlangıç derlemelerini keşfedin

Yüklü barındırma başlangıç derlemelerini keşfetmek için günlüğe kaydetmeyi etkinleştirin ve uygulamanın günlüklerini kontrol edin. Derlemeler günlüğe yüklenirken oluşan hatalar. Yüklü barındırma başlangıç derlemeleri Hata Ayıklama düzeyinde günlüğe kaydedilir ve tüm hatalar günlüğe kaydedilir.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Barındırma başlangıç montajlarının otomatik yüklemesini devre dışı

Barındırma başlangıç derlemelerinin otomatik yüklemesini devre dışı kullanabilirsiniz, aşağıdaki yaklaşımlardan birini kullanın:

* Tüm barındırma başlangıç derlemelerinin yüklenmesini önlemek için `true` `1`aşağıdakilerden birini ayarlayın veya:
  * [Hosting Başlangıç](xref:fundamentals/host/web-host#prevent-hosting-startup) ana bilgisayar yapılandırma ayarını engelleyin.
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`çevre değişkenidir.
* Belirli barındırma başlangıç derlemelerinin yüklenmesinden korunmak için, başlangıçta hariç tutmak için aşağıdakilerden birini yarı sütunlu sınırlı sayıda barındırma başlangıç derlemesi dizesine ayarlayın:
  * [Barındırma Başlangıç Dışlama Derlemeleri](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ana bilgisayar yapılandırma ayarı.
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`çevre değişkenidir.

Hem ana bilgisayar yapılandırma ayarı hem de ortam değişkeni ayarlanırsa, ana bilgisayar ayarı davranışı denetler.

Ana bilgisayar ayarı veya ortam değişkenini kullanarak barındırma başlangıç derlemelerini devre dışı bırakmak, genel olarak montajı devre dışı bırakırsa ve bir uygulamanın çeşitli özelliklerini devre dışı bırakabilir.

## <a name="project"></a>Project

Aşağıdaki proje türlerinden birini içeren bir barındırma başlatma oluşturma:

* [Sınıf kitaplığı](#class-library)
* [Giriş noktası olmayan konsol uygulaması](#console-app-without-an-entry-point)

### <a name="class-library"></a>Sınıf kitaplığı

Bir barındırma başlangıç geliştirme bir sınıf kitaplığı sağlanabilir. Kitaplık bir `HostingStartup` öznitelik içerir.

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) bir Razor Pages uygulaması, *HostingStartupApp*ve bir sınıf kütüphanesi, *HostingStartupLibrary*içerir. Sınıf kitaplığı:

* Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular. `ServiceKeyInjection`bellek içi yapılandırma sağlayıcısını[(AddInMemoryCollection)](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)kullanarak uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.
* Barındırma `HostingStartup` başlangıç adı alanını ve sınıfını tanımlayan bir öznitelik içerir.

Sınıfın `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın Dizin sayfası, sınıf kitaplığı barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:

*HostingStartupApp/Sayfalar/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) da ayrı bir hosting başlangıç sağlayan bir NuGet paket projesi içerir, *HostingStartupPackage*. Paket, daha önce açıklanan sınıf kitaplığıyla aynı özelliklere sahiptir. Paket:

* Bir barındırma başlangıç `ServiceKeyInjection`sınıfı içerir, `IHostingStartup`hangi uygular. `ServiceKeyInjection`uygulamanın yapılandırmasına bir çift hizmet dizeleri ekler.
* Bir `HostingStartup` öznitelik içerir.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Uygulamanın Dizin sayfası, paketin barındırma başlangıç derlemesi tarafından ayarlanan iki anahtar için yapılandırma değerlerini okur ve işler:

*HostingStartupApp/Sayfalar/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Giriş noktası olmayan konsol uygulaması

*Bu yaklaşım ,.NET Framework için değil, yalnızca .NET Core uygulamaları için kullanılabilir.*

Etkinleştirme için derleme zamanı başvurusu gerektirmeyen dinamik bir `HostingStartup` barındırma başlatma geliştirmesi, öznitelik içeren bir giriş noktası olmayan bir konsol uygulamasında sağlanabilir. Konsol uygulamasının yayımedilmesi, çalışma zamanı deposundan tüketilebilen bir barındırma başlangıç derlemesi üretir.

Giriş noktası olmayan bir konsol uygulaması bu işlemde kullanılır, çünkü:

* Barındırma başlangıç derlemesinde barındırma başlangıç ını tüketmek için bir bağımlılık lar dosyası gereklidir. Bağımlılıklar dosyası, kitaplık değil, bir uygulamayayımlayarak üretilen çalıştırılabilir bir uygulama varlığıdır.
* Kitaplık, paylaşılan çalışma süresini hedefleyen çalıştırılabilir bir proje gerektiren [çalışma zamanı paket deposuna](/dotnet/core/deploying/runtime-store)doğrudan ekilemez.

Dinamik bir barındırma başlangıç oluşturulmasında:

* Konsol uygulamasından bir giriş noktası olmadan bir barındırma başlangıç derlemesi oluşturulur:
  * `IHostingStartup` Uygulamayı içeren bir sınıf içerir.
  * Uygulama sınıfını `IHostingStartup` tanımlamak için bir [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği içerir.
* Konsol uygulaması, barındırma başlatmanın bağımlılıklarını elde etmek için yayınlanır. Konsol uygulamasını yayımlamanın bir sonucu, kullanılmayan bağımlılıkların bağımlılıklar dosyasından kırpılmış olmasıdır.
* Bağımlılıklar dosyası, barındırma başlangıç derlemesinin çalışma zamanı konumunu ayarlamak üzere değiştirilir.
* Barındırma başlangıç derlemesi ve bağımlılıkları dosyası çalışma zamanı paket deposuna yerleştirilir. Barındırma başlangıç derlemesini ve bağımlılıkları dosyasını keşfetmek için, bunlar bir çift ortam değişkeninde listelenir.

Konsol uygulaması [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) paketine başvurur:

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) özniteliği, bir sınıfı yükleme `IHostingStartup` ve yürütme için <xref:Microsoft.AspNetCore.Hosting.IWebHost>bir uygulama olarak tanımlar. Aşağıdaki örnekte, ad alanı `StartupEnhancement`ve sınıf: `StartupEnhancementHostingStartup`

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Bir sınıf `IHostingStartup`uygular. Sınıfın <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> yöntemi, bir <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> uygulamaya geliştirme eklemek için kullanır. `IHostingStartup.Configure`barındırma başlangıç derlemesinde, kullanıcı `Startup.Configure` kodunda önceki çalışma süresine göre çağrılır ve bu da kullanıcı kodunun barındırma başlangıç derlemesi tarafından sağlanan herhangi bir yapılandırmanın üzerine yazmasına olanak tanır.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Bir `IHostingStartup` proje inşa ederken, bağımlılıklar dosyası (*.deps.json*) derlemenin `runtime` konumunu *çöp* kutusu klasörüne ayarlar:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Dosyanın yalnızca bir bölümü gösterilir. Örnekteki montaj `StartupEnhancement`adı.

## <a name="configuration-provided-by-the-hosting-startup"></a>Barındırma başlatma tarafından sağlanan yapılandırma

Barındırma başlangıç yapılandırmasının öncelikli olmasını mı yoksa uygulamanın yapılandırmasının öncelikli olmasını mı istediğinize bağlı olarak yapılandırmayı işlemek için iki yaklaşım vardır:

1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri çalıştırdıktan sonra yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın. Başlangıç yapılandırmasını barındırma, bu yaklaşımı kullanarak uygulamanın yapılandırması üzerinde öncelik alır.
1. Uygulamanın <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> temsilcileri yürütmeden önce yapılandırmayı yüklemek için kullanarak uygulamaya yapılandırma sağlayın. Uygulamanın yapılandırma değerleri, bu yaklaşımı kullanarak barındırma başlatma tarafından sağlananlara göre önceliklidir.

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

Sınıf kitaplığı veya konsol uygulaması yla sağlanan barındırma başlatma için, ortam değişkeninde barındırma başlangıç derlemesinin adını belirtin. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` Ortam değişkeni, yarı kolonlu sınırlı bir derleme listesidir.

Öznitelik için `HostingStartup` yalnızca barındırma başlangıç derlemeleri taranır. Örnek uygulama, *HostingStartupApp*için, daha önce açıklanan barındırma başlangıçları keşfetmek için, ortam değişkeni aşağıdaki değere ayarlanır:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Barındırma başlangıç derlemesi, [Barındırma Başlangıç Derlemeleri](xref:fundamentals/host/web-host#hosting-startup-assemblies) ana bilgisayar yapılandırma ayarı kullanılarak da ayarlanabilir.

Birden çok barındırma başlangıç derlemesi olduğunda, yöntemleri <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> derlemelerin listelenme sırasına göre yürütülür.

## <a name="activation"></a>Etkinleştirme

Başlangıç etkinleştirme barındırma seçenekleri şunlardır:

* [Runtime store](#runtime-store) &ndash; Etkinleştirme etkinleştirme için derleme zamanı başvurusu gerektirmez. Örnek uygulama, barındırma başlatma başlatma ve bağımlılık dosyalarını çok makineli bir ortamda barındırma başlatmanın dağıtımını kolaylaştırmak için bir klasöre, *dağıtıma*yerleştirir. *Dağıtım* klasörü, barındırma başlatmayı etkinleştirmek için dağıtım sisteminde ortam değişkenleri oluşturan veya değiştiren bir PowerShell komut dosyası da içerir.
* Etkinleştirme için gerekli zaman başvurularını derleme
  * [NuGet paketi](#nuget-package)
  * [Proje bin klasörü](#project-bin-folder)

### <a name="runtime-store"></a>Runtime deposu

Barındırma başlangıç uygulaması çalışma [zamanı deposuna](/dotnet/core/deploying/runtime-store)yerleştirilir. Gelişmiş uygulama tarafından derleme zamanı derleme süresi başvurusu gerekmez.

Barındırma başlatma oluşturulduktan sonra, manifest proje dosyası ve [dotnet deposu](/dotnet/core/tools/dotnet-store) komutu kullanılarak bir runtime deposu oluşturulur.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Örnek uygulamada *(RuntimeStore* projesi) aşağıdaki komut kullanılır:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Çalışma zamanı deposunu keşfetmek için, çalışma zamanı mağazasının konumu ortam `DOTNET_SHARED_STORE` değişkenine eklenir.

**Barındırma başlatmanın bağımlılıklar dosyasını değiştirin ve yerleştirin**

Geliştirmeye paket başvurusu yapmadan geliştirmeyi etkinleştirmek için, çalışma süresine ek bağımlılıklar belirtin. `additionalDeps` `additionalDeps`şunları yapmanızı sağlar:

* Başlangıçta uygulamanın kendi *.deps.json* dosyasıyla birleştirmek için bir dizi ek *.deps.json* dosyası sağlayarak uygulamanın kitaplık grafiğini genişletin.
* Barındırma başlangıç tertibatını kullanılabilir ve yüklenebilir hale getirin.

Ek bağımlılıklar dosyasını oluşturmak için önerilen yaklaşım:

 1. Önceki `dotnet publish` bölümde başvurulan çalışma zamanı deposu bildirimi dosyasında yürütün.
 1. Açıktan başvuruyu kitaplıklardan ve ortaya çıkan `runtime` *.deps.json* dosyasının bölümünden kaldırın.

Örnek projede, `store.manifest/1.0.0` özellik `targets` ve `libraries` bölüm kaldırılır:

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

*.deps.json* dosyasını aşağıdaki konuma yerleştirin:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`&ndash; Ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenen konum.
* `{SHARED FRAMEWORK NAME}`&ndash; Bu ek bağımlılıklar dosyası için paylaşılan çerçeve gereklidir.
* `{SHARED FRAMEWORK VERSION}`&ndash; Minimum paylaşılan çerçeve sürümü.
* `{ENHANCEMENT ASSEMBLY NAME}`&ndash; Geliştirmenin montaj adı.

Örnek uygulamada *(RuntimeStore* projesi), ek bağımlılıklar dosyası aşağıdaki konuma yerleştirilir:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Çalışma zamanı deposu konumunu bulmak için çalışma zamanı için ek bağımlılıklar dosya konumu ortam değişkenine `DOTNET_ADDITIONAL_DEPS` eklenir.

Örnek uygulamada *(RuntimeStore* projesi), çalışma zamanı deposunu oluşturmak ve ek bağımlılıklar dosyası oluşturmak [powershell](/powershell/scripting/powershell-scripting) komut dosyası kullanılarak gerçekleştirilir.

Çeşitli işletim sistemleri için ortam değişkenlerinin nasıl ayarlanabildiğini örnekler için [bkz.](xref:fundamentals/environments)

**Dağıtım**

Çok makineli bir ortamda barındırma başlatma dağıtımını kolaylaştırmak için, örnek uygulama yayımlanmış çıktıda aşağıdakileri içeren bir *dağıtım* klasörü oluşturur:

* Barındırma başlangıç çalışma zamanı mağazası.
* Barındırma başlangıç bağımlılıkları dosyası.
* Bir PowerShell komut dosyası oluşturur veya `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_SHARED_STORE`değiştirir `DOTNET_ADDITIONAL_DEPS` , , , ve barındırma başlangıç etkinleştirme desteklemek için. Komut dosyasını dağıtım sistemindeki yönetimpowershell komut isteminden çalıştırın.

### <a name="nuget-package"></a>NuGet paketi

Bir barındırma başlangıç geliştirme bir NuGet paketinde sağlanabilir. Paketin bir `HostingStartup` özelliği vardır. Paket tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmaktadır:

* Geliştirilmiş uygulamanın proje dosyası, uygulamanın proje dosyasındaki barındırma başlatma için bir paket başvurusu yapar (derleme zamanı başvurusu). Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir. Bu yaklaşım, [nuget.org](https://www.nuget.org/)için yayınlanan bir barındırma başlangıç montaj paketi için geçerlidir.
* Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).

NuGet paketleri ve çalışma zamanı mağazası hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Çapraz Platform Araçları ile NuGet Paketi Nasıl Oluşturulur?](/dotnet/core/deploying/creating-nuget-packages)
* [Yayımlama paketleri](/nuget/create-packages/publish-a-package)
* [Çalışma zamanı paket deposu](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Proje bin klasörü

Barındırma başlatma geliştirmesi, geliştirilmiş uygulamada depo *gözü*tarafından dağıtılan bir derleme tarafından sağlanabilir. Derleme tarafından sağlanan barındırma başlangıç türleri aşağıdaki yaklaşımlardan biri kullanılarak uygulamaya sunulmuştur:

* Geliştirilmiş uygulamanın proje dosyası, barındırma başlatma (derleme zamanı başvurusu) için bir derleme başvurusu yapar. Derleme zamanı başvurusu yerinde olduğundan, barındırma başlangıç derlemesi ve tüm bağımlılıkları uygulamanın bağımlılık dosyasına *(.deps.json)* dahil edilir. Bu yaklaşım, dağıtım senaryosu barındırma başlangıç derlemesi *(.dll* dosyası) için derleme zamanı başvurusu yapılması ve derlemenin aşağıdakilerden herhangi biri için taşınması gerektiğinde geçerlidir:
  * Tüketen proje.
  * Tüketen proje tarafından erişilebilen bir konum.
* Barındırma başlatmanın bağımlılıklar dosyası, [Runtime store](#runtime-store) bölümünde açıklandığı gibi gelişmiş uygulama için kullanılabilir (derleme zamanı başvurusu olmadan).
* .NET Framework hedeflenirken, derleme varsayılan yük bağlamında yüklenebilir, bu da .NET Framework'de derlemenin aşağıdaki konumlardan herhangi birinde bulunduğu anlamına gelir:
  * Uygulama temel &ndash; yolu Uygulamanın yürütülebileceği kutu *klasörü* (*.exe*) bulunur.
  * Küresel Derleme Önbelleği &ndash; (GAC) GAC, birkaç .NET Framework uygulamasının paylaştığı derlemeleri depolar. Daha fazla bilgi için bkz: .NET Framework belgelerindeki [genel derleme önbelleğine montaj yükleme.](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)

## <a name="sample-code"></a>Örnek kod

[Örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)başlangıç uygulama senaryolarını barındırmayı gösterir:

* İki barındırma başlangıç derlemesi (sınıf kitaplıkları) her biri bellek içi yapılandırma anahtar değeri çiftleri kümesi:
  * NuGet paketi (*HostingStartupPackage*)
  * Sınıf kitaplığı (*HostingStartupLibrary*)
* Barındırma başlatma, depotarafından dağıtılan bir derlemeden *(Başlangıç Tanılama)* etkinleştirilir. Derleme, başlangıçta uygulamaya tanılama bilgileri sağlayan iki ara yazılım ekler:
  * Kayıtlı hizmetler
  * Adres (şema, ana bilgisayar, yol tabanı, yol, sorgu dizesi)
  * Bağlantı (uzak IP, uzak bağlantı noktası, yerel IP, yerel bağlantı noktası, istemci sertifikası)
  * İstek üst bilgileri
  * Ortam değişkenleri

Örneği çalıştırmak için:

**NuGet paketinden etkinleştirme**

1. *HostingStartupPackage* paketini [dotnet paketi](/dotnet/core/tools/dotnet-pack) komutu ile derle.
1. *HostingStartupPackage* paketinin montaj adını ortam değişkenine `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` ekleyin.
1. Uygulamayı derle ve çalıştır. Geliştirilmiş uygulamada bir paket başvurusu bulunur (derleme zamanı başvurusu). Uygulamanın proje dosyasında a, `<PropertyGroup>` paket projenin çıktısını belirtir (*... /HostingStartupPackage/bin/Debug*) paket kaynağı olarak. Bu, uygulamanın paketi [nuget.org](https://www.nuget.org/)yüklemeden paketini kullanmasına olanak tanır. Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin paket `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.

*HostingStartupPackage* projesinde değişiklik yapar ve yeniden derlerseniz, *HostingStartupApp'ın* yerel önbellekten eski bir paket değil, güncelleştirilmiş paketi aldığından emin olmak için yerel NuGet paket önbelleklerini temizleyin. Yerel NuGet önbelleklerini temizlemek için aşağıdaki [dotnet nuget yerel komutunu](/dotnet/core/tools/dotnet-nuget-locals) uygulayın:

```dotnetcli
dotnet nuget locals all --clear
```

**Sınıf kitaplığından etkinleştirme**

1. *HostingStartupLibrary* sınıf kitaplığını [dotnet build](/dotnet/core/tools/dotnet-build) komutuyla derle.
1. Sınıf kitaplığı *derleme* sinin derleme adını çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine ekleyin.
1. *bin*-sınıf kitaplığı derlemesi'nin derlenmiş çıktısından uygulamanın *bin/Debug* klasörüne *HostingStartupLibrary.dll* dosyasını kopyalayarak sınıf kitaplığı derlemesini uygulamaya dağıtın.
1. Uygulamayı derle ve çalıştır. Uygulamanın proje `<ItemGroup>` dosyasında sınıf kitaplığı derlemesi *(.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll)*(derleme zamanı başvurusu) başvurur. Daha fazla bilgi için HostingStartupApp'ın proje dosyasındaki notlara bakın.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Dizin sayfası tarafından işlenen hizmet yapılandırma anahtar değerlerinin sınıf kitaplığı `ServiceKeyInjection.Configure` yöntemitarafından ayarlanan değerlerle eşleştirdiğini gözlendirin.

**Çalışma zamanı depotarafından dağıtılan bir derlemeden etkinleştirme**

1. *StartupDiagnostics* *projesi, StartupDiagnostics.deps.json* dosyasını değiştirmek için [PowerShell'i](/powershell/scripting/powershell-scripting) kullanır. PowerShell, Windows 7 SP1 ve Windows Server 2008 R2 SP1 ile başlayarak Windows'da varsayılan olarak yüklenir. PowerShell'i diğer platformlarda elde etmek için [PowerShell'in çeşitli sürümlerini yükleme bölümüne](/powershell/scripting/install/installing-powershell)bakın.
1. *RuntimeStore* klasöründe *build.ps1* komut dosyasını çalıştırın. Komut dosyası:
   * `StartupDiagnostics` Paketi *obj\packages* klasöründe oluşturur.
   * `StartupDiagnostics` *Mağaza* klasöründe çalışma zamanı deposunu oluşturur. Komut `dotnet store` dosyasındaki komut, `win7-x64` Windows'a dağıtılan bir barındırma başlatma için [çalışma zamanı tanımlayıcısını (RID)](/dotnet/core/rid-catalog) kullanır. Barındırma başlatmayı farklı bir çalışma süresi için sağlarken, komut dosyasının 37. Çalışma zamanı deposu `StartupDiagnostics` daha sonra montajın tüketileceği makinede kullanıcının veya sistemin çalışma zamanı deposuna taşınır. Montaj için `StartupDiagnostics` kullanıcı runtime mağaza yükleme yeri *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.
   * *Ek Deps* klasöründe `additionalDeps` for `StartupDiagnostics` oluşturur. Ek bağımlılıklar daha sonra kullanıcının veya sistemin ek bağımlılıklarına taşınır. Kullanıcı `StartupDiagnostics` ek bağımlılıkları yükleme konumu *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.
   * *deploy.ps1* dosyasını *dağıtım* klasörüne yerleştirir.
1. *Dağıtım* klasöründe *deploy.ps1* komut dosyasını çalıştırın. Komut dosyası ekler:
   * `StartupDiagnostics`çevre `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` değişkenine.
   * Barındırma başlangıç bağımlılıkları yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_ADDITIONAL_DEPS`
   * Çalışma zamanı deposu yolu (RuntimeStore projesinin *dağıtım* klasöründe) ortam değişkenine. `DOTNET_SHARED_STORE`
1. Örnek uygulamayı çalıştırın.
1. Uygulamanın `/services` kayıtlı hizmetlerini görmek için bitiş noktasını isteyin. Tanılama `/diag` bilgilerini görmek için bitiş noktasını isteyin.

::: moniker-end
