---
title: ASP.NET Core Blazor düzenleri
author: guardrex
description: Uygulamalar için Blazor yeniden kullanılabilir düzen bileşenleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5c6771dd7249bfb8280ba20e1ce75967f279971c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771591"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor düzenleri

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)

Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin parçasıdır ve uygulamadaki her bileşen tarafından kullanılır. Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak, öğelerden biri bir güncelleştirme gerektirdiğinde her bileşenin güncelleştirilmesi gereken&mdash;etkili bir yaklaşım değildir. Bu tür çoğaltmaya devam etmek zordur ve zaman içinde tutarsız içeriğe yol açabilir. *Düzenler* bu sorunu çözüyor.

Teknik olarak, düzen yalnızca başka bir bileşendir. Düzen bir Razor şablonda veya C# kodunda tanımlanır ve [veri bağlama](xref:blazor/data-binding), [bağımlılık ekleme](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.

Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:

* Düzen içindeki `LayoutComponentBase`işlenmiş içerik için bir `Body` özellik tanımlayan öğesinden devralır.
* İçeriğin işlendiği Razor yerleşim `@Body` biçimlendirmesinde konumu belirtmek için söz dizimini kullanır.

Aşağıdaki kod örneğinde, Razor *mainlayout. Razor*olan bir düzen bileşeninin şablonu gösterilmektedir. Düzen, gezinti `LayoutComponentBase` çubuğu ve alt `@Body` bilgi arasında devralır ve Ayarlar:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Uygulama şablonlarından Blazor birini temel alan bir uygulamada `MainLayout` , bileşen (*Mainlayout. Razor*) uygulamanın *paylaşılan* klasöründedir.

## <a name="default-layout"></a>Varsayılan düzen

Uygulamanın App `Router` *. Razor* dosyasındaki bileşende varsayılan uygulama yerleşimini belirtin. Varsayılan `Router` Blazor şablonlar tarafından belirtilen aşağıdaki bileşen, `MainLayout` bileşene varsayılan düzeni ayarlar:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

İçerik için `NotFound` varsayılan bir düzen sağlamak üzere bir `LayoutView` `NotFound` içerik belirtin:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

`Router` Bileşen hakkında daha fazla bilgi için bkz <xref:blazor/routing>..

Düzen bir varsayılan düzen olarak belirtildiğinde, bileşen başına veya klasör temelinde geçersiz kılınabileceğinden, yararlı bir uygulamadır. En genel teknik olduğundan, uygulamanın varsayılan yerleşimini ayarlamak için yönlendiriciyi kullanmayı tercih edin.

## <a name="specify-a-layout-in-a-component"></a>Bir bileşende düzen belirtme

Bir bileşene Razor Düzen `@layout` uygulamak için yönergesini kullanın. Derleyici, bileşen `@layout` sınıfına uygulanan `LayoutAttribute`öğesine dönüştürür.

Aşağıdaki `MasterList` bileşenin içeriği konumuna öğesine `MasterLayout` eklenir: `@Body`

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Düzen doğrudan bir bileşen içinde belirtildiğinde, yönlendiricide ayarlanan *varsayılan bir düzen* veya *_Imports. Razor*'den `@layout` içeri aktarılan bir yönerge geçersiz kılınır.

## <a name="centralized-layout-selection"></a>Merkezi düzen seçimi

Bir uygulamanın her klasörü, isteğe bağlı olarak *_Imports. Razor*adlı bir şablon dosyası içerebilir. Derleyici, içeri aktarmalar dosyasında belirtilen yönergeleri aynı klasörde ve özyinelemeli olarak tüm alt Razor klasörlerinde bulunan tüm şablonlarda içerir. Bu nedenle, içeren `@layout MyCoolLayout` bir *_Imports. Razor* dosyası, bir klasördeki tüm bileşenlerin kullanımını `MyCoolLayout`sağlar. Klasör ve alt klasörlerdeki tüm `@layout MyCoolLayout` *. Razor* dosyalarına tekrar tekrar ekleme gereksinimi yoktur. `@using`yönergeler aynı zamanda bileşenlere aynı şekilde uygulanır.

Aşağıdaki *_Imports. Razor* dosyası içeri aktarmaları:

* `MyCoolLayout`.
* Aynı Razor klasörde ve tüm alt klasörlerde bulunan tüm bileşenler.
* `BlazorApp1.Data` Ad alanı.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports. Razor* dosyası, [görünümler ve sayfalar için Razor _ViewImports. cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzerdir, ancak özellikle Razor bileşen dosyalarına uygulanır.

_Imports bir düzen belirtme *. Razor* , yönlendiricinin *varsayılan düzeni*olarak belirtilen bir düzeni geçersiz kılar.

## <a name="nested-layouts"></a>İç içe düzenleri

Uygulamalar iç içe düzenleri içerebilir. Bir bileşen, daha sonra başka bir düzene başvuruda bulunan bir düzene başvurabilir. Örneğin, iç içe düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.

Aşağıdaki örnek, iç içe düzenleri nasıl kullanacağınızı gösterir. *Epısodescomponent. Razor* dosyası görüntülenecek bileşendir. Bileşen öğesine başvurur `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*Masterlistlayout. Razor* dosyası sağlar `MasterListLayout`. Düzen, nerede işlendiği başka bir `MasterLayout`düzene başvurur. `EpisodesComponent`, görüntülendiği yerde `@Body` işlenir:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Son olarak `MasterLayout` , *masterlayout. Razor* içinde üstbilgi, ana menü ve altbilgi gibi en üst düzey düzen öğelerini içerir. `MasterListLayout`ile birlikte `EpisodesComponent` işlendiğinde, şu `@Body` şekilde görünür:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Tümleşik bileşenlerle Razor bir sayfa düzeni paylaşma

Yönlendirilebilir bileşenler bir Razor sayfalar uygulamasıyla tümleştirildiğinde, uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir. Daha fazla bilgi için bkz. <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/layout>
