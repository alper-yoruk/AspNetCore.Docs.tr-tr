---
title: ASP.NET Core 'da açık yeniden yönlendirme saldırılarını önleme
author: ardalis
description: ASP.NET Core uygulamasında açık yeniden yönlendirme saldırılarının nasıl önleneceği gösterilmektedir
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: eb18c599d84fd08ffe97867b67a837303af188db
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408155"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core 'da açık yeniden yönlendirme saldırılarını önleme

QueryString veya form verileri gibi istek aracılığıyla belirtilen bir URL 'ye yeniden yönlendiren bir Web uygulaması, kullanıcıları harici, kötü amaçlı bir URL 'ye yönlendirmek için ile oynanmış olabilir. Bu değişikliklere açık yeniden yönlendirme saldırısı denir.

Uygulamanızın mantığı belirtilen bir URL 'ye yeniden yönlendirdiğinde, yeniden yönlendirme URL 'sinin değiştirilmediğini doğrulamanız gerekir. ASP.NET Core, uygulamaların yeniden yönlendirme (açık yeniden yönlendirme olarak da bilinir) saldırıları korumasına yardımcı olacak yerleşik işlevlere sahiptir.

## <a name="what-is-an-open-redirect-attack"></a>Açık yeniden yönlendirme saldırısı nedir?

Web uygulamaları, kimlik doğrulaması gerektiren kaynaklara erişirken kullanıcıları sıklıkla bir oturum açma sayfasına yönlendirir. Yeniden yönlendirme tipik olarak bir `returnUrl` QueryString parametresi içerir, böylece Kullanıcı başarıyla oturum açtıktan sonra kullanıcının Istenen URL 'ye dönebilmesini sağlar. Kullanıcı kimlik doğrulamasından geçtikten sonra, ilk olarak istediği URL 'ye yeniden yönlendirilir.

Hedef URL isteğin QueryString öğesinde belirtildiğinden, kötü niyetli bir Kullanıcı QueryString ile oynayabilir. Değiştirilen bir QueryString, sitenin kullanıcıyı harici, kötü amaçlı bir siteye yönlendirmesine izin verebilir. Bu teknik bir açık yeniden yönlendirme (veya yeniden yönlendirme) saldırısı olarak adlandırılır.

### <a name="an-example-attack"></a>Örnek saldırı

Kötü niyetli bir Kullanıcı, kötü niyetli kullanıcıların bir kullanıcının kimlik bilgilerine veya gizli bilgilere erişmesine izin veren bir saldırı geliştirebilir. Kötü amaçlı Kullanıcı, saldırının başlaması için kullanıcıyı, `returnUrl` URL 'ye bir QueryString değeri eklenerek sitenizin oturum açma sayfasının bağlantısını tıklamayı ikna eder. Örneğin, ' de bir `contoso.com` oturum açma sayfası içeren bir uygulama düşünün `http://contoso.com/Account/LogOn?returnUrl=/Home/About` . Saldırı aşağıdaki adımları izler:

1. Kullanıcı kötü amaçlı bir bağlantıyı tıklatır `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (ıkıncı URL "contoso**1**. com", "contoso.com" değil).
2. Kullanıcı başarıyla oturum açar.
3. Kullanıcı (site tarafından) öğesine `http://contoso1.com/Account/LogOn` (tam olarak gerçek site gibi görünen kötü amaçlı bir site) yönlendirilir.
4. Kullanıcı yeniden oturum açar (kötü amaçlı site kimlik bilgileri verir) ve gerçek siteye yeniden yönlendirilir.

Kullanıcı, ilk oturum açma girişiminin başarısız olduğunu ve ikinci denemesinin başarılı olduğunu düşündüğü bir durum olabilir. Kullanıcı kimlik bilgilerinin güvenliğinin aşıldığına ilişkin büyük olasılıkla Kullanıcı farkında değildir.

![Yeniden yönlendirme saldırı Işlemini aç](preventing-open-redirects/_static/open-redirection-attack-process.png)

Bazı siteler, oturum açma sayfalarının yanı sıra yeniden yönlendirme sayfaları veya uç noktaları sağlar. Uygulamanızın açık bir yeniden yönlendirmeye sahip bir sayfası olduğunu düşünün `/Home/Redirect` . Bir saldırgan, bir e-postada bağlantı oluşturabilir, örneğin `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` . Tipik bir Kullanıcı URL 'ye bakar ve site adınızla başlayıp başlamadığını görür. Bunun için bağlantıya tıklamaları gerekir. Açık yeniden yönlendirme daha sonra kullanıcıyı sizinkilerle aynı olan kimlik avı sitesine gönderir ve Kullanıcı büyük olasılıkla sitenizin ne olduğuna inandıkları anlamına gelir.

## <a name="protecting-against-open-redirect-attacks"></a>Açık yeniden yönlendirme saldırılarına karşı koruma

Web uygulamaları geliştirirken, Kullanıcı tarafından sağlanmış tüm verileri güvenilir değil olarak değerlendirin. Uygulamanızın, URL 'nin içeriğine göre kullanıcıyı yönlendiren işlevselliği varsa, bu yeniden yönlerinizin yalnızca uygulamanızda (veya QueryString 'de sağlanabilecek URL 'ler değil, bilinen bir URL 'ye değil) yerel olarak yapıldığından emin olun.

### <a name="localredirect"></a>LocalRedirect

`LocalRedirect`Temel sınıftan yardımcı yöntemini kullanın `Controller` :

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect`Yerel olmayan bir URL belirtilirse, bir özel durum oluşturur. Aksi takdirde, tıpkı yöntemi gibi davranır `Redirect` .

### <a name="islocalurl"></a>IsLocalUrl 'Si

Yeniden yönlendirmeden önce URL 'Leri test etmek için [ıslocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) metodunu kullanın:

Aşağıdaki örnek, yeniden yönlendirmeden önce bir URL 'nin yerel olup olmadığını denetlemeyi gösterir.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

`IsLocalUrl`Yöntemi, kullanıcıların farkında olmadan kötü amaçlı bir siteye yönlendirilmesini önler. Yerel bir URL 'yi beklediğiniz bir durumda, yerel olmayan bir URL sağlandığında sağlanan URL 'nin ayrıntılarını günlüğe kaydedebilirsiniz. Günlüğe kaydetme yeniden yönlendirme URL 'Leri, yeniden yönlendirme saldırılarını tanılamaya yardımcı olabilir.
