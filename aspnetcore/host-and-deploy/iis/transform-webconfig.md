---
title: Web.config’i dönüştürme
author: rick-anderson
description: ASP.NET Core uygulamasını yayımlarken Web. config dosyasını dönüştürmeyi öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: f7e1fb0adc669b4bffa02e6688231c8f1447bd98
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775940"
---
# <a name="transform-webconfig"></a>Web.config’i dönüştürme

Tarafından [Vijay Oymakrishnan](https://github.com/vijayrkn)

*Web. config* dosyasına dönüşümler, bir uygulama temel alınarak yayımlandığında otomatik olarak uygulanabilir:

* [Yapı yapılandırması](#build-configuration)
* [Profil](#profile)
* [Ortam](#environment)
* [Özel](#custom)

Bu dönüşümler aşağıdaki *Web. config* oluşturma senaryolarından biri için oluşur:

* `Microsoft.NET.Sdk.Web` SDK tarafından otomatik olarak oluşturulur.
* Uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) geliştirici tarafından sağlanmaktadır.

## <a name="build-configuration"></a>Yapı yapılandırması

Derleme yapılandırma dönüştürmeleri önce çalıştırılır.

Bir *Web ekleyin. { Her derleme yapılandırması için CONFIGURATION}. config* dosyası [(Hata Ayıkla | Yayın)](/dotnet/core/tools/dotnet-publish#options) bir *Web. config* dönüştürmesi gerektirir.

Aşağıdaki örnekte, Web 'de yapılandırmaya özgü bir ortam değişkeni ayarlanır *. Release. config*:

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

Yapılandırma için MSBuild özelliği `$(Configuration)`.

## <a name="profile"></a>Profil

Profil dönüştürmeleri, [Derleme yapılandırması](#build-configuration) dönüşümlerinden sonra ikinci çalıştırılır.

Bir *Web ekleyin. { *Bir *Web. config* dönüşümü gerektiren her PROFIL yapılandırması için profile}. config dosyası.

Aşağıdaki örnekte, Web 'de profile özgü bir ortam değişkeni ayarlanır *. *Bir klasör yayımlama profili Için folderprofile. config:

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

Profil adı için MSBuild özelliği `$(PublishProfile)`.

Hiçbir profil geçirilmemişse, varsayılan profil adı **dosya sistemi** ve Web olur *. *Dosya uygulamanın içerik kökünde mevcutsa FileSystem. config uygulanır.

## <a name="environment"></a>Ortam

Ortam dönüştürmeleri, [Derleme yapılandırması](#build-configuration) ve [profil](#profile) dönüşümleri sonrasında üçüncü olarak çalıştırılır.

Bir *Web ekleyin. { *Bir *Web. config* dönüştürmesi gerektiren her [ortam](xref:fundamentals/environments) için Environment}. config dosyası.

Aşağıdaki örnekte, ortama özgü bir ortam değişkeni Web 'de ayarlanır *. Üretim ortamı için Production. config* :

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

Ortamın MSBuild özelliği `$(EnvironmentName)`.

Visual Studio 'dan yayımlama ve bir yayımlama profili kullanma sırasında, bkz <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>..

`ASPNETCORE_ENVIRONMENT` Ortam değişkeni, ortam adı belirtildiğinde *Web. config* dosyasına otomatik olarak eklenir.

## <a name="custom"></a>Özel

Özel dönüştürmeler son olarak, [Derleme yapılandırması](#build-configuration), [profil](#profile)ve [ortam](#environment) dönüşümlerinden sonra çalıştırılır.

Bir *Web. config* dönüştürmesi gerektiren her özel yapılandırma için bir *{CUSTOM_NAME}. Transform* dosyası ekleyin.

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

Profil adı için MSBuild özelliği `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Web. config dönüşümünü engelle

*Web. config* dosyasının dönüştürmelerini engellemek için MSBuild özelliğini `$(IsWebConfigTransformDisabled)`ayarlayın:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Web uygulaması proje dağıtımı için Web. config dönüştürme sözdizimi](/previous-versions/dd465326(v=vs.100))
* [Visual Studio kullanarak Web proje dağıtımı için Web. config dönüştürme sözdizimi](/previous-versions/aspnet/dd465326(v=vs.110))
