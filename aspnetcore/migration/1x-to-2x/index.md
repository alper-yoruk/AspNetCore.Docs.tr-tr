---
title: ASP.NET Core 1. x ile 2,0 arasında geçiş yapın
author: scottaddie
description: Bu makalede, ASP.NET Core 1. x projesini ASP.NET Core 2,0 ' ye geçirmek için ön koşullar ve en sık kullanılan adımlar özetlenmektedir.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 1b7b89b130f66c851bf01d0eb6d643e4b3676a1e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774229"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>ASP.NET Core 1. x ile 2,0 arasında geçiş yapın

[Scott Ade](https://github.com/scottaddie) tarafından

Bu makalede, mevcut bir ASP.NET Core 1. x projesini ASP.NET Core 2,0 ' ye güncelleştirme konusunda size kılavuzluk ederiz. Uygulamanızı ASP.NET Core 2,0 ' ye geçirmek, [birçok yeni özellikten ve performans iyileştirmelerinden](xref:aspnetcore-2.0)yararlanmanızı sağlar.

Mevcut ASP.NET Core 1. x uygulamaları sürüme özgü proje şablonlarını temel alınır. ASP.NET Core Framework geliştikçe, proje şablonlarını ve bunların içinde yer alan başlangıç kodunu yapın. ASP.NET Core çerçevesini güncelleştirmenin yanı sıra, uygulamanız için kodu güncelleştirmeniz gerekir.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Önkoşullar

Bkz. [ASP.NET Core kullanmaya başlama](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Hedef Framework bilinen adını güncelleştir (tfd)

.NET Core 'un hedeflediği projeler, .NET Core 2,0 ' den büyük veya buna eşit bir sürümün [tfd](/dotnet/standard/frameworks) 'sini kullanmalıdır. *. Csproj* `netcoreapp2.0`dosyasında `<TargetFramework>` düğümünü arayın ve iç metnini ile değiştirin:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

.NET Framework hedefleyen projeler, .NET Framework 4.6.1 daha büyük veya buna eşit bir sürümün tfd 'sini kullanmalıdır. *. Csproj* `net461`dosyasında `<TargetFramework>` düğümünü arayın ve iç metnini ile değiştirin:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2,0, .NET Core 1. x ' ten çok daha büyük bir yüzey alanı sunar. .NET Core 1. x içinde eksik API 'Ler nedeniyle yalnızca .NET Framework hedefliyorsanız, .NET Core 2,0 ' i hedeflemek büyük olasılıkla işe çalışabilmektedir.

Proje dosyası içeriyorsa `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)bakın.

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Global. JSON içinde .NET Core SDK sürümü güncelleştirme

Çözümünüz belirli bir .NET Core SDK sürümünü hedeflemek için bir [Global. JSON](/dotnet/core/tools/global-json) dosyası kullanıyorsa, `version` özelliğini makinenizde yüklü 2,0 sürümünü kullanacak şekilde güncelleştirin:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Paket başvurularını Güncelleştir

1. x projesindeki *. csproj* dosyası, proje tarafından kullanılan her bir NuGet paketini listeler.

.NET Core 2,0 ' i hedefleyen ASP.NET Core 2,0 projesinde, *. csproj* dosyasındaki tek bir [metapackage](xref:fundamentals/metapackage) başvurusu, paket koleksiyonunun yerini alır:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

ASP.NET Core 2,0 ve Entity Framework Core 2,0 ' nin tüm özellikleri metapackage 'e dahildir.

.NET Framework hedefleyen ASP.NET Core 2,0 projeleri bireysel NuGet paketlerine başvurmasına devam etmelidir. Her `<PackageReference />` düğümün `Version` özniteliğini 2.0.0 olarak güncelleştirin.

Örneğin, tipik bir ASP.NET Core 2,0 proje hedefleme `<PackageReference />` .NET Framework için kullanılan düğümlerin listesi aşağıda verilmiştir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>.NET Core CLI araçlarını Güncelleştir

*. Csproj* dosyasında, her `Version` `<DotNetCliToolReference />` bir düğümün özniteliğini 2.0.0 olarak güncelleştirin.

Örneğin, .NET Core 2,0 ' i hedefleyen tipik bir ASP.NET Core 2,0 projesinde kullanılan CLı araçlarının listesi aşağıda verilmiştir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Paket hedefi geri dönüş özelliğini yeniden adlandır

Bir 1. x projesinin *. csproj* dosyası bir `PackageTargetFallback` düğüm ve değişken kullandı:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Hem node hem de değişkenini olarak `AssetTargetFallback`yeniden adlandırın:

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

1. x projelerinde, bir uygulamaya yapılandırma sağlayıcıları eklemek `Startup` Oluşturucu aracılığıyla gerçekleştirildi. Öğesinin bir örneğini oluşturma `ConfigurationBuilder`, ilgili sağlayıcıları yükleme (ortam değişkenleri, uygulama ayarları vb.) ve bir üyesini başlatma adımları. `IConfigurationRoot`

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Yukarıdaki örnek, `Configuration` üyeyi *appSettings. JSON* ' dan ve herhangi bir appSettings 'ten yapılandırma ayarları ile yükler *.\< Özelliğiyle eşleşen EnvironmentName\>. JSON* dosyası. `IHostingEnvironment.EnvironmentName` Bu dosyaların konumu *Startup.cs*ile aynı yoldur.

2,0 projesinde, 1. x projelerine devralınan ortak yapılandırma kodu, arka planda çalışır. Örneğin, ortam değişkenleri ve uygulama ayarları başlangıçta yüklenir. Denk *Startup.cs* kodu, eklenen örnekle başlatılacak `IConfiguration` şekilde azaltılır:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Tarafından `WebHostBuilder.CreateDefaultBuilder`eklenen varsayılan sağlayıcıları kaldırmak için, içindeki `Clear` `IConfigurationBuilder.Sources` özelliğindeki yöntemi çağırın. `ConfigureAppConfiguration` Sağlayıcıları geri eklemek için `ConfigureAppConfiguration` *program.cs*içindeki yöntemi kullanın:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Önceki kod parçacığında `CreateDefaultBuilder` yöntemi tarafından kullanılan yapılandırma [burada](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)görülebilir.

Daha fazla bilgi için [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index)konusuna bakın.

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Veritabanı başlatma kodunu taşı

EF Core 1. x kullanan 1. x projelerinde, gibi bir komut şunları `dotnet ef migrations add` yapar:

1. Bir `Startup` örneği başlatır
1. Bağımlılık ekleme `ConfigureServices` (türler dahil `DbContext` ) tüm hizmetleri kaydetmek için yöntemini çağırır
1. Önkoşul görevlerini gerçekleştirir

EF Core 2,0 `Program.BuildWebHost` kullanan 2,0 projesinde uygulama hizmetleri elde etmek için çağrılır. 1. x aksine, bu, çağırma `Startup.Configure`için ek yan etkiye sahiptir. 1. x uygulamanız `Configure` yönteminde veritabanı başlatma kodu çağırırdı, beklenmeyen sorunlar meydana gelebilir. Örneğin, veritabanı henüz yoksa, dengeli dağıtım kodu EF Core geçiş komutu yürütmeden önce çalışır. Bu sorun, veritabanı `dotnet ef migrations list` henüz yoksa bir komutun başarısız olmasına neden olur.

`Configure` *Startup.cs*yönteminde aşağıdaki 1. x çekirdek başlatma kodunu göz önünde bulundurun:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

2,0 projesinde, `SeedData.Initialize` çağrıyı `Main` *program.cs*yöntemine taşıyın:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

2,0 itibariyle, Web konağını derleme ve yapılandırma dışında içinde `BuildWebHost` herhangi bir şey yapmak hatalı bir uygulamadır. Uygulamayı çalıştırmaya ilişkin her şey, `BuildWebHost` &mdash; genellikle `Main` *program.cs*yönteminde dışında işlenmelidir.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Görünüm Razor derleme ayarını gözden geçir

Daha hızlı uygulama başlangıç süresi ve daha küçük yayımlanmış paketleri size en önemli öneme sahiptir. Bu nedenlerden dolayı, [ Razor ](xref:mvc/views/view-compilation) ASP.NET Core 2,0 ' de derleme görünümü varsayılan olarak etkinleştirilmiştir.

`MvcRazorCompileOnPublish` Özelliği true olarak ayarlamak artık gerekli değildir. Görünümü derlemeyi devre dışı bırakmadığınız takdirde, özelliği *. csproj* dosyasından kaldırılabilir.

.NET Framework hedeflenirken, yine de [Microsoft. AspNetCore. Mvc öğesine açıkça başvurmanız gerekirRazor. ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) *. Csproj* dosyanızdaki viewcompilation NuGet paketi:

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

## <a name="adopt-authenticationidentity-improvements"></a>Kimlik doğrulaması/Identity iyileştirmeleri benimseyin

ASP.NET Core 2,0 ' de yeni bir kimlik doğrulama modeli ve ASP.NET Core Identitybirçok önemli değişiklik vardır. Projenizi ayrı ayrı kullanıcı hesaplarıyla oluşturduysanız veya el ile kimlik doğrulaması eklediyseniz veya Identity [ASP.NET Core 2,0 ' Identity ye](xref:migration/1x-to-2x/identity-2x)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 2,0 ' deki Son değişiklikler](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
