---
title: ASP.NET Core görünümlere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC görünümlerine bağımlılık ekleme işlemini nasıl desteklediğini öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/dependency-injection
ms.openlocfilehash: aee4152bed50576f087862142e7ce9f261c7da19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775459"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>ASP.NET Core görünümlere bağımlılık ekleme

[Steve Smith](https://ardalis.com/) tarafından

ASP.NET Core, görünümlere [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini destekler. Bu, yalnızca görünüm öğelerini doldurmak için gereken yerelleştirme veya veriler gibi görünüme özgü hizmetler için yararlı olabilir. Denetleyicileriniz ve görünümleriniz arasındaki [kaygıların ayrılmasını](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) korumayı denemelisiniz. Görünümlerinizin görüntüleyeceği verilerin çoğu denetleyiciden geçirilmelidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="configuration-injection"></a>Yapılandırma ekleme

*appSettings. JSON* değerleri doğrudan bir görünüme eklenebilir.

Bir *appSettings. JSON* dosyası örneği:

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

İçin `@inject`sözdizimi:`@inject <type> <name>`

Şunu kullanarak `@inject`bir örnek:

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a>Hizmet ekleme

Bir hizmet, `@inject` yönergesini kullanarak bir görünüme eklenebilir. Görünüme özellik ekleme ve `@inject` dı kullanarak özelliği doldurma gibi düşünebilirsiniz.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

Bu görünüm, `ToDoItem` örneklerin listesini, genel istatistikleri gösteren bir Özet ile birlikte görüntüler. Özet, eklenen `StatisticsService`öğesinden doldurulur. Bu hizmet, `ConfigureServices` *Startup.cs*içinde bağımlılık ekleme için kaydedilir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

, `StatisticsService` Bir depo aracılığıyla eriştiği `ToDoItem` örnek kümesi üzerinde bazı hesaplamalar gerçekleştirir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

Örnek depo, bellek içi bir koleksiyon kullanır. Yukarıda gösterilen uygulama (bellekteki tüm veriler üzerinde çalışır), büyük ve uzaktan erişilen veri kümeleri için önerilmez.

Örnek, görüntüleme ile bağlantılı modeldeki verileri ve görünüme eklenen hizmeti görüntüler:

![Toplam öğe, tamamlanan öğe, ortalama öncelik ve tamamlanma düzeylerine sahip bir görev listesi ve tamamlanma belirten Boole değerlerini görüntülemek için.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Arama verileri dolduruluyor

Görünüm ekleme, açılan listeler gibi kullanıcı arabirimi öğelerindeki seçenekleri doldurmak için yararlı olabilir. Cinsiyet, eyalet ve diğer tercihlerin belirtilmesine yönelik seçenekleri içeren bir kullanıcı profili formu düşünün. Standart bir MVC yaklaşımı kullanarak bu tür bir formu işlemek, denetleyicinin Bu seçenek kümelerinin her biri için veri erişim Hizmetleri istemesi ve sonra bir modeli ya `ViewBag` da bağlanacak her bir seçenek kümesiyle doldurmasını gerektirir.

Alternatif yaklaşım, seçenekleri almak için Hizmetleri doğrudan görünüme çıkarır. Bu, denetleyicinin gerektirdiği kod miktarını en aza indirir ve bu görünüm öğesi oluşturma mantığını görünümün içine taşır. Bir profil düzenlemesi formunu görüntülemeye yönelik denetleyici eylemi yalnızca profil örneği formunu geçirmeniz gerekir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Bu tercihleri güncelleştirmek için kullanılan HTML formu, özelliklerin üç yanındaki açılan listeleri içerir:

![Ad, cinsiyet, eyalet ve sık kullanılan renk girişine izin veren bir formla profil görünümünü güncelleştirin.](dependency-injection/_static/updateprofile.png)

Bu listeler, görünüme eklenen bir hizmet tarafından doldurulur:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

, `ProfileOptionsService` Yalnızca bu form için gereken verileri sağlamak üzere tasarlanan bir UI düzeyi hizmettir:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> Uygulamasına bağımlılık ekleme yoluyla istek yaptığınız türleri kaydetmeyi unutmayın `Startup.ConfigureServices`. Kaydedilmemiş bir tür, hizmet sağlayıcısı, [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice)aracılığıyla dahili olarak sorgulandığından çalışma zamanında bir özel durum oluşturur.

## <a name="overriding-services"></a>Hizmetleri geçersiz kılma

Bu teknik, ekleme yeni hizmetlere ek olarak, bir sayfada önceden eklenen Hizmetleri geçersiz kılmak için de kullanılabilir. Aşağıdaki şekilde, ilk örnekte kullanılan sayfada bulunan tüm alanlar gösterilmektedir:

![HTML, bileşen, StatsService ve URL alanları için belirlenmiş bir @ symbol üzerinde IntelliSense bağlamsal menüsü](dependency-injection/_static/razor-fields.png)

Gördüğünüz gibi, `Html`varsayılan alanlar, `Component`, ve ( `Url` `StatsService` eklediğimiz), ve ' i içerir. Örneğin varsayılan HTML yardımcılarını kendi kendinize değiştirmek istiyorsanız, şunu kullanarak `@inject`kolayca yapabilirsiniz:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Mevcut hizmetleri genişletmek istiyorsanız, bu tekniği kullanarak var olan uygulamadan devralma veya kaydırma yaparken bu tekniği kullanabilirsiniz.

## <a name="see-also"></a>Ayrıca Bkz.

* Simon Timms blogu: [görünümünüze arama verileri alma](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
