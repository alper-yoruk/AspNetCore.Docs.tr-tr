---
title: ASP.NET Core 3,1 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: f25cb38a3390600664980933caaf1003b533b2b6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627578"
---
# <a name="whats-new-in-aspnet-core-31"></a>ASP.NET Core 3,1 ' deki yenilikler

Bu makalede, ASP.NET Core 3,1 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.

## <a name="partial-class-support-for-no-locrazor-components"></a>Bileşenler için kısmi sınıf desteği Razor

Razor bileşenler artık kısmi sınıflar olarak oluşturulmuştur. Bir bileşenin kodu, Razor tek bir dosyada bileşen için tüm kodu tanımlamak yerine kısmi bir sınıf olarak tanımlanmış bir arka plan kod dosyası kullanılarak yazılabilir. Daha fazla bilgi için bkz. [kısmi sınıf desteği](xref:blazor/components/index#partial-class-support).

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor Bileşen etiketi Yardımcısı ve parametreleri en üst düzey bileşenlere geçir

BlazorASP.NET Core 3,0 ' de, BILEŞENLER HTML Yardımcısı () kullanılarak sayfalar ve görünümler halinde işlenmiştir `Html.RenderComponentAsync` . ASP.NET Core 3,1 ' de, yeni bileşen etiketi Yardımcısı ile bir sayfadan veya görünümden bir bileşeni işleme:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

HTML Yardımcısı ASP.NET Core 3,1 ' de desteklenmeye devam eder, ancak bileşen etiketi Yardımcısı önerilir.

Blazor Server uygulamalar, artık ilk işleme sırasında parametreleri en üst düzey bileşenlere geçirebilir. Daha önce, parametreleri yalnızca [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)olan en üst düzey bileşene geçirebilirsiniz. Bu sürümle birlikte, hem [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) hem de [RenderMode. Serverprerenimli](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) desteklenir. Belirtilen parametre değerleri JSON olarak serileştirilir ve başlangıç yanıtına dahil edilir.

Örneğin, `Counter` artış miktarı () olan bir bileşen için `IncrementAmount` :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Daha fazla bilgi için bkz. [bileşenleri Razor Sayfalar ve MVC uygulamaları ile tümleştirme](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>HTTP.sys 'de paylaşılan sıralar için destek

[HTTP.sys](xref:fundamentals/servers/httpsys) anonim istek kuyrukları oluşturmayı destekler. ASP.NET Core 3,1 ' de, var olan adlandırılmış HTTP.sys isteği kuyruğu oluşturma veya ekleme yeteneğine ekledik. Mevcut bir adlandırılmış HTTP.sys isteği kuyruğu oluşturmak veya eklemek, sıranın sahibi olan HTTP.sys denetleyicisi işleminin dinleyici işleminden bağımsız olduğu senaryolara olanak sağlar. Bu bağımsızlık, var olan bağlantıları ve dinleyici işlemi yeniden başlatmalar arasında sıraya alınmış istekleri korumayı mümkün kılar:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>SameSite s için son değişiklikler cookie

SameSite s 'nin davranışı cookie yaklaşan tarayıcı değişikliklerini yansıtacak şekilde değiştirilmiştir. Bu, AzureAd, Openıdconnect veya WsFederation gibi kimlik doğrulama senaryolarını etkileyebilir. Daha fazla bilgi için bkz. <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Uygulamalardaki olaylar için varsayılan eylemleri engelleme Blazor

`@on{EVENT}:preventDefault`Bir olayın varsayılan eylemini engellemek için Directive özniteliğini kullanın. Aşağıdaki örnekte, metin kutusunda anahtarın karakterini görüntülemenin varsayılan eylemi engellenir:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Daha fazla bilgi için bkz. [varsayılan eylemleri engelleme](xref:blazor/components/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Uygulamalarda olay yaymayı Durdur Blazor

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

Daha fazla bilgi için bkz. [olay yaymayı durdurma](xref:blazor/components/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Uygulama geliştirme sırasında ayrıntılı hatalar Blazor

BlazorGeliştirme sırasında bir uygulama düzgün çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerini alma sorunu gidermeye ve soruna yardımcı olur. Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:

* Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.
* Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.

Daha fazla bilgi için bkz. [geliştirme sırasında ayrıntılı hatalar](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).
