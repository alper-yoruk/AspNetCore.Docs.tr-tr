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
ms.openlocfilehash: 92545eab4004f6b67080f79d64b94bb424d5a102
ms.sourcegitcommit: 43a540e703b9096921de27abc6b66bc0783fe905
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320089"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="b785b-103">BlazorCSS yalıtımı ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b785b-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="b785b-104">By [Davve Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="b785b-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="b785b-105">CSS yalıtımı, genel stillerdeki bağımlılıkları önleyerek uygulamanın CSS parmak izini basitleştirir ve bileşenler ve kitaplıklar arasında stil çakışmalarını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="b785b-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="b785b-106">CSS yalıtımını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b785b-106">Enable CSS isolation</span></span> 

<span data-ttu-id="b785b-107">Bileşene özgü stiller tanımlamak için, `.razor.css` bileşenin dosya adıyla eşleşen bir dosya oluşturun `.razor` .</span><span class="sxs-lookup"><span data-stu-id="b785b-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="b785b-108">Bu `.razor.css` dosya kapsamlı BIR *CSS dosyasıdır*.</span><span class="sxs-lookup"><span data-stu-id="b785b-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="b785b-109">`MyComponent`Bir dosya içeren bir bileşen için `MyComponent.razor` , adlı bileşenin yanında bir dosya oluşturun `MyComponent.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="b785b-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="b785b-110">`MyComponent` `.razor.css` Dosya adının değeri büyük/küçük harfe duyarlı **değildir** .</span><span class="sxs-lookup"><span data-stu-id="b785b-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="b785b-111">Örneğin, `Counter` varsayılan Proje şablonundaki BILEŞENE CSS yalıtımı eklemek için Blazor , dosyanın yanında adlı yeni bir dosya ekleyin `Counter.razor.css` `Counter.razor` ve ardından aşağıdaki CSS 'yi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b785b-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="b785b-112">İçinde tanımlanan stiller `Counter.razor.css` Yalnızca bileşenin işlenmiş çıktısına uygulanır `Counter` .</span><span class="sxs-lookup"><span data-stu-id="b785b-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="b785b-113">`h1`Uygulamanın başka bir yerinde tanımlanan CSS bildirimleri stillerle çakışmaz `Counter` .</span><span class="sxs-lookup"><span data-stu-id="b785b-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="b785b-114">Paketleme gerçekleştiğinde stil yalıtımının garanti edilmesi için `@import` Razor bloklar kapsamlı CSS dosyaları ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b785b-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="b785b-115">CSS yalıtım paketlemeyi oluşturma</span><span class="sxs-lookup"><span data-stu-id="b785b-115">CSS isolation bundling</span></span>

<span data-ttu-id="b785b-116">CSS yalıtımı derleme zamanında oluşur.</span><span class="sxs-lookup"><span data-stu-id="b785b-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="b785b-117">Bu işlem sırasında, Blazor CSS seçicileri bileşen tarafından işlenen biçimlendirmeye uyacak şekilde yeniden yazar.</span><span class="sxs-lookup"><span data-stu-id="b785b-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="b785b-118">Bu yeniden yazan CSS stilleri `{PROJECT NAME}.styles.css` , yer tutucunun `{PROJECT NAME}` başvurulan paket veya ürün adı olduğu yerde statik bir varlık olarak paketlenmiştir ve üretilir.</span><span class="sxs-lookup"><span data-stu-id="b785b-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="b785b-119">Bu statik dosyalara varsayılan olarak uygulamanın kök yolundan başvurulur.</span><span class="sxs-lookup"><span data-stu-id="b785b-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="b785b-120">Uygulamada, oluşturulan HTML etiketinin içindeki başvuruyu inceleyerek paketlenmiş dosyaya başvurun `<head>` :</span><span class="sxs-lookup"><span data-stu-id="b785b-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="b785b-121">Paketlenmiş dosya içinde her bileşen bir kapsam tanımlayıcısı ile ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="b785b-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="b785b-122">Stil uygulanmış her bileşen için, biçimiyle bir HTML özniteliği eklenir `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="b785b-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="b785b-123">Tanımlayıcı benzersiz ve her uygulama için farklıdır.</span><span class="sxs-lookup"><span data-stu-id="b785b-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="b785b-124">İşlenmiş `Counter` bileşende, Blazor öğesine bir kapsam tanımlayıcısı ekler `h1` :</span><span class="sxs-lookup"><span data-stu-id="b785b-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="b785b-125">`MyProjectName.styles.css`Dosya, bir stil bildirimini bileşeniyle gruplamak için kapsam tanımlayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b785b-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="b785b-126">Aşağıdaki örnek, önceki öğenin stilini sağlar `<h1>` :</span><span class="sxs-lookup"><span data-stu-id="b785b-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="b785b-127">Derleme zamanında,, `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` yer tutucunun `{STATIC WEB ASSETS BASE PATH}` statik Web varlıkları temel yolu olduğu, kuralıyla birlikte bir proje paketi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b785b-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="b785b-128">NuGet paketleri veya [ Razor sınıf kitaplıkları](xref:blazor/components/class-libraries)gibi diğer projeler kullanılıyorsa, paketlenmiş dosya:</span><span class="sxs-lookup"><span data-stu-id="b785b-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="b785b-129">CSS içeri aktarmaları kullanarak stillere başvurur.</span><span class="sxs-lookup"><span data-stu-id="b785b-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="b785b-130">, Stilleri tüketen uygulamanın statik bir web varlığı olarak yayımlanmaz.</span><span class="sxs-lookup"><span data-stu-id="b785b-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="b785b-131">Alt bileşen desteği</span><span class="sxs-lookup"><span data-stu-id="b785b-131">Child component support</span></span>

<span data-ttu-id="b785b-132">Varsayılan olarak, CSS yalıtımı yalnızca biçimiyle ilişkilendirdiğiniz bileşen için geçerlidir `{COMPONENT NAME}.razor.css` ; burada yer tutucu `{COMPONENT NAME}` genellikle bileşen adıdır.</span><span class="sxs-lookup"><span data-stu-id="b785b-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="b785b-133">Bir alt bileşene değişiklikler uygulamak için, `::deep` Combinator üst bileşenin dosyasındaki tüm alt öğeleri kullanın `.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="b785b-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="b785b-134">`::deep`Combinator, bir öğenin oluşturulan kapsam *descendants* tanımlayıcısının alt öğeleri olan öğeleri seçer.</span><span class="sxs-lookup"><span data-stu-id="b785b-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="b785b-135">Aşağıdaki örnek, `Parent` adlı bir alt bileşen ile çağrılan bir üst bileşeni gösterir `Child` .</span><span class="sxs-lookup"><span data-stu-id="b785b-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="b785b-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="b785b-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="b785b-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="b785b-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="b785b-138">`h1` `Parent.razor.css` `::deep` `h1` Stil bildiriminin üst bileşene ve alt öğelerine uygulanması gerektiğini belirtmek için Combinator ile içindeki bildirimi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b785b-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="b785b-139">`h1`Stil artık `Parent` `Child` alt bileşen için ayrı bir kapsamlı CSS dosyası oluşturmaya gerek olmadan ve bileşenleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="b785b-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="b785b-140">`::deep`Combinator yalnızca alt öğeler ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b785b-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="b785b-141">Aşağıdaki HTML yapısı, `h1` stilleri istenen şekilde bileşenlere uygular:</span><span class="sxs-lookup"><span data-stu-id="b785b-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="b785b-142">Bu senaryoda ASP.NET Core üst bileşenin kapsam tanımlayıcısını `div` öğesine uygular, böylece tarayıcı üst bileşenden stil devralmayı bilir.</span><span class="sxs-lookup"><span data-stu-id="b785b-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="b785b-143">Ancak, öğesinin hariç tutulması `div` alt ilişkiyi kaldırır ve stil alt bileşene uygulanmaz: **not**</span><span class="sxs-lookup"><span data-stu-id="b785b-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="b785b-144">CSS Önişlemci desteği</span><span class="sxs-lookup"><span data-stu-id="b785b-144">CSS preprocessor support</span></span>

<span data-ttu-id="b785b-145">CSS preiþlemcileri, değişkenler, iç içe geçme, modüller, mixıns ve devralma gibi özelliklerden yararlanarak CSS geliştirmeyi iyileştirmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="b785b-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="b785b-146">CSS yalıtımı, Sass veya daha düşük gibi CSS ön işlemcilerini yerel olarak desteklemeirken, Blazor derleme işlemi SıRASıNDA CSS seçicileri yeniden yazmadan önce, ön işlemci derlemesi gerçekleştiği sürece CSS ön işlemcilerini tümleştirmek sorunsuz olur.</span><span class="sxs-lookup"><span data-stu-id="b785b-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="b785b-147">Örneğin, Visual Studio 'yu kullanarak, Visual Studio görev Çalıştırıcısı Gezgininde **derleme** görevi olarak var olan Önişlemci derlemesini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b785b-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="b785b-148">[Delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)gibi birçok üçüncü taraf NuGet PAKETI, CSS yalıtımı gerçekleşmeden önce derleme işleminin başlangıcında Sass/SCSS dosyalarını derleyebilir ve ek yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b785b-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="b785b-149">CSS yalıtım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b785b-149">CSS isolation configuration</span></span>

<span data-ttu-id="b785b-150">CSS yalıtım, kullanıma hazır çalışacak şekilde tasarlanmıştır, ancak mevcut araçlar ya da iş akışlarında bağımlılıklar olduğu gibi bazı gelişmiş senaryolar için yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b785b-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="b785b-151">Kapsam tanımlayıcı biçimini Özelleştir</span><span class="sxs-lookup"><span data-stu-id="b785b-151">Customize scope identifier format</span></span>

<span data-ttu-id="b785b-152">Varsayılan olarak, kapsam tanımlayıcıları biçimini kullanır `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="b785b-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="b785b-153">Kapsam tanımlayıcı biçimini özelleştirmek için, proje dosyasını istenen bir düzene güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b785b-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="b785b-154">Yukarıdaki örnekte, için oluşturulan CSS, `MyComponent.Razor.css` kapsam tanımlayıcısını ' dan ' a değiştirir `b-<10-character-string>` `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="b785b-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="b785b-155">Statik web varlıklarının taban yolunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="b785b-155">Change base path for static web assets</span></span>

<span data-ttu-id="b785b-156">`scoped.styles.css`Dosya, uygulamanın kökünde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b785b-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="b785b-157">Proje dosyasında, `StaticWebAssetBasePath` varsayılan yolu değiştirmek için özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b785b-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="b785b-158">Aşağıdaki örnek, `scoped.styles.css` yolundaki dosyayı ve uygulamanın varlıklarını geri kalanını şu `_content` yolda koyar:</span><span class="sxs-lookup"><span data-stu-id="b785b-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="b785b-159">Otomatik paketlemeyi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b785b-159">Disable automatic bundling</span></span>

<span data-ttu-id="b785b-160">BlazorÇalışma zamanında bulunan kapsamlı dosyaları nasıl yayımlayıp yüklediğini devre dışı bırakmak için özelliğini kullanın `DisableScopedCssBundling` .</span><span class="sxs-lookup"><span data-stu-id="b785b-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="b785b-161">Bu özelliği kullanırken, diğer araçların veya işlemlerin dizinden yalıtılmış CSS dosyalarını alma `obj` ve çalışma zamanında bunları yayımlama ve yükleme işlemlerinden sorumlu olduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="b785b-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="b785b-162">Razor sınıf kitaplığı (RCL) desteği</span><span class="sxs-lookup"><span data-stu-id="b785b-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="b785b-163">Bir [ Razor sınıf kitaplığı (RCL)](xref:razor-pages/ui-class) yalıtılmış stiller sağlıyorsa, `<link>` etiketin `href` özniteliği öğesine işaret eder `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` ; burada yer tutucular:</span><span class="sxs-lookup"><span data-stu-id="b785b-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="b785b-164">`{STATIC WEB ASSET BASE PATH}`: Statik Web varlık temel yolu.</span><span class="sxs-lookup"><span data-stu-id="b785b-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="b785b-165">`{ASSEMBLY NAME}`: Sınıf kitaplığının derleme adı.</span><span class="sxs-lookup"><span data-stu-id="b785b-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="b785b-166">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="b785b-166">In the following example:</span></span>

* <span data-ttu-id="b785b-167">Statik Web varlık temel yolu `_content/ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="b785b-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="b785b-168">Sınıf kitaplığının derleme adı `ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="b785b-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="b785b-169">RCLs ve bileşen kitaplıkları hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="b785b-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="b785b-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="b785b-170"><xref:blazor/components/class-libraries>.</span></span>
