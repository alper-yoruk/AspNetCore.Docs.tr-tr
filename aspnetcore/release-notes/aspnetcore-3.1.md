---
title: ASP.NET Core 3.1'deki yenilikler
author: rick-anderson
description: ASP.NET Core 3.1'deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662710"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3.1'deki yenilikler

Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 3.1 en önemli değişiklikleri vurgulamaktadır.

## <a name="partial-class-support-for-razor-components"></a>Razor bileşenleri için kısmi sınıf desteği

Jilet bileşenleri artık kısmi sınıflar olarak oluşturulur. Bir Razor bileşeninin kodu, tek bir dosyadaki bileşenin tüm kodunu tanımlamak yerine kısmi sınıf olarak tanımlanan kod arkası dosyası kullanılarak yazılabilir. Daha fazla bilgi için [Kısmi sınıf desteğine](xref:blazor/components#partial-class-support)bakın.

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorBileşen Tag Helper ve parametreleri üst düzey bileşenlere geçirin

Core Blazor 3.0 ASP.NET ile bileşenler, html yardımcısı kullanılarak sayfalara`Html.RenderComponentAsync`ve görünümlere işlenmiştir. Core 3.1ASP.NET, yeni Bileşen Etiket Yardımcısı ile bir sayfadan veya görünümden bir bileşen oluşturma:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

HTML Yardımcısı ASP.NET Core 3.1'de desteklenmeye devam eder, ancak Bileşen Etiket Yardımcısı önerilir.

BlazorSunucu uygulamaları artık parametreleri ilk işleme sırasında üst düzey bileşenlere aktarabilir. Daha önce yalnızca [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)ile bir üst düzey bileşene parametreleri geçirebilirsiniz. Bu sürümle hem [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) hem de [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) desteklenir. Belirtilen parametre değerleri JSON olarak serihale edilir ve ilk yanıta dahil edilir.

Örneğin, bir `Counter` bileşeni artış tutarıyla önceden`IncrementAmount`işlemek ( ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Daha fazla bilgi için [bkz.](xref:blazor/integrate-components)

## <a name="support-for-shared-queues-in-httpsys"></a>HTTP.sys'deki paylaşılan kuyruklar için destek

[HTTP.sys](xref:fundamentals/servers/httpsys) anonim istek kuyrukları oluşturmayı destekler. Core 3.1ASP.NET, mevcut bir HTTP.sys istek kuyruğu oluşturma veya ekleme özelliğine ekledik. Varolan bir HTTP.sys istek kuyruğu oluşturma veya iliştirme, sıranın sahibi http.sys denetleyicisi işleminin dinleyici işleminden bağımsız olduğu senaryolara olanak tanır. Bu bağımsızlık, varolan bağlantıların ve dinleyici işlemi yeniden başlatmaları arasında sıralanmış isteklerin korunmasını mümkün kılar:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>SameSite çerezleri için son dakika değişiklikleri

SameSite tanımlama bilgilerinin davranışı, yaklaşan tarayıcı değişikliklerini yansıtacak şekilde değiştirildi. Bu, AzureAd, OpenIdConnect veya WsFederation gibi kimlik doğrulama senaryolarını etkileyebilir. Daha fazla bilgi için bkz. <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Uygulamalardaki Blazor olaylar için varsayılan eylemleri önleme

Bir `@on{EVENT}:preventDefault` olay için varsayılan eylemi önlemek için yönerge özniteliğini kullanın. Aşağıdaki örnekte, metin kutusunda anahtarın karakterini görüntülemenin varsayılan eylemi engellenir:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Daha fazla bilgi için [bkz.](xref:blazor/event-handling#prevent-default-actions)

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Uygulamalarda Blazor olay yayılmasını durdurma

Olay `@on{EVENT}:stopPropagation` yayılmasını durdurmak için yönerge özniteliğini kullanın. Aşağıdaki örnekte, onay kutusunu n seçilmesi, alt `<div>` öğenin üst öğeye `<div>`yayılmasını engelleyen tıklama olaylarıdır:

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Daha fazla bilgi için olay [yayılmasını durdur](xref:blazor/event-handling#stop-event-propagation)una bakın.

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Uygulama geliştirme Blazor sırasında ayrıntılı hatalar

Bir Blazor uygulama geliştirme sırasında düzgün çalışmadığında, uygulamadan ayrıntılı hata bilgileri almak sorun giderme ve sorunu gidermeye yardımcı olur. Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında bir altın çubuk görüntüler:

* Geliştirme sırasında, gold bar sizi özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.
* Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirer ve tarayıcının yenilenmesini önerir.

Daha fazla bilgi için [geliştirme sırasında ayrıntılı hatalar](xref:blazor/handle-errors#detailed-errors-during-development)abakın.
