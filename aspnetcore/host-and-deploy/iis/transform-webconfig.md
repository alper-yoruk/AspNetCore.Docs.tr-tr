---
title: Web.config’i dönüştürme
author: rick-anderson
description: ASP.NET Core uygulamasını yayımlarken web.config dosyasını dönüştürmeyi öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: bebba7a72012b8be6257b14642bf130613627778
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404008"
---
# <a name="transform-webconfig"></a>Web.config’i dönüştürme

Tarafından [Vijay Oymakrishnan](https://github.com/vijayrkn)

*web.config* dosyaya dönüşümler, bir uygulama temel alınarak yayımlandığında otomatik olarak uygulanabilir:

* [Yapı yapılandırması](#build-configuration)
* [Profil](#profile)
* [Ortam](#environment)
* [Özel](#custom)

Bu dönüşümler aşağıdaki *web.config* oluşturma senaryolarından biri için oluşur:

* SDK tarafından otomatik olarak oluşturulur `Microsoft.NET.Sdk.Web` .
* Uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) geliştirici tarafından sağlanmaktadır.

## <a name="build-configuration"></a>Yapı yapılandırması

Derleme yapılandırma dönüştürmeleri önce çalıştırılır.

Bir *Web ekleyin. { Her derleme yapılandırması için CONFIGURATION}. config* dosyası [(Hata Ayıkla | Yayın)](/dotnet/core/tools/dotnet-publish#options) *web.config* dönüştürmesi gerektirir.

Aşağıdaki örnekte, yapılandırmaya özgü bir ortam değişkeni *web.Release.config*olarak ayarlanır:

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

Yapılandırma *yayın*olarak ayarlandığında dönüşüm uygulanır:

```dotnetcli
dotnet publish --configuration Release
```

Yapılandırma için MSBuild özelliği `$(Configuration)` .

## <a name="profile"></a>Profil

Profil dönüştürmeleri, [Derleme yapılandırması](#build-configuration) dönüşümlerinden sonra ikinci çalıştırılır.

Bir *Web ekleyin. { *Her profil yapılandırması için bir *web.config* dönüşümü gerektiren profile}. config dosyası.

Aşağıdaki örnekte, bir klasör yayımlama profili için *web.FolderProfile.config* profile özgü bir ortam değişkeni ayarlanır:

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

Profil *Folderprofile*olduğunda dönüştürme uygulanır:

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Profil adı için MSBuild özelliği `$(PublishProfile)` .

Hiçbir profil geçirilmemişse, varsayılan profil adı dosya **sistemi** olur ve dosya uygulamanın içerik kökünde mevcutsa *web.FileSystem.config* uygulanır.

## <a name="environment"></a>Ortam

Ortam dönüştürmeleri, [Derleme yapılandırması](#build-configuration) ve [profil](#profile) dönüşümleri sonrasında üçüncü olarak çalıştırılır.

Bir *Web ekleyin. { ORTAM}. yapılandırma* dosyası *web.config* dönüşüm gerektiren her [ortam](xref:fundamentals/environments) için.

Aşağıdaki örnekte, ortama özgü bir ortam değişkeni, üretim ortamı için *web.Production.config* ayarlanır:

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

Dönüşüm, ortam *Üretim*olduğunda uygulanır:

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Ortamın MSBuild özelliği `$(EnvironmentName)` .

Visual Studio 'dan yayımlama ve bir yayımlama profili kullanma sırasında, bkz <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment> ..

Ortam `ASPNETCORE_ENVIRONMENT` değişkeni, ortam adı belirtildiğinde *web.config* dosyasına otomatik olarak eklenir.

## <a name="custom"></a>Özel

Özel dönüştürmeler son olarak, [Derleme yapılandırması](#build-configuration), [profil](#profile)ve [ortam](#environment) dönüşümlerinden sonra çalıştırılır.

*web.config* dönüştürmesi gerektiren her özel yapılandırma için bir *{CUSTOM_NAME}. Transform* dosyası ekleyin.

Aşağıdaki örnekte, özel bir Transform ortam değişkeni *Custom. Transform*olarak ayarlanır:

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

Dönüşüm, `CustomTransformFileName` özellik [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuna geçirildiğinde uygulanır:

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Profil adı için MSBuild özelliği `$(CustomTransformFileName)` .

## <a name="prevent-webconfig-transformation"></a>web.config dönüştürmeyi engelle

*web.config* dosyasının dönüştürmelerini engellemek için MSBuild özelliğini ayarlayın `$(IsWebConfigTransformDisabled)` :

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Web uygulaması proje dağıtımı içinWeb.config dönüştürme sözdizimi](/previous-versions/dd465326(v=vs.100))
* [Visual Studio kullanarak Web proje dağıtımı içinWeb.config dönüştürme sözdizimi](/previous-versions/aspnet/dd465326(v=vs.110))
