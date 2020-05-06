---
title: ASP.NET Core ve Entity Framework 6 ile çalışmaya başlama
author: rick-anderson
description: Bu makalede, Entity Framework 6 ' nın ASP.NET Core bir uygulamada nasıl kullanılacağı gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 7c77ecb663bebcaf0c6239f07ac15dda23f17b9c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775485"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>ASP.NET Core ve Entity Framework 6 ile çalışmaya başlama

By [Paweł Grudzień](https://github.com/pgrudzien12), [Davmien Pontıex](https://github.com/DamienPontifex)ve [Tom Dykstra](https://github.com/tdykstra)

Bu makalede, Entity Framework 6 ' nın ASP.NET Core bir uygulamada nasıl kullanılacağı gösterilmektedir.

## <a name="overview"></a>Genel Bakış

Entity Framework 6 ' yı kullanmak için, Entity Framework 6 ' nın .NET Core 'u desteklemediği için, projenizin .NET Framework karşı derlenmesi gerekir. Platformlar arası özelliklere ihtiyacınız varsa [Entity Framework Core](/ef/)yükseltmeniz gerekir.

ASP.NET Core uygulamasında 6 Entity Framework kullanmanın önerilen yolu, EF6 bağlamını ve model sınıflarını .NET Framework hedefleyen bir sınıf kitaplığı projesine koykullanmaktır. ASP.NET Core projesinden sınıf kitaplığına bir başvuru ekleyin. Örnek [Visual Studio çözümüne EF6 ve ASP.NET Core projelerine](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)bakın.

.NET Core projeleri, *Enable-geçişler* gibi EF6 komutlarının tüm işlevlerini desteklemediğinden, bir ASP.NET Core projesine EF6 bağlamı koyamazsınız.

EF6 bağlamını bulmakta olduğunuz proje türünden bağımsız olarak, yalnızca EF6 komut satırı araçları bir EF6 bağlamıyla çalışır. Örneğin, `Scaffold-DbContext` yalnızca Entity Framework Core kullanılabilir. Bir veritabanının EF6 modeline ters mühendislik uygulamanız gerekiyorsa bkz. [var olan bir veritabanına Code First](https://msdn.microsoft.com/jj200620).

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>ASP.NET Core projesindeki tam Framework ve EF6 başvurusu

ASP.NET Core projenizin .NET Framework ve başvuru EF6 hedeflemesi gerekir. Örneğin, ASP.NET Core projenizin *. csproj* dosyası aşağıdaki örneğe benzer olacaktır (yalnızca dosyanın ilgili bölümleri gösterilir).

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

Yeni bir proje oluştururken **ASP.NET Core Web uygulaması (.NET Framework)** şablonunu kullanın.

## <a name="handle-connection-strings"></a>Bağlantı dizelerini işle

EF6 sınıf kitaplığı projesinde kullanacağınız EF6 komut satırı araçları, bağlamı örneklenebilen bir varsayılan Oluşturucu gerektirir. Ancak büyük olasılıkla, ASP.NET Core projesinde kullanılacak bağlantı dizesini belirtmek isteyeceksiniz. Bu durumda, bağlam oluşturucunun bağlantı dizesinde geçiş yapmanızı sağlayan bir parametreye sahip olması gerekir. Bir örneği aşağıda verilmiştir.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

EF6 içeriğiniz parametresiz bir oluşturucuya sahip olmadığından, EF6 projenizin bir [ıdbcontextfactory](https://msdn.microsoft.com/library/hh506876)uygulamasını sağlaması gerekir. EF6 komut satırı araçları, bağlamı örneklebilmeleri için bu uygulamayı bulup kullanacaktır. Bir örneği aşağıda verilmiştir.

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

Bu örnek kodda, `IDbContextFactory` uygulama sabit kodlanmış bir bağlantı dizesinde geçirilir. Bu, komut satırı araçlarının kullanacağı bağlantı dizesidir. Sınıf kitaplığının çağıran uygulamanın kullandığı bağlantı dizesini kullandığından emin olmak için bir strateji uygulamak isteyeceksiniz. Örneğin, her iki projedeki bir ortam değişkeninden değeri alabilirsiniz.

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>ASP.NET Core projesine bağımlılık ekleme işlemini ayarlama

Çekirdek projenin *Startup.cs* dosyasında, içindeki `ConfigureServices`bağımlılık ekleme (dı) için EF6 bağlamını ayarlayın. EF bağlam nesneleri, istek başına ömür için kapsamı belirlenmiş olmalıdır.

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

Daha sonra, DI kullanarak denetleyicilerinizdeki bağlamın bir örneğini alabilirsiniz. Kod, EF Core bağlamı için yazdıklarınız ile benzerdir:

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>Örnek uygulama

Çalışan bir örnek uygulama için, bu makaleye eşlik eden [örnek Visual Studio çözümü](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) ' ne bakın.

Bu örnek, Visual Studio 'da aşağıdaki adımlarla sıfırdan oluşturulabilir:

* Bir çözüm oluşturun.

* **Add** > **Yeni**Project > **Web**Web > **ASP.NET Core Web uygulaması** Ekle
  * Proje şablonu seçimi iletişim kutusunda, açılan menüde API ve .NET Framework seçin

* **Add** > **Yeni**proje > Ekle**Windows Masaüstü** > **sınıf kitaplığı (.NET Framework)**

* Her iki proje için de **Paket Yöneticisi konsolunda** (PMC) komutunu `Install-Package Entityframework`çalıştırın.

* Sınıf kitaplığı projesinde, veri modeli sınıfları ve bağlam sınıfı ve uygulamasını oluşturun `IDbContextFactory`.

* Sınıf kitaplığı projesi için PMC 'de, ve `Enable-Migrations` `Add-Migration Initial`komutlarını çalıştırın. ASP.NET Core projesini başlangıç projesi olarak ayarladıysanız, bu komutlara ekleyin `-StartupProjectName EF6` .

* Çekirdek projede, sınıf kitaplığı projesine bir proje başvurusu ekleyin.

* Çekirdek projede, *Startup.cs*IÇINDE, dı için bağlamını kaydedin.

* Core projesinde, *appSettings. JSON*içinde bağlantı dizesini ekleyin.

* Temel projede, verileri okuyabildiğinizi ve yazabildiğinizi doğrulamak için bir denetleyici ve görünüm ekleyin. (ASP.NET Core MVC yapı iskelesi, sınıf kitaplığından başvurulan EF6 bağlamıyla çalışmaz.)

## <a name="summary"></a>Özet

Bu makale, bir ASP.NET Core uygulamasında 6 Entity Framework kullanmak için temel rehberlik sağlamıştır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Kod tabanlı Entity Framework yapılandırma](https://msdn.microsoft.com/data/jj680699.aspx)
