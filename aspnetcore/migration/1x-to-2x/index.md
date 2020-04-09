---
title: ASP.NET Core 1.x'ten 2.0'a geçirin
author: scottaddie
description: Bu makalede, core 2.0'ASP.NET ASP.NET Core 1.x projesinin geçişinin ön koşulları ve en yaygın adımları özetgündür.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/1x-to-2x/index
ms.openlocfilehash: c46f50a418cf630980ac2ba94407e4370d36e7d5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667617"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>ASP.NET Core 1.x'ten 2.0'a geçirin

Yazar: [Scott Addie](https://github.com/scottaddie)

Bu makalede, core 2.0 ASP.NET mevcut bir ASP.NET Core 1.x proje güncelleme ile size yol. Uygulamanızı core 2.0'a ASP.NET geçirmek birçok [yeni özellik ve performans iyileştirmelerinden](xref:aspnetcore-2.0)yararlanmanızı sağlar.

Varolan ASP.NET Core 1.x uygulamaları, sürüme özgü proje şablonları temel alınmaktadır. ASP.NET Çekirdek çerçevesi geliştikçe, proje şablonları ve bunların içinde bulunan başlangıç kodu da gelişir. ASP.NET Core çerçevesini güncelleştirmenin yanı sıra, uygulamanızın kodunu güncelleştirmeniz gerekir.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Ön koşullar

Bkz. [ASP.NET Core ile Başlayın.](xref:getting-started)

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Hedef Çerçeve Takma Aparatı (TFM) Güncelleştir

.NET Core'u hedefleyen projeler, .NET Core 2.0'dan büyük veya eşit bir sürümün [TFM'sini](/dotnet/standard/frameworks) kullanmalıdır. *.csproj* dosyasındaki `<TargetFramework>` düğümü arayın ve iç metnini `netcoreapp2.0`şuyla değiştirin:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

.NET Framework'ü hedefleyen projeler, .NET Framework 4.6.1'den daha büyük veya eşit bir sürümün TFM'sini kullanmalıdır. *.csproj* dosyasındaki `<TargetFramework>` düğümü arayın ve iç metnini `net461`şuyla değiştirin:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2.0 ,NET Core 1.x'ten çok daha büyük bir yüzey alanı sunar. .NET Framework'ü yalnızca .NET Core 1.x'teki eksik API'ler nedeniyle hedefliyorsanız, .NET Core 2.0'ı hedeflemek olasıdır.

Proje dosyası içeriyorsa, `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`bu [GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)bakın.

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>global.json'da .NET Core SDK sürümünü güncelleyin

Çözümünüz belirli bir .NET Core SDK sürümünü hedeflemek için [global.json](/dotnet/core/tools/global-json) dosyasına güveniyorsa, makinenizde yüklü olan 2.0 sürümünü kullanmak için özelliğini `version` güncelleyin:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Paket referanslarını güncelleştir

1.x projedeki *.csproj* dosyası, proje tarafından kullanılan her NuGet paketini listeler.

.NET Core 2.0'ı hedefleyen ASP.NET Core 2.0 projesinde, *.csproj* dosyasındaki tek bir [metapaket](xref:fundamentals/metapackage) başvurusu, paketlerin toplanmasının yerini alır:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

ASP.NET Core 2.0 ve Entity Framework Core 2.0'ın tüm özellikleri meta pakete dahildir.

ASP.NET.NET Framework'u hedefleyen Core 2.0 projeleri tek tek NuGet paketlerine başvurmaya devam etmelidir. Her `<PackageReference />` `Version` düğümün özniteliğini 2.0.0 olarak güncelleştirin.

Örneğin, .NET Framework'ü `<PackageReference />` hedefleyen tipik bir ASP.NET Core 2.0 projesinde kullanılan düğümlerin listesi aşağıda verilmiştir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Güncelleme .NET Core CLI araçları

*.csproj* dosyasında, her `Version` `<DotNetCliToolReference />` düğümün özniteliğini 2.0.0 olarak güncelleştirin.

Örneğin, .NET Core 2.0'ı hedefleyen tipik bir ASP.NET Core 2.0 projesinde kullanılan CLI araçlarının listesi aşağıda verilmiştir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Paket Hedef Geri Dönüş özelliğini yeniden adlandırın

1.x projesinin *.csproj* dosyasında `PackageTargetFallback` düğüm ve değişken kullanılmıştır:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Düğümü ve değişkeni yeniden `AssetTargetFallback`adlandırın:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>ana yöntemi Program.cs güncelleştir

1.x projelerde `Main` *Program.cs* yöntemi şu na benzer:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

2.0 projelerde `Main` *Program.cs* yöntemi basitleştirilmiştir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Bu yeni 2.0 deseninin benimsenmesi şiddetle tavsiye edilir ve [Entity Framework (EF) Çekirdek Geçişler](xref:data/ef-mvc/migrations) gibi ürün özelliklerinin çalışması için gereklidir. Örneğin, Paket `Update-Database` Yöneticisi Konsolu penceresinden veya `dotnet ef database update` komut satırından (ASP.NET Core 2.0'a dönüştürülen projelerde) çalışan aşağıdaki hatayı oluşturur:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Yapılandırma sağlayıcıları ekleme

1.x projelerde, yapılandırma sağlayıcılarının bir uygulamaya `Startup` eklenmesi yapıcı aracılığıyla gerçekleştirildi. Adımlar, uygulanabilir sağlayıcıların `ConfigurationBuilder`(ortam değişkenleri, uygulama ayarları, vb.) bir örneğini `IConfigurationRoot`oluşturmayı ve bir üyenin başlatılmasını içeriyordu.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Önceki örnek, üyeyi `Configuration` *appsettings.json'dan* ve herhangi *bir uygulama\< ayarlarından yapılandırma ayarlarıyla yükler. EnvironmentName\>.json* dosyası `IHostingEnvironment.EnvironmentName` özelliği yle eşleşmektedir. Bu dosyaların konumu *Startup.cs*ile aynı yolda.

2.0 projelerinde, 1.x projelerinin doğasında bulunan ortak yapılandırma kodu perde arkasında çalışır. Örneğin, ortam değişkenleri ve uygulama ayarları başlangıçta yüklenir. Eşdeğer *Startup.cs* kodu enjekte `IConfiguration` edilen örnekle başlatmaya indirgenir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Tarafından eklenen varsayılan sağlayıcıları `WebHostBuilder.CreateDefaultBuilder`kaldırmak `Clear` için, `IConfigurationBuilder.Sources` içindeki özellik `ConfigureAppConfiguration`te yöntemi ni çağırın. Sağlayıcıları geri eklemek için, `ConfigureAppConfiguration` *Program.cs*yöntemini kullanmak:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Önceki kod snippet `CreateDefaultBuilder` yöntemi tarafından kullanılan yapılandırma [burada](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)görülebilir.

Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index)bakın.

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Veritabanı başlatma kodunu taşıma

EF Core 1.x'i kullanan 1.x `dotnet ef migrations add` projelerinde aşağıdaki gibi bir komut oluşur:

1. Bir `Startup` örneği anında
1. Bağımlılık enjeksiyonu `ConfigureServices` `DbContext` (türleri dahil) ile tüm hizmetleri kaydetmek için yöntemi çağırır
1. Gerekli görevleri gerçekleştirir

EF Core 2.0 kullanan 2.0 projelerde, `Program.BuildWebHost` uygulama hizmetlerini almak için çağrılmaktadır. 1.x aksine, bu çağıran ek yan `Startup.Configure`etkisi vardır. 1.x uygulamanız yönteminde `Configure` veritabanı başlatma kodunu çağırırsa, beklenmeyen sorunlar oluşabilir. Örneğin, veritabanı henüz yoksa, tohumlama kodu EF Çekirdek Geçişleri komut yürütme önce çalışır. Bu sorun, `dotnet ef migrations list` veritabanı henüz yoksa bir komutun başarısız olmasına neden olur.

`Configure` *Startup.cs*yönteminde aşağıdaki 1.x tohum başlatma kodunu göz önünde bulundurun:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

2.0 projelerde, `SeedData.Initialize` çağrıyı `Main` *Program.cs*yöntemine taşıyın:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

2.0 itibariyle, web barındırma oluşturmak ve `BuildWebHost` yapılandırmak dışında bir şey yapmak kötü bir uygulamadır. Uygulamayı çalıştırmakla ilgili her *şey,* Program.cs `BuildWebHost` &mdash; `Main` yönteminde genellikle dışında ele alınmalıdır.

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a>Razor görünüm derleme ayarını gözden geçirin

Daha hızlı uygulama başlangıç süresi ve daha küçük yayınlanan demetleri sizin için son derece önemlidir. Bu nedenlerden dolayı, [Razor görünüm derlemesi](xref:mvc/views/view-compilation) varsayılan olarak ASP.NET Core 2.0'da etkinleştirilir.

Özelliği `MvcRazorCompileOnPublish` niçin doğru ayarlanın artık gerekli değildir. Görünüm derlemesini devre dışı bırakmadığınız sürece, özellik *.csproj* dosyasından kaldırılabilir.

.NET Framework'ü hedef alırken, *.csproj* dosyanızda [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet paketine açıkça başvurmanız gerekir:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Uygulama Öngörüleri 'ışık-up" özelliklerine güvenme

Uygulama performans enstrümantasyonunun zahmetsizce kurulumu önemlidir. Artık Visual Studio 2017 aracında bulunan yeni [Application Insights](/azure/application-insights/app-insights-overview) "Light-up" özelliklerine güvenebilirsiniz.

Visual Studio 2017'de oluşturulan ASP.NET Core 1.1 projeleri varsayılan olarak Application Insights'ı ekledi. Uygulama Öngörüleri SDK'yı *doğrudan, Program.cs* ve *Startup.cs*dışında kullanmıyorsanız aşağıdaki adımları izleyin:

1. .NET Core'u hedefliyorsanız, `<PackageReference />` *.csproj* dosyasından aşağıdaki düğümü kaldırın:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. .NET Core'u hedefliyorsanız, `UseApplicationInsights` *Program.cs*uzantı yöntemini kaldırın:

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. *_Layout.cshtml'den*Application Insights istemci tarafındaki API çağrısını kaldırın. Aşağıdaki iki kod satırından oluşur:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Doğrudan Application Insights SDK kullanıyorsanız, bunu yapmaya devam edin. 2.0 [metapaketi](xref:fundamentals/metapackage) Application Insights'ın en son sürümünü içerir, bu nedenle eski bir sürüme başvuruyorsanız bir paket düşürme hatası görüntülenir.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a>Kimlik doğrulama/kimlik iyileştirmelerini benimseme

ASP.NET Core 2.0 yeni bir kimlik doğrulama modeli ve ASP.NET Çekirdek Kimlik önemli değişiklikler bir dizi vardır. Projenizi Bireysel Kullanıcı Hesapları etkinleştirilmiş olarak oluşturduysanız veya el ile kimlik doğrulama veya kimlik eklediyseniz, [Bkz. ASP.NET Core 2.0'a Kimlik Doğrulamayı ve Kimliği Geçir](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 2.0'daki Son Değişiklikler](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
