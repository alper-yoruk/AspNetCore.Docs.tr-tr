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
# <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yazar: [Vijay Ramakrishnan](https://github.com/vijayrkn)

*Web.config* dosyasına dönüşümler, aşağıdakitemel lere göre bir uygulama yayınlandığında otomatik olarak uygulanabilir:

* [Yapı yapılandırması](#build-configuration)
* [Profil](#profile)
* [Ortam](#environment)
* [Özel](#custom)

Bu dönüşümler aşağıdaki *web.config* oluşturma senaryolarından biri için oluşur:

* `Microsoft.NET.Sdk.Web` SDK tarafından otomatik olarak oluşturulur.
* Uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) geliştirici tarafından sağlanır.

## <a name="build-configuration"></a>Yapı yapılandırması

Yapı yapılandırma dönüşümleri önce çalıştırılır.

Bir *web ekleyin.{ Yapılandırma}.her* yapı yapılandırması için config dosyası [(Hata Ayıklama| Yayın)](/dotnet/core/tools/dotnet-publish#options) bir *web.config* dönüşüm gerektiren.

Aşağıdaki örnekte, yapılandırmaya özgü bir ortam değişkeni *web'de ayarlanır. Sürüm.config*:

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

Dönüştürme, yapılandırma *Sürüm*olarak ayarlandığında uygulanır:

```dotnetcli
dotnet publish --configuration Release
```

Yapılandırma için MSBuild `$(Configuration)`özelliği.

## <a name="profile"></a>Profil

Profil dönüşümleri, Yapı [yapılandırması](#build-configuration) dönüşümlerinden sonra ikinci olarak çalıştırılır.

Bir *web ekleyin.{ BIR web.config* dönüştürme gerektiren her profil yapılandırması için *PROFILE}.config* dosyası.

Aşağıdaki örnekte, web'de profile özgü bir ortam değişkeni *ayarlanır. FolderProfile.config* bir klasör yayımlama profili için:

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

Profil *FolderProfile*olduğunda dönüştürme uygulanır:

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Profil adı için MSBuild `$(PublishProfile)`özelliği.

Profil geçirilemezse, varsayılan profil adı **FileSystem** ve *web'dir. Dosya* uygulamanın içerik kökünde varsa FileSystem.config uygulanır.

## <a name="environment"></a>Ortam

[Yapı yapılandırması](#build-configuration) ve [Profil](#profile) dönüşümleri dönüştürülmeden sonra ortam dönüşümleri üçüncü olarak çalıştırılır.

Bir *web ekleyin.{ ENVIRONMENT}.config* dosyası her [ortam](xref:fundamentals/environments) için bir *web.config* dönüştürme gerektiren.

Aşağıdaki örnekte, ortama özgü bir ortam değişkeni *web'de ayarlanır. *Üretim ortamı için Production.config:

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

Dönüşüm, çevre *üretim*olduğunda uygulanır:

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Ortam için MSBuild `$(EnvironmentName)`özelliği.

Visual Studio'dan yayımlarken ve bir <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>yayımlama profili kullanırken bkz.

Ortam `ASPNETCORE_ENVIRONMENT` adı belirtildiğinde ortam değişkeni *web.config* dosyasına otomatik olarak eklenir.

## <a name="custom"></a>Özel

Özel dönüşümler, Yapı [yapılandırması,](#build-configuration) [Profil](#profile)ve [Çevre](#environment) dönüşümlerinden sonra en son çalıştırılır.

*Web.config* dönüştürme gerektiren her özel yapılandırma için *{CUSTOM_NAME}.transform* dosyası ekleyin.

Aşağıdaki örnekte, özel bir dönüştürme ortamı *değişkeni custom.transform*olarak ayarlanır:

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

Dönüştürme, özellik `CustomTransformFileName` [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutuna geçirildiğinde uygulanır:

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Profil adı için MSBuild `$(CustomTransformFileName)`özelliği.

## <a name="prevent-webconfig-transformation"></a>web.config dönüşümlerini önleme

*web.config* dosyasının dönüşümlerini önlemek için MSBuild `$(IsWebConfigTransformDisabled)`özelliğini ayarlayın:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Web Uygulaması Proje Dağıtımı için Web.config Dönüşüm Sözdizimi](/previous-versions/dd465326(v=vs.100))
* [Visual Studio kullanarak Web Proje Dağıtım için Web.config Dönüşüm Sözdizimi](/previous-versions/aspnet/dd465326(v=vs.110))
