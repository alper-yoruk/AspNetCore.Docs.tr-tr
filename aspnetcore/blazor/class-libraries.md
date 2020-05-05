---
title: ASP.NET Core Razor bileşenleri sınıf kitaplıkları
author: guardrex
description: Bileşenlerin bir dış bileşen kitaplığından Blazor uygulamalara nasıl dahil edileceğini öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 57f3494fd825b6549c40f56962da2c8076e8fd51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767102"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="f674d-103">ASP.NET Core Razor bileşenleri sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="f674d-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="f674d-104">[Simon Timms](https://github.com/stimms) tarafından</span><span class="sxs-lookup"><span data-stu-id="f674d-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="f674d-105">Bileşenler, projeler genelinde [Razor sınıf kitaplığı 'nda (RCL)](xref:razor-pages/ui-class) paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="f674d-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="f674d-106">*Razor bileşenleri sınıf kitaplığı* , şuradan eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="f674d-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="f674d-107">Çözümdeki başka bir proje.</span><span class="sxs-lookup"><span data-stu-id="f674d-107">Another project in the solution.</span></span>
* <span data-ttu-id="f674d-108">Bir NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="f674d-108">A NuGet package.</span></span>
* <span data-ttu-id="f674d-109">Başvurulan bir .NET kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="f674d-109">A referenced .NET library.</span></span>

<span data-ttu-id="f674d-110">Bileşenler normal .NET türleri olduğu gibi, bir RCL tarafından sunulan bileşenler normal .NET derlemelerdir.</span><span class="sxs-lookup"><span data-stu-id="f674d-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f674d-111">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="f674d-111">Create an RCL</span></span>

<span data-ttu-id="f674d-112">Ortamınızı Blazor için yapılandırmak üzere <xref:blazor/get-started> makalesindeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="f674d-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f674d-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f674d-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f674d-114">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="f674d-114">Create a new project.</span></span>
1. <span data-ttu-id="f674d-115">**Razor sınıfı kitaplığı**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="f674d-116">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-116">Select **Next**.</span></span>
1. <span data-ttu-id="f674d-117">**Yeni bir Razor sınıf kitaplığı oluştur** Iletişim kutusunda **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="f674d-118">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="f674d-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f674d-119">Bu konudaki örneklerde proje adı `MyComponentLib1`kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f674d-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="f674d-120">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-120">Select **Create**.</span></span>
1. <span data-ttu-id="f674d-121">RCL 'yi bir çözüme ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f674d-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="f674d-122">Çözüme sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f674d-122">Right-click the solution.</span></span> <span data-ttu-id="f674d-123">**Varolan proje** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="f674d-124">RCL 'nin proje dosyasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f674d-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="f674d-125">RCL 'nin proje dosyasını (*. csproj*) seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="f674d-126">Uygulamadan RCL 'ye bir başvuru ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f674d-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="f674d-127">Uygulama projesine sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f674d-127">Right-click the app project.</span></span> <span data-ttu-id="f674d-128">Başvuru **Ekle** > **Reference**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="f674d-129">RCL projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-129">Select the RCL project.</span></span> <span data-ttu-id="f674d-130">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f674d-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="f674d-131">Şablondan RCL oluşturulurken **destek sayfaları ve görünümler** onay kutusu Işaretliyse, Razor bileşeni yazma özelliğini etkinleştirmek için aşağıdaki içeriklerle oluşturulan projenin köküne bir *_Imports. Razor* dosyası da ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f674d-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="f674d-132">Dosyayı oluşturulan projenin köküne el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f674d-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f674d-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f674d-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="f674d-134">Bir komut kabuğunda [DotNet New](/dotnet/core/tools/dotnet-new) komutuyla **Razor sınıf kitaplığı** şablonunu (`razorclasslib`) kullanın.</span><span class="sxs-lookup"><span data-stu-id="f674d-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="f674d-135">Aşağıdaki örnekte adlı `MyComponentLib1`bir RCL oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f674d-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="f674d-136">Komut yürütüldüğünde, tutan `MyComponentLib1` klasör otomatik olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f674d-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="f674d-137">Şablondan RCL oluşturulurken `-s|--support-pages-and-views` anahtar kullanılıyorsa, Razor bileşeni yazma özelliğini etkinleştirmek için aşağıdaki içeriklerle oluşturulan projenin köküne bir *_Imports. Razor* dosyası da ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f674d-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="f674d-138">Dosyayı oluşturulan projenin köküne el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f674d-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="f674d-139">Kitaplığı var olan bir projeye eklemek için, bir komut kabuğunda [DotNet Add Reference](/dotnet/core/tools/dotnet-add-reference) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="f674d-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="f674d-140">Aşağıdaki örnekte, RCL uygulamaya eklenir.</span><span class="sxs-lookup"><span data-stu-id="f674d-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="f674d-141">Uygulamanın proje klasöründen, kitaplığın yolunu kullanarak aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="f674d-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="f674d-142">Kitaplık bileşeni kullanma</span><span class="sxs-lookup"><span data-stu-id="f674d-142">Consume a library component</span></span>

<span data-ttu-id="f674d-143">Başka bir projedeki bir kitaplıkta tanımlanan bileşenleri kullanmak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f674d-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="f674d-144">Ad alanı ile tam tür adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="f674d-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="f674d-145">Using yönergesini kullanın Razor. [ \@](xref:mvc/views/razor#using)</span><span class="sxs-lookup"><span data-stu-id="f674d-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="f674d-146">Tek tek bileşenler, ada göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f674d-146">Individual components can be added by name.</span></span>

<span data-ttu-id="f674d-147">Aşağıdaki örneklerde, `MyComponentLib1` bir `SalesReport` bileşeni içeren bir bileşen kitaplığı vardır.</span><span class="sxs-lookup"><span data-stu-id="f674d-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="f674d-148">Bileşene `SalesReport` , ad alanı ile tam tür adı kullanılarak başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="f674d-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="f674d-149">Ayrıca, kitaplık bir `@using` yönergeyle kapsama alınırsa bileşene de başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="f674d-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="f674d-150">Kitaplığın bileşenlerini `@using MyComponentLib1` bir projenin tamamına kullanılabilir hale getirmek için üst düzey *_Import. Razor* dosyasına yönergesini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f674d-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="f674d-151">Ad alanını tek bir sayfaya veya bir klasör içindeki sayfa kümesine uygulamak için herhangi bir düzeydeki bir *_Import. Razor* dosyasına yönergesini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f674d-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="f674d-152">Statik varlıklar Razor içeren bir bileşen sınıfı kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="f674d-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="f674d-153">RCL statik varlıkları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f674d-153">An RCL can include static assets.</span></span> <span data-ttu-id="f674d-154">Statik varlıklar, kitaplığı kullanan tüm uygulamalar tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f674d-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="f674d-155">Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="f674d-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="f674d-156">NuGet 'i derleyin, paketleyebilir ve iade edin</span><span class="sxs-lookup"><span data-stu-id="f674d-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="f674d-157">Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, paketlemeden ve tüm kitaplıkları NuGet 'e dağıtmadan farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="f674d-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="f674d-158">Paketleme, bir komut kabuğu 'nda [DotNet Pack](/dotnet/core/tools/dotnet-pack) komutu kullanılarak gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="f674d-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="f674d-159">Bir komut kabuğunda [DotNet NuGet Push](/dotnet/core/tools/dotnet-nuget-push) komutunu kullanarak paketi NuGet 'e yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f674d-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f674d-160">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f674d-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="f674d-161">Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="f674d-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
