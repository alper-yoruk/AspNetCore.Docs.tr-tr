---
title: ASP.NET Core 'da açık yeniden yönlendirme saldırılarını önleme
author: ardalis
description: ASP.NET Core uygulamasında açık yeniden yönlendirme saldırılarının nasıl önleneceği gösterilmektedir
ms.author: riande
ms.date: 07/07/2017
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058395"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="ffa94-103">ASP.NET Core 'da açık yeniden yönlendirme saldırılarını önleme</span><span class="sxs-lookup"><span data-stu-id="ffa94-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="ffa94-104">QueryString veya form verileri gibi istek aracılığıyla belirtilen bir URL 'ye yeniden yönlendiren bir Web uygulaması, kullanıcıları harici, kötü amaçlı bir URL 'ye yönlendirmek için ile oynanmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="ffa94-105">Bu değişikliklere açık yeniden yönlendirme saldırısı denir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="ffa94-106">Uygulamanızın mantığı belirtilen bir URL 'ye yeniden yönlendirdiğinde, yeniden yönlendirme URL 'sinin değiştirilmediğini doğrulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="ffa94-107">ASP.NET Core, uygulamaların yeniden yönlendirme (açık yeniden yönlendirme olarak da bilinir) saldırıları korumasına yardımcı olacak yerleşik işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="ffa94-108">Açık yeniden yönlendirme saldırısı nedir?</span><span class="sxs-lookup"><span data-stu-id="ffa94-108">What is an open redirect attack?</span></span>

<span data-ttu-id="ffa94-109">Web uygulamaları, kimlik doğrulaması gerektiren kaynaklara erişirken kullanıcıları sıklıkla bir oturum açma sayfasına yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="ffa94-110">Yeniden yönlendirme tipik olarak bir `returnUrl` QueryString parametresi içerir, böylece Kullanıcı başarıyla oturum açtıktan sonra kullanıcının Istenen URL 'ye dönebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ffa94-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="ffa94-111">Kullanıcı kimlik doğrulamasından geçtikten sonra, ilk olarak istediği URL 'ye yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="ffa94-112">Hedef URL isteğin QueryString öğesinde belirtildiğinden, kötü niyetli bir Kullanıcı QueryString ile oynayabilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="ffa94-113">Değiştirilen bir QueryString, sitenin kullanıcıyı harici, kötü amaçlı bir siteye yönlendirmesine izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="ffa94-114">Bu teknik bir açık yeniden yönlendirme (veya yeniden yönlendirme) saldırısı olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ffa94-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="ffa94-115">Örnek saldırı</span><span class="sxs-lookup"><span data-stu-id="ffa94-115">An example attack</span></span>

<span data-ttu-id="ffa94-116">Kötü niyetli bir Kullanıcı, kötü niyetli kullanıcıların bir kullanıcının kimlik bilgilerine veya gizli bilgilere erişmesine izin veren bir saldırı geliştirebilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="ffa94-117">Kötü amaçlı Kullanıcı, saldırının başlaması için kullanıcıyı, `returnUrl` URL 'ye bir QueryString değeri eklenerek sitenizin oturum açma sayfasının bağlantısını tıklamayı ikna eder.</span><span class="sxs-lookup"><span data-stu-id="ffa94-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="ffa94-118">Örneğin, ' de bir `contoso.com` oturum açma sayfası içeren bir uygulama düşünün `http://contoso.com/Account/LogOn?returnUrl=/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="ffa94-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="ffa94-119">Saldırı aşağıdaki adımları izler:</span><span class="sxs-lookup"><span data-stu-id="ffa94-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="ffa94-120">Kullanıcı kötü amaçlı bir bağlantıyı tıklatır `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (ıkıncı URL "contoso **1** . com", "contoso.com" değil).</span><span class="sxs-lookup"><span data-stu-id="ffa94-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso **1** .com", not "contoso.com").</span></span>
2. <span data-ttu-id="ffa94-121">Kullanıcı başarıyla oturum açar.</span><span class="sxs-lookup"><span data-stu-id="ffa94-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="ffa94-122">Kullanıcı (site tarafından) öğesine `http://contoso1.com/Account/LogOn` (tam olarak gerçek site gibi görünen kötü amaçlı bir site) yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="ffa94-123">Kullanıcı yeniden oturum açar (kötü amaçlı site kimlik bilgileri verir) ve gerçek siteye yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="ffa94-124">Kullanıcı, ilk oturum açma girişiminin başarısız olduğunu ve ikinci denemesinin başarılı olduğunu düşündüğü bir durum olabilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="ffa94-125">Kullanıcı kimlik bilgilerinin güvenliğinin aşıldığına ilişkin büyük olasılıkla Kullanıcı farkında değildir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Yeniden yönlendirme saldırı Işlemini aç](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="ffa94-127">Bazı siteler, oturum açma sayfalarının yanı sıra yeniden yönlendirme sayfaları veya uç noktaları sağlar.</span><span class="sxs-lookup"><span data-stu-id="ffa94-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="ffa94-128">Uygulamanızın açık bir yeniden yönlendirmeye sahip bir sayfası olduğunu düşünün `/Home/Redirect` .</span><span class="sxs-lookup"><span data-stu-id="ffa94-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="ffa94-129">Bir saldırgan, bir e-postada bağlantı oluşturabilir, örneğin `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="ffa94-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="ffa94-130">Tipik bir Kullanıcı URL 'ye bakar ve site adınızla başlayıp başlamadığını görür.</span><span class="sxs-lookup"><span data-stu-id="ffa94-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="ffa94-131">Bunun için bağlantıya tıklamaları gerekir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="ffa94-132">Açık yeniden yönlendirme daha sonra kullanıcıyı sizinkilerle aynı olan kimlik avı sitesine gönderir ve Kullanıcı büyük olasılıkla sitenizin ne olduğuna inandıkları anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="ffa94-133">Açık yeniden yönlendirme saldırılarına karşı koruma</span><span class="sxs-lookup"><span data-stu-id="ffa94-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="ffa94-134">Web uygulamaları geliştirirken, Kullanıcı tarafından sağlanmış tüm verileri güvenilir değil olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="ffa94-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="ffa94-135">Uygulamanızın, URL 'nin içeriğine göre kullanıcıyı yönlendiren işlevselliği varsa, bu yeniden yönlerinizin yalnızca uygulamanızda (veya QueryString 'de sağlanabilecek URL 'ler değil, bilinen bir URL 'ye değil) yerel olarak yapıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="ffa94-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="ffa94-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="ffa94-136">LocalRedirect</span></span>

<span data-ttu-id="ffa94-137">`LocalRedirect`Temel sınıftan yardımcı yöntemini kullanın `Controller` :</span><span class="sxs-lookup"><span data-stu-id="ffa94-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="ffa94-138">`LocalRedirect` Yerel olmayan bir URL belirtilirse, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ffa94-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="ffa94-139">Aksi takdirde, tıpkı yöntemi gibi davranır `Redirect` .</span><span class="sxs-lookup"><span data-stu-id="ffa94-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="ffa94-140">IsLocalUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="ffa94-140">IsLocalUrl</span></span>

<span data-ttu-id="ffa94-141">Yeniden yönlendirmeden önce URL 'Leri test etmek için [ıslocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) metodunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="ffa94-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="ffa94-142">Aşağıdaki örnek, yeniden yönlendirmeden önce bir URL 'nin yerel olup olmadığını denetlemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="ffa94-143">`IsLocalUrl`Yöntemi, kullanıcıların farkında olmadan kötü amaçlı bir siteye yönlendirilmesini önler.</span><span class="sxs-lookup"><span data-stu-id="ffa94-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="ffa94-144">Yerel bir URL 'yi beklediğiniz bir durumda, yerel olmayan bir URL sağlandığında sağlanan URL 'nin ayrıntılarını günlüğe kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ffa94-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="ffa94-145">Günlüğe kaydetme yeniden yönlendirme URL 'Leri, yeniden yönlendirme saldırılarını tanılamaya yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ffa94-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
