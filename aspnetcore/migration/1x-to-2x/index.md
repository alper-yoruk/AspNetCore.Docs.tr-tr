---
title: ASP.NET Core 1. x ile 2,0 arasında geçiş yapın
author: scottaddie
description: Bu makalede, ASP.NET Core 1. x projesini ASP.NET Core 2,0 ' ye geçirmek için ön koşullar ve en sık kullanılan adımlar özetlenmektedir.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 97fe2f36aed4a2ac60a7ffc30ede5e682a838e5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408701"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>ASP.NET Core 1. x ile 2,0 arasında geçiş yapın

[Scott Ade](https://github.com/scottaddie) tarafından

Bu makalede, mevcut bir ASP.NET Core 1. x projesini ASP.NET Core 2,0 ' ye güncelleştirme konusunda size kılavuzluk ederiz. Uygulamanızı ASP.NET Core 2,0 ' ye geçirmek, [birçok yeni özellikten ve performans iyileştirmelerinden](xref:aspnetcore-2.0)yararlanmanızı sağlar.

Mevcut ASP.NET Core 1. x uygulamaları sürüme özgü proje şablonlarını temel alınır. ASP.NET Core Framework geliştikçe, proje şablonlarını ve bunların içinde yer alan başlangıç kodunu yapın. ASP.NET Core çerçevesini güncelleştirmenin yanı sıra, uygulamanız için kodu güncelleştirmeniz gerekir.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Ön koşullar

Bkz. [ASP.NET Core kullanmaya başlama](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Hedef Framework bilinen adını güncelleştir (tfd)

.NET Core 'un hedeflediği projeler, .NET Core 2,0 ' den büyük veya buna eşit bir sürümün [tfd](/dotnet/standard/frameworks) 'sini kullanmalıdır. `<TargetFramework>` *. Csproj* dosyasında düğümünü arayın ve iç metnini ile değiştirin `netcoreapp2.0` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

.NET Framework hedefleyen projeler, .NET Framework 4.6.1 daha büyük veya buna eşit bir sürümün tfd 'sini kullanmalıdır. `<TargetFramework>` *. Csproj* dosyasında düğümünü arayın ve iç metnini ile değiştirin `net461` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2,0, .NET Core 1. x ' ten çok daha büyük bir yüzey alanı sunar. .NET Core 1. x içinde eksik API 'Ler nedeniyle yalnızca .NET Framework hedefliyorsanız, .NET Core 2,0 ' i hedeflemek büyük olasılıkla işe çalışabilmektedir.

Proje dosyası içeriyorsa `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>` , [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)bakın.

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>global.jsüzerinde .NET Core SDK sürümü Güncelleştir

Çözümünüz belirli bir .NET Core SDK sürümünü hedeflemek için dosyadaki bir [global.js](/dotnet/core/tools/global-json) kullanıyorsa, `version` özelliğini makinenizde yüklü 2,0 sürümünü kullanacak şekilde güncelleştirin:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Paket başvurularını Güncelleştir

1. x projesindeki *. csproj* dosyası, proje tarafından kullanılan her bir NuGet paketini listeler.

.NET Core 2,0 ' i hedefleyen ASP.NET Core 2,0 projesinde, *. csproj* dosyasındaki tek bir [metapackage](xref:fundamentals/metapackage) başvurusu, paket koleksiyonunun yerini alır:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

ASP.NET Core 2,0 ve Entity Framework Core 2,0 ' nin tüm özellikleri metapackage 'e dahildir.

.NET Framework hedefleyen ASP.NET Core 2,0 projeleri bireysel NuGet paketlerine başvurmasına devam etmelidir. `Version`Her `<PackageReference />` düğümün özniteliğini 2.0.0 olarak güncelleştirin.

Örneğin, `<PackageReference />` tipik bir ASP.NET Core 2,0 proje hedefleme .NET Framework için kullanılan düğümlerin listesi aşağıda verilmiştir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>.NET Core CLI araçlarını Güncelleştir

*. Csproj* dosyasında, `Version` her bir `<DotNetCliToolReference />` düğümün özniteliğini 2.0.0 olarak güncelleştirin.

Örneğin, .NET Core 2,0 ' i hedefleyen tipik bir ASP.NET Core 2,0 projesinde kullanılan CLı araçlarının listesi aşağıda verilmiştir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Paket hedefi geri dönüş özelliğini yeniden adlandır

Bir 1. x projesinin *. csproj* dosyası bir `PackageTargetFallback` düğüm ve değişken kullandı:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Hem node hem de değişkenini olarak yeniden adlandırın `AssetTargetFallback` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Program.cs 'de ana yöntemi Güncelleştir

1. x projelerinde, `Main` *program.cs* yöntemi şunun gibi aranır:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

2,0 projesinde `Main` *program.cs* yöntemi basitleştirilmiştir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Bu yeni 2,0 deseninin benimsenmesi önemle önerilir ve [Entity Framework (EF) çekirdek geçişleri](xref:data/ef-mvc/migrations) gibi ürün özellikleri için gereklidir. Örneğin, `Update-Database` Paket Yöneticisi konsol penceresinden veya `dotnet ef database update` komut satırından (ASP.NET Core 2,0 ' e dönüştürülen projelerde), şu hatayı üretir:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Yapılandırma sağlayıcıları Ekle

1. x projelerinde, bir uygulamaya yapılandırma sağlayıcıları eklemek Oluşturucu aracılığıyla gerçekleştirildi `Startup` . Öğesinin bir örneğini oluşturma `ConfigurationBuilder` , ilgili sağlayıcıları yükleme (ortam değişkenleri, uygulama ayarları vb.) ve bir üyesini başlatma adımları `IConfigurationRoot` .

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Yukarıdaki örnek, `Configuration` üyeyiappsettings.jsve tüm appSettings *'teki* yapılandırma ayarlarıyla yükler *. \<EnvironmentName\> *özelliği ile eşleşen JSON dosyası `IHostingEnvironment.EnvironmentName` . Bu dosyaların konumu *Startup.cs*ile aynı yoldur.

2,0 projesinde, 1. x projelerine devralınan ortak yapılandırma kodu, arka planda çalışır. Örneğin, ortam değişkenleri ve uygulama ayarları başlangıçta yüklenir. Denk *Startup.cs* kodu, `IConfiguration` eklenen örnekle başlatılacak şekilde azaltılır:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Tarafından eklenen varsayılan sağlayıcıları kaldırmak için `WebHostBuilder.CreateDefaultBuilder` , `Clear` `IConfigurationBuilder.Sources` içindeki özelliğindeki yöntemi çağırın `ConfigureAppConfiguration` . Sağlayıcıları geri eklemek için `ConfigureAppConfiguration` *program.cs*içindeki yöntemi kullanın:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

`CreateDefaultBuilder`Önceki kod parçacığında yöntemi tarafından kullanılan yapılandırma [burada](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)görülebilir.

Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index)konusuna bakın.

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Veritabanı başlatma kodunu taşı

EF Core 1. x kullanan 1. x projelerinde, gibi bir komut `dotnet ef migrations add` şunları yapar:

1. Bir `Startup` örneği başlatır
1. `ConfigureServices`Bağımlılık ekleme (türler dahil) tüm hizmetleri kaydetmek için yöntemini çağırır `DbContext`
1. Önkoşul görevlerini gerçekleştirir

EF Core 2,0 kullanan 2,0 projesinde `Program.BuildWebHost` uygulama hizmetleri elde etmek için çağrılır. 1. x aksine, bu, çağırma için ek yan etkiye sahiptir `Startup.Configure` . 1. x uygulamanız yönteminde veritabanı başlatma kodu çağırırdı `Configure` , beklenmeyen sorunlar meydana gelebilir. Örneğin, veritabanı henüz yoksa, dengeli dağıtım kodu EF Core geçiş komutu yürütmeden önce çalışır. Bu sorun `dotnet ef migrations list` , veritabanı henüz yoksa bir komutun başarısız olmasına neden olur.

Startup.cs yönteminde aşağıdaki 1. x çekirdek başlatma kodunu göz önünde bulundurun `Configure` : *Startup.cs*

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

2,0 projesinde, `SeedData.Initialize` çağrıyı `Main` *program.cs*yöntemine taşıyın:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

2,0 itibariyle, `BuildWebHost` Web konağını derleme ve yapılandırma dışında içinde herhangi bir şey yapmak hatalı bir uygulamadır. Uygulamayı çalıştırmaya ilişkin her şey, `BuildWebHost` &mdash; genellikle `Main` *program.cs*yönteminde dışında işlenmelidir.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>RazorGörünüm derleme ayarını gözden geçir

Daha hızlı uygulama başlangıç süresi ve daha küçük yayımlanmış paketleri size en önemli öneme sahiptir. Bu nedenlerden dolayı, ASP.NET Core 2,0 ' de [ Razor derleme görünümü](xref:mvc/views/view-compilation) varsayılan olarak etkinleştirilmiştir.

`MvcRazorCompileOnPublish`Özelliği true olarak ayarlamak artık gerekli değildir. Görünümü derlemeyi devre dışı bırakmadığınız takdirde, özelliği *. csproj* dosyasından kaldırılabilir.

.NET Framework hedeflenirken, yine de [Microsoft. AspNetCore. Mvc öğesine açıkça başvurmanız gerekir. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) *. Csproj* dosyanızdaki viewcompilation NuGet paketi:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Application Insights "hafif" özelliklere güvenin

Uygulama performansı izleme için daha kolay bir kurulum önemlidir. Artık, Visual Studio 2017 Tooling ' de bulunan yeni [Application Insights](/azure/application-insights/app-insights-overview) "hafif" özelliklerine güvenebilirsiniz.

Visual Studio 2017 ' de oluşturulan ASP.NET Core 1,1 projeleri varsayılan olarak Application Insights eklenmiştir. Application Insights SDK 'yı doğrudan *program.cs* ve *Startup.cs*dışında kullanmıyorsanız, şu adımları izleyin:

1. .NET Core 'u hedefliyorsanız, `<PackageReference />` *. csproj* dosyasından aşağıdaki düğümü kaldırın:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. .NET Core 'u hedefliyorsanız, `UseApplicationInsights` *program.cs*öğesinden genişletme yöntemi çağrısını kaldırın:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. *_Layout. cshtml*'den Application Insights ISTEMCI tarafı API çağrısını kaldırın. Aşağıdaki iki satır kodu içerir:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Application Insights SDK 'Yı doğrudan kullanıyorsanız, bunu yapmaya devam edin. 2,0 [metapackage](xref:fundamentals/metapackage) , en son Application Insights sürümünü içerir, bu nedenle eski bir sürüme başvursanız bir paket düşürme hatası görüntülenir.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Kimlik doğrulaması/ Identity iyileştirmeleri benimseyin

ASP.NET Core 2,0 ' de yeni bir kimlik doğrulama modeli ve ASP.NET Core birçok önemli değişiklik vardır Identity . Projenizi ayrı ayrı kullanıcı hesaplarıyla oluşturduysanız veya el ile kimlik doğrulaması eklediyseniz veya Identity [ Identity ASP.NET Core 2,0 ' ye](xref:migration/1x-to-2x/identity-2x)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 2,0 ' deki Son değişiklikler](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
