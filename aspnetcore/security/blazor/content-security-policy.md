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
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>ASP.NET Core için bir Içerik Güvenlik Ilkesi zorlaBlazor

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Siteler arası betik oluşturma (XSS)](xref:security/cross-site-scripting) , bir saldırganın bir veya daha fazla kötü amaçlı istemci tarafı komut dosyasını uygulamanın işlenmiş içeriğine yerleştirdiği bir güvenlik açığıdır. Bir Içerik Güvenlik Ilkesi (CSP), geçerli bir tarayıcıya bildirerek XSS saldırılarına karşı korunmaya yardımcı olur:

* Betikler, stil sayfaları ve görüntüler dahil olmak üzere yüklenen içerik kaynakları.
* Formun izin verilen URL hedeflerini belirten bir sayfa tarafından gerçekleştirilen eylemler.
* Yüklenebilen eklentiler.

Bir CSP 'yi bir uygulamaya uygulamak için, geliştirici bir veya daha fazla `Content-Security-Policy` üst bilgi veya `<meta>` etiket içinde çeşitli CSP içerik güvenliği *yönergeleri* belirler.

İlkeler, bir sayfa yüklenirken tarayıcı tarafından değerlendirilir. Tarayıcı, sayfanın kaynaklarını inceler ve içerik güvenliği yönergelerinin gereksinimlerini karşılayıp karşılamadığını belirler. Bir kaynak için ilke yönergeleri karşılanmazsa, tarayıcı kaynağı yüklemez. Örneğin, üçüncü taraf betiklerine izin veren bir ilkeyi göz önünde bulundurun. Bir sayfa, `src` özniteliğinde üçüncü `<script>` taraf kaynağına sahip bir etiket içerdiğinde, tarayıcı betiğin yüklenmesini engeller.

CSP, Chrome, Edge, Firefox, Opera ve Safari dahil olmak üzere çoğu modern masaüstü ve mobil tarayıcılarda desteklenir. CSP, uygulamalar için Blazor önerilir.

## <a name="policy-directives"></a>İlke yönergeleri

En düşük düzeyde, uygulamalar için Blazor aşağıdaki yönergeleri ve kaynakları belirtin. Gerektiğinde ek yönergeler ve kaynaklar ekleyin. Aşağıdaki yönergeler, bu makalenin [Ilkeyi Uygula](#apply-the-policy) bölümünde, burada webassembly ve Blazor Blazor Server için güvenlik ilkelerinin sağlandığı durumlarda kullanılır:

* [taban URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; , sayfanın `<base>` etiketinin URL 'lerini kısıtlar. Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
* [Engelle-tümü-karışık-içerik](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; , karışık http ve HTTPS içeriğini yüklemeyi engeller.
* [varsayılan-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; , ilke tarafından açıkça belirtilmeyen kaynak yönergeleri için bir geri dönüş gösterir. Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; görüntüler için geçerli kaynakları gösterir.
  * URL `data:` 'lerden `data:` görüntü yüklemeye izin vermek için belirtin.
  * HTTPS `https:` uç noktalarından görüntülerin yüklenmesine izin vermek için belirtin.
* [Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; ,, ve `<object>` `<embed>` `<applet>` etiketleri için geçerli kaynakları gösterir. Tüm `none` URL kaynaklarını engellemek için belirtin.
* [Script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; , betikler için geçerli kaynakları gösterir.
  * Önyükleme betikleri `https://stackpath.bootstrapcdn.com/` için konak kaynağını belirtin.
  * Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
  * Blazor Webassembly uygulamasında:
    * Gerekli Blazor webassembly satır içi betiklerinin yüklenmesine izin vermek için aşağıdaki karmaları belirtin:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * ' `unsafe-eval` In kullanılacağını `eval()` ve dizelerden kod oluşturma yöntemlerini belirtin.
  * Bir Blazor sunucu uygulamasında, stil sayfaları için `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` geri dönüş algılamayı gerçekleştiren satır içi betiğin karmasını belirtin.
* [Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; , stil sayfaları için geçerli kaynakları gösterir.
  * Önyükleme stil `https://stackpath.bootstrapcdn.com/` sayfaları için konak kaynağını belirtin.
  * Uygulamanın `self` kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
  * Satır `unsafe-inline` içi stillerin kullanılmasına izin vermek için belirtin. İstemci ve sunucunun ilk istekten sonra yeniden bağlanması için Blazor sunucu uygulamalarındaki Kullanıcı arabirimi için satır içi bildirimi gerekir. Gelecekteki bir sürümde, artık gerekli olmaması için satır içi stillendirme `unsafe-inline` kaldırılmış olabilir.
* [yükseltme-güvenli olmayan-istekler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; , güvenli olmayan (http) kaynaklardaki içerik URL 'lerinin https üzerinden güvenli bir şekilde alınması gerektiğini gösterir.

Yukarıdaki yönergeler, Microsoft Internet Explorer hariç tüm tarayıcılar tarafından desteklenir.

Ek satır içi betikler için SHA karmaları almak için:

* [Ilkeyi Uygula](#apply-the-policy) bölümünde gösterilen CSP 'yi uygulayın.
* Uygulamayı yerel olarak çalıştırırken tarayıcının geliştirici araçları konsoluna erişin. Tarayıcı, bir CSP üstbilgisi veya `meta` etiketi mevcut olduğunda engellenen betikler için karmaları hesaplar ve görüntüler.
* Tarayıcı tarafından sunulan karmaları `script-src` kaynaklara kopyalayın. Her karmaya ait tek tırnakları kullanın.

Içerik Güvenlik Ilkesi düzey 2 tarayıcı desteği matrisi için bkz. [Içerik Güvenlik Ilkesi düzeyi 2 ' yi kullanabilir miyim](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>İlkeyi Uygula

İlkeyi uygulamak `<meta>` için bir etiket kullanın:

* `http-equiv` Özniteliğinin değerini olarak `Content-Security-Policy`ayarlayın.
* Yönergeleri `content` öznitelik değerine yerleştirin. Yönergeleri noktalı virgül (`;`) ile ayırın.
* `meta` Etiketi her zaman `<head>` içeriğe yerleştirin.

Aşağıdaki bölümlerde webassembly ve Blazor Blazor Server için örnek ilkeler gösterilmektedir. Bu örnekler, uygulamasının Blazorher sürümü için bu makalede sürümü oluşturulur. Sürümünüze uygun bir sürümü kullanmak için bu Web sayfasında **Sürüm** açılan Seçicisi seçiciyle birlikte belge sürümü ' nü seçin.

### <a name="blazor-webassembly"></a>BlazorWebAssembly

Wwwroot/index.html ana `<head>` bilgisayarı Içeriğinde, *wwwroot/index.html* [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:

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

### <a name="blazor-server"></a>BlazorServer

`<head>` *Pages/_Host. cshtml* ana bilgisayar sayfasındaki içerik sayfasında, [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:

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

## <a name="meta-tag-limitations"></a>Meta etiketi sınırlamaları

Bir `<meta>` etiket ilkesi aşağıdaki yönergeleri desteklemez:

* [çerçeve-üst öğeleri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [raporla](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [rapor-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [alanda](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Önceki yönergeleri desteklemek için adlı `Content-Security-Policy`bir üst bilgi kullanın. Yönerge dizesi üst bilginin değeridir.

## <a name="test-a-policy-and-receive-violation-reports"></a>İlkeyi test etme ve ihlal raporları alma

Test, ilk ilke oluşturulurken üçüncü taraf betiklerin yanlışlıkla engellenmediğinden emin olmaya yardımcı olur.

İlke yönergelerini uygulamadan bir süre boyunca bir ilkeyi test etmek için, üst bilgi tabanlı bir ilkenin `<meta>` etiketinin `http-equiv` özniteliğini veya üstbilgi adını olarak `Content-Security-Policy-Report-Only`ayarlayın. Hata raporları, belirtilen bir URL 'ye JSON belgeleri olarak gönderilir. Daha fazla bilgi için bkz. [MDN Web belgeleri: içerik-güvenlik-ilke-yalnızca rapor](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Bir ilke etkinken ihlal hakkında raporlamak için aşağıdaki makalelere bakın:

* [raporla](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [rapor-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Artık `report-uri` kullanım için önerilmese de, ana tarayıcıların tümü tarafından desteklenene kadar `report-to` her iki yönergeler de kullanılmalıdır. İçin `report-uri` destek, `report-uri` tarayıcıların *herhangi bir saatinde* ayrılmasına tabi olduğundan özel olarak kullanmayın. , Tam olarak `report-uri` desteklendikleri zaman `report-to` ilkelerinizde desteğini kaldırın. Benimseme durumunu `report-to`izlemek için, bkz [: Raporlama-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Uygulamanın ilkesini her sürümde test edin ve güncelleştirin.

## <a name="troubleshoot"></a>Sorun giderme

* Hatalar tarayıcının geliştirici araçları konsolunda görünür. Tarayıcılar hakkında bilgi sağlar:
  * İlkeyle uyumlu olmayan öğeler.
  * İlkeyi engellenen bir öğe için izin verecek şekilde değiştirme.
* Bir ilke, istemci tarayıcısı tüm dahil edilen yönergeleri destekliyorsa tamamen etkilidir. Geçerli bir tarayıcı desteği matrisi için, bkz [: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Ek kaynaklar

* [MDN Web belgeleri: Içerik-güvenlik-Ilke](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [İçerik Güvenlik Ilkesi düzeyi 2](https://www.w3.org/TR/CSP2/)
* [Google CSP değerlendiricisi](https://csp-evaluator.withgoogle.com/)
