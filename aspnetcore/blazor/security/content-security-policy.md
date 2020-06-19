---
title: ASP.NET Core için bir Içerik Güvenlik Ilkesi zorlaBlazor
author: guardrex
description: BlazorSiteler arası komut dosyası (XSS) saldırılarına karşı korumaya yardımcı olmak için ASP.NET Core uygulamalarla bir Içerik güvenlik ilkesi (CSP) kullanmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 63359260766846ccf14ececfae82bf02cc598cf9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103786"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="2364c-103">ASP.NET Core için bir Içerik Güvenlik Ilkesi zorlaBlazor</span><span class="sxs-lookup"><span data-stu-id="2364c-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2364c-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="2364c-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2364c-105">[Siteler arası betik oluşturma (XSS)](xref:security/cross-site-scripting) , bir saldırganın bir veya daha fazla kötü amaçlı istemci tarafı komut dosyasını uygulamanın işlenmiş içeriğine yerleştirdiği bir güvenlik açığıdır.</span><span class="sxs-lookup"><span data-stu-id="2364c-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="2364c-106">Bir Içerik Güvenlik Ilkesi (CSP), geçerli bir tarayıcıya bildirerek XSS saldırılarına karşı korunmaya yardımcı olur:</span><span class="sxs-lookup"><span data-stu-id="2364c-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="2364c-107">Betikler, stil sayfaları ve görüntüler dahil olmak üzere yüklenen içerik kaynakları.</span><span class="sxs-lookup"><span data-stu-id="2364c-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="2364c-108">Formun izin verilen URL hedeflerini belirten bir sayfa tarafından gerçekleştirilen eylemler.</span><span class="sxs-lookup"><span data-stu-id="2364c-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="2364c-109">Yüklenebilen eklentiler.</span><span class="sxs-lookup"><span data-stu-id="2364c-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="2364c-110">Bir CSP 'yi bir uygulamaya uygulamak için, geliştirici bir veya daha fazla *directives* `Content-Security-Policy` üst bilgi veya ETIKET içinde çeşitli CSP içerik güvenliği yönergeleri belirler `<meta>` .</span><span class="sxs-lookup"><span data-stu-id="2364c-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="2364c-111">İlkeler, bir sayfa yüklenirken tarayıcı tarafından değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2364c-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="2364c-112">Tarayıcı, sayfanın kaynaklarını inceler ve içerik güvenliği yönergelerinin gereksinimlerini karşılayıp karşılamadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="2364c-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="2364c-113">Bir kaynak için ilke yönergeleri karşılanmazsa, tarayıcı kaynağı yüklemez.</span><span class="sxs-lookup"><span data-stu-id="2364c-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="2364c-114">Örneğin, üçüncü taraf betiklerine izin veren bir ilkeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="2364c-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="2364c-115">Bir sayfa, `<script>` özniteliğinde üçüncü taraf kaynağına sahip bir etiket içerdiğinde `src` , tarayıcı betiğin yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="2364c-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="2364c-116">CSP, Chrome, Edge, Firefox, Opera ve Safari dahil olmak üzere çoğu modern masaüstü ve mobil tarayıcılarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2364c-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="2364c-117">CSP, uygulamalar için önerilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="2364c-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="2364c-118">İlke yönergeleri</span><span class="sxs-lookup"><span data-stu-id="2364c-118">Policy directives</span></span>

<span data-ttu-id="2364c-119">En düşük düzeyde, uygulamalar için aşağıdaki yönergeleri ve kaynakları belirtin Blazor .</span><span class="sxs-lookup"><span data-stu-id="2364c-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="2364c-120">Gerektiğinde ek yönergeler ve kaynaklar ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2364c-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="2364c-121">Aşağıdaki yönergeler, bu makalenin [Ilkeyi Uygula](#apply-the-policy) bölümünde, burada Blazor webassembly ve Server için güvenlik ilkelerinin sağlandığı durumlarda kullanılır Blazor :</span><span class="sxs-lookup"><span data-stu-id="2364c-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="2364c-122">[Base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): bir sayfanın etiketinin URL 'lerini kısıtlar `<base>` .</span><span class="sxs-lookup"><span data-stu-id="2364c-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="2364c-123">`self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="2364c-124">[Engelle-tümü-karışık-içerik](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): karışık http ve HTTPS içeriğini yüklemeyi engeller.</span><span class="sxs-lookup"><span data-stu-id="2364c-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="2364c-125">[varsayılan-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): ilke tarafından açıkça belirtilmeyen kaynak yönergeleri için bir geri dönüş gösterir.</span><span class="sxs-lookup"><span data-stu-id="2364c-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="2364c-126">`self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="2364c-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): görüntüler için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="2364c-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="2364c-128">`data:`URL 'lerden görüntü yüklemeye izin vermek için belirtin `data:` .</span><span class="sxs-lookup"><span data-stu-id="2364c-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="2364c-129">`https:`Https uç noktalarından görüntülerin yüklenmesine izin vermek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="2364c-130">[Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): `<object>` ,, ve etiketleri için geçerli kaynakları gösterir `<embed>` `<applet>` .</span><span class="sxs-lookup"><span data-stu-id="2364c-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="2364c-131">`none`Tüm URL kaynaklarını engellemek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="2364c-132">[Script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): betikler için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="2364c-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="2364c-133">`https://stackpath.bootstrapcdn.com/`Önyükleme betikleri için konak kaynağını belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="2364c-134">`self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="2364c-135">BlazorWebassembly uygulamasında:</span><span class="sxs-lookup"><span data-stu-id="2364c-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="2364c-136">Gerekli Blazor webassembly satır içi betiklerinin yüklenmesine izin vermek için aşağıdaki karmaları belirtin:</span><span class="sxs-lookup"><span data-stu-id="2364c-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="2364c-137">`unsafe-eval`' In kullanılacağını `eval()` ve dizelerden kod oluşturma yöntemlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="2364c-138">Bir Blazor sunucu uygulamasında, `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` stil sayfaları için geri dönüş algılamayı gerçekleştiren satır içi betiğin karmasını belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="2364c-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): stil sayfaları için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="2364c-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="2364c-140">`https://stackpath.bootstrapcdn.com/`Önyükleme stil sayfaları için konak kaynağını belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="2364c-141">`self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="2364c-142">`unsafe-inline`Satır içi stillerin kullanılmasına izin vermek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="2364c-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="2364c-143">Blazorİstemci ve sunucunun ilk istekten sonra yeniden bağlanması için sunucu uygulamalarındaki Kullanıcı arabirimi için satır içi bildirimi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2364c-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="2364c-144">Gelecekteki bir sürümde, artık gerekli olmaması için satır içi stillendirme kaldırılmış olabilir `unsafe-inline` .</span><span class="sxs-lookup"><span data-stu-id="2364c-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="2364c-145">[yükseltme-güvenli olmayan-istekler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): güvenli olmayan (http) kaynaklardaki Içerik URL 'lerinin https üzerinden güvenli bir şekilde alınması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2364c-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="2364c-146">Yukarıdaki yönergeler, Microsoft Internet Explorer hariç tüm tarayıcılar tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2364c-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="2364c-147">Ek satır içi betikler için SHA karmaları almak için:</span><span class="sxs-lookup"><span data-stu-id="2364c-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="2364c-148">[Ilkeyi Uygula](#apply-the-policy) bölümünde gösterilen CSP 'yi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="2364c-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="2364c-149">Uygulamayı yerel olarak çalıştırırken tarayıcının geliştirici araçları konsoluna erişin.</span><span class="sxs-lookup"><span data-stu-id="2364c-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="2364c-150">Tarayıcı, bir CSP üstbilgisi veya etiketi mevcut olduğunda engellenen betikler için karmaları hesaplar ve görüntüler `meta` .</span><span class="sxs-lookup"><span data-stu-id="2364c-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="2364c-151">Tarayıcı tarafından sunulan karmaları `script-src` kaynaklara kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="2364c-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="2364c-152">Her karmaya ait tek tırnakları kullanın.</span><span class="sxs-lookup"><span data-stu-id="2364c-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="2364c-153">Içerik Güvenlik Ilkesi düzey 2 tarayıcı desteği matrisi için bkz. [Içerik Güvenlik Ilkesi düzeyi 2 ' yi kullanabilir miyim](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="2364c-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="2364c-154">İlkeyi Uygula</span><span class="sxs-lookup"><span data-stu-id="2364c-154">Apply the policy</span></span>

<span data-ttu-id="2364c-155">`<meta>`İlkeyi uygulamak için bir etiket kullanın:</span><span class="sxs-lookup"><span data-stu-id="2364c-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="2364c-156">`http-equiv`Özniteliğinin değerini olarak ayarlayın `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="2364c-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="2364c-157">Yönergeleri `content` öznitelik değerine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="2364c-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="2364c-158">Yönergeleri noktalı virgül () ile ayırın `;` .</span><span class="sxs-lookup"><span data-stu-id="2364c-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="2364c-159">Etiketi her zaman `meta` `<head>` içeriğe yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="2364c-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="2364c-160">Aşağıdaki bölümlerde Blazor webassembly ve Server için örnek ilkeler gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="2364c-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="2364c-161">Bu örnekler, uygulamasının her sürümü için bu makalede sürümü oluşturulur Blazor .</span><span class="sxs-lookup"><span data-stu-id="2364c-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="2364c-162">Sürümünüze uygun bir sürümü kullanmak için bu Web sayfasında **Sürüm** açılan Seçicisi seçiciyle birlikte belge sürümü ' nü seçin.</span><span class="sxs-lookup"><span data-stu-id="2364c-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="2364c-163">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2364c-163"> WebAssembly</span></span>

<span data-ttu-id="2364c-164">`<head>` *Wwwroot/index.html* ana bilgisayar Içeriği sayfasında, [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="2364c-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>Blazor<span data-ttu-id="2364c-165">Server</span><span class="sxs-lookup"><span data-stu-id="2364c-165"> Server</span></span>

<span data-ttu-id="2364c-166">`<head>` *Pages/_Host. cshtml* ana bilgisayar sayfasındaki Içerik sayfasında, [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="2364c-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="2364c-167">Meta etiketi sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="2364c-167">Meta tag limitations</span></span>

<span data-ttu-id="2364c-168">Bir `<meta>` Etiket ilkesi aşağıdaki yönergeleri desteklemez:</span><span class="sxs-lookup"><span data-stu-id="2364c-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="2364c-169">çerçeve-üst öğeleri</span><span class="sxs-lookup"><span data-stu-id="2364c-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="2364c-170">raporla</span><span class="sxs-lookup"><span data-stu-id="2364c-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="2364c-171">rapor-URI</span><span class="sxs-lookup"><span data-stu-id="2364c-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="2364c-172">alanda</span><span class="sxs-lookup"><span data-stu-id="2364c-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="2364c-173">Önceki yönergeleri desteklemek için adlı bir üst bilgi kullanın `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="2364c-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="2364c-174">Yönerge dizesi üst bilginin değeridir.</span><span class="sxs-lookup"><span data-stu-id="2364c-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="2364c-175">İlkeyi test etme ve ihlal raporları alma</span><span class="sxs-lookup"><span data-stu-id="2364c-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="2364c-176">Test, ilk ilke oluşturulurken üçüncü taraf betiklerin yanlışlıkla engellenmediğinden emin olmaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2364c-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="2364c-177">İlke yönergelerini uygulamadan bir süre boyunca bir ilkeyi test etmek için, `<meta>` `http-equiv` üst bilgi tabanlı bir ilkenin etiketinin özniteliğini veya üstbilgi adını olarak ayarlayın `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="2364c-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="2364c-178">Hata raporları, belirtilen bir URL 'ye JSON belgeleri olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="2364c-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="2364c-179">Daha fazla bilgi için bkz. [MDN Web belgeleri: içerik-güvenlik-ilke-yalnızca rapor](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="2364c-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="2364c-180">Bir ilke etkinken ihlal hakkında raporlamak için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="2364c-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="2364c-181">raporla</span><span class="sxs-lookup"><span data-stu-id="2364c-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="2364c-182">rapor-URI</span><span class="sxs-lookup"><span data-stu-id="2364c-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="2364c-183">`report-uri`Artık kullanım için önerilmese de, `report-to` ana tarayıcıların tümü tarafından desteklenene kadar her iki yönergeler de kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2364c-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="2364c-184">`report-uri`İçin destek `report-uri` , tarayıcıların *herhangi bir saatinde* ayrılmasına tabi olduğundan özel olarak kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="2364c-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="2364c-185">`report-uri` `report-to` , Tam olarak desteklendikleri zaman ilkelerinizde desteğini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="2364c-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="2364c-186">Benimseme durumunu izlemek için `report-to` , bkz [: Raporlama-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="2364c-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="2364c-187">Uygulamanın ilkesini her sürümde test edin ve güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2364c-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2364c-188">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="2364c-188">Troubleshoot</span></span>

* <span data-ttu-id="2364c-189">Hatalar tarayıcının geliştirici araçları konsolunda görünür.</span><span class="sxs-lookup"><span data-stu-id="2364c-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="2364c-190">Tarayıcılar hakkında bilgi sağlar:</span><span class="sxs-lookup"><span data-stu-id="2364c-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="2364c-191">İlkeyle uyumlu olmayan öğeler.</span><span class="sxs-lookup"><span data-stu-id="2364c-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="2364c-192">İlkeyi engellenen bir öğe için izin verecek şekilde değiştirme.</span><span class="sxs-lookup"><span data-stu-id="2364c-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="2364c-193">Bir ilke, istemci tarayıcısı tüm dahil edilen yönergeleri destekliyorsa tamamen etkilidir.</span><span class="sxs-lookup"><span data-stu-id="2364c-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="2364c-194">Geçerli bir tarayıcı desteği matrisi için, bkz [: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="2364c-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2364c-195">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2364c-195">Additional resources</span></span>

* [<span data-ttu-id="2364c-196">MDN Web belgeleri: Içerik-güvenlik-Ilke</span><span class="sxs-lookup"><span data-stu-id="2364c-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="2364c-197">İçerik Güvenlik Ilkesi düzeyi 2</span><span class="sxs-lookup"><span data-stu-id="2364c-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="2364c-198">Google CSP değerlendiricisi</span><span class="sxs-lookup"><span data-stu-id="2364c-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
