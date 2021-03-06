---
title: ASP.NET Core Blazor düzenleri
author: guardrex
description: Uygulamalar için yeniden kullanılabilir düzen bileşenleri oluşturmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- appsettings.json
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
uid: blazor/layouts
ms.openlocfilehash: 417f69e797296cdcd01fc4ce326388512a406368
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058278"
---
# <a name="aspnet-core-no-locblazor-layouts"></a>ASP.NET Core Blazor düzenleri

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)

Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin parçasıdır ve uygulamadaki her bileşen tarafından kullanılır. Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak etkili bir yaklaşım değildir. Öğelerden biri bir güncelleştirme gerektirdiğinde her bileşenin güncelleştirilmesi gerekir. Bu tür çoğaltmaya devam etmek zordur ve zaman içinde tutarsız içeriğe yol açabilir. *Düzenler* bu sorunu çözüyor.

Teknik olarak, düzen yalnızca başka bir bileşendir. Düzen bir Razor şablonda veya C# kodunda tanımlanır ve [veri bağlama](xref:blazor/components/data-binding), [bağımlılık ekleme](xref:blazor/fundamentals/dependency-injection)ve diğer bileşen senaryolarını kullanabilir. Düzenler yalnızca yönergeleri olan yönlendirilebilir Razor bileşenler için geçerlidir [`@page`](xref:mvc/views/razor#page) .

Bir bileşeni düzene dönüştürmek için:

* Bileşenini öğesinden alın <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> . , <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> Düzen içindeki işlenmiş içerik için bir özelliği tanımlar.
* Razor `@Body` İçeriğin işlendiği yerleşim biçimlendirmesinde konumu belirtmek için söz dizimini kullanın.

Aşağıdaki kod örneği Razor , bir düzen bileşeninin şablonunu gösterir `MainLayout.razor` . Düzen, <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> `@Body` Gezinti çubuğu ve alt bilgi arasında devralır ve Ayarlar:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a>`MainLayout` bileşeni

Proje şablonlarından birini temel alan bir uygulamada Blazor , `MainLayout` bileşen ( `MainLayout.razor` ) uygulamanın `Shared` klasöründedir:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a>Varsayılan düzen

Uygulamanın dosyasındaki varsayılan uygulama yerleşimini belirtin <xref:Microsoft.AspNetCore.Components.Routing.Router> `App.razor` . <xref:Microsoft.AspNetCore.Components.Routing.Router>Varsayılan Şablonlar tarafından belirtilen aşağıdaki bileşen, Blazor bileşene varsayılan düzeni ayarlar `MainLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

İçerik için varsayılan bir düzen sağlamak üzere <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bir içerik belirtin <xref:Microsoft.AspNetCore.Components.LayoutView> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> :

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Bileşen hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.Routing.Router> bkz <xref:blazor/fundamentals/routing> ..

Düzen bir varsayılan düzen olarak belirtildiğinde, bileşen başına veya klasör temelinde geçersiz kılınabileceğinden, yararlı bir uygulamadır. En genel teknik olduğundan, uygulamanın varsayılan yerleşimini ayarlamak için yönlendiriciyi kullanmayı tercih edin.

## <a name="specify-a-layout-in-a-component"></a>Bir bileşende düzen belirtme

Yönergesini [`@layout`](xref:mvc/views/razor#layout) Razor de içeren yönlendirilebilir bir bileşene düzen uygulamak için yönergesini kullanın Razor [`@page`](xref:mvc/views/razor#page) . Derleyici, `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> bileşen sınıfına uygulanan öğesine dönüştürür.

Aşağıdaki `MasterList` bileşenin içeriği konumuna öğesine eklenir `MasterLayout` `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Düzen doğrudan bir bileşen içinde belirtildiğinde, yönlendirici veya  ' `@layout` den içe aktarılan bir yönergede ayarlanan varsayılan bir düzen geçersiz kılınır `_Imports.razor` .

## <a name="centralized-layout-selection"></a>Merkezi düzen seçimi

Bir uygulamanın her klasörü, isteğe bağlı olarak adlı bir şablon dosyası içerebilir `_Imports.razor` . Derleyici, içeri aktarmalar dosyasında belirtilen yönergeleri Razor aynı klasörde ve özyinelemeli olarak tüm alt klasörlerinde bulunan tüm şablonlarda içerir. Bu nedenle, `_Imports.razor` içeren bir dosya, `@layout MyCoolLayout` bir klasördeki tüm bileşenlerin kullanımını sağlar `MyCoolLayout` . `@layout MyCoolLayout` `.razor` Klasöre ve alt klasörlerde bulunan tüm dosyalara tekrar tekrar eklemeniz gerekmez. `@using` yönergeler aynı zamanda bileşenlere aynı şekilde uygulanır.

Aşağıdaki `_Imports.razor` Dosya içeri aktarmalar:

* `MyCoolLayout`.
* RazorAynı klasörde ve tüm alt klasörlerde bulunan tüm bileşenler.
* `BlazorApp1.Data`Ad alanı.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

`_Imports.razor`Dosya, [ Razor görünümler ve sayfalar için _ViewImports. cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzerdir, ancak özellikle Razor bileşen dosyalarına uygulanır.

İçinde bir düzen belirtildiğinde, `_Imports.razor` yönlendiricinin *varsayılan düzeni* olarak belirtilen bir düzen geçersiz kılınır.

> [!WARNING]
>  Razor `@layout` Kök dosyasına bir yönerge eklemeyin `_Imports.razor` ve bu, uygulamadaki sınırsız bir düzen döngüsüne neden olur. Varsayılan uygulama yerleşimini denetlemek için, bileşenin yerleşimini belirtin `Router` . Daha fazla bilgi için [varsayılan düzen](#default-layout) bölümüne bakın.

> [!NOTE]
> [`@layout`](xref:mvc/views/razor#layout) Razor Yönergesi yalnızca yönergeleriyle yönlendirilebilir bileşenlere bir düzen uygular Razor [`@page`](xref:mvc/views/razor#page) .

## <a name="nested-layouts"></a>İç içe düzenleri

Uygulamalar iç içe düzenleri içerebilir. Bir bileşen, daha sonra başka bir düzene başvuruda bulunan bir düzene başvurabilir. Örneğin, iç içe düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.

Aşağıdaki örnek, iç içe düzenleri nasıl kullanacağınızı gösterir. `EpisodesComponent.razor`Dosya görüntülenecek bileşendir. Bileşen öğesine başvurur `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

`MasterListLayout.razor`Dosyası sağlar `MasterListLayout` . Düzen, nerede işlendiği başka bir düzene başvurur `MasterLayout` . `EpisodesComponent` , görüntülendiği yerde işlenir `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Son olarak, `MasterLayout` içinde `MasterLayout.razor` üstbilgi, ana menü ve altbilgi gibi en üst düzey düzen öğelerini içerir. `MasterListLayout` ile birlikte `EpisodesComponent` işlendiğinde, şu şekilde `@Body` görünür:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a>RazorTümleşik bileşenlerle bir sayfa düzeni paylaşma

Yönlendirilebilir bileşenler bir sayfalar uygulamasıyla tümleştirildiğinde Razor , uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir. Daha fazla bilgi için bkz. <xref:blazor/components/prerendering-and-integration>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/layout>
