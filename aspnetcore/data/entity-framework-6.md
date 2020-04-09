---
title: ASP.NET Çekirdek ve Varlık Çerçevesi 6 ile Başlayın
author: rick-anderson
description: Bu makalede, ASP.NET Core uygulamasında Entity Framework 6 nasıl kullanılacağı gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656389"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>ASP.NET Çekirdek ve Varlık Çerçevesi 6 ile Başlayın

Yazar: [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), ve [Tom Dykstra](https://github.com/tdykstra)

Bu makalede, ASP.NET Core uygulamasında Entity Framework 6 nasıl kullanılacağı gösterilmektedir.

## <a name="overview"></a>Genel Bakış

Entity Framework 6'yı kullanmak için, Entity Framework 6 .NET Core'u desteklemediği için projenizin .NET Framework'e karşı derlemesi vardır. Platform ötesi özelliklere ihtiyacınız varsa [Entity Framework Core'a](/ef/)yükseltmeniz gerekir.

Varlık Framework 6'yı ASP.NET Core uygulamasında kullanmanın önerilen yolu, EF6 bağlamını ve model sınıflarını .NET Framework'ü hedefleyen bir sınıf kitaplığı projesine koymaktır. ASP.NET Core projesinden sınıf kitaplığına bir başvuru ekleyin. [EF6 ve ASP.NET Core projeleri ile](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)örnek Visual Studio çözümüne bakın.

bir ASP.NET Çekirdek projesine BIR EF6 bağlamı koyamazsınız, çünkü .NET Core *projeleri, Geçişleri Etkinleştir* gibi EF6 komutlarının gerektirdiği tüm işlevleri desteklemez.

EF6 bağlamınızı hangi proje türünden bulursanız bulun, yalnızca EF6 komut satırı araçları bir EF6 bağlamıyla çalışır. Örneğin, `Scaffold-DbContext` yalnızca Entity Framework Core'da kullanılabilir. Bir veritabanının bir EF6 modeline ters mühendislik yapması gerekiyorsa, [Önce Varolan Veritabanına Kod'u](https://msdn.microsoft.com/jj200620)bakın.

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>ASP.NET Core projesinde referans tam çerçeve ve EF6

ASP.NET Core projenizin .NET Framework'u hedeflemesi ve EF6'ya başvurması gerekir. Örneğin, ASP.NET Core projenizin *.csproj* dosyası aşağıdaki örneğe benzer (yalnızca dosyanın ilgili bölümleri gösterilir).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Yeni bir proje **oluştururken, ASP.NET Çekirdek Web Uygulaması (.NET Framework)** şablonundan yararlanın.

## <a name="handle-connection-strings"></a>Bağlantı dizelerini işleme

EF6 sınıf kitaplığı projesinde kullanacağınız EF6 komut satırı araçları, bağlamı anında oluşturabilmeleri için varsayılan bir oluşturucu gerektirir. Ancak büyük olasılıkla ASP.NET Çekirdek projesinde kullanılacak bağlantı dizesini belirtmek istersiniz, bu durumda bağlam oluşturucunuz bağlantı dizesini geçmenizi sağlayan bir parametreye sahip olmalıdır. Bir örneği aşağıda verilmiştir.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

EF6 bağlamınızda parametresiz bir oluşturucu olmadığından, EF6 projenizin [IDbContextFactory](https://msdn.microsoft.com/library/hh506876)uygulamasını sağlaması gerekir. EF6 komut satırı araçları, bağlamı anında kullanabilmeleri için bu uygulamayı bulur ve kullanır. Bir örneği aşağıda verilmiştir.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

Bu örnek kodda, `IDbContextFactory` uygulama sabit kodlu bir bağlantı dizesinde geçer. Bu, komut satırı araçlarının kullanacağı bağlantı dizesidir. Sınıf kitaplığı arama uygulamasının kullandığı bağlantı dizesini kullandığından emin olmak için bir strateji uygulamak isteyeceksiniz. Örneğin, her iki projede de bir ortam değişkeninden değer alabilirsiniz.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>ASP.NET Core projesinde bağımlılık enjeksiyonu ayarlama

Core projesinin *Startup.cs* dosyasında, bağımlılık enjeksiyonu (DI) `ConfigureServices`için EF6 bağlamını . EF bağlam nesneleri, istek başına bir yaşam süresi için kapsamalmalıdır.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Daha sonra DI kullanarak denetleyicileri bağlamında bir örnek alabilirsiniz. Kod, EF Core bağlamı için yazdıklarına benzer:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Örnek uygulama

Çalışan bir örnek uygulama için, bu makaleye eşlik eden [örnek Visual Studio çözümüne](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) bakın.

Bu örnek Visual Studio'da aşağıdaki adımlarla sıfırdan oluşturulabilir:

* Bir çözüm oluşturun.

* **Add** > **Yeni Proje** > **Web** > **ASP.NET Çekirdek Web Uygulaması** Ekle
  * Proje şablonu seçim iletişim kutusunda, açılır açılır durumda API ve .NET Framework'u seçin

* **Add** > **Yeni Proje** > **Ekle Windows Masaüstü** > **Sınıf Kitaplığı (.NET Framework)**

* Her iki proje için **paket yöneticisi konsolunda** `Install-Package Entityframework`(PMC) komutu çalıştırın.

* Sınıf kitaplığı projesinde, veri modeli sınıfları ve bağlam `IDbContextFactory`sınıfı ve bir uygulama oluşturun.

* Sınıf kitaplığı projesi için PMC'de `Enable-Migrations` `Add-Migration Initial`komutları çalıştırın ve . ASP.NET Core projesini başlangıç projesi olarak ayarladıysanız, bu komutları ekleyin. `-StartupProjectName EF6`

* Çekirdek projesinde, sınıf kitaplığı projesine bir proje başvurusu ekleyin.

* Core projesinde, *Startup.cs,* diçin bağlamı kaydedin.

* Core projesinde, *appsettings.json,* bağlantı dizesini ekleyin.

* Çekirdek projesinde, verileri okuyup yazabileceğinizi doğrulamak için bir denetleyici ve görünüm(ler) ekleyin. (ASP.NET Core MVC iskelesinin sınıf kitaplığından başvurulan EF6 bağlamıyla çalışmayacağını unutmayın.)

## <a name="summary"></a>Özet

Bu makalede, ASP.NET Core uygulamasında Varlık Çerçevesi 6'yı kullanmak için temel kılavuz sağlanmıştır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Varlık Çerçevesi - Kod Tabanlı Yapılandırma](https://msdn.microsoft.com/data/jj680699.aspx)
