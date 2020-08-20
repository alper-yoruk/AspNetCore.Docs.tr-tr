---
title: 10. kısım, ASP.NET Core uygulamasının ayrıntılarını ve silme yöntemlerini inceleyin
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin Bölüm 10 ' dur.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: b25113f8e2132c06e06d53a25e71ed2a42deb00f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629710"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>10. kısım, ASP.NET Core uygulamasının ayrıntılarını ve silme yöntemlerini inceleyin

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Film denetleyicisini açın ve `Details` yöntemi inceleyin:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Bu eylem yöntemini oluşturan MVC yapı iskelesi altyapısı, yöntemi çağıran bir HTTP isteğini gösteren bir açıklama ekler. Bu durumda, üç URL segmentine, `Movies` denetleyiciye, `Details` yönteme ve bir değere sahıp bir get isteği olur `id` . Bu kesimleri geri çağır *Startup.cs*içinde tanımlanmıştır.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF, yöntemi kullanarak verileri aramanızı kolaylaştırır `FirstOrDefaultAsync` . Yöntemi içinde yerleşik olarak bulunan önemli bir güvenlik özelliği, kodun, onunla herhangi bir şey yapmayı denemeden önce arama yönteminin bir filmi buldığını doğrulamasından kaynaklanmaktadır. Örneğin, bir korsan bağlantıları tarafından oluşturulan URL 'yi `http://localhost:{PORT}/Movies/Details/1`  `http://localhost:{PORT}/Movies/Details/12345` (veya gerçek bir filmi temsil eden başka bir değer) gibi bir konuma değiştirerek siteye hata verebilir. Null bir filmi denetmediyseniz, uygulama bir özel durum oluşturur.

`Delete`Ve yöntemlerini inceleyin `DeleteConfirmed` .

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

`HTTP GET Delete`Yöntemin belirtilen filmi silmediğini unutmayın. Bu, silme işlemini gönderebileceğiniz (HttpPost) filmin bir görünümünü döndürür. Bir GET isteğine yanıt olarak silme işlemi gerçekleştirme (veya bu konuyla ilgili olarak, düzenleme işlemi gerçekleştirme, oluşturma işlemi yapma veya verileri değiştiren başka bir işlem) bir güvenlik deliği açılır.

`[HttpPost]`Verileri silen yöntemi, `DeleteConfirmed` http post yöntemine benzersiz bir imza veya ad vermek için olarak adlandırılır. İki yöntem imzası aşağıda gösterilmiştir:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Ortak dil çalışma zamanı (CLR), aşırı yüklenmiş yöntemlerin benzersiz bir parametre imzasına sahip olmasını gerektirir (aynı yöntem adı ancak farklı parametre listesi). Ancak, `Delete` her ikisi de aynı parametre imzasına sahip olan--get için bir tane olmak üzere iki yöntem gereklidir. (Her ikisi de parametre olarak tek bir tamsayıyı kabul etmelidir.)

Bu sorunun iki yaklaşımı vardır, biri yöntemlere farklı adlar vermektir. Bu, önceki örnekte bulunan yapı iskelesi mekanizmasına göre yapılır. Ancak, bu küçük bir sorun ortaya çıkarır: ASP.NET bir URL 'nin segmentlerini ada göre eylem yöntemlerine eşler ve bir yöntemi yeniden adlandırırsanız, yönlendirme normalde bu yöntemi bulamaz. Çözüm, örnekte gördüğünüz şeydir. Bu, `ActionName("Delete")` yöntemine özniteliği eklemektir `DeleteConfirmed` . Bu öznitelik, yönlendirme sistemi için eşleme gerçekleştirerek, bir POST isteği için/Delete/içeren bir URL `DeleteConfirmed` yöntemi bulur.

Özdeş adlara ve imzalara sahip yöntemler için bir diğer yaygın çalışma yapay, POST yönteminin imzasını bir ek (kullanılmamış) parametre içerecek şekilde değiştirecek. Bu, parametreyi eklediğimiz sırada önceki bir gönderimiz tarafından yaptığımız `notUsed` . Yöntemi için burada aynı şeyi yapabilirsiniz `[HttpPost] Delete` :

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Azure’da Yayımlama

Azure 'a dağıtma hakkında bilgi için bkz. [öğretici: Azure App Service .NET Core ve SQL veritabanı Web uygulaması oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Önceki](validation.md)
