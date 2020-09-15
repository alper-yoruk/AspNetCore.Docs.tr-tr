---
title: ASP.NET Core Razor bileşenleri sınıf kitaplıkları
author: guardrex
description: Bileşenlerin Blazor bir dış bileşen kitaplığından uygulamalara nasıl dahil edileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 82969bf92965bfdeb1d1474ab47ca74ecbe6dd97
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080309"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="090f4-103">ASP.NET Core Razor bileşenleri sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="090f4-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="090f4-104">[Simon Timms](https://github.com/stimms) tarafından</span><span class="sxs-lookup"><span data-stu-id="090f4-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="090f4-105">Bileşenler, projeler genelinde bir [ Razor sınıf kitaplığı 'NDA (RCL)](xref:razor-pages/ui-class) paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="090f4-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="090f4-106">Bir \* Razor bileşen sınıfı kitaplığı\* , şuradan eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="090f4-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="090f4-107">Çözümdeki başka bir proje.</span><span class="sxs-lookup"><span data-stu-id="090f4-107">Another project in the solution.</span></span>
* <span data-ttu-id="090f4-108">Bir NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="090f4-108">A NuGet package.</span></span>
* <span data-ttu-id="090f4-109">Başvurulan bir .NET kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="090f4-109">A referenced .NET library.</span></span>

<span data-ttu-id="090f4-110">Bileşenler normal .NET türleri olduğu gibi, bir RCL tarafından sunulan bileşenler normal .NET derlemelerdir.</span><span class="sxs-lookup"><span data-stu-id="090f4-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="090f4-111">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="090f4-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="090f4-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="090f4-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="090f4-113">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="090f4-113">Create a new project.</span></span>
1. <span data-ttu-id="090f4-114">\*\* Razor Sınıf kitaplığı\*\*' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="090f4-115">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-115">Select **Next**.</span></span>
1. <span data-ttu-id="090f4-116">**Yeni Razor sınıf kitaplığı oluştur** iletişim kutusunda **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="090f4-117">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="090f4-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="090f4-118">Bu konudaki örneklerde proje adı kullanılır `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="090f4-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="090f4-119">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-119">Select **Create**.</span></span>
1. <span data-ttu-id="090f4-120">RCL 'yi bir çözüme ekleyin:</span><span class="sxs-lookup"><span data-stu-id="090f4-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="090f4-121">Çözüme sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="090f4-121">Right-click the solution.</span></span> <span data-ttu-id="090f4-122">**Add**  >  **Varolan proje**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="090f4-123">RCL 'nin proje dosyasına gidin.</span><span class="sxs-lookup"><span data-stu-id="090f4-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="090f4-124">RCL 'nin proje dosyasını () seçin `.csproj` .</span><span class="sxs-lookup"><span data-stu-id="090f4-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="090f4-125">Uygulamadan RCL 'ye bir başvuru ekleyin:</span><span class="sxs-lookup"><span data-stu-id="090f4-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="090f4-126">Uygulama projesine sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="090f4-126">Right-click the app project.</span></span> <span data-ttu-id="090f4-127">Başvuru **Ekle**' yi seçin  >  **Reference**.</span><span class="sxs-lookup"><span data-stu-id="090f4-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="090f4-128">RCL projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-128">Select the RCL project.</span></span> <span data-ttu-id="090f4-129">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="090f4-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="090f4-130">Şablondan RCL oluşturulurken **destek sayfaları ve görünümler** onay kutusu işaretliyse, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne da bir dosya ekleyin Razor :</span><span class="sxs-lookup"><span data-stu-id="090f4-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="090f4-131">Dosyayı oluşturulan projenin köküne el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="090f4-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="090f4-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="090f4-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="090f4-133">Komut kabuğunda komutuyla \*\* Razor sınıf kitaplığı\*\* şablonu ( `razorclasslib` ) kullanın [`dotnet new`](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="090f4-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="090f4-134">Aşağıdaki örnekte adlı bir RCL oluşturulur `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="090f4-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="090f4-135">Komut yürütüldüğünde, tutan klasör `ComponentLibrary` otomatik olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="090f4-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="090f4-136">`-s|--support-pages-and-views`Şablondan RCL oluşturulurken anahtar kullanılıyorsa, `_Imports.razor` bileşen yazmayı etkinleştirmek için aşağıdaki içeriğe sahip oluşturulan projenin köküne bir dosya ekleyin Razor :</span><span class="sxs-lookup"><span data-stu-id="090f4-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="090f4-137">Dosyayı oluşturulan projenin köküne el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="090f4-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="090f4-138">Kitaplığı var olan bir projeye eklemek için komut [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) kabuğu 'nda komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="090f4-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="090f4-139">Aşağıdaki örnekte, RCL uygulamaya eklenir.</span><span class="sxs-lookup"><span data-stu-id="090f4-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="090f4-140">Uygulamanın proje klasöründen, kitaplığın yolunu kullanarak aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="090f4-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="090f4-141">Kitaplık bileşeni kullanma</span><span class="sxs-lookup"><span data-stu-id="090f4-141">Consume a library component</span></span>

<span data-ttu-id="090f4-142">Başka bir projedeki bir kitaplıkta tanımlanan bileşenleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="090f4-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="090f4-143">Ad alanı ile tam tür adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="090f4-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="090f4-144">Kullanım Razor [`@using`](xref:mvc/views/razor#using) yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="090f4-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="090f4-145">Tek tek bileşenler, ada göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="090f4-145">Individual components can be added by name.</span></span>

<span data-ttu-id="090f4-146">Aşağıdaki örneklerde, `ComponentLibrary` bileşeni () içeren bir bileşen kitaplığı vardır `Component1` `Component1.razor` .</span><span class="sxs-lookup"><span data-stu-id="090f4-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="090f4-147">`Component1`Bileşen, kitaplık oluşturulduğunda RCL proje şablonu tarafından otomatik olarak eklenen örnek bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="090f4-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="090f4-148">`Component1`Bileşene ad alanını kullanarak başvurun:</span><span class="sxs-lookup"><span data-stu-id="090f4-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="090f4-149">Alternatif olarak, kitaplığı bir yönergeyle kapsama taşıyın [`@using`](xref:mvc/views/razor#using) ve bileşeni ad alanı olmadan kullanın:</span><span class="sxs-lookup"><span data-stu-id="090f4-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="090f4-150">İsteğe bağlı olarak, `@using ComponentLibrary` `_Import.razor` kitaplığın bileşenlerini bir projenin tamamına kullanılabilir hale getirmek için en üst düzey dosyaya yönergesini dahil edin.</span><span class="sxs-lookup"><span data-stu-id="090f4-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="090f4-151">`_Import.razor`Ad alanını tek bir bileşene veya bir klasör içindeki bileşen kümesine uygulamak için herhangi bir düzeydeki bir dosyaya yönerge ekleyin.</span><span class="sxs-lookup"><span data-stu-id="090f4-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="090f4-152">`Component1` `my-component` Bileşene CSS sınıfı sağlamak için, ' deki Framework [ `Link` bileşeni](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) kullanılarak kitaplığın stil sayfasına bağlanın `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="090f4-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="090f4-153">Uygulama genelinde stil sayfasını sağlamak için, bunun yerine uygulamanın `wwwroot/index.html` dosya ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyasında () kitaplığın stil sayfasına bağlanabilirsiniz Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="090f4-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="090f4-154">`Link`Bileşen bir alt bileşende kullanıldığında, bileşenin alt öğesi işlendiği sürece bağlantılı varlık ana bileşenin diğer alt bileşenleri için de kullanılabilir `Link` .</span><span class="sxs-lookup"><span data-stu-id="090f4-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="090f4-155">`Link`Bir alt bileşende bileşeni kullanma ve veya ' a bir HTML etiketi yerleştirme arasındaki ayrım, `<link>` `wwwroot/index.html` `Pages/_Host.cshtml` çerçeve bileşeninin işlenmiş html etiketinin:</span><span class="sxs-lookup"><span data-stu-id="090f4-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="090f4-156">, Uygulama durumu ile değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="090f4-156">Can be modified by application state.</span></span> <span data-ttu-id="090f4-157">Sabit kodlanmış `<link>` HTML etiketi uygulama durumu tarafından değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="090f4-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="090f4-158">`<head>`Üst bileşen artık IŞLENMEDIĞINDE HTML 'den kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="090f4-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="090f4-159">`Component1` `my-component` CSS sınıfını sağlamak için, uygulamanın `wwwroot/index.html` dosya ( Blazor WebAssembly ) veya `Pages/_Host.cshtml` dosyasında () kitaplığın stil sayfasına bağlanın Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="090f4-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="090f4-160">RazorStatik varlıklar içeren bir bileşen sınıfı kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="090f4-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="090f4-161">RCL statik varlıkları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="090f4-161">An RCL can include static assets.</span></span> <span data-ttu-id="090f4-162">Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="090f4-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="090f4-163">Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="090f4-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="090f4-164">Birden çok barındırılan uygulamaya bileşenleri ve statik varlıkları sağlama Blazor</span><span class="sxs-lookup"><span data-stu-id="090f4-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="090f4-165">Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="090f4-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="090f4-166">NuGet 'i derleyin, paketleyebilir ve iade edin</span><span class="sxs-lookup"><span data-stu-id="090f4-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="090f4-167">Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="090f4-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="090f4-168">Paketleme, komut [`dotnet pack`](/dotnet/core/tools/dotnet-pack) kabuğu 'nda komutu kullanılarak gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="090f4-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="090f4-169">Komut kabuğunda komutunu kullanarak paketi NuGet 'e yükleyin [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) .</span><span class="sxs-lookup"><span data-stu-id="090f4-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="090f4-170">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="090f4-170">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="090f4-171">Kitaplığa bir XML ara dili (IL) ayarlayıcısı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="090f4-171">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="090f4-172">Kitaplığa bir XML ara dili (IL) bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="090f4-172">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
