---
title: ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)
author: rick-anderson
description: Visual Studio 'da yayımlama profilleri oluşturmayı ve bunları çeşitli hedeflere ASP.NET Core uygulama dağıtımlarını yönetmek için kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 1c4b1def75a76b8392427fd6916a7b5927737cac
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015458"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)

[Sayed Ibrampahashve](https://github.com/sayedihashimi) [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

Bu belge, yayımlama profillerinin oluşturulması ve kullanılması için Visual Studio 2019 veya sonraki bir sürümü kullanılarak odaklanmıştır. Visual Studio ile oluşturulan yayımlama profilleri MSBuild ve Visual Studio ile birlikte kullanılabilir. Azure 'da yayımlama yönergeleri için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..

`dotnet new mvc`Komut aşağıdaki kök düzeyi [ \<Project> öğeyi](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Önceki `<Project>` öğenin özniteliği, `Sdk` sırasıyla *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.props* ve *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.targets*öğesinden MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) içeri aktarır. İçin varsayılan konum `$(MSBuildSDKsPath)` (Visual Studio 2019 Enterprise ile) *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\enterprise\msbuild\sdk* klasörüdür.

`Microsoft.NET.Sdk.Web`([Web SDK](xref:razor-pages/web-sdk)) `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) ve `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)) dahil diğer SDK 'lara bağlıdır. Her bağımlı SDK ile ilişkili MSBuild özellikleri ve hedefleri içeri aktarılır. Yayımlama hedefleri, kullanılan Yayımla yöntemine göre uygun hedef kümesini içeri aktarır.

MSBuild veya Visual Studio bir projeyi yüklediğinde, aşağıdaki üst düzey eylemler gerçekleşir:

* Projeyi oluştur
* Yayımlanacak işlem dosyaları
* Dosyaları hedefe Yayımla

## <a name="compute-project-items"></a>İşlem projesi öğeleri

Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır. Öğe türü, dosyanın nasıl işlendiğini belirler. Varsayılan olarak, *. cs* dosyaları `Compile` öğe listesine eklenir. `Compile`Öğe listesindeki dosyalar derlenir.

`Content`Öğe listesi, derleme çıktılarına ek olarak yayımlanan dosyaları içerir. Varsayılan olarak, ve desenleriyle eşleşen `wwwroot\**` dosyalar `**\*.config` `**\*.json` `Content` öğe listesine dahil edilir. Örneğin, `wwwroot\**` [Glob deseninin](https://gruntjs.com/configuring-tasks#globbing-patterns) *Wwwroot* klasörü ve alt klasörlerindeki tüm dosyalar eşleşir.

::: moniker range=">= aspnetcore-3.0"

[Web SDK](xref:razor-pages/web-sdk) [ Razor 'sı SDK 'yı](xref:razor-pages/sdk)içeri aktarır. Sonuç olarak, desenlerle eşleşen dosyalar `**\*.cshtml` ve `**\*.razor` `Content` öğe listesine de dahildir.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[Web SDK](xref:razor-pages/web-sdk) [ Razor 'sı SDK 'yı](xref:razor-pages/sdk)içeri aktarır. Sonuç olarak, düzeniyle eşleşen dosyalar `**\*.cshtml` da `Content` öğe listesine dahil edilir.

::: moniker-end

Yayınlama listesine açıkça bir dosya eklemek için, dosyayı, [dosyaları dahil et](#include-files) bölümünde gösterildiği gibi doğrudan *. csproj* dosyasına ekleyin.

Visual Studio 'da veya komut satırından yayımlarken **Yayımla** düğmesini seçerken:

* Özellikler/öğeler hesaplanır (oluşturmak için gereken dosyalar).
* **Yalnızca Visual Studio**: NuGet paketleri geri yüklendi. (Geri yüklemenin CLı üzerinde kullanıcı tarafından açık olması gerekir.)
* Proje oluşturulur.
* Yayımlama öğeleri hesaplanır (yayımlamak için gereken dosyalar).
* Proje yayımlandı (hesaplanan dosyalar yayımlama hedefine kopyalanır).

Proje dosyasına bir ASP.NET Core projesi başvurduğunda `Microsoft.NET.Sdk.Web` , Web uygulaması dizininin köküne bir *app_offline.htm* dosyası yerleştirilir. Dosya mevcut olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.

## <a name="basic-command-line-publishing"></a>Temel komut satırı yayımlama

Komut satırı yayımlama, .NET Core tarafından desteklenen tüm platformlarda çalışmaktadır ve Visual Studio 'Yu gerektirmez. Aşağıdaki örneklerde .NET Core CLI [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır ( *. csproj* dosyasını içerir). Proje klasörü geçerli çalışma dizini değilse, proje dosyası yolunda açıkça geçiş yapın. Örnek:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Bir Web uygulaması oluşturmak ve yayımlamak için aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet new mvc
dotnet publish
```

`dotnet publish`Komut aşağıdaki çıkışın bir çeşidini üretir:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Varsayılan yayımlama klasörü biçimi *bin\Debug \\ {Target Framework bilinen ad} \publish \\ *şeklindedir. Örneğin, *Bin\debug\netcoreapp2,2\publish \\ *.

Aşağıdaki komut bir `Release` derlemeyi ve yayımlama dizinini belirtir:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

`dotnet publish`Komutu, hedefi çağıran MSBuild 'i çağırır `Publish` . Öğesine geçirilen parametreler `dotnet publish` MSBuild 'e geçirilir. `-c`Ve `-o` parametreleri `Configuration` sırasıyla MSBuild ve özellikler ile eşlenir `OutputPath` .

MSBuild özellikleri aşağıdaki biçimlerden birini kullanarak geçirilebilir:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Örneğin, aşağıdaki komut bir `Release` ağ paylaşımında bir derlemeyi yayımlar. Ağ paylaşma, eğik çizgiler (/*saat*) ile belirtilir ve tüm .NET Core desteklenen platformlarda çalışmaktadır.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Dağıtım için yayımlanan uygulamanın çalışmadığından emin olun. *Yayımla* klasöründeki dosyalar, uygulama çalışırken kilitlenir. Kilitli dosyalar kopyalanamadığından dağıtım gerçekleştirilemez.

## <a name="publish-profiles"></a>Yayımlama profilleri

Bu bölüm, bir yayımlama profili oluşturmak için Visual Studio 2019 veya üstünü kullanır. Profil oluşturulduktan sonra, Visual Studio 'dan veya komut satırından yayımlama kullanılabilir. Yayımlama profilleri Yayımlama sürecini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.

Aşağıdaki yollardan birini seçerek Visual Studio 'da bir yayımlama profili oluşturun:

* **Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla**' yı seçin.
* **Build** menüsünden **{Project Name} Yayımla** ' yı seçin.

Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir. Projenin bir yayımlama profili yoksa, **bir yayımlama hedefi seçin** sayfası görüntülenir. Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:

* Azure App Service
* Linux üzerinde Azure App Service
* Azure Sanal Makineler
* Klasör
* IIS, FTP, Web Dağıtımı (herhangi bir Web sunucusu için)
* Profili içeri aktar

En uygun yayımlama hedefini belirlemek için, [hangi yayımlama seçeneklerinin benim için](/visualstudio/ide/not-in-toc/web-publish-options)uygun olduğunu öğrenin.

Hedef Yayımla **klasörü** seçildiğinde, yayımlanmış varlıkları depolamak için bir klasör yolu belirtin. Varsayılan klasör yolu, *bin \\ {Project CONFIGURATION} \\ {Target Framework bilinen adı} \publish \\ *şeklindedir. Örneğin, *Bin\release\netcoreapp2,2\publish \\ *. Tamamlanacak **Profil oluştur** düğmesini seçin.

Bir yayımlama profili oluşturulduktan sonra, **Yayımla** sekmesinin içeriği değişir. Yeni oluşturulan profil bir açılan listede görüntülenir. Aşağı açılan listenin altında **Yeni profil** oluştur ' u seçerek yeni bir profil oluşturun.

Visual Studio 'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE Name}. pubxml* MSBuild dosyası oluşturuyor. *. Pubxml* dosyası:

* Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından kullanılır.
* Derleme ve yayımlama işlemini özelleştirmek için değiştirilebilir.

Azure hedefine yayımlarken, *. pubxml* dosyası Azure abonelik tanımlarınızı içerir. Bu hedef türünde, bu dosyayı kaynak denetimine eklemek önerilmez. Azure olmayan bir hedefe yayımlarken, *. pubxml* dosyasını denetlemek güvenlidir.

Gizli bilgiler (yayımlama parolası gibi) Kullanıcı/makine düzeyinde şifrelenir. *Özellikler/PublishProfiles/{PROFILE Name}. pubxml. User* dosyasında depolanır. Bu dosya hassas bilgileri depolayabildiğinden, kaynak denetimine denetlenmemelidir.

ASP.NET Core Web uygulamasının nasıl yayımlanacağı hakkında genel bir bakış için, bkz <xref:host-and-deploy/index> .. ASP.NET Core Web uygulaması yayımlamak için gereken MSBuild görevleri ve hedefleri, [DotNet/WebSDK deposunda](https://github.com/dotnet/websdk)açık kaynaktır.

Aşağıdaki komutlar Folder, MSDeploy ve [kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir. MSDeploy platformlar arası destek olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows 'da desteklenir.

**Klasör (platformlar arası):**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy**

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

Yukarıdaki örneklerde:

* `dotnet publish`ve `dotnet build` Azure 'da herhangi bir platformda yayımlanacak kudu API 'lerini destekler. Visual Studio yayımlama, kudu API 'Lerini destekler, ancak Azure 'da platformlar arası yayımlama için WebSDK tarafından desteklenir.
* Komuta geçme `DeployOnBuild` `dotnet publish` .

Daha fazla bilgi için bkz. [Microsoft. net. SDK. Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).

Projenin *Properties/PublishProfiles* klasörüne aşağıdaki içeriğe sahip bir yayımlama profili ekleyin:

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

## <a name="folder-publish-example"></a>Klasör Yayımla örneği

*Folderprofile*adlı bir profille yayımlarken, aşağıdaki komutlardan birini kullanın:

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

.NET Core CLI [DotNet Build](/dotnet/core/tools/dotnet-build) komutu, `msbuild` derleme ve yayımlama işlemini çalıştırmak için çağırır. `dotnet build`Ve `msbuild` komutları, bir klasör profilinde geçirilerek eşdeğerdir. `msbuild`Doğrudan Windows üzerinde çağrılırken, MSBuild 'in .NET Framework sürümü kullanılır. `dotnet build`Klasör olmayan bir profilde çağırma:

* `msbuild`MSDeploy kullanan çağırır.
* Hataya neden olur (Windows üzerinde çalışırken bile). Klasör olmayan bir profille yayımlamak için `msbuild` doğrudan çağırın.

Aşağıdaki klasör yayımlama profili, Visual Studio ile oluşturulmuştur ve bir ağ paylaşımında yayımlar:

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

Yukarıdaki örnekte:

* `<ExcludeApp_Data>`Özelliği yalnızca BIR XML şeması gereksinimini karşılamak için vardır. `<ExcludeApp_Data>`Proje kökünde bir *App_Data* klasörü olsa bile, özelliğin yayımlama işlemi üzerinde hiçbir etkisi yoktur. *App_Data* klasörü, ASP.NET 4. x projelerinde olduğu gibi özel bir işleme almaz.
* `<LastUsedBuildConfiguration>` özelliği `Release` olarak ayarlanmıştır. Visual Studio 'dan yayımlarken değeri, `<LastUsedBuildConfiguration>` Yayımlama işlemi başlatıldığında değeri kullanılarak ayarlanır. `<LastUsedBuildConfiguration>`özeldir ve içeri aktarılan MSBuild dosyasında geçersiz kılınmamalıdır. Ancak, bu özellik aşağıdaki yaklaşımlardan birini kullanarak komut satırından geçersiz kılınabilir.
  * .NET Core CLI kullanarak:

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * MSBuild 'i kullanma:

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Daha fazla bilgi için bkz. [MSBuild: yapılandırma özelliğini ayarlama](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Komut satırından bir MSDeploy uç noktasına yayımlama

Aşağıdaki örnek, *AzureWebApp*adlı Visual Studio tarafından oluşturulan bir ASP.NET Core Web uygulamasını kullanır. Visual Studio ile bir Azure Apps yayımlama profili eklenir. Profil oluşturma hakkında daha fazla bilgi için, [Yayımlama profilleri](#publish-profiles) bölümüne bakın.

Uygulamayı bir yayımlama profili kullanarak dağıtmak için, `msbuild` bir Visual Studio **Geliştirici komut istemi**komutunu yürütün. Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe bulunur. Daha kolay erişim için, Visual Studio 'daki **Araçlar** menüsüne komut istemi ekleyebilirsiniz. Daha fazla bilgi için bkz. [Visual Studio için geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild aşağıdaki komut sözdizimini kullanır:

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`: Uygulamanın proje dosyasının yolu.
* `{PROFILE}`: Yayımlama profilinin adı.
* `{USERNAME}`: MSDeploy Kullanıcı adı. , `{USERNAME}` Yayımlama profilinde bulunabilir.
* `{PASSWORD}`: MSDeploy parolası. `{PASSWORD}` *{PROFILE} öğesinden alın. PublishSettings* dosyası. ' Nı indirin *. PublishSettings* dosyası şunlardan biri:
  * **Çözüm Gezgini**: **View**  >  **bulut Gezginini**görüntüle ' yi seçin. Azure aboneliğinize bağlanın. **Uygulama hizmetleri**'ni açın. Uygulamaya sağ tıklayın. **Yayımlama profilini indir**' i seçin.
  * Azure portal: Web uygulamasının **genel bakış** panelinde **Yayımlama profilini al** ' ı seçin.

Aşağıdaki örnek, *AzureWebApp-Web dağıtımı*adlı bir yayımlama profili kullanır:

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Bir yayımlama profili, Windows komut kabuğu 'ndan .NET Core CLI [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> `dotnet msbuild`Komut, platformlar arası bir komuttur ve macOS ve Linux üzerinde ASP.NET Core uygulamalar derleyebilir. Ancak, macOS ve Linux 'ta MSBuild, bir uygulamayı Azure 'a veya diğer MSDeploy uç noktalarına dağıtmıyor.

## <a name="set-the-environment"></a>Ortamı ayarlama

`<EnvironmentName>`Uygulamanın [ortamını](xref:fundamentals/environments)ayarlamak için Publish profile (*. pubxml*) veya proje dosyasına özelliği ekleyin:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

*web.config* Dönüştürmelere ihtiyacınız varsa (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak), bkz <xref:host-and-deploy/iis/transform-webconfig> ..

## <a name="exclude-files"></a>Dosyaları Dışla

ASP.NET Core Web Apps yayımlandığında, aşağıdaki varlıklar dahil edilmiştir:

* Yapı yapıtları
* Aşağıdaki glob desenleriyle eşleşen klasörler ve dosyalar:
  * `**\*.config`(örneğin, *web.config*)
  * `**\*.json`(örneğin, *appsettings.js*)
  * `wwwroot\**`

MSBuild, [Glob desenlerini](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler. Örneğin, aşağıdaki öğe, `<Content>` metin (*. txt*) dosyalarının *wwwroot\content* klasörü ve alt klasörlerinde kopyalanmasını bastırır:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Önceki biçimlendirme bir yayımlama profiline veya *. csproj* dosyasına eklenebilir. *. Csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.

Aşağıdaki `<MsDeploySkipRules>` öğe, tüm dosyaları *wwwroot\content* klasöründen dışlar:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`dağıtım sitesinden *atlama* hedeflerini silmez. `<Content>`hedeflenen dosya ve klasörler dağıtım sitesinden silinir. Örneğin, dağıtılan bir Web uygulamasının aşağıdaki dosyalar olduğunu varsayalım:

* *Görünümler/Home/about1. cshtml*
* *Görünümler/Home/About2. cshtml*
* *Görünümler/Home/About3. cshtml*

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

Önceki `<MsDeploySkipRules>` öğeler *Atlanan* dosyaların dağıtılmasını engeller. Dağıtıldıktan sonra bu dosyaları silmez.

Aşağıdaki `<Content>` öğe, dağıtım sitesindeki hedeflenen dosyaları siler:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Önceki öğeyle komut satırı dağıtımı kullanmak `<Content>` , aşağıdaki çıkışın bir varyasyonunu verir:

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

Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar ana hatlarıyla verilmiştir. [Genel dosya ekleme](#general-file-inclusion) bölümü, `DotNetPublishFiles` [Web SDK 'sında](xref:razor-pages/web-sdk)bir Yayımla hedefi dosyası tarafından belirtilen öğesini kullanır. [Seçmeli dosya ekleme](#selective-file-inclusion) bölümü, `ResolvedFileToPublish` [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)bir Yayımla hedefi dosyası tarafından belirtilen öğesini kullanır. Web SDK .NET Core SDK bağlı olduğundan, her iki öğe bir ASP.NET Core projesinde kullanılabilir.

### <a name="general-file-inclusion"></a>Genel dosya ekleme

Aşağıdaki örnek öğesi, `<ItemGroup>` proje dizininin dışında bulunan bir klasörü yayımlanmış sitenin bir klasörüne kopyalamayı gösterir. Aşağıdaki biçimlendirmeye eklenen tüm dosyalar `<ItemGroup>` Varsayılan olarak dahil edilir.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Yukarıdaki biçimlendirme:

* *. Csproj* dosyasına veya yayımlama profiline eklenebilir. *. Csproj* dosyasına eklenirse, bu, projedeki her bir yayımlama profiline eklenir.
* `_CustomFiles`Özniteliğin glob düzeniyle eşleşen dosyaları depolamak için bir öğe bildirir `Include` . Düzende başvurulan *görüntüler* klasörü, proje dizininin dışında bulunur. Adlı [ayrılmış bir özellik](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), `$(MSBuildProjectDirectory)` Proje dosyasının mutlak yoluna çözümlenir.
* Öğe için dosyaların bir listesini sağlar `DotNetPublishFiles` . Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur. Varsayılan değer, biçimlendirmede geçersiz kılınır ve gibi [iyi bilinen öğe meta verilerini](/visualstudio/msbuild/msbuild-well-known-item-metadata) kullanır `%(RecursiveDir)` . İç metin, yayımlanan sitenin *Wwwroot/görüntüler* klasörünü temsil eder.

### <a name="selective-file-inclusion"></a>Seçmeli dosya ekleme

Aşağıdaki örnekte vurgulanan biçimlendirme şunları göstermektedir:

* Projenin dışında bulunan bir dosyayı yayınlanan sitenin *Wwwroot* klasörüne kopyalama. *ReadMe2.MD* dosyasının adı korunur.
* *Wwwroot\content* klasörü dışlanıyor.
* *Views\home\about2,cshtml*hariç tutulanıyor.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Yukarıdaki örnek, `ResolvedFileToPublish` varsayılan davranışı özniteliğinde belirtilen dosyaları her zaman yayımlanan siteye kopyalamak için olan öğesini kullanır `Include` . `<CopyToPublishDirectory>`Ya da ' ın iç metniyle bir alt öğe ekleyerek varsayılan davranışı geçersiz kılın `Never` `PreserveNewest` . Örnek:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Daha fazla dağıtım örneği için bkz. [Web SDK Benioku dosyası](https://github.com/dotnet/sdk/tree/master/src/WebSdk).

## <a name="run-a-target-before-or-after-publishing"></a>Yayımlamadan önce veya sonra bir hedef Çalıştır

Yerleşik `BeforePublish` ve `AfterPublish` hedefler, yayımlama hedefinden önce veya sonra bir hedef yürütür. Aşağıdaki öğeleri yayımlama profiline, yayımlamadan önce ve sonra da konsol iletilerini günlüğe kaydetmek için ekleyin:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Güvenilmeyen bir sertifikayı kullanarak bir sunucuya yayımlama

`<AllowUntrustedCertificate>`Değerini yayımlama profiline değeri olan özelliği ekleyin `True` :

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Kudu hizmeti

Azure App Service Web uygulaması dağıtımında dosyaları görüntülemek için [kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın. `scm`Belirteci Web uygulaması adına ekleyin. Örnek:

| URL                                    | Sonuç       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Web App      |
| `http://mysite.scm.azurewebsites.net/` | Kudu hizmeti |

Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [hata ayıklama konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Web dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), IIS sunucularına Web uygulamaları ve Web siteleri dağıtımını basitleştirir.
* [Web SDK GitHub deposu](https://github.com/dotnet/websdk/issues): dağıtım için dosya sorunları ve istek özellikleri.
* [Visual Studio 'dan bir Azure VM 'de ASP.NET Web uygulaması yayımlama](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
