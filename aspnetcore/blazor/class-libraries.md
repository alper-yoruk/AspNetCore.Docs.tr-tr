---
title: ASP.NET Core Razor bileşenleri sınıf kütüphaneleri
author: guardrex
description: Bileşenlerin harici bileşen kitaplığından uygulamalara Blazor nasıl eklenebilir olduğunu keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218772"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="bf301-103">ASP.NET Core Razor bileşenleri sınıf kütüphaneleri</span><span class="sxs-lookup"><span data-stu-id="bf301-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="bf301-104">Yazar: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="bf301-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="bf301-105">Bileşenler, projeler arasında [Bir Razor sınıf kitaplığında (RCL)](xref:razor-pages/ui-class) paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="bf301-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="bf301-106">*Razor bileşenleri sınıf kitaplığı* şunları arasından eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="bf301-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="bf301-107">Çözümde başka bir proje.</span><span class="sxs-lookup"><span data-stu-id="bf301-107">Another project in the solution.</span></span>
* <span data-ttu-id="bf301-108">Bir NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="bf301-108">A NuGet package.</span></span>
* <span data-ttu-id="bf301-109">Başvurulan .NET kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="bf301-109">A referenced .NET library.</span></span>

<span data-ttu-id="bf301-110">Bileşenler normal .NET türleri olduğu gibi, RCL tarafından sağlanan bileşenler de normal .NET derlemeleridir.</span><span class="sxs-lookup"><span data-stu-id="bf301-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="bf301-111">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf301-111">Create an RCL</span></span>

<span data-ttu-id="bf301-112">Blazor için <xref:blazor/get-started> ortamınızı yapılandırmak için makaledeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="bf301-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf301-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf301-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bf301-114">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="bf301-114">Create a new project.</span></span>
1. <span data-ttu-id="bf301-115">**Razor Class Kitaplığı'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="bf301-116">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-116">Select **Next**.</span></span>
1. <span data-ttu-id="bf301-117">Yeni **bir Razor sınıfı kitaplık oluştur** iletişim kutusunda **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="bf301-118">**Proje adı** alanında bir proje adı sağlayın veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="bf301-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="bf301-119">Bu konudaki örneklerde proje `MyComponentLib1`adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bf301-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="bf301-120">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-120">Select **Create**.</span></span>
1. <span data-ttu-id="bf301-121">RCL'yi çözüme ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bf301-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="bf301-122">Çözüme sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="bf301-122">Right-click the solution.</span></span> <span data-ttu-id="bf301-123">**Varolan Proje** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="bf301-124">RCL'nin proje dosyasına gidin.</span><span class="sxs-lookup"><span data-stu-id="bf301-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="bf301-125">RCL'nin proje dosyasını seçin (*.csproj*).</span><span class="sxs-lookup"><span data-stu-id="bf301-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="bf301-126">Uygulamadan RCL bir başvuru ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bf301-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="bf301-127">Uygulama projesine sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="bf301-127">Right-click the app project.</span></span> <span data-ttu-id="bf301-128">**Referans** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="bf301-129">RCL projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-129">Select the RCL project.</span></span> <span data-ttu-id="bf301-130">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf301-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="bf301-131">Şablondan RCL oluştururken **Destek sayfaları ve görünümleri** onay kutusu seçilirse, razor bileşeninin yazılmasını etkinleştirmek için oluşturulan projenin köküne aşağıdaki içeriklerle bir *_Imports.razor* dosyası da ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bf301-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="bf301-132">Dosyayı oluşturulan projenin kökünü el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bf301-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bf301-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bf301-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="bf301-134">Bir komut kabuğunda`razorclasslib` [dotnet yeni](/dotnet/core/tools/dotnet-new) komutu ile Razor Class **Library** şablonunu ( ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="bf301-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="bf301-135">Aşağıdaki örnekte, bir RCL `MyComponentLib1`adlı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="bf301-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="bf301-136">Komut yürütüldüğünde tutan `MyComponentLib1` klasör otomatik olarak oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="bf301-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="bf301-137">Şablondan `-s|--support-pages-and-views` RCL oluştururken anahtar kullanılırsa, Razor bileşeninin yazılmasını etkinleştirmek için oluşturulan projenin köküne bir *_Imports.razor* dosyası da ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bf301-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="bf301-138">Dosyayı oluşturulan projenin kökünü el ile ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bf301-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="bf301-139">Kitaplığı varolan bir projeye eklemek için, komut kabuğundaki [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="bf301-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="bf301-140">Aşağıdaki örnekte, RCL uygulamaya eklenir.</span><span class="sxs-lookup"><span data-stu-id="bf301-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="bf301-141">Uygulamanın proje klasöründen kitaplık yolunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="bf301-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="bf301-142">Kitaplık bileşenini tüketin</span><span class="sxs-lookup"><span data-stu-id="bf301-142">Consume a library component</span></span>

<span data-ttu-id="bf301-143">Başka bir projede kitaplıkta tanımlanan bileşenleri tüketmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="bf301-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="bf301-144">Ad alanı yla tam tür adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="bf301-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="bf301-145">Razor'ın direktifini [ \@kullan.](xref:mvc/views/razor#using)</span><span class="sxs-lookup"><span data-stu-id="bf301-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="bf301-146">Tek tek bileşenler ada göre eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="bf301-146">Individual components can be added by name.</span></span>

<span data-ttu-id="bf301-147">Aşağıdaki örneklerde, `MyComponentLib1` bir bileşen içeren `SalesReport` bir bileşen kitaplığı dır.</span><span class="sxs-lookup"><span data-stu-id="bf301-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="bf301-148">Bileşen, `SalesReport` ad alanı ile tam tür adı kullanılarak başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="bf301-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="bf301-149">Kitaplık bir `@using` yönergeyle kapsam haline getirilirse bileşene de başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="bf301-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="bf301-150">Kitaplığın `@using MyComponentLib1` bileşenlerini tüm proje için kullanılabilir hale getirmek için yönergeleri üst düzey *_Import.jilet* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bf301-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="bf301-151">Ad alanını bir klasördeki tek bir sayfaya veya sayfa kümesine uygulamak için yönergeleri herhangi bir düzeyde *ki _Import.razor* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bf301-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="bf301-152">Statik varlıklarla bir Razor bileşenleri sınıf kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf301-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="bf301-153">Bir RCL statik varlıkları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="bf301-153">An RCL can include static assets.</span></span> <span data-ttu-id="bf301-154">Statik varlıklar kitaplığı tüketen herhangi bir uygulama için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bf301-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="bf301-155">Daha fazla bilgi için bkz. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="bf301-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="bf301-156">NuGet'e oluşturun, paketlein ve gönderiyapın</span><span class="sxs-lookup"><span data-stu-id="bf301-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="bf301-157">Bileşen kitaplıkları standart .NET kitaplıkları olduğundan, bunları NuGet'e paketlemek ve göndermenin herhangi bir kitaplığı Paketlemeve NuGet'e göndermesinden hiçbir farkı yoktur.</span><span class="sxs-lookup"><span data-stu-id="bf301-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="bf301-158">Paketleme komut kabuğundaki [dotnet paketi](/dotnet/core/tools/dotnet-pack) komutu kullanılarak gerçekleştirilir:</span><span class="sxs-lookup"><span data-stu-id="bf301-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="bf301-159">Paketi bir komut kabuğundaki [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) komutunu kullanarak NuGet'e yükleyin.</span><span class="sxs-lookup"><span data-stu-id="bf301-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf301-160">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bf301-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="bf301-161">Kitaplık için XML bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="bf301-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
