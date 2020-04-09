---
title: Visual Studio ASP.NET Core uygulama dağıtımı için profilleri (.pubxml) yayınlamak
author: rick-anderson
description: Visual Studio'da yayımlama profilleri oluşturmayı ve bunları çeşitli hedeflere ASP.NET Core uygulama dağıtımlarını yönetmek için nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659378"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Visual Studio ASP.NET Core uygulama dağıtımı için profilleri (.pubxml) yayınlamak

Yazar: [Sayed Ibrahim Haşimi](https://github.com/sayedihashimi) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu belge, yayın profilleri oluşturmak ve kullanmak için Visual Studio 2019 veya daha sonra sını kullanmaya odaklanır. Visual Studio ile oluşturulan yayın profilleri MSBuild ve Visual Studio ile kullanılabilir. Azure'da yayımlama yla <xref:tutorials/publish-to-azure-webapp-using-vs>ilgili talimatlar için bkz.

Komut, `dotnet new mvc` aşağıdaki kök düzeyinde [ \<proje> öğesini](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

`<Project>` Önceki `Sdk` öğenin özniteliği, sırasıyla *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* ve *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets'den*MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) alır. (Visual Studio `$(MSBuildSDKsPath)` 2019 Enterprise ile) için varsayılan konum *%programdosyaları(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* klasörüdür.

`Microsoft.NET.Sdk.Web`(Web SDK) `Microsoft.NET.Sdk` (.NET Core SDK) ve `Microsoft.NET.Sdk.Razor` [(Razor SDK)](xref:razor-pages/sdk)dahil olmak üzere diğer SDK'lara bağlıdır. MSBuild özellikleri ve her bağımlı SDK ile ilişkili hedefleri alınır. Yayımlama hedefleri kullanılan yayımlama yöntemini temel alan uygun hedef kümesini içe aktarın.

MSBuild veya Visual Studio bir proje yükler, aşağıdaki üst düzey eylemler oluşur:

* Proje oluşturma
* Yayımlanacak dosyaları hesaplama
* Dosyaları hedefe yayımlama

## <a name="compute-project-items"></a>Proje öğelerini hesapla

Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır. Öğe türü, dosyanın nasıl işlenir olduğunu belirler. Varsayılan olarak, *.cs* dosyaları öğe `Compile` listesine dahil edilir. Madde listesindeki `Compile` dosyalar derlenir.

Madde `Content` listesi, yapı çıktılarına ek olarak yayımlanan dosyaları içerir. Varsayılan olarak, desenleri `wwwroot\**` `**\*.config`eşleşen `**\*.json` dosyalar , , `Content` , ve öğe listesine dahildir. Örneğin, `wwwroot\**` [globbing deseni,](https://gruntjs.com/configuring-tasks#globbing-patterns) *wwwroot* klasöründeki tüm dosyalarla ve alt klasörlerine eşleşir.

::: moniker range=">= aspnetcore-3.0"

Web SDK [Razor SDK](xref:razor-pages/sdk)ithal eder. Sonuç olarak, desenlerle `**\*.cshtml` eşleşen `**\*.razor` dosyalar da madde `Content` listesine dahil edilir.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Web SDK [Razor SDK](xref:razor-pages/sdk)ithal eder. Sonuç olarak, `**\*.cshtml` deseneşleşen dosyalar da öğe `Content` listesine dahil edilir.

::: moniker-end

Yayımlama listesine açıkça bir dosya eklemek için, dosyaları Dosyaları [Ekle](#include-files) bölümünde gösterildiği gibi doğrudan *.csproj* dosyasına ekleyin.

Visual Studio'da **Yayımla** düğmesini seçerken veya komut satırından yayımlarken:

* Özellikler/öğeler (oluşturmak için gereken dosyalar) hesaplanır.
* **Sadece Visual Studio**: NuGet paketleri geri yüklenir. (Geri yükleme, CLI'de kullanıcı tarafından açık olmalıdır.)
* Proje inşa eder.
* Yayımlama öğeleri (yayımlamak için gerekli olan dosyalar) hesaplanır.
* Proje yayımlanır (hesaplanan dosyalar yayımlama hedefine kopyalanır).

Proje dosyasında bir `Microsoft.NET.Sdk.Web` ASP.NET Core proje başvuruları yapıldığında, web uygulama dizininin köküne bir *app_offline.htm* dosyası yerleştirilir. Dosya bulunduğunda, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.

## <a name="basic-command-line-publishing"></a>Temel komut satırı yayımlama

Komut satırı yayımlama, .NET Çekirdek destekli tüm platformlarda çalışır ve Visual Studio gerektirmez. Aşağıdaki örneklerde,.NET Core CLI'nin [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır *(.csproj* dosyasını içerir). Proje klasörü geçerli çalışma dizini değilse, açıkça proje dosyası yoluna geçirin. Örneğin:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Bir web uygulaması oluşturmak ve yayınlamak için aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet new mvc
dotnet publish
```

Komut `dotnet publish` aşağıdaki çıktının bir varyasyonunu üretir:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Varsayılan yayımlama klasörü biçimi *bin\Debug\\{TARGET FRAMEWORK\\MONIKER}\publish.* Örneğin, *bin\Debug\netcoreapp2.2\publish\\*.

Aşağıdaki komut, bir `Release` yapı yı ve yayımlama dizinini belirtir:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Komut, `dotnet publish` hedefi çağıran MSBuild'i `Publish` çağırır. Geçirilen parametreler `dotnet publish` MSBuild'e geçirilir. Ve `-c` `-o` parametreler, sırasıyla MSBuild'in `Configuration` ve `OutputPath` özelliklerinin eşlenir.

MSBuild özellikleri aşağıdaki biçimlerden biri kullanılarak geçirilebilir:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Örneğin, aşağıdaki komut bir `Release` ağ paylaşımıiçin bir yapı yayımlar. Ağ payı,*(//r8/*) ileri eğik çizgilerle belirtilir ve tüm .NET Core destekli platformlarda çalışır.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Dağıtım için yayınlanan uygulamanın çalışmadığını doğrulayın. Uygulama çalışırken *yayımlama* klasöründeki dosyalar kilitlenir. Kilitli dosyalar kopyalanamadığından dağıtım gerçekleşemez.

## <a name="publish-profiles"></a>Yayımlama profilleri

Bu bölümde, bir yayımlama profili oluşturmak için Visual Studio 2019 veya daha sonra kullanır. Profil oluşturulduktan sonra Visual Studio'dan veya komut satırından yayımlama kullanılabilir. Yayımlama profilleri yayımlama işlemini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.

Aşağıdaki yollardan birini seçerek Visual Studio'da bir yayımlama profili oluşturun:

* **Solution Explorer'da** projeyi sağ tıklatın ve **Yayımla'yı**seçin.
* **Yapı** menüsünden **"PROJE ADINI"** Yayımla'yı seçin.

Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir. Projede yayımlama profili yoksa, **yayımlama hedef** sayfasını seç görüntülenir. Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:

* Azure App Service
* Linux'ta Azure Uygulama Hizmeti
* Azure Sanal Makineler
* Klasör
* IIS, FTP, Web Dağıtımı (herhangi bir web sunucusu için)
* İthalat Profili

En uygun yayımlama hedefini belirlemek için, hangi [yayımlama seçeneklerinin benim için doğru olduğunu](/visualstudio/ide/not-in-toc/web-publish-options)görün.

**Klasör** yayımlama hedefi seçildiğinde, yayımlanan varlıkları depolamak için bir klasör yolu belirleyin. Varsayılan klasör yolu *\\bin {PROJECT\\CONFIGURATION} {TARGET FRAMEWORK MONIKER}\publish.\\* Örneğin, *bin\Release\netcoreapp2.2\publish\\*. Bitirmek için **Profil Oluştur** düğmesini seçin.

Yayımlama profili oluşturulduktan sonra **Yayımsekmesinin** içeriği değişir. Yeni oluşturulan profil açılır listede görünür. Açılan listenin altında, yeni bir profil oluşturmak için **yeni profil oluştur'u** seçin.

Visual Studio'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild dosyası oluşturur. *.pubxml* dosyası:

* Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından tüketilir.
* Yapı ve yayımlama işlemini özelleştirmek için değiştirilebilir.

Bir Azure hedefine yayımlama *yaparken,.pubxml* dosyası Azure abonelik tanımlayıcınızı içerir. Bu hedef türüyle, bu dosyayı kaynak denetimine ekleme önerilmez. Azure olmayan bir hedefe yayımlama yaparken *,.pubxml* dosyasını iade etmek güvenlidir.

Hassas bilgiler (yayımlama parolası gibi) kullanıcı/makine düzeyinde şifrelenir. *Özellikler/PublishProfiles/{PROFILE NAME}.pubxml.user* dosyasında saklanır. Bu dosya hassas bilgileri depolayabildiği için kaynak denetiminde denetlenmemelidir.

ASP.NET Core web uygulamasının nasıl yayımlandığına <xref:host-and-deploy/index>genel bir bakış için bkz. ASP.NET Core web uygulaması yayınlamak için gereken MSBuild görevleri ve hedefleri [aspnet/websdk deposunda](https://github.com/aspnet/websdk)açık kaynak kodludur.

Aşağıdaki komutlar klasör, MSDeploy ve [Kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir. MSDeploy'ın platformlar arası desteği olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows'da desteklenir.

**Klasör (çapraz platform çalışır):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**Msdeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**MSDeploy paketi:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

Önceki örneklerde:

* `dotnet publish`ve `dotnet build` Kudu API'lerini herhangi bir platformdan Azure'da yayımlamak için destekleyin. Visual Studio yayımlamak Kudu API'lerini destekler, ancak platformlar arası Azure'da yayımlanması için WebSDK tarafından desteklenir.
* Komutayı `DeployOnBuild` `dotnet publish` geçme.

Daha fazla bilgi için [Microsoft.NET.Sdk.Publish'e](https://github.com/aspnet/websdk#microsoftnetsdkpublish)bakın.

Projenin *Özellikler/Yayımlama Profilleri* klasörüne aşağıdaki içeriği içeren bir yayımlama profili ekleyin:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Klasör yayımlama örneği

*FolderProfile*adlı bir profille yayımlama yaparken aşağıdaki komutlardan birini kullanın:

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

.NET Core CLI'nin [dotnet](/dotnet/core/tools/dotnet-build) `msbuild` build komutu, yapı ve yayımlama işlemini çalıştırmak için çağrıda bulunur. Ve `dotnet build` `msbuild` komutları bir klasör profilinde geçerken eşdeğerdir. Doğrudan `msbuild` Windows'u ararken, MSBuild'in .NET Framework sürümü kullanılır. Klasör `dotnet build` olmayan bir profili arama:

* `msbuild`MSDeploy kullanan çağırır.
* Başarısızlığa neden olan (Windows'ta çalışırken bile). Klasör olmayan bir profille yayımlamak için doğrudan arayın. `msbuild`

Aşağıdaki klasör yayımlama profili Visual Studio ile oluşturuldu ve bir ağ paylaşımında yayımlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Önceki örnekte:

* Özellik `<ExcludeApp_Data>` yalnızca bir XML şema gereksinimini karşılamak için mevcuttur. `<ExcludeApp_Data>` Proje kökünde *App_Data* bir klasör olsa bile özelliğin yayımlama işlemi üzerinde hiçbir etkisi yoktur. *App_Data* klasörü, ASP.NET 4.x projelerinde olduğu gibi özel muamele almaz.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* `Release`Özellik. `<LastUsedBuildConfiguration>` Visual Studio'dan yayımlanırken, yayımlama işlemi başlatıldığında değeri `<LastUsedBuildConfiguration>` değer kullanılarak ayarlanır. `<LastUsedBuildConfiguration>`özeldir ve içe aktarılan bir MSBuild dosyasında geçersiz kılınmamalıdır. Ancak bu özellik, aşağıdaki yaklaşımlardan biri kullanılarak komut satırından geçersiz kılınabilir.
  * .NET Çekirdek CLI'yi kullanma:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * MSBuild kullanma:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Daha fazla bilgi için [MSBuild'e bakın: yapılandırma özelliği nasıl ayarlanan.](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Komut satırından MSDeploy bitiş noktasına yayımlama

Aşağıdaki örnekte Visual Studio tarafından oluşturulan *AzureWebApp*adlı ASP.NET Core web uygulaması kullanır. Visual Studio ile Azure Apps yayımlama profili eklenir. Profil oluşturma hakkında daha fazla bilgi için [Profilleri Yayımla](#publish-profiles) bölümüne bakın.

Bir yayımlama profili kullanarak uygulamayı `msbuild` dağıtmak için, visual studio **geliştirici komut komut u komut u komutunu**çalıştırın. Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe kullanılabilir. Daha kolay erişim için Visual Studio'daki **Araçlar** menüsüne komut istemini ekleyebilirsiniz. Daha fazla bilgi için [Visual Studio için Geliştirici Komut Komut Ustem'e](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)bakın.

MSBuild aşağıdaki komut sözdizimini kullanır:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {YOL} &ndash; Uygulamanın proje dosyasına giden yol.
* {PROFIL} &ndash; Yayımlama profilinin adı.
* {KULLANıCı Adı} &ndash; MSDeploy kullanıcı adı. {USERNAME} yayımlama profilinde bulunabilir.
* {PAROLA} &ndash; MSDeploy parolası. {PROFILE}'dan {PASSWORD} *alın. PublishSettings* dosyası. İndirin *. PublishSettings* dosyası aşağıdakilerden biri:
  * **Çözüm Gezgini**:**Bulut Gezginini** **Görüntüle'yi** > seçin. Azure aboneliğinizle bağlantı kurun. **Uygulama Hizmetlerini**Açın. Uygulamaya sağ tıklayın. **İndir Yayın Profilini**Seçin.
  * Azure portalı: Web uygulamasının **Genel Bakış** panelinde **yayımlama profilini al'ı** seçin.

Aşağıdaki örnekte *AzureWebApp - Web Deploy*adlı bir yayımlama profili kullanır:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Bir yayımlama profili, Windows komut uyruktan .NET Core CLI'nin [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Komut `dotnet msbuild` bir çapraz platform komutudur ve macOS ve Linux'ta ASP.NET Core uygulamaları derleyebilir. Ancak, macOS ve Linux'taki MSBuild, bir uygulamayı Azure'a veya diğer MSDeploy bitiş noktalarına dağıtamaz.

## <a name="set-the-environment"></a>Ortamı ayarlama

Uygulamanın `<EnvironmentName>` [ortamını](xref:fundamentals/environments)ayarlamak için özelliği yayımlama profiline (*.pubxml)* veya proje dosyasına ekleyin:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

*web.config* dönüşümleri (örneğin, yapılandırma, profil veya ortama göre ortam değişkenleri <xref:host-and-deploy/iis/transform-webconfig>ayarlama) gerekiyorsa, bkz.

## <a name="exclude-files"></a>Dosyaları hariç tut

ASP.NET Core web uygulamalarını yayınlarken aşağıdaki varlıklar dahildir:

* Yapı eserleri
* Aşağıdaki globbing desenleri eşleşen klasörler ve dosyalar:
  * `**\*.config`(örneğin, *web.config*)
  * `**\*.json`(örneğin, *appsettings.json*)
  * `wwwroot\**`

MSBuild [globbing desenleri](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler. Örneğin, aşağıdaki `<Content>` *öğe, wwwroot\content* klasöründe ve alt klasörlerinde metnin (*.txt*) dosyalarının kopyasını bastırır:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Önceki biçimlendirme bir yayımlama profiline veya *.csproj* dosyasına eklenebilir. *.csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.

Aşağıdaki `<MsDeploySkipRules>` *öğe, wwwroot\content* klasöründeki tüm dosyaları hariç tutar:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`*atlama* hedeflerini dağıtım sitesinden silmez. `<Content>`hedeflenen dosya ve klasörler dağıtım sitesinden silinir. Örneğin, dağıtılan bir web uygulamasının aşağıdaki dosyalara sahip olduğunu varsayalım:

* *Görünümler/Ana Sayfa/About1.cshtml*
* *Görünümler/Ana Sayfa/About2.cshtml*
* *Görünümler/Ana Sayfa/About3.cshtml*

Aşağıdaki `<MsDeploySkipRules>` öğeler eklenirse, bu dosyalar dağıtım sitesinde silinmez.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Önceki `<MsDeploySkipRules>` öğeler *atlanan* dosyaların dağıtılmasını engeller. Dağıtıldıktan sonra bu dosyaları silmez.

Aşağıdaki `<Content>` öğe dağıtım sitesinde hedeflenen dosyaları siler:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Komut satırı dağıtımının önceki `<Content>` öğeyle birlikte kullanılması aşağıdaki çıktının bir varyasyonunu verir:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Dosyaları dahil etme

Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar sıralanır. [Genel dosya ekleme](#general-file-inclusion) bölümü, Web SDK'daki `DotNetPublishFiles` bir yayımlama hedefleri dosyası tarafından sağlanan öğeyi kullanır. [Seçici dosya ekleme](#selective-file-inclusion) bölümü, .NET Core SDK'daki bir yayımlama hedefleri dosyası tarafından sağlanan `ResolvedFileToPublish` öğeyi kullanır. Web SDK .NET Core SDK'ya bağlı olduğundan, her iki öğe de ASP.NET Core projesinde kullanılabilir.

### <a name="general-file-inclusion"></a>Genel dosya ekleme

Aşağıdaki örnek `<ItemGroup>` öğesi, proje dizininin dışında bulunan bir klasörün yayımlanmış sitenin bir klasörüne kopyalanması gösterir. Aşağıdaki biçimlendirmeye eklenen tüm `<ItemGroup>` dosyalar varsayılan olarak dahil edilir.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Önceki biçimlendirme:

* *.csproj* dosyasına veya yayımlama profiline eklenebilir. *.csproj* dosyasına eklenirse, projedeki her yayımlama profiline eklenir.
* Öznitelik `_CustomFiles` globbing deseni `Include` eşleşen dosyaları depolamak için bir öğe bildirir. Desende başvurulan *görüntüler* klasörü proje dizininin dışında yer alır. Ayrılmış [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)bir özellik `$(MSBuildProjectDirectory)`, adlı , proje dosyasının mutlak yoluna giderir.
* `DotNetPublishFiles` Öğeye dosyaların bir listesini sağlar. Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur. Varsayılan değer biçimlendirmede geçersiz kılınmış ve ' [.](/visualstudio/msbuild/msbuild-well-known-item-metadata) `%(RecursiveDir)` İç metin, yayınlanan sitenin *wwwroot/images* klasörünü temsil eder.

### <a name="selective-file-inclusion"></a>Seçici dosya ekleme

Aşağıdaki örnekte vurgulanan biçimlendirme şunları gösterir:

* Projenin dışında bulunan bir dosyayı yayınlanan sitenin *wwwroot* klasörüne kopyalama. *ReadMe2.md* dosya adı korunur.
* *wwwroot\Content* klasörü hariç.
* *Görünümler\Ana Sayfa\About2.cshtml*hariç .

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Önceki örnek, varsayılan `ResolvedFileToPublish` davranışı her zaman yayımlanan siteye öznitelik `Include` sağlanan dosyaları kopyalamak için öğeyi kullanır. Ya da `<CopyToPublishDirectory>` `Never` `PreserveNewest`iç metin ile bir alt öğe ekleyerek varsayılan davranışı geçersiz kılmak Örneğin:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Daha fazla dağıtım örneği için [Web SDK deposu Readme'a](https://github.com/aspnet/websdk)bakın.

## <a name="run-a-target-before-or-after-publishing"></a>Yayımlamadan önce veya sonra bir hedef çalıştırma

Yerleşik `BeforePublish` ve `AfterPublish` hedefler, yayımlama hedefinden önce veya sonra bir hedefi yürütür. Yayımlamadan önce ve sonra konsol iletilerini günlüğe kaydetmek için yayımlama profiline aşağıdaki öğeleri ekleyin:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Güvenilmeyen bir sertifika kullanarak sunucuda yayımlama

Yayımlama `<AllowUntrustedCertificate>` profiline değeri `True` olan özelliği ekleyin:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Kudu servisi

Azure App Service web uygulaması dağıtımındaki dosyaları görüntülemek için [Kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın. Web uygulaması `scm` adının belirteci ekini ekle. Örneğin:

| URL'si                                    | Sonuç       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Web App      |
| `http://mysite.scm.azurewebsites.net/` | Kudu servisi |

Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [Hata Ayıklama Konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Web Dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), web uygulamalarının ve web sitelerinin IIS sunucularına dağıtımını kolaylaştırır.
* [Web SDK GitHub deposu](https://github.com/aspnet/websdk/issues): Dosya sorunları ve dağıtım özellikleri isteği.
* [Visual Studio'dan azure vm'de ASP.NET bir Web Uygulaması yayınlama](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
