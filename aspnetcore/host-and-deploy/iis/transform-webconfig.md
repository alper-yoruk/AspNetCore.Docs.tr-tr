---
title: Web.config’i dönüştürme
author: rick-anderson
description: ASP.NET Core uygulamasını yayımlarken web.config dosyasını dönüştürmeyi öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 3d0bbfceb6dfedd7234112d33fd99e91a34f5793
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015588"
---
# <a name="transform-webconfig"></a><span data-ttu-id="d7b4e-103">Web.config’i dönüştürme</span><span class="sxs-lookup"><span data-stu-id="d7b4e-103">Transform web.config</span></span>

<span data-ttu-id="d7b4e-104">Tarafından [Vijay Oymakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="d7b4e-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="d7b4e-105">*web.config* dosyaya dönüşümler, bir uygulama temel alınarak yayımlandığında otomatik olarak uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="d7b4e-106">Yapı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d7b4e-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="d7b4e-107">Profil</span><span class="sxs-lookup"><span data-stu-id="d7b4e-107">Profile</span></span>](#profile)
* [<span data-ttu-id="d7b4e-108">Ortam</span><span class="sxs-lookup"><span data-stu-id="d7b4e-108">Environment</span></span>](#environment)
* [<span data-ttu-id="d7b4e-109">Özel</span><span class="sxs-lookup"><span data-stu-id="d7b4e-109">Custom</span></span>](#custom)

<span data-ttu-id="d7b4e-110">Bu dönüşümler aşağıdaki *web.config* oluşturma senaryolarından biri için oluşur:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="d7b4e-111">SDK tarafından otomatik olarak oluşturulur `Microsoft.NET.Sdk.Web` .</span><span class="sxs-lookup"><span data-stu-id="d7b4e-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="d7b4e-112">Uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) geliştirici tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="d7b4e-113">Yapı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d7b4e-113">Build configuration</span></span>

<span data-ttu-id="d7b4e-114">Derleme yapılandırma dönüştürmeleri önce çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="d7b4e-115">Bir *Web ekleyin. { Her derleme yapılandırması için CONFIGURATION}. config* dosyası [(Hata Ayıkla | Yayın)](/dotnet/core/tools/dotnet-publish#options) *web.config* dönüştürmesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="d7b4e-116">Aşağıdaki örnekte, yapılandırmaya özgü bir ortam değişkeni *web.Release.config*olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

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

<span data-ttu-id="d7b4e-117">Yapılandırma *yayın*olarak ayarlandığında dönüşüm uygulanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="d7b4e-118">Yapılandırma için MSBuild özelliği `$(Configuration)` .</span><span class="sxs-lookup"><span data-stu-id="d7b4e-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="d7b4e-119">Profil</span><span class="sxs-lookup"><span data-stu-id="d7b4e-119">Profile</span></span>

<span data-ttu-id="d7b4e-120">Profil dönüştürmeleri, [Derleme yapılandırması](#build-configuration) dönüşümlerinden sonra ikinci çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="d7b4e-121">Bir \*Web ekleyin. { \*Her profil yapılandırması için bir *web.config* dönüşümü gerektiren profile}. config dosyası.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="d7b4e-122">Aşağıdaki örnekte, bir klasör yayımlama profili için *web.FolderProfile.config* profile özgü bir ortam değişkeni ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

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

<span data-ttu-id="d7b4e-123">Profil *Folderprofile*olduğunda dönüştürme uygulanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="d7b4e-124">Profil adı için MSBuild özelliği `$(PublishProfile)` .</span><span class="sxs-lookup"><span data-stu-id="d7b4e-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="d7b4e-125">Hiçbir profil geçirilmemişse, varsayılan profil adı dosya **sistemi** olur ve dosya uygulamanın içerik kökünde mevcutsa *web.FileSystem.config* uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="d7b4e-126">Ortam</span><span class="sxs-lookup"><span data-stu-id="d7b4e-126">Environment</span></span>

<span data-ttu-id="d7b4e-127">Ortam dönüştürmeleri, [Derleme yapılandırması](#build-configuration) ve [profil](#profile) dönüşümleri sonrasında üçüncü olarak çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="d7b4e-128">Bir *Web ekleyin. { ORTAM}. yapılandırma* dosyası *web.config* dönüşüm gerektiren her [ortam](xref:fundamentals/environments) için.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="d7b4e-129">Aşağıdaki örnekte, ortama özgü bir ortam değişkeni, üretim ortamı için *web.Production.config* ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

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

<span data-ttu-id="d7b4e-130">Dönüşüm, ortam *Üretim*olduğunda uygulanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="d7b4e-131">Ortamın MSBuild özelliği `$(EnvironmentName)` .</span><span class="sxs-lookup"><span data-stu-id="d7b4e-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="d7b4e-132">Visual Studio 'dan yayımlama ve bir yayımlama profili kullanma sırasında, bkz <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment> ..</span><span class="sxs-lookup"><span data-stu-id="d7b4e-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="d7b4e-133">Ortam `ASPNETCORE_ENVIRONMENT` değişkeni, ortam adı belirtildiğinde *web.config* dosyasına otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="d7b4e-134">Özel</span><span class="sxs-lookup"><span data-stu-id="d7b4e-134">Custom</span></span>

<span data-ttu-id="d7b4e-135">Özel dönüştürmeler son olarak, [Derleme yapılandırması](#build-configuration), [profil](#profile)ve [ortam](#environment) dönüşümlerinden sonra çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="d7b4e-136">*web.config* dönüştürmesi gerektiren her özel yapılandırma için bir *{CUSTOM_NAME}. Transform* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d7b4e-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="d7b4e-137">Aşağıdaki örnekte, özel bir Transform ortam değişkeni *Custom. Transform*olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

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

<span data-ttu-id="d7b4e-138">Dönüşüm, `CustomTransformFileName` özellik [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuna geçirildiğinde uygulanır:</span><span class="sxs-lookup"><span data-stu-id="d7b4e-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="d7b4e-139">Profil adı için MSBuild özelliği `$(CustomTransformFileName)` .</span><span class="sxs-lookup"><span data-stu-id="d7b4e-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="d7b4e-140">web.config dönüştürmeyi engelle</span><span class="sxs-lookup"><span data-stu-id="d7b4e-140">Prevent web.config transformation</span></span>

<span data-ttu-id="d7b4e-141">*web.config* dosyasının dönüştürmelerini engellemek için MSBuild özelliğini ayarlayın `$(IsWebConfigTransformDisabled)` :</span><span class="sxs-lookup"><span data-stu-id="d7b4e-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="d7b4e-142">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d7b4e-142">Additional resources</span></span>

* <span data-ttu-id="d7b4e-143">[Web uygulaması proje dağıtımı içinWeb.config dönüştürme sözdizimi](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="d7b4e-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="d7b4e-144">[Visual Studio kullanarak Web proje dağıtımı içinWeb.config dönüştürme sözdizimi](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="d7b4e-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
