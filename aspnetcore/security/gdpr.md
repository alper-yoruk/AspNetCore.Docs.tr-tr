---
title: ASP.NET Core Genel Veri Koruma Yönetmeliği (GDPR) desteği
author: rick-anderson
description: ASP.NET Core Web uygulamasındaki GDPR uzantı noktalarına nasıl erişebileceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 0fef14388f83d8b4c708194954f2b75b2164b746
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404502"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="9a113-103">ASP.NET Core içinde AB Genel Veri Koruma Yönetmeliği (GDPR) desteği</span><span class="sxs-lookup"><span data-stu-id="9a113-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="9a113-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9a113-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9a113-105">ASP.NET Core, bazı [ab genel veri koruma yönetmeliği (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) gereksinimlerini karşılamaya yardımcı olmak Için API 'ler ve şablonlar sağlar:</span><span class="sxs-lookup"><span data-stu-id="9a113-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9a113-106">Proje şablonları, gizlilik ve tanımlama bilgisi kullanım ilkenize göre değiştireceğiniz uzantı noktalarını ve saplaması işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="9a113-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a113-107">*Sayfalar/gizlilik. cshtml* sayfası veya *Görünümler/Home/privacy. cshtml* görünümü sitenizin gizlilik ilkesini ayrıntılandırmakta olan bir sayfa sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="9a113-108">ASP.NET Core 3,0 şablonu tarafından oluşturulan bir uygulamada ASP.NET Core 2,2 şablonlarında bulunan gibi varsayılan tanımlama bilgisi onayı özelliğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="9a113-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="9a113-109">`using Microsoft.AspNetCore.Http`Using yönergeleri listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9a113-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="9a113-110">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) `Startup.ConfigureServices` Şunları yapmak için ve [Usemapolimapolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 'e ilişkin ıepolicyoptions ekleyin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9a113-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="9a113-111">Tanımlama bilgisi onayını kısmen *_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9a113-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="9a113-112">Bu projeye \* \_ pişirme ıeconsentpartial. cshtml\* dosyasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9a113-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="9a113-113">Tanımlama bilgisi onayı özelliği hakkında bilgi edinmek için bu makalenin ASP.NET Core 2,2 sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="9a113-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="9a113-114">Proje şablonları, gizlilik ve tanımlama bilgisi kullanım ilkenize göre değiştireceğiniz uzantı noktalarını ve saplaması işaretlemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="9a113-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a113-115">Bir tanımlama bilgisi onayı özelliği, kişisel bilgileri depolamak için kullanıcılarınıza izin vermenizi (ve izlemenizi) sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="9a113-116">Bir kullanıcı veri toplamaya ayrılmamışsa ve uygulamada [Checkconsentgerekliyse](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) `true` , temel olmayan tanımlama bilgileri tarayıcıya gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="9a113-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="9a113-117">Tanımlama bilgileri, temel olarak işaretlenebilir.</span><span class="sxs-lookup"><span data-stu-id="9a113-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="9a113-118">Kullanıcı onaylı değil ve izleme devre dışı bırakılmış olsa bile, temel tanımlama bilgileri tarayıcıya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a113-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="9a113-119">İzleme devre dışı bırakıldığında [TempData ve oturum tanımlama bilgileri](#tempdata) işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="9a113-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="9a113-120">[ Identity Yönet](#pd) sayfası, Kullanıcı verilerini indirmek ve silmek için bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="9a113-121">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) , ASP.NET Core 2,1 ŞABLONLARıNA eklenen GDPR uzantı noktalarının ve API 'lerin çoğunu test etmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="9a113-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="9a113-122">Test yönergeleri için [Benioku](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="9a113-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="9a113-123">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a113-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="9a113-124">Şablon tarafından üretilen kodda ASP.NET Core GDPR desteği</span><span class="sxs-lookup"><span data-stu-id="9a113-124">ASP.NET Core GDPR support in template-generated code</span></span>

Razor<span data-ttu-id="9a113-125">Proje Şablonlarıyla oluşturulan sayfalar ve MVC projeleri aşağıdaki GDPR desteğini içerir:</span><span class="sxs-lookup"><span data-stu-id="9a113-125"> Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="9a113-126">Bir sınıfında, [tanımlama, ıepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) ve [Useınte ıepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ayarlanır `Startup` .</span><span class="sxs-lookup"><span data-stu-id="9a113-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="9a113-127">\* \_ Pişirme ıeconsentpartial. cshtml\* [kısmi görünümü](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9a113-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="9a113-128">Bu dosyaya bir **kabul** düğmesi dahildir.</span><span class="sxs-lookup"><span data-stu-id="9a113-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="9a113-129">Kullanıcı **kabul et** düğmesine tıkladığında, tanımlama bilgilerini depolamak için onay sağlanır.</span><span class="sxs-lookup"><span data-stu-id="9a113-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="9a113-130">*Sayfalar/gizlilik. cshtml* sayfası veya *Görünümler/Home/privacy. cshtml* görünümü sitenizin gizlilik ilkesini ayrıntılandırmakta olan bir sayfa sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="9a113-131">\* \_ Pişirme ıeconsentpartial. cshtml\* dosyası gizlilik sayfasına bir bağlantı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9a113-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="9a113-132">Bireysel kullanıcı hesaplarıyla oluşturulan uygulamalarda, Yönet sayfası [kişisel kullanıcı verilerini](#pd)indirmek ve silmek için bağlantılar sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="9a113-133">Tanımlama, ıepolicyoptions ve Usepişiriepolicy</span><span class="sxs-lookup"><span data-stu-id="9a113-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="9a113-134">[Pişirme ıepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) şu şekilde başlatılır `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a113-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="9a113-135">[Usepişiriepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) şu şekilde çağrılır `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9a113-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="9a113-136">\_Pişirme ıeconsentpartial. cshtml kısmi görünümü</span><span class="sxs-lookup"><span data-stu-id="9a113-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="9a113-137">\* \_ Pişirme ıeconsentpartial. cshtml\* kısmi görünümü:</span><span class="sxs-lookup"><span data-stu-id="9a113-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="9a113-138">Bu kısmi:</span><span class="sxs-lookup"><span data-stu-id="9a113-138">This partial:</span></span>

* <span data-ttu-id="9a113-139">Kullanıcı için izleme durumunu alır.</span><span class="sxs-lookup"><span data-stu-id="9a113-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="9a113-140">Uygulama onay gerektirecek şekilde yapılandırıldıysa, tanımlama bilgilerinin izlenmemesi için kullanıcının onayı gerekir.</span><span class="sxs-lookup"><span data-stu-id="9a113-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="9a113-141">İzin gerekliyse, tanımlama bilgisi onay paneli \* \_ Düzen. cshtml\* dosyası tarafından oluşturulan gezinti çubuğunun üstünde düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="9a113-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="9a113-142">`<p>`Gizlilik ve tanımlama bilgisi kullanım ilkenizi özetlemek için BIR HTML öğesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9a113-143">Gizlilik sayfasına bir bağlantı sağlar veya sitenizin gizlilik ilkesini ayrıntılandırınızın ne olduğunu görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a113-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="9a113-144">Temel tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="9a113-144">Essential cookies</span></span>

<span data-ttu-id="9a113-145">Tanımlama bilgilerini depolamak için izin sağlanmazsa, tarayıcıya yalnızca temel olarak işaretlenen tanımlama bilgileri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="9a113-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="9a113-146">Aşağıdaki kod, bir tanımlama bilgisini temel yapar:</span><span class="sxs-lookup"><span data-stu-id="9a113-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="9a113-147">TempData sağlayıcısı ve oturum durumu tanımlama bilgileri gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="9a113-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="9a113-148">[TempData sağlayıcısı](xref:fundamentals/app-state#tempdata) tanımlama bilgisi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="9a113-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="9a113-149">İzleme devre dışıysa, TempData sağlayıcısı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="9a113-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="9a113-150">İzleme devre dışı bırakıldığında TempData sağlayıcısını etkinleştirmek için, TempData tanımlama bilgisini ' de gerekli olarak işaretleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9a113-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="9a113-151">[Oturum durumu](xref:fundamentals/app-state) tanımlama bilgileri gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="9a113-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="9a113-152">İzleme devre dışı olduğunda oturum durumu işlevsel değil.</span><span class="sxs-lookup"><span data-stu-id="9a113-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="9a113-153">Aşağıdaki kod, oturum tanımlama bilgilerini temel yapar:</span><span class="sxs-lookup"><span data-stu-id="9a113-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="9a113-154">Kişisel veriler</span><span class="sxs-lookup"><span data-stu-id="9a113-154">Personal data</span></span>

<span data-ttu-id="9a113-155">Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core uygulamalar, kişisel verileri indirmek ve silmek için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="9a113-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="9a113-156">Kullanıcı adını seçin ve **kişisel veriler**' i seçin:</span><span class="sxs-lookup"><span data-stu-id="9a113-156">Select the user name and then select **Personal data**:</span></span>

![Kişisel verileri yönetme sayfası](gdpr/_static/pd.png)

<span data-ttu-id="9a113-158">Notlar:</span><span class="sxs-lookup"><span data-stu-id="9a113-158">Notes:</span></span>

* <span data-ttu-id="9a113-159">Kodu oluşturmak için `Account/Manage` bkz. [ Identity Yapı iskelesi ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="9a113-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="9a113-160">**Silme** ve **indirme** bağlantıları yalnızca varsayılan kimlik verilerinde işlem görür.</span><span class="sxs-lookup"><span data-stu-id="9a113-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="9a113-161">Özel Kullanıcı verileri oluşturan uygulamalar, özel kullanıcı verilerini silmek/indirmek için genişletilmelidir.</span><span class="sxs-lookup"><span data-stu-id="9a113-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="9a113-162">Daha fazla bilgi için bkz. [Özel Kullanıcı verilerini Identity ekleme, indirme ve silme ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="9a113-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="9a113-163">Veritabanı tablosunda depolanan kullanıcının kaydedilmiş belirteçleri, Identity `AspNetUserTokens` Kullanıcı [yabancı anahtar](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)nedeniyle basamaklı silme davranışı aracılığıyla silindiğinde silinir.</span><span class="sxs-lookup"><span data-stu-id="9a113-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="9a113-164">Facebook ve Google gibi [dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index), tanımlama bilgisi İlkesi kabul edilmeden önce kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="9a113-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="9a113-165">Bekleme sırasında şifreleme</span><span class="sxs-lookup"><span data-stu-id="9a113-165">Encryption at rest</span></span>

<span data-ttu-id="9a113-166">Bazı veritabanları ve depolama mekanizmaları, bekleyen şifreleme için izin verir.</span><span class="sxs-lookup"><span data-stu-id="9a113-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="9a113-167">Bekleyen şifreleme:</span><span class="sxs-lookup"><span data-stu-id="9a113-167">Encryption at rest:</span></span>

* <span data-ttu-id="9a113-168">Depolanan verileri otomatik olarak şifreler.</span><span class="sxs-lookup"><span data-stu-id="9a113-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="9a113-169">Veriye erişen yazılımlar için yapılandırma, programlama veya diğer çalışmasız şifreler.</span><span class="sxs-lookup"><span data-stu-id="9a113-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="9a113-170">En kolay ve en güvenli seçenektir.</span><span class="sxs-lookup"><span data-stu-id="9a113-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="9a113-171">Veritabanının anahtarları ve şifrelemeyi yönetmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="9a113-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="9a113-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9a113-172">For example:</span></span>

* <span data-ttu-id="9a113-173">Microsoft SQL ve Azure SQL, [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption) (tde) sağlar.</span><span class="sxs-lookup"><span data-stu-id="9a113-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="9a113-174">SQL Azure, varsayılan olarak veritabanını şifreler</span><span class="sxs-lookup"><span data-stu-id="9a113-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="9a113-175">[Azure Blobları, dosyalar, tablo ve kuyruk depolaması varsayılan olarak şifrelenir](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="9a113-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="9a113-176">Bekleyen şifreleme sağlamayan veritabanları için aynı korumayı sağlamak üzere disk şifrelemeyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9a113-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="9a113-177">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9a113-177">For example:</span></span>

* [<span data-ttu-id="9a113-178">Windows Server için BitLocker</span><span class="sxs-lookup"><span data-stu-id="9a113-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="9a113-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="9a113-179">Linux:</span></span>
  * [<span data-ttu-id="9a113-180">Eccryptotfs</span><span class="sxs-lookup"><span data-stu-id="9a113-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="9a113-181">[Şifreleme](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="9a113-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a113-182">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9a113-182">Additional resources</span></span>

* [<span data-ttu-id="9a113-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="9a113-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="9a113-184">GDPR-ASP.NET Core Izin Iptali düğmesi ekleme</span><span class="sxs-lookup"><span data-stu-id="9a113-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
