---
title: ASP.NET Core etiket Yardımcısı bağlantı
author: rick-anderson
ms.author: riande
description: ASP.NET Core link etiketi yardımcı özniteliklerini ve her bir özniteliğin, HTML bağlantısı etiketinin genişletme davranışında oynadığı rolü bulur.
ms.custom: mvc
ms.date: 09/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: ac9f6449e2b7b135318ecf116e1dba7b33ddff83
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212390"
---
# <a name="link-tag-helper-in-aspnet-core"></a>ASP.NET Core etiket Yardımcısı bağlantı

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Bağlantı etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) , birincil veya GERI dönüş CSS dosyasına bir bağlantı oluşturur. Genellikle birincil CSS dosyası [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Bağlantı etiketi Yardımcısı, CSS dosyası için CDN ve CDN kullanılabilir olmadığında geri dönüş belirtmenize olanak tanır. Bağlantı etiketi Yardımcısı, CDN 'nin performans avantajlarından yararlanarak yerel barındırma sağlamlığı sağlar.

Aşağıdaki Razor biçimlendirme `head` ASP.NET Core Web uygulaması şablonuyla oluşturulan bir düzen dosyasının öğesini göstermektedir:

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Aşağıdaki kod, önceki koddan (geliştirme olmayan bir ortamda) HTML olarak işlenir:

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

Önceki kodda, bağlantı etiketi Yardımcısı `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` öğesi ve istenen *Bootstrap. min. css* dosyasının CDN üzerinde kullanılabilir olduğunu doğrulamak Için kullanılan aşağıdaki JavaScript 'i oluşturdu. Bu durumda, CSS dosyası kullanılabilir olduğundan, etiket Yardımcısı `<link />` CDN CSS dosyası ile öğeyi üretti.

## <a name="commonly-used-link-tag-helper-attributes"></a>Yaygın olarak kullanılan bağlantı etiketi Yardımcısı öznitelikleri

Tüm bağlantı etiketi Yardımcısı öznitelikleri, özellikleri ve yöntemleri için [bağlantı etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) ' na bakın.

### <a name="href"></a>değerini

Bağlı kaynağın tercih edilen adresi. Adres, her durumda oluşturulan HTML 'ye düşünce olarak iletilir.

### <a name="asp-fallback-href"></a>ASP-geri dönüş-href

Birincil URL 'nin başarısız olması durumunda öğesine geri dönüş için CSS stil sayfasının URL 'SI.

### <a name="asp-fallback-test-class"></a>ASP-geri dönüş-test sınıfı

Geri dönüş testi için kullanılacak stil sayfasında tanımlanan sınıf adı. Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP-Fallback-test-özelliği

Geri dönüş testi için kullanılacak CSS özellik adı. Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP-geri dönüş-test-değeri

Geri dönüş testi için kullanılacak CSS özelliği değeri. Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
