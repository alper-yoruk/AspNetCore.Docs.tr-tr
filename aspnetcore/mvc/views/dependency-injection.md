---
title: ASP.NET Core görünümlere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC görünümlerine bağımlılık ekleme işlemini nasıl desteklediğini öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/dependency-injection
ms.openlocfilehash: 0cc452b25392fe92c87ef346b2e16350fb3ec19a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405893"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>ASP.NET Core görünümlere bağımlılık ekleme

[Steve Smith](https://ardalis.com/) tarafından

ASP.NET Core, görünümlere [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini destekler. Bu, yalnızca görünüm öğelerini doldurmak için gereken yerelleştirme veya veriler gibi görünüme özgü hizmetler için yararlı olabilir. Denetleyicileriniz ve görünümleriniz arasındaki [kaygıların ayrılmasını](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) korumayı denemelisiniz. Görünümlerinizin görüntüleyeceği verilerin çoğu denetleyiciden geçirilmelidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="configuration-injection"></a>Yapılandırma ekleme

Değerler *üzerindeappsettings.js* doğrudan bir görünüme eklenebilir.

Dosyadaki bir *appsettings.js* örneği:

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

İçin sözdizimi `@inject` :`@inject <type> <name>`

Şunu kullanarak bir örnek `@inject` :

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a>Hizmet ekleme

Bir hizmet, yönergesini kullanarak bir görünüme eklenebilir `@inject` . `@inject`Görünüme özellik ekleme ve dı kullanarak özelliği doldurma gibi düşünebilirsiniz.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

Bu görünüm `ToDoItem` , örneklerin listesini, genel istatistikleri gösteren bir Özet ile birlikte görüntüler. Özet, eklenen öğesinden doldurulur `StatisticsService` . Bu hizmet, Startup.cs içinde bağımlılık ekleme için `ConfigureServices` kaydedilir *Startup.cs*:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

, `StatisticsService` `ToDoItem` Bir depo aracılığıyla eriştiği örnek kümesi üzerinde bazı hesaplamalar gerçekleştirir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

Örnek depo, bellek içi bir koleksiyon kullanır. Yukarıda gösterilen uygulama (bellekteki tüm veriler üzerinde çalışır), büyük ve uzaktan erişilen veri kümeleri için önerilmez.

Örnek, görüntüleme ile bağlantılı modeldeki verileri ve görünüme eklenen hizmeti görüntüler:

![Toplam öğe, tamamlanan öğe, ortalama öncelik ve tamamlanma düzeylerine sahip bir görev listesi ve tamamlanma belirten Boole değerlerini görüntülemek için.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Arama verileri dolduruluyor

Görünüm ekleme, açılan listeler gibi kullanıcı arabirimi öğelerindeki seçenekleri doldurmak için yararlı olabilir. Cinsiyet, eyalet ve diğer tercihlerin belirtilmesine yönelik seçenekleri içeren bir kullanıcı profili formu düşünün. Standart bir MVC yaklaşımı kullanarak bu tür bir formu işlemek, denetleyicinin Bu seçenek kümelerinin her biri için veri erişim Hizmetleri istemesi ve sonra bir modeli ya da `ViewBag` bağlanacak her bir seçenek kümesiyle doldurmasını gerektirir.

Alternatif yaklaşım, seçenekleri almak için Hizmetleri doğrudan görünüme çıkarır. Bu, denetleyicinin gerektirdiği kod miktarını en aza indirir ve bu görünüm öğesi oluşturma mantığını görünümün içine taşır. Bir profil düzenlemesi formunu görüntülemeye yönelik denetleyici eylemi yalnızca profil örneği formunu geçirmeniz gerekir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Bu tercihleri güncelleştirmek için kullanılan HTML formu, özelliklerin üç yanındaki açılan listeleri içerir:

![Ad, cinsiyet, eyalet ve sık kullanılan renk girişine izin veren bir formla profil görünümünü güncelleştirin.](dependency-injection/_static/updateprofile.png)

Bu listeler, görünüme eklenen bir hizmet tarafından doldurulur:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

, `ProfileOptionsService` Yalnızca bu form için gereken verileri sağlamak üzere tasarlanan BIR UI düzeyi hizmettir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> Uygulamasına bağımlılık ekleme yoluyla istek yaptığınız türleri kaydetmeyi unutmayın `Startup.ConfigureServices` . Kaydedilmemiş bir tür, hizmet sağlayıcısı, [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice)aracılığıyla dahili olarak sorgulandığından çalışma zamanında bir özel durum oluşturur.

## <a name="overriding-services"></a>Hizmetleri geçersiz kılma

Bu teknik, ekleme yeni hizmetlere ek olarak, bir sayfada önceden eklenen Hizmetleri geçersiz kılmak için de kullanılabilir. Aşağıdaki şekilde, ilk örnekte kullanılan sayfada bulunan tüm alanlar gösterilmektedir:

![HTML, bileşen, StatsService ve URL alanları için belirlenmiş bir @ symbol üzerinde IntelliSense bağlamsal menüsü](dependency-injection/_static/razor-fields.png)

Gördüğünüz gibi, varsayılan alanlar `Html` ,, `Component` ve `Url` ( `StatsService` eklediğimiz), ve ' i içerir. Örneğin varsayılan HTML yardımcılarını kendi kendinize değiştirmek istiyorsanız, şunu kullanarak kolayca yapabilirsiniz `@inject` :

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Mevcut hizmetleri genişletmek istiyorsanız, bu tekniği kullanarak var olan uygulamadan devralma veya kaydırma yaparken bu tekniği kullanabilirsiniz.

## <a name="see-also"></a>Ayrıca Bkz.

* Simon Timms blogu: [görünümünüze arama verileri alma](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
