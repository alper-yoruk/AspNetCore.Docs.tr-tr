---
title: 'ASP.NET Core :::no-loc(Razor)::: bileşenleri sınıf kitaplıkları'
author: guardrex
description: 'Bileşenlerin :::no-loc(Blazor)::: bir dış bileşen kitaplığından uygulamalara nasıl dahil edileceğini öğrenin.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/class-libraries
ms.openlocfilehash: f8e36cbe905b5ec2e674123c0f2ab6db99683c7c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056419"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="15d38-103">ASP.NET Core :::no-loc(Razor)::: bileşenleri sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="15d38-103">ASP.NET Core :::no-loc(Razor)::: components class libraries</span></span>

<span data-ttu-id="15d38-104">[Simon Timms](https://github.com/stimms) tarafından</span><span class="sxs-lookup"><span data-stu-id="15d38-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="15d38-105">Bileşenler, projeler genelinde bir [ :::no-loc(Razor)::: sınıf kitaplığı 'NDA (RCL)](xref:razor-pages/ui-class) paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="15d38-105">Components can be shared in a [:::no-loc(Razor)::: class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="15d38-106">Bir *:::no-loc(Razor)::: bileşen sınıfı kitaplığı* , şuradan eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="15d38-106">A *:::no-loc(Razor)::: components class library* can be included from:</span></span>

* <span data-ttu-id="15d38-107">Çözümdeki başka bir proje.</span><span class="sxs-lookup"><span data-stu-id="15d38-107">Another project in the solution.</span></span>
* <span data-ttu-id="15d38-108">Bir NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="15d38-108">A NuGet package.</span></span>
* <span data-ttu-id="15d38-109">Başvurulan bir .NET kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="15d38-109">A referenced .NET library.</span></span>

<span data-ttu-id="15d38-110">Bileşenler normal .NET türleri olduğu gibi, bir RCL tarafından sunulan bileşenler normal .NET derlemelerdir.</span><span class="sxs-lookup"><span data-stu-id="15d38-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="15d38-111">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="15d38-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="15d38-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="15d38-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="15d38-113">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="15d38-113">Create a new project.</span></span>
1. <span data-ttu-id="15d38-114">**:::no-loc(Razor)::: Sınıf kitaplığı** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-114">Select **:::no-loc(Razor)::: Class Library** .</span></span> <span data-ttu-id="15d38-115">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-115">Select **Next** .</span></span>
1. <span data-ttu-id="15d38-116">**Yeni :::no-loc(Razor)::: sınıf kitaplığı oluştur** iletişim kutusunda **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-116">In the **Create a new :::no-loc(Razor)::: class library** dialog, select **Create** .</span></span>
1. <span data-ttu-id="15d38-117">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="15d38-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="15d38-118">Bu konudaki örneklerde proje adı kullanılır `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="15d38-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="15d38-119">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-119">Select **Create** .</span></span>
1. <span data-ttu-id="15d38-120">RCL 'yi bir çözüme ekleyin:</span><span class="sxs-lookup"><span data-stu-id="15d38-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="15d38-121">Çözüme sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="15d38-121">Right-click the solution.</span></span> <span data-ttu-id="15d38-122">**Add**  >  **Varolan proje** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-122">Select **Add** > **Existing Project** .</span></span>
   1. <span data-ttu-id="15d38-123">RCL 'nin proje dosyasına gidin.</span><span class="sxs-lookup"><span data-stu-id="15d38-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="15d38-124">RCL 'nin proje dosyasını () seçin `.csproj` .</span><span class="sxs-lookup"><span data-stu-id="15d38-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="15d38-125">Uygulamadan RCL 'ye bir başvuru ekleyin:</span><span class="sxs-lookup"><span data-stu-id="15d38-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="15d38-126">Uygulama projesine sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="15d38-126">Right-click the app project.</span></span> <span data-ttu-id="15d38-127">Başvuru **Ekle** ' yi seçin  >  **Reference** .</span><span class="sxs-lookup"><span data-stu-id="15d38-127">Select **Add** > **Reference** .</span></span>
   1. <span data-ttu-id="15d38-128">RCL projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-128">Select the RCL project.</span></span> <span data-ttu-id="15d38-129">**Tamam** ’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="15d38-129">Select **OK** .</span></span>

> [!NOTE]
> <span data-ttu-id="15d38-130">Şablondan RCL oluşturulurken **destek sayfaları ve görünümler** onay kutusu işaretliyse, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne da bir dosya ekleyin :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="15d38-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable :::no-loc(Razor)::: component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="15d38-131">Dosyayı oluşturulan projenin köküne el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="15d38-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="15d38-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="15d38-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="15d38-133">Komut kabuğunda komutuyla **:::no-loc(Razor)::: sınıf kitaplığı** şablonu ( `razorclasslib` ) kullanın [`dotnet new`](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="15d38-133">Use the **:::no-loc(Razor)::: Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="15d38-134">Aşağıdaki örnekte adlı bir RCL oluşturulur `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="15d38-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="15d38-135">Komut yürütüldüğünde, tutan klasör `ComponentLibrary` otomatik olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="15d38-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="15d38-136">`-s|--support-pages-and-views`Şablondan RCL oluşturulurken anahtar kullanılıyorsa, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne bir dosya ekleyin :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="15d38-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable :::no-loc(Razor)::: component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="15d38-137">Dosyayı oluşturulan projenin köküne el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="15d38-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="15d38-138">Kitaplığı var olan bir projeye eklemek için komut [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) kabuğu 'nda komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="15d38-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="15d38-139">Aşağıdaki örnekte, RCL uygulamaya eklenir.</span><span class="sxs-lookup"><span data-stu-id="15d38-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="15d38-140">Uygulamanın proje klasöründen, kitaplığın yolunu kullanarak aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="15d38-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="15d38-141">Kitaplık bileşeni kullanma</span><span class="sxs-lookup"><span data-stu-id="15d38-141">Consume a library component</span></span>

<span data-ttu-id="15d38-142">Başka bir projedeki bir kitaplıkta tanımlanan bileşenleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="15d38-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="15d38-143">Ad alanı ile tam tür adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="15d38-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="15d38-144">Kullanım :::no-loc(Razor)::: [`@using`](xref:mvc/views/razor#using) yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="15d38-144">Use :::no-loc(Razor):::'s [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="15d38-145">Tek tek bileşenler, ada göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="15d38-145">Individual components can be added by name.</span></span>

<span data-ttu-id="15d38-146">Aşağıdaki örneklerde, `ComponentLibrary` bileşeni () içeren bir bileşen kitaplığı vardır `Component1` `Component1.razor` .</span><span class="sxs-lookup"><span data-stu-id="15d38-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="15d38-147">`Component1`Bileşen, kitaplık oluşturulduğunda RCL proje şablonu tarafından otomatik olarak eklenen örnek bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="15d38-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="15d38-148">`Component1`Bileşene ad alanını kullanarak başvurun:</span><span class="sxs-lookup"><span data-stu-id="15d38-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="15d38-149">Alternatif olarak, kitaplığı bir yönergeyle kapsama taşıyın [`@using`](xref:mvc/views/razor#using) ve bileşeni ad alanı olmadan kullanın:</span><span class="sxs-lookup"><span data-stu-id="15d38-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="15d38-150">İsteğe bağlı olarak, `@using ComponentLibrary` `_Import.razor` kitaplığın bileşenlerini bir projenin tamamına kullanılabilir hale getirmek için en üst düzey dosyaya yönergesini dahil edin.</span><span class="sxs-lookup"><span data-stu-id="15d38-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="15d38-151">`_Import.razor`Ad alanını tek bir bileşene veya bir klasör içindeki bileşen kümesine uygulamak için herhangi bir düzeydeki bir dosyaya yönerge ekleyin.</span><span class="sxs-lookup"><span data-stu-id="15d38-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This :::no-loc(Blazor)::: component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="15d38-152">`Component1` `my-component` CSS sınıfını sağlamak için, uygulamanın `wwwroot/index.html` dosya ( :::no-loc(Blazor WebAssembly)::: ) veya `Pages/_Host.cshtml` dosyasında () kitaplığın stil sayfasına bağlanın :::no-loc(Blazor Server)::: :</span><span class="sxs-lookup"><span data-stu-id="15d38-152">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="15d38-153">:::no-loc(Razor):::Statik varlıklar içeren bir bileşen sınıfı kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="15d38-153">Create a :::no-loc(Razor)::: components class library with static assets</span></span>

<span data-ttu-id="15d38-154">RCL statik varlıkları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="15d38-154">An RCL can include static assets.</span></span> <span data-ttu-id="15d38-155">Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="15d38-155">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="15d38-156">Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="15d38-156">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="15d38-157">Birden çok barındırılan uygulamaya bileşenleri ve statik varlıkları sağlama :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="15d38-157">Supply components and static assets to multiple hosted :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="15d38-158">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="15d38-158">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="15d38-159">İçin tarayıcı uyumluluk Çözümleyicisi :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="15d38-159">Browser compatibility analyzer for :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="15d38-160">:::no-loc(Blazor WebAssembly)::: uygulamalar tam .NET API yüzey alanını hedefletir, ancak tüm .NET API 'Leri, tarayıcı korumalı alan kısıtlamaları nedeniyle WebAssembly üzerinde desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="15d38-160">:::no-loc(Blazor WebAssembly)::: apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="15d38-161"><xref:System.PlatformNotSupportedException>WebAssembly üzerinde çalışırken desteklenmeyen API 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="15d38-161">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="15d38-162">Uygulama, uygulamanın hedef platformları tarafından desteklenmeyen API 'Ler kullandığında, bir platform uyumluluğu Çözümleyicisi geliştiriciyi uyarır.</span><span class="sxs-lookup"><span data-stu-id="15d38-162">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="15d38-163">:::no-loc(Blazor WebAssembly):::Uygulamalar için bu, API 'lerin tarayıcılarda desteklendiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="15d38-163">For :::no-loc(Blazor WebAssembly)::: apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="15d38-164">Uyumluluk Çözümleyicisi için .NET Framework API 'Lerine açıklama ekleme işlemi devam eder, bu nedenle tüm .NET Framework API 'SI Şu anda açıklanmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="15d38-164">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="15d38-165">:::no-loc(Blazor WebAssembly):::ve :::no-loc(Razor)::: sınıf kitaplığı projeleri *automatically* `browser` , MSBuild öğesiyle desteklenen bir platform olarak ekleyerek tarayıcı uyumluluk denetimlerini otomatik olarak etkinleştirir `SupportedPlatform` .</span><span class="sxs-lookup"><span data-stu-id="15d38-165">:::no-loc(Blazor WebAssembly)::: and :::no-loc(Razor)::: class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="15d38-166">Kitaplık geliştiricileri, `SupportedPlatform` özelliği etkinleştirmek için öğeyi bir kitaplığın proje dosyasına el ile ekleyebilir:</span><span class="sxs-lookup"><span data-stu-id="15d38-166">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="15d38-167">Bir kitaplık yazarken belirli bir API 'nin şu şekilde belirterek tarayıcılarda desteklenmediğini belirtin `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :</span><span class="sxs-lookup"><span data-stu-id="15d38-167">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="15d38-168">Daha fazla bilgi için bkz. [belirli platformlarda API 'Leri açıklama ekleme (DotNet/Designs GitHub deposu](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="15d38-168">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="15d38-169">:::no-loc(Blazor)::: JavaScript yalıtımı ve nesne başvuruları</span><span class="sxs-lookup"><span data-stu-id="15d38-169">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="15d38-170">:::no-loc(Blazor)::: Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="15d38-170">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="15d38-171">JavaScript yalıtımı aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="15d38-171">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="15d38-172">İçeri aktarılan JavaScript artık genel ad alanını pollutes.</span><span class="sxs-lookup"><span data-stu-id="15d38-172">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="15d38-173">Kitaplık ve bileşenlerin tüketicileri, ilgili JavaScript 'ı el ile içeri aktarmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="15d38-173">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="15d38-174">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="15d38-174">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="15d38-175">NuGet 'i derleyin, paketleyebilir ve iade edin</span><span class="sxs-lookup"><span data-stu-id="15d38-175">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="15d38-176">Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="15d38-176">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="15d38-177">Paketleme, komut [`dotnet pack`](/dotnet/core/tools/dotnet-pack) kabuğu 'nda komutu kullanılarak gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="15d38-177">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="15d38-178">Komut kabuğunda komutunu kullanarak paketi NuGet 'e yükleyin [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) .</span><span class="sxs-lookup"><span data-stu-id="15d38-178">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15d38-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="15d38-179">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="15d38-180">Kitaplığa bir XML ara dili (IL) ayarlayıcısı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="15d38-180">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="15d38-181">Kitaplığa bir XML ara dili (IL) bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="15d38-181">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
