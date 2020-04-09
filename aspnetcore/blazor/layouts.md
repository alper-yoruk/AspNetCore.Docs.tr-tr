---
title: ASP.NET Blazor Çekirdek düzenleri
author: guardrex
description: Uygulamalar için Blazor yeniden kullanılabilir düzen bileşenlerini nasıl oluşturabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660414"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.NET Blazor Çekirdek düzenleri

Yazar: [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)

Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin bir parçasıdır ve uygulamadaki her bileşen tarafından kullanılır. Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak,&mdash;öğelerden biri güncelleştirme gerektirdiğinde, her bileşengüncelleştirilse etkili bir yaklaşım değildir. Bu tür yinelemekorumak zordur ve zaman içinde tutarsız içeriğe yol açabilir. *Düzenlerbu* sorunu çözer.

Teknik olarak, bir düzen sadece başka bir bileşendir. Bir düzen, Razor şablonunda veya C# kodunda tanımlanır ve [veri bağlama,](xref:blazor/data-binding) [bağımlılık enjeksiyonu](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.

Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:

* Düzen içinde `LayoutComponentBase`işlenen içerik `Body` için bir özellik tanımlayan, devralır.
* İçeriğin işlendiği `@Body` düzen biçimlendirmesinde konumu belirtmek için Razor sözdizimini kullanır.

Aşağıdaki kod örneği, bir düzen bileşeni olan *MainLayout.razor'un*Razor şablonunu gösterir. Düzen, gezinti `LayoutComponentBase` çubuğu `@Body` ile altbilgi arasındaki devralır ve ayarlar:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Blazor Uygulama şablonlarından birine dayalı bir uygulamada, `MainLayout` bileşen *(MainLayout.razor)* uygulamanın *Paylaşılan* klasöründedir.

## <a name="default-layout"></a>Varsayılan düzen

Uygulamanın *App.razor* `Router` dosyasındaki bileşendeki varsayılan uygulama düzenini belirtin. Varsayılan `Router` Blazor şablonlar tarafından sağlanan aşağıdaki bileşen, varsayılan düzeni `MainLayout` bileşene ayarlar:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

İçerik için varsayılan `NotFound` bir düzen `LayoutView` sağlamak `NotFound` için, bir içerik için belirtin:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

`Router` Bileşen hakkında daha fazla <xref:blazor/routing>bilgi için bkz.

Düzenin yönlendiricide varsayılan düzen olarak belirtilmesi yararlı bir uygulamadır, çünkü bileşen başına veya klasör başına olarak geçersiz kılınabilir. En genel teknik olduğundan uygulamanın varsayılan düzenini ayarlamak için yönlendiriciyi kullanmayı tercih edin.

## <a name="specify-a-layout-in-a-component"></a>Bileşende düzen belirtin

Bir bileşene `@layout` düzen uygulamak için Razor yönergesini kullanın. Derleyici, bileşen `@layout` sınıfına uygulanan bir `LayoutAttribute`, a dönüştürür.

Aşağıdaki `MasterList` bileşenin içeriği aşağıdaki konuma `MasterLayout` `@Body`eklenir:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Bir bileşende düzeni doğrudan belirtmek, yönlendiricide ayarlanan *varsayılan* `@layout` düzeni veya *_Imports.razor'dan*alınan bir yönergeyi geçersiz kılar.

## <a name="centralized-layout-selection"></a>Merkezi düzen seçimi

Bir uygulamanın her klasörü isteğe bağlı olarak *_Imports.razor*adlı bir şablon dosyası içerebilir. Derleyici, tüm razor şablonlarında, tüm alt klasörlerinde ve özyinelemeli olarak içe aktarım dosyasında belirtilen yönergeleri içerir. Bu nedenle, *_Imports.razor* dosyası içeren `@layout MyCoolLayout` bir klasördeki tüm `MyCoolLayout`bileşenlerin kullanılmasını sağlar. Klasör ve alt klasörlerdeki `@layout MyCoolLayout` tüm *.razor* dosyalarına tekrar tekrar eklemenize gerek yoktur. `@using`direktifler de bileşenlere aynı şekilde uygulanır.

Aşağıdaki *_Imports.razor* dosya alma:

* `MyCoolLayout`.
* Aynı klasördeki tüm Razor bileşenleri ve alt klasörler.
* İsim `BlazorApp1.Data` alanı.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports.razor* [dosyası, Razor görünümleri ve sayfaları için _ViewImports.cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzer, ancak özellikle Razor bileşen dosyalarına uygulanır.

*_Imports.razor'da* bir düzen belirtilmesi, yönlendiricinin varsayılan *düzeni*olarak belirtilen düzeni geçersiz kılar.

## <a name="nested-layouts"></a>İç içe düzenler

Uygulamalar iç içe doğru düzenlerden oluşabilir. Bileşen, sırayla başka bir düzene başvuran bir düzene başvurur. Örneğin, iç içe geçme düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.

Aşağıdaki örnekte iç içe kullanılan düzenlerin nasıl kullanılacağı gösterilmektedir. *EpisodesComponent.razor* dosyası görüntülenecek bileşendir. Bileşen `MasterListLayout`başvurur:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*MasterListLayout.razor* dosyası `MasterListLayout`sağlar. Düzen, `MasterLayout`işlendiği başka bir düzene başvurur. `EpisodesComponent`göründüğü yerde `@Body` işlenir:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Son `MasterLayout` olarak, *MasterLayout.razor'da* üstbilgi, ana menü ve altbilgi gibi üst düzey düzen öğeleri içerir. `MasterListLayout`göründüğü `EpisodesComponent` yerde `@Body` işlenir:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Bir Razor Pages düzenini tümleşik bileşenlerle paylaşma

Routable bileşenleri bir Razor Pages uygulamasına entegre edildiğinde, uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir. Daha fazla bilgi için bkz. <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/layout>
