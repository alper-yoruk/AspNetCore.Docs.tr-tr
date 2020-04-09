---
title: ASP.NET Çekirdek dizin yapısı
author: rick-anderson
description: Yayınlanan ASP.NET Core uygulamalarının dizin yapısı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 0e7bf40520385b7719cb37120709e0a3fd2442e3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989735"
---
# <a name="aspnet-core-directory-structure"></a>ASP.NET Çekirdek dizin yapısı

::: moniker range=">= aspnetcore-3.0"

*Yayımlama* dizini, uygulamanın [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu tarafından üretilen dağıtılabilir varlıklarını içerir. Dizin şunları içerir:

* Uygulama dosyaları
* Yapılandırma dosyaları
* Statik varlıklar
* Paketler
* Çalışma zamanı (yalnızca[bağımsız dağıtım)](/dotnet/core/deploying/#self-contained-deployments-scd)

| Uygulama Türü | Dizin Yapısı |
| -------- | ------------------- |
| [Çerçeveye bağımlı Yürütülebilir (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>Yayımlamak&dagger;<ul><li>MVC uygulamalarını görüntüler;&dagger; görünümler önceden derlenmiyorsa</li><li>Sayfalar&dagger; önceden derlenmemişse, Sayfalar MVC veya Razor Pages uygulamaları</li><li>wwwroot&dagger;</li><li>\*.dll dosyaları</li><li>{MONTAJ ADI}.deps.json</li><li>{MONTAJ ADI}.dll</li><li>{MONTAJ Adı} {. EXTENSION} *.exe* uzantısı Windows'da, macOS veya Linux'ta uzantı yok</li><li>{MONTAJ ADI}.pdb</li><li>{MONTAJ Adı}. Görünümler.dll</li><li>{MONTAJ Adı}. Görünümler.pdb</li><li>{MONTAJ ADI}.runtimeconfig.json</li><li>web.config (IIS dağıtımları)</li><li>createdump ([Linux createdump programı](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (Linux paylaşılan nesne kitaplığı)</li><li>\*.a (macOS arşivi)</li><li>\*.dylib (macOS dinamik kitaplık)</li></ul></li></ul> |
| [Bağımsız Dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Yayımlamak&dagger;<ul><li>Görünümler önceden derlenmiyorsa MVC uygulamalarını görüntüler&dagger;</li><li>Sayfalar&dagger; önceden derlenmemişse, Sayfalar MVC veya Razor Pages uygulamaları</li><li>wwwroot&dagger;</li><li>\*.dll dosyaları</li><li>{MONTAJ ADI}.deps.json</li><li>{MONTAJ ADI}.dll</li><li>{MONTAJ ADI}.exe</li><li>{MONTAJ ADI}.pdb</li><li>{MONTAJ Adı}. Görünümler.dll</li><li>{MONTAJ Adı}. Görünümler.pdb</li><li>{MONTAJ ADI}.runtimeconfig.json</li><li>web.config (IIS dağıtımları)</li></ul></li></ul> |

&dagger;Bir dizini gösterir

*Yayımlama* dizini, dağıtımın *uygulama temel yolu*olarak da adlandırılan içerik kök *yolunu*temsil eder. Sunucuda dağıtılan uygulamanın *yayımlama* dizinine hangi ad verilirse verilsin, konumu sunucunun barındırılan uygulamaya fiziksel yolu olarak hizmet vermektedir.

*Wwwroot* dizini, varsa, yalnızca statik varlıkları içerir.

## <a name="additional-resources"></a>Ek kaynaklar

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* [Hedef çerçeveler](/dotnet/standard/frameworks)
* [.NET Core RID Kataloğu](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Yayımlama* dizini, uygulamanın [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu tarafından üretilen dağıtılabilir varlıklarını içerir. Dizin şunları içerir:

* Uygulama dosyaları
* Yapılandırma dosyaları
* Statik varlıklar
* Paketler
* Çalışma zamanı (yalnızca[bağımsız dağıtım)](/dotnet/core/deploying/#self-contained-deployments-scd)

| Uygulama Türü | Dizin Yapısı |
| -------- | ------------------- |
| [Çerçeveye bağımlı Yürütülebilir (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>Yayımlamak&dagger;<ul><li>MVC uygulamalarını görüntüler;&dagger; görünümler önceden derlenmiyorsa</li><li>Sayfalar&dagger; önceden derlenmemişse, Sayfalar MVC veya Razor Pages uygulamaları</li><li>wwwroot&dagger;</li><li>\*.dll dosyaları</li><li>{MONTAJ ADI}.deps.json</li><li>{MONTAJ ADI}.dll</li><li>{MONTAJ Adı} {. EXTENSION} *.exe* uzantısı Windows'da, macOS veya Linux'ta uzantı yok</li><li>{MONTAJ ADI}.pdb</li><li>{MONTAJ Adı}. Görünümler.dll</li><li>{MONTAJ Adı}. Görünümler.pdb</li><li>{MONTAJ ADI}.runtimeconfig.json</li><li>web.config (IIS dağıtımları)</li><li>createdump ([Linux createdump programı](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (Linux paylaşılan nesne kitaplığı)</li><li>\*.a (macOS arşivi)</li><li>\*.dylib (macOS dinamik kitaplık)</li></ul></li></ul> |
| [Bağımsız Dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Yayımlamak&dagger;<ul><li>Görünümler önceden derlenmiyorsa MVC uygulamalarını görüntüler&dagger;</li><li>Sayfalar&dagger; önceden derlenmemişse, Sayfalar MVC veya Razor Pages uygulamaları</li><li>wwwroot&dagger;</li><li>\*.dll dosyaları</li><li>{MONTAJ ADI}.deps.json</li><li>{MONTAJ ADI}.dll</li><li>{MONTAJ ADI}.exe</li><li>{MONTAJ ADI}.pdb</li><li>{MONTAJ Adı}. Görünümler.dll</li><li>{MONTAJ Adı}. Görünümler.pdb</li><li>{MONTAJ ADI}.runtimeconfig.json</li><li>web.config (IIS dağıtımları)</li></ul></li></ul> |
-
&dagger;Bir dizini gösterir

*Yayımlama* dizini, dağıtımın *uygulama temel yolu*olarak da adlandırılan içerik kök *yolunu*temsil eder. Sunucuda dağıtılan uygulamanın *yayımlama* dizinine hangi ad verilirse verilsin, konumu sunucunun barındırılan uygulamaya fiziksel yolu olarak hizmet vermektedir.

*Wwwroot* dizini, varsa, yalnızca statik varlıkları içerir.

*Günlükler* klasörü [oluşturmak, Çekirdek Modülü gelişmiş hata ayıklama günlüğe kaydetme ASP.NET](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)için yararlıdır. `<handlerSetting>` Değere sağlanan yoldaki klasörler modül tarafından otomatik olarak oluşturulmaz ve modülün hata ayıklama günlüğünü yazmasıiçin dağıtımda önceden var olmalıdır.

Aşağıdaki iki yaklaşımdan biri kullanılarak dağıtım için *günlükler* dizini oluşturulabilir:

* Proje dosyasına aşağıdaki `<Target>` öğeyi ekleyin:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   Öğe, `<MakeDir>` yayımlanmış çıktıda boş bir *Günlükler* klasörü oluşturur. Öğe, klasörü `PublishDir` oluşturmak için hedef konumu belirlemek için özelliği kullanır. Web Dağıtımı gibi çeşitli dağıtım yöntemleri, dağıtım sırasında boş klasörleri atlar. Öğe, `<WriteLinesToFile>` Klasörün sunucuya dağıtımını garanti eden *Günlükler* klasöründe bir dosya oluşturur. Alt işlem hedef klasöre yazma erişimi yoksa, bu yaklaşımı kullanarak klasör oluşturma başarısız olur.

* Dağıtımdaki sunucudaki *Günlükler* dizinini fiziksel olarak oluşturun.

Dağıtım dizini Okuma/Yürüt izinleri gerektirir. *Günlükler* dizini okuma/yazma izinleri gerektirir. Dosyaların yazıldığı ek dizinler için Okuma/Yazma izinleri gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* [Hedef çerçeveler](/dotnet/standard/frameworks)
* [.NET Core RID Kataloğu](/dotnet/core/rid-catalog)

::: moniker-end
