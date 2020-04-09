---
title: Web.config’i dönüştürme
author: rick-anderson
description: ASP.NET Core uygulamasını yayınlarken web.config dosyasını nasıl dönüştüreceklerini öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 069b9bb516644a1a722235b33d4916460488ebf2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657936"
---
# <a name="transform-webconfig"></a><span data-ttu-id="cb4a1-103">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="cb4a1-103">Transform web.config</span></span>

<span data-ttu-id="cb4a1-104">Yazar: [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="cb4a1-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="cb4a1-105">*Web.config* dosyasına dönüşümler, aşağıdakitemel lere göre bir uygulama yayınlandığında otomatik olarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="cb4a1-106">Yapı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="cb4a1-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="cb4a1-107">Profil</span><span class="sxs-lookup"><span data-stu-id="cb4a1-107">Profile</span></span>](#profile)
* [<span data-ttu-id="cb4a1-108">Ortam</span><span class="sxs-lookup"><span data-stu-id="cb4a1-108">Environment</span></span>](#environment)
* [<span data-ttu-id="cb4a1-109">Özel</span><span class="sxs-lookup"><span data-stu-id="cb4a1-109">Custom</span></span>](#custom)

<span data-ttu-id="cb4a1-110">Bu dönüşümler aşağıdaki *web.config* oluşturma senaryolarından biri için oluşur:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="cb4a1-111">`Microsoft.NET.Sdk.Web` SDK tarafından otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="cb4a1-112">Uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) geliştirici tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="cb4a1-113">Yapı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="cb4a1-113">Build configuration</span></span>

<span data-ttu-id="cb4a1-114">Yapı yapılandırma dönüşümleri önce çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="cb4a1-115">Bir *web ekleyin.{ Yapılandırma}.her* yapı yapılandırması için config dosyası [(Hata Ayıklama| Yayın)](/dotnet/core/tools/dotnet-publish#options) bir *web.config* dönüşüm gerektiren.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="cb4a1-116">Aşağıdaki örnekte, yapılandırmaya özgü bir ortam değişkeni *web'de ayarlanır. Sürüm.config*:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cb4a1-117">Dönüştürme, yapılandırma *Sürüm*olarak ayarlandığında uygulanır:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="cb4a1-118">Yapılandırma için MSBuild `$(Configuration)`özelliği.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="cb4a1-119">Profil</span><span class="sxs-lookup"><span data-stu-id="cb4a1-119">Profile</span></span>

<span data-ttu-id="cb4a1-120">Profil dönüşümleri, Yapı [yapılandırması](#build-configuration) dönüşümlerinden sonra ikinci olarak çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="cb4a1-121">Bir *web ekleyin.{ BIR web.config* dönüştürme gerektiren her profil yapılandırması için *PROFILE}.config* dosyası.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="cb4a1-122">Aşağıdaki örnekte, web'de profile özgü bir ortam değişkeni *ayarlanır. FolderProfile.config* bir klasör yayımlama profili için:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cb4a1-123">Profil *FolderProfile*olduğunda dönüştürme uygulanır:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="cb4a1-124">Profil adı için MSBuild `$(PublishProfile)`özelliği.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="cb4a1-125">Profil geçirilemezse, varsayılan profil adı **FileSystem** ve *web'dir. Dosya* uygulamanın içerik kökünde varsa FileSystem.config uygulanır.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="cb4a1-126">Ortam</span><span class="sxs-lookup"><span data-stu-id="cb4a1-126">Environment</span></span>

<span data-ttu-id="cb4a1-127">[Yapı yapılandırması](#build-configuration) ve [Profil](#profile) dönüşümleri dönüştürülmeden sonra ortam dönüşümleri üçüncü olarak çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="cb4a1-128">Bir *web ekleyin.{ ENVIRONMENT}.config* dosyası her [ortam](xref:fundamentals/environments) için bir *web.config* dönüştürme gerektiren.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="cb4a1-129">Aşağıdaki örnekte, ortama özgü bir ortam değişkeni \*web'de ayarlanır. \*Üretim ortamı için Production.config:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cb4a1-130">Dönüşüm, çevre *üretim*olduğunda uygulanır:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="cb4a1-131">Ortam için MSBuild `$(EnvironmentName)`özelliği.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="cb4a1-132">Visual Studio'dan yayımlarken ve bir <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>yayımlama profili kullanırken bkz.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="cb4a1-133">Ortam `ASPNETCORE_ENVIRONMENT` adı belirtildiğinde ortam değişkeni *web.config* dosyasına otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="cb4a1-134">Özel</span><span class="sxs-lookup"><span data-stu-id="cb4a1-134">Custom</span></span>

<span data-ttu-id="cb4a1-135">Özel dönüşümler, Yapı [yapılandırması,](#build-configuration) [Profil](#profile)ve [Çevre](#environment) dönüşümlerinden sonra en son çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="cb4a1-136">*Web.config* dönüştürme gerektiren her özel yapılandırma için *{CUSTOM_NAME}.transform* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="cb4a1-137">Aşağıdaki örnekte, özel bir dönüştürme ortamı *değişkeni custom.transform*olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cb4a1-138">Dönüştürme, özellik `CustomTransformFileName` [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuna geçirildiğinde uygulanır:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="cb4a1-139">Profil adı için MSBuild `$(CustomTransformFileName)`özelliği.</span><span class="sxs-lookup"><span data-stu-id="cb4a1-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="cb4a1-140">web.config dönüşümlerini önleme</span><span class="sxs-lookup"><span data-stu-id="cb4a1-140">Prevent web.config transformation</span></span>

<span data-ttu-id="cb4a1-141">*web.config* dosyasının dönüşümlerini önlemek için MSBuild `$(IsWebConfigTransformDisabled)`özelliğini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="cb4a1-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="cb4a1-142">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cb4a1-142">Additional resources</span></span>

* <span data-ttu-id="cb4a1-143">[Web Uygulaması Proje Dağıtımı için Web.config Dönüşüm Sözdizimi](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="cb4a1-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="cb4a1-144">[Visual Studio kullanarak Web Proje Dağıtım için Web.config Dönüşüm Sözdizimi](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="cb4a1-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
