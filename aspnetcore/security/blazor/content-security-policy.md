---
title: ASP.NET Core için bir Içerik Güvenlik Ilkesi zorlaBlazor
author: guardrex
description: Siteler arası komut dosyası (XSS) saldırılarına karşı korumaya yardımcı olmak için Blazor ASP.NET Core uygulamalarla bir Içerik güvenlik IlkesI (CSP) kullanmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8c5e1c5dd2d41efade91a612bea2855569a61fee
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775589"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="52b6a-103">ASP.NET Core için bir Içerik Güvenlik Ilkesi zorlaBlazor</span><span class="sxs-lookup"><span data-stu-id="52b6a-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="52b6a-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="52b6a-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="52b6a-105">[Siteler arası betik oluşturma (XSS)](xref:security/cross-site-scripting) , bir saldırganın bir veya daha fazla kötü amaçlı istemci tarafı komut dosyasını uygulamanın işlenmiş içeriğine yerleştirdiği bir güvenlik açığıdır.</span><span class="sxs-lookup"><span data-stu-id="52b6a-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="52b6a-106">Bir Içerik Güvenlik Ilkesi (CSP), geçerli bir tarayıcıya bildirerek XSS saldırılarına karşı korunmaya yardımcı olur:</span><span class="sxs-lookup"><span data-stu-id="52b6a-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="52b6a-107">Betikler, stil sayfaları ve görüntüler dahil olmak üzere yüklenen içerik kaynakları.</span><span class="sxs-lookup"><span data-stu-id="52b6a-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="52b6a-108">Formun izin verilen URL hedeflerini belirten bir sayfa tarafından gerçekleştirilen eylemler.</span><span class="sxs-lookup"><span data-stu-id="52b6a-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="52b6a-109">Yüklenebilen eklentiler.</span><span class="sxs-lookup"><span data-stu-id="52b6a-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="52b6a-110">Bir CSP 'yi bir uygulamaya uygulamak için, geliştirici bir veya daha fazla `Content-Security-Policy` üst bilgi veya `<meta>` etiket içinde çeşitli CSP içerik güvenliği *yönergeleri* belirler.</span><span class="sxs-lookup"><span data-stu-id="52b6a-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="52b6a-111">İlkeler, bir sayfa yüklenirken tarayıcı tarafından değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="52b6a-112">Tarayıcı, sayfanın kaynaklarını inceler ve içerik güvenliği yönergelerinin gereksinimlerini karşılayıp karşılamadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="52b6a-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="52b6a-113">Bir kaynak için ilke yönergeleri karşılanmazsa, tarayıcı kaynağı yüklemez.</span><span class="sxs-lookup"><span data-stu-id="52b6a-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="52b6a-114">Örneğin, üçüncü taraf betiklerine izin veren bir ilkeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="52b6a-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="52b6a-115">Bir sayfa, `src` özniteliğinde üçüncü `<script>` taraf kaynağına sahip bir etiket içerdiğinde, tarayıcı betiğin yüklenmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="52b6a-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="52b6a-116">CSP, Chrome, Edge, Firefox, Opera ve Safari dahil olmak üzere çoğu modern masaüstü ve mobil tarayıcılarda desteklenir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="52b6a-117">CSP, uygulamalar için Blazor önerilir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="52b6a-118">İlke yönergeleri</span><span class="sxs-lookup"><span data-stu-id="52b6a-118">Policy directives</span></span>

<span data-ttu-id="52b6a-119">En düşük düzeyde, uygulamalar için Blazor aşağıdaki yönergeleri ve kaynakları belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="52b6a-120">Gerektiğinde ek yönergeler ve kaynaklar ekleyin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="52b6a-121">Aşağıdaki yönergeler, bu makalenin [Ilkeyi Uygula](#apply-the-policy) bölümünde, burada webassembly ve Blazor Blazor Server için güvenlik ilkelerinin sağlandığı durumlarda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="52b6a-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="52b6a-122">[taban URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; , sayfanın `<base>` etiketinin URL 'lerini kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="52b6a-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="52b6a-123">Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="52b6a-124">[Engelle-tümü-karışık-içerik](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; , karışık http ve HTTPS içeriğini yüklemeyi engeller.</span><span class="sxs-lookup"><span data-stu-id="52b6a-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="52b6a-125">[varsayılan-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; , ilke tarafından açıkça belirtilmeyen kaynak yönergeleri için bir geri dönüş gösterir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="52b6a-126">Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="52b6a-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; görüntüler için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="52b6a-128">URL `data:` 'lerden `data:` görüntü yüklemeye izin vermek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="52b6a-129">HTTPS `https:` uç noktalarından görüntülerin yüklenmesine izin vermek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="52b6a-130">[Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; ,, ve `<object>` `<embed>` `<applet>` etiketleri için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="52b6a-131">Tüm `none` URL kaynaklarını engellemek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="52b6a-132">[Script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; , betikler için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="52b6a-133">Önyükleme betikleri `https://stackpath.bootstrapcdn.com/` için konak kaynağını belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="52b6a-134">Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="52b6a-135">Blazor Webassembly uygulamasında:</span><span class="sxs-lookup"><span data-stu-id="52b6a-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="52b6a-136">Gerekli Blazor webassembly satır içi betiklerinin yüklenmesine izin vermek için aşağıdaki karmaları belirtin:</span><span class="sxs-lookup"><span data-stu-id="52b6a-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="52b6a-137">' `unsafe-eval` In kullanılacağını `eval()` ve dizelerden kod oluşturma yöntemlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="52b6a-138">Bir Blazor sunucu uygulamasında, stil sayfaları için `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` geri dönüş algılamayı gerçekleştiren satır içi betiğin karmasını belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="52b6a-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; , stil sayfaları için geçerli kaynakları gösterir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="52b6a-140">Önyükleme stil `https://stackpath.bootstrapcdn.com/` sayfaları için konak kaynağını belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="52b6a-141">Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="52b6a-142">Satır `unsafe-inline` içi stillerin kullanılmasına izin vermek için belirtin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="52b6a-143">İstemci ve sunucunun ilk istekten sonra yeniden bağlanması için Blazor sunucu uygulamalarındaki Kullanıcı arabirimi için satır içi bildirimi gerekir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="52b6a-144">Gelecekteki bir sürümde, artık gerekli olmaması için satır içi stillendirme `unsafe-inline` kaldırılmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="52b6a-145">[yükseltme-güvenli olmayan-istekler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; , güvenli olmayan (http) kaynaklardaki içerik URL 'lerinin https üzerinden güvenli bir şekilde alınması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="52b6a-146">Yukarıdaki yönergeler, Microsoft Internet Explorer hariç tüm tarayıcılar tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="52b6a-147">Ek satır içi betikler için SHA karmaları almak için:</span><span class="sxs-lookup"><span data-stu-id="52b6a-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="52b6a-148">[Ilkeyi Uygula](#apply-the-policy) bölümünde gösterilen CSP 'yi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="52b6a-149">Uygulamayı yerel olarak çalıştırırken tarayıcının geliştirici araçları konsoluna erişin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="52b6a-150">Tarayıcı, bir CSP üstbilgisi veya `meta` etiketi mevcut olduğunda engellenen betikler için karmaları hesaplar ve görüntüler.</span><span class="sxs-lookup"><span data-stu-id="52b6a-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="52b6a-151">Tarayıcı tarafından sunulan karmaları `script-src` kaynaklara kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="52b6a-152">Her karmaya ait tek tırnakları kullanın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="52b6a-153">Içerik Güvenlik Ilkesi düzey 2 tarayıcı desteği matrisi için bkz. [Içerik Güvenlik Ilkesi düzeyi 2 ' yi kullanabilir miyim](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="52b6a-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="52b6a-154">İlkeyi Uygula</span><span class="sxs-lookup"><span data-stu-id="52b6a-154">Apply the policy</span></span>

<span data-ttu-id="52b6a-155">İlkeyi uygulamak `<meta>` için bir etiket kullanın:</span><span class="sxs-lookup"><span data-stu-id="52b6a-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="52b6a-156">`http-equiv` Özniteliğinin değerini olarak `Content-Security-Policy`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="52b6a-157">Yönergeleri `content` öznitelik değerine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="52b6a-158">Yönergeleri noktalı virgül (`;`) ile ayırın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="52b6a-159">`meta` Etiketi her zaman `<head>` içeriğe yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="52b6a-160">Aşağıdaki bölümlerde webassembly ve Blazor Blazor Server için örnek ilkeler gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="52b6a-161">Bu örnekler, uygulamasının Blazorher sürümü için bu makalede sürümü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="52b6a-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="52b6a-162">Sürümünüze uygun bir sürümü kullanmak için bu Web sayfasında **Sürüm** açılan Seçicisi seçiciyle birlikte belge sürümü ' nü seçin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="52b6a-163">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="52b6a-163"> WebAssembly</span></span>

<span data-ttu-id="52b6a-164">Wwwroot/index.html ana `<head>` bilgisayarı Içeriğinde, *wwwroot/index.html* [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="52b6a-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="52b6a-165">Server</span><span class="sxs-lookup"><span data-stu-id="52b6a-165"> Server</span></span>

<span data-ttu-id="52b6a-166">`<head>` *Pages/_Host. cshtml* ana bilgisayar sayfasındaki içerik sayfasında, [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:</span><span class="sxs-lookup"><span data-stu-id="52b6a-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="52b6a-167">Meta etiketi sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="52b6a-167">Meta tag limitations</span></span>

<span data-ttu-id="52b6a-168">Bir `<meta>` etiket ilkesi aşağıdaki yönergeleri desteklemez:</span><span class="sxs-lookup"><span data-stu-id="52b6a-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="52b6a-169">çerçeve-üst öğeleri</span><span class="sxs-lookup"><span data-stu-id="52b6a-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="52b6a-170">raporla</span><span class="sxs-lookup"><span data-stu-id="52b6a-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="52b6a-171">rapor-URI</span><span class="sxs-lookup"><span data-stu-id="52b6a-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="52b6a-172">alanda</span><span class="sxs-lookup"><span data-stu-id="52b6a-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="52b6a-173">Önceki yönergeleri desteklemek için adlı `Content-Security-Policy`bir üst bilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="52b6a-174">Yönerge dizesi üst bilginin değeridir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="52b6a-175">İlkeyi test etme ve ihlal raporları alma</span><span class="sxs-lookup"><span data-stu-id="52b6a-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="52b6a-176">Test, ilk ilke oluşturulurken üçüncü taraf betiklerin yanlışlıkla engellenmediğinden emin olmaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="52b6a-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="52b6a-177">İlke yönergelerini uygulamadan bir süre boyunca bir ilkeyi test etmek için, üst bilgi tabanlı bir ilkenin `<meta>` etiketinin `http-equiv` özniteliğini veya üstbilgi adını olarak `Content-Security-Policy-Report-Only`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="52b6a-178">Hata raporları, belirtilen bir URL 'ye JSON belgeleri olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="52b6a-179">Daha fazla bilgi için bkz. [MDN Web belgeleri: içerik-güvenlik-ilke-yalnızca rapor](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="52b6a-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="52b6a-180">Bir ilke etkinken ihlal hakkında raporlamak için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="52b6a-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="52b6a-181">raporla</span><span class="sxs-lookup"><span data-stu-id="52b6a-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="52b6a-182">rapor-URI</span><span class="sxs-lookup"><span data-stu-id="52b6a-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="52b6a-183">Artık `report-uri` kullanım için önerilmese de, ana tarayıcıların tümü tarafından desteklenene kadar `report-to` her iki yönergeler de kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="52b6a-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="52b6a-184">İçin `report-uri` destek, `report-uri` tarayıcıların *herhangi bir saatinde* ayrılmasına tabi olduğundan özel olarak kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="52b6a-185">, Tam olarak `report-uri` desteklendikleri zaman `report-to` ilkelerinizde desteğini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="52b6a-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="52b6a-186">Benimseme durumunu `report-to`izlemek için, bkz [: Raporlama-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="52b6a-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="52b6a-187">Uygulamanın ilkesini her sürümde test edin ve güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="52b6a-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="52b6a-188">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="52b6a-188">Troubleshoot</span></span>

* <span data-ttu-id="52b6a-189">Hatalar tarayıcının geliştirici araçları konsolunda görünür.</span><span class="sxs-lookup"><span data-stu-id="52b6a-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="52b6a-190">Tarayıcılar hakkında bilgi sağlar:</span><span class="sxs-lookup"><span data-stu-id="52b6a-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="52b6a-191">İlkeyle uyumlu olmayan öğeler.</span><span class="sxs-lookup"><span data-stu-id="52b6a-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="52b6a-192">İlkeyi engellenen bir öğe için izin verecek şekilde değiştirme.</span><span class="sxs-lookup"><span data-stu-id="52b6a-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="52b6a-193">Bir ilke, istemci tarayıcısı tüm dahil edilen yönergeleri destekliyorsa tamamen etkilidir.</span><span class="sxs-lookup"><span data-stu-id="52b6a-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="52b6a-194">Geçerli bir tarayıcı desteği matrisi için, bkz [: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="52b6a-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52b6a-195">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="52b6a-195">Additional resources</span></span>

* [<span data-ttu-id="52b6a-196">MDN Web belgeleri: Içerik-güvenlik-Ilke</span><span class="sxs-lookup"><span data-stu-id="52b6a-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="52b6a-197">İçerik Güvenlik Ilkesi düzeyi 2</span><span class="sxs-lookup"><span data-stu-id="52b6a-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="52b6a-198">Google CSP değerlendiricisi</span><span class="sxs-lookup"><span data-stu-id="52b6a-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
