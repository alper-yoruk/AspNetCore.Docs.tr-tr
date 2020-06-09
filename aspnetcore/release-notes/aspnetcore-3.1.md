---
title: ASP.NET Core 3,1 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 5b6ae8173ca3d968e220faa4a060e1b42b14f8bb
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507250"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3,1 ' deki yenilikler

Bu makalede, ASP.NET Core 3,1 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.

## <a name="partial-class-support-for-razor-components"></a>Bileşenler için kısmi sınıf desteği Razor

Razorbileşenler artık kısmi sınıflar olarak oluşturulmuştur. Bir bileşenin kodu, Razor tek bir dosyada bileşen için tüm kodu tanımlamak yerine kısmi bir sınıf olarak tanımlanmış bir arka plan kod dosyası kullanılarak yazılabilir. Daha fazla bilgi için bkz. [kısmi sınıf desteği](xref:blazor/components#partial-class-support).

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorBileşen etiketi Yardımcısı ve parametreleri en üst düzey bileşenlere geçir

BlazorASP.NET Core 3,0 ' de, BILEŞENLER HTML Yardımcısı () kullanılarak sayfalar ve görünümler halinde işlenmiştir `Html.RenderComponentAsync` . ASP.NET Core 3,1 ' de, yeni bileşen etiketi Yardımcısı ile bir sayfadan veya görünümden bir bileşeni işleme:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

HTML Yardımcısı ASP.NET Core 3,1 ' de desteklenmeye devam eder, ancak bileşen etiketi Yardımcısı önerilir.

BlazorSunucu uygulamaları artık ilk işleme sırasında parametreleri en üst düzey bileşenlere geçirebilir. Daha önce, parametreleri yalnızca [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)olan en üst düzey bileşene geçirebilirsiniz. Bu sürümle birlikte, hem [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) hem de [RenderMode. Serverprerenimli](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) desteklenir. Belirtilen parametre değerleri JSON olarak serileştirilir ve başlangıç yanıtına dahil edilir.

Örneğin, `Counter` artış miktarı () olan bir bileşen için `IncrementAmount` :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Daha fazla bilgi için bkz. [bileşenleri Razor Sayfalar ve MVC uygulamaları ile tümleştirme](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>HTTP. sys dosyasındaki paylaşılan sıralar için destek

[Http. sys](xref:fundamentals/servers/httpsys) , anonim istek kuyrukları oluşturmayı destekler. ASP.NET Core 3,1 ' de, var olan bir HTTP. sys istek kuyruğunu oluşturma veya ekleme yeteneğine ekledik. Var olan adlandırılmış bir HTTP. sys istek kuyruğunu oluşturma veya iliştirme, sıranın sahibi olan HTTP. sys denetleyici işleminin dinleyici işleminden bağımsız olduğu senaryolara olanak sağlar. Bu bağımsızlık, var olan bağlantıları ve dinleyici işlemi yeniden başlatmalar arasında sıraya alınmış istekleri korumayı mümkün kılar:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>SameSite tanımlama bilgilerine yönelik son değişiklikler

SameSite tanımlama bilgilerinin davranışı yaklaşan tarayıcı değişikliklerini yansıtacak şekilde değiştirilmiştir. Bu, AzureAd, Openıdconnect veya WsFederation gibi kimlik doğrulama senaryolarını etkileyebilir. Daha fazla bilgi için bkz. <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a>Uygulamalardaki olaylar için varsayılan eylemleri engelleme Blazor

`@on{EVENT}:preventDefault`Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın. Aşağıdaki örnekte, metin kutusunda anahtarın karakterini görüntülemenin varsayılan eylemi engellenir:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Daha fazla bilgi için bkz. [varsayılan eylemleri engelleme](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-blazor-apps"></a>Uygulamalarda olay yaymayı Durdur Blazor

`@on{EVENT}:stopPropagation`Olay yaymayı durdurmak için Directive özniteliğini kullanın. Aşağıdaki örnekte, onay kutusunun seçilmesi alt öğeden `<div>` üst öğeye yayılmalarını önler `<div>` :

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

Daha fazla bilgi için bkz. [olay yaymayı durdurma](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-blazor-app-development"></a>Uygulama geliştirme sırasında ayrıntılı hatalar Blazor

BlazorGeliştirme sırasında bir uygulama düzgün çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerini alma sorunu gidermeye ve soruna yardımcı olur. Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:

* Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.
* Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.

Daha fazla bilgi için bkz. [geliştirme sırasında ayrıntılı hatalar](xref:blazor/handle-errors#detailed-errors-during-development).
