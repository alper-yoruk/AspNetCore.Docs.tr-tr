---
title: ASP.NET Core uygulamasının Ayrıntılarını ve Silme yöntemlerini inceleyin
author: rick-anderson
description: Temel bir ASP.NET Core MVC uygulamasında Ayrıntılar denetleyici yöntemi ve görünümü hakkında bilgi edinin.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662913"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>ASP.NET Core uygulamasının Ayrıntılarını ve Silme yöntemlerini inceleyin

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Film denetleyicisini açın `Details` ve yöntemi inceleyin:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Bu eylem yöntemini oluşturan MVC iskele altyapısı, yöntemi çağıran bir HTTP isteğini gösteren bir yorum ekler. Bu durumda, üç URL segmenti, `Movies` denetleyici, `Details` yöntem ve bir değer `id` içeren bir GET isteğidir. Bu kesimlerin *Startup.cs*olarak tanımlandığını hatırlayın.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF, `FirstOrDefaultAsync` yöntemi kullanarak veri aramayı kolaylaştırır. Yöntemde yerleşik önemli bir güvenlik özelliği, kodun arama yönteminin bir film bulduğunu doğrular. Örneğin, bir bilgisayar korsanı, bağlantılar `http://localhost:{PORT}/Movies/Details/1` tarafından oluşturulan URL'yi benzer `http://localhost:{PORT}/Movies/Details/12345` bir şeye (veya gerçek bir filmi temsil etmeyen başka bir değere) değiştirerek siteye hatalar getirebilir. Null film için kontrol etmediyseniz, uygulama bir özel durum oluşturur.

Yöntemleri `Delete` ve `DeleteConfirmed` yöntemleriince.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Yöntemin `HTTP GET Delete` belirtilen filmi silmediğini, silme işlemini (HttpPost) gönderebileceğiniz bir film görünümünü döndürtüğünü unutmayın. GET isteğine yanıt olarak bir silme işlemi gerçekleştirmek (veya bu konuda, bir düzenleme işlemi gerçekleştirmek, işlem oluşturmak veya verileri değiştiren başka bir işlem) bir güvenlik açığı açar.

Verileri `[HttpPost]` silen yöntem, `DeleteConfirmed` HTTP POST yöntemine benzersiz bir imza veya ad vermek için adlandırılır. İki yöntem imzaları aşağıda gösterilmiştir:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Ortak dil çalışma süresi (CLR) benzersiz bir parametre imzası (aynı yöntem adı ama farklı parametre listesi) için aşırı yüklenen yöntemler gerektirir. Ancak, burada iki `Delete` yöntem gerekir - get için bir ve POST için bir - her ikisi de aynı parametre imzaya sahip. (Her ikisi de bir parametre olarak tek bir tamsayı kabul etmek gerekir.)

Bu soruna iki yaklaşım vardır, bir yöntemleri farklı adlar vermektir. İskele mekanizması nın önceki örnekte yaptığı da buydu. Ancak, bu küçük bir sorun ortaya çıkarır: ASP.NET eşler bir URL'nin kesimlerini adlarına göre eylem yöntemleriyle eşler ve bir yöntemi yeniden adlarsanız, yönlendirme normalde bu yöntemi bulamaz. Çözüm, `ActionName("Delete")` `DeleteConfirmed` yönteme öznitelik eklemek için örnekte gördüğünüz şeydir. Bu öznitelik, yönlendirme sistemi için eşleme gerçekleştirir, böylece bir POST isteği için `DeleteConfirmed` /Delete/ içeren bir URL yöntemi bulur.

Aynı adlara ve imzalara sahip yöntemler için yapılan bir diğer yaygın çözüm de, POST yönteminin imzasını yapay olarak ekstra (kullanılmayan) bir parametre içerecek şekilde değiştirmektir. `notUsed` Parametreyi eklediğimizde bir önceki yazıda yaptığımız buydu. Burada `[HttpPost] Delete` yöntem için aynı şeyi yapabilirsiniz:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Azure’da Yayımlama

Azure'a dağıtım hakkında daha fazla bilgi için [Bkz.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

> [!div class="step-by-step"]
> [Önceki](validation.md)
