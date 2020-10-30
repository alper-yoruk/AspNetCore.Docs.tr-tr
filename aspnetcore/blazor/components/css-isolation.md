---
title: BlazorCSS yalıtımı ASP.NET Core
author: daveabrock
description: CSS yalıtımının, CSS 'nizi basitleştirecek ve diğer bileşenler veya kitaplıklarla çakışmaları önlemenize olanak sağlayan CSS 'yi bileşenlerinizin kapsamını nasıl sağlayacağınızı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056328"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a>BlazorCSS yalıtımı ASP.NET Core

By [Davve Brock](https://twitter.com/daveabrock)

CSS yalıtımı, genel stillerdeki bağımlılıkları önleyerek uygulamanın CSS parmak izini basitleştirir ve bileşenler ve kitaplıklar arasında stil çakışmalarını önlemeye yardımcı olur.

## <a name="enable-css-isolation"></a>CSS yalıtımını etkinleştir 

Bileşene özgü stiller tanımlamak için, `razor.css` bileşenin dosya adıyla eşleşen bir dosya oluşturun `.razor` . Bu `razor.css` dosya kapsamlı BIR *CSS dosyasıdır* . 

`MyComponent`Bir dosya içeren bir bileşen için `MyComponent.razor` , adlı bileşenin yanında bir dosya oluşturun `MyComponent.razor.css` . `MyComponent` `razor.css` Dosya adının değeri büyük/küçük harfe duyarlı **değildir** .

Örneğin, `Counter` varsayılan Proje şablonundaki BILEŞENE CSS yalıtımı eklemek için Blazor , dosyanın yanında adlı yeni bir dosya ekleyin `Counter.razor.css` `Counter.razor` ve ardından aşağıdaki CSS 'yi ekleyin:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

İçinde tanımlanan stiller `Counter.razor.css` Yalnızca bileşenin işlenmiş çıktısına uygulanır `Counter` . `h1`Uygulamanın başka bir yerinde tanımlanan CSS bildirimleri stillerle çakışmaz `Counter` .

> [!NOTE]
> Paketleme gerçekleştiğinde stil yalıtımının garanti edilmesi için `@import` Razor bloklar kapsamlı CSS dosyaları ile desteklenmez.

## <a name="css-isolation-bundling"></a>CSS yalıtım paketlemeyi oluşturma

CSS yalıtımı derleme zamanında oluşur. Bu işlem sırasında, Blazor CSS seçicileri bileşen tarafından işlenen biçimlendirmeye uyacak şekilde yeniden yazar. Bu yeniden yazan CSS stilleri `{PROJECT NAME}.styles.css` , yer tutucunun `{PROJECT NAME}` başvurulan paket veya ürün adı olduğu yerde statik bir varlık olarak paketlenmiştir ve üretilir.

Bu statik dosyalara varsayılan olarak uygulamanın kök yolundan başvurulur. Uygulamada, oluşturulan HTML etiketinin içindeki başvuruyu inceleyerek paketlenmiş dosyaya başvurun `<head>` :

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

Paketlenmiş dosya içinde her bileşen bir kapsam tanımlayıcısı ile ilişkilendirilir. Stil uygulanmış her bileşen için, biçimiyle bir HTML özniteliği eklenir `b-<10-character-string>` . Tanımlayıcı benzersiz ve her uygulama için farklıdır. İşlenmiş `Counter` bileşende, Blazor öğesine bir kapsam tanımlayıcısı ekler `h1` :

```html
<h1 b-3xxtam6d07>
```

`MyProjectName.styles.css`Dosya, bir stil bildirimini bileşeniyle gruplamak için kapsam tanımlayıcısını kullanır. Aşağıdaki örnek, önceki öğenin stilini sağlar `<h1>` :

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

Derleme zamanında,, `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` yer tutucunun `{STATIC WEB ASSETS BASE PATH}` statik Web varlıkları temel yolu olduğu, kuralıyla birlikte bir proje paketi oluşturulur.

NuGet paketleri veya [ Razor sınıf kitaplıkları](xref:blazor/components/class-libraries)gibi diğer projeler kullanılıyorsa, paketlenmiş dosya:

* CSS içeri aktarmaları kullanarak stillere başvurur.
* , Stilleri tüketen uygulamanın statik bir web varlığı olarak yayımlanmaz.

## <a name="child-component-support"></a>Alt bileşen desteği

Varsayılan olarak, CSS yalıtımı yalnızca biçimiyle ilişkilendirdiğiniz bileşen için geçerlidir `{COMPONENT NAME}.razor.css` ; burada yer tutucu `{COMPONENT NAME}` genellikle bileşen adıdır. Bir alt bileşene değişiklikler uygulamak için, `::deep` Combinator üst bileşenin dosyasındaki tüm alt öğeleri kullanın `razor.css` . `::deep`Combinator, bir öğenin oluşturulan kapsam *descendants* tanımlayıcısının alt öğeleri olan öğeleri seçer. 

Aşağıdaki örnek, `Parent` adlı bir alt bileşen ile çağrılan bir üst bileşeni gösterir `Child` .

`Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Child.razor`:

```razor
<h1>Child Component</h1>
```

`h1` `Parent.razor.css` `::deep` `h1` Stil bildiriminin üst bileşene ve alt öğelerine uygulanması gerektiğini belirtmek için Combinator ile içindeki bildirimi güncelleştirin:

```css
::deep h1 { 
    color: red;
}
```

`h1`Stil artık `Parent` `Child` alt bileşen için ayrı bir kapsamlı CSS dosyası oluşturmaya gerek olmadan ve bileşenleri için geçerlidir.

> [!NOTE]
> `::deep`Combinator yalnızca alt öğeler ile kullanılabilir. Aşağıdaki HTML yapısı, `h1` stilleri istenen şekilde bileşenlere uygular:
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> Bu senaryoda ASP.NET Core üst bileşenin kapsam tanımlayıcısını `div` öğesine uygular, böylece tarayıcı üst bileşenden stil devralmayı bilir.
>
> Ancak, öğesinin hariç tutulması `div` alt ilişkiyi kaldırır ve stil alt bileşene uygulanmaz: **not**
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a>CSS Önişlemci desteği

CSS preiþlemcileri, değişkenler, iç içe geçme, modüller, mixıns ve devralma gibi özelliklerden yararlanarak CSS geliştirmeyi iyileştirmek için yararlıdır. CSS yalıtımı, Sass veya daha düşük gibi CSS ön işlemcilerini yerel olarak desteklemeirken, Blazor derleme işlemi SıRASıNDA CSS seçicileri yeniden yazmadan önce, ön işlemci derlemesi gerçekleştiği sürece CSS ön işlemcilerini tümleştirmek sorunsuz olur. Örneğin, Visual Studio 'yu kullanarak, Visual Studio görev Çalıştırıcısı Gezgininde **derleme** görevi olarak var olan Önişlemci derlemesini yapılandırın.

[Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)gibi birçok üçüncü taraf NuGet PAKETI, CSS yalıtımı gerçekleşmeden önce derleme işleminin başlangıcında Sass/SCSS dosyalarını derleyebilir ve ek ek yapılandırma gerekmez.

## <a name="css-isolation-configuration"></a>CSS yalıtım yapılandırması

CSS yalıtım, kullanıma hazır çalışacak şekilde tasarlanmıştır, ancak mevcut araçlar ya da iş akışlarında bağımlılıklar olduğu gibi bazı gelişmiş senaryolar için yapılandırma sağlar.

### <a name="customize-scope-identifier-format"></a>Kapsam tanımlayıcı biçimini Özelleştir

Varsayılan olarak, kapsam tanımlayıcıları biçimini kullanır `b-<10-character-string>` . Kapsam tanımlayıcı biçimini özelleştirmek için, proje dosyasını istenen bir düzene güncelleştirin:

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Yukarıdaki örnekte, için oluşturulan CSS, `MyComponent.Razor.css` kapsam tanımlayıcısını ' dan ' a değiştirir `b-<10-character-string>` `my-custom-scope-identifier` .

### <a name="change-base-path-for-static-web-assets"></a>Statik web varlıklarının taban yolunu değiştirme

`scoped.styles.css`Dosya, uygulamanın kökünde oluşturulur. Proje dosyasında, `StaticWebAssetBasePath` varsayılan yolu değiştirmek için özelliğini kullanın. Aşağıdaki örnek, `scoped.styles.css` yolundaki dosyayı ve uygulamanın varlıklarını geri kalanını şu `_content` yolda koyar:

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Otomatik paketlemeyi devre dışı bırak

BlazorÇalışma zamanında bulunan kapsamlı dosyaları nasıl yayımlayıp yüklediğini devre dışı bırakmak için özelliğini kullanın `DisableScopedCssBundling` . Bu özelliği kullanırken, diğer araçların veya işlemlerin dizinden yalıtılmış CSS dosyalarını alma `obj` ve çalışma zamanında bunları yayımlama ve yükleme işlemlerinden sorumlu olduğu anlamına gelir:

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
