---
title: 'ASP.NET Core giriş :::no-loc(Blazor):::'
author: guardrex
description: 'ASP.NET Core uygulamasında :::no-loc(Blazor)::: .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
no-loc:
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
uid: blazor/index
ms.openlocfilehash: bae3e96021971e373ad743a0b52da7f69d839c40
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690592"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="8b61e-103">ASP.NET Core giriş :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="8b61e-103">Introduction to ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="8b61e-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="8b61e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8b61e-105">*Hoş geldiniz :::no-loc(Blazor)::: !*</span><span class="sxs-lookup"><span data-stu-id="8b61e-105">*Welcome to :::no-loc(Blazor):::!*</span></span>

<span data-ttu-id="8b61e-106">:::no-loc(Blazor)::: , [.net](/dotnet/standard/tour)ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="8b61e-106">:::no-loc(Blazor)::: is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="8b61e-107">[JavaScript](https://www.javascript.com)yerine [C#](/dotnet/csharp/) kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b61e-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="8b61e-108">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b61e-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="8b61e-109">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="8b61e-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="8b61e-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="8b61e-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="8b61e-111">İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="8b61e-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="8b61e-112">JavaScript yerine C# dilinde kod yazın.</span><span class="sxs-lookup"><span data-stu-id="8b61e-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="8b61e-113">[.Net kitaplıklarının](/dotnet/standard/class-libraries)mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="8b61e-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="8b61e-114">Sunucu ve istemci arasında uygulama mantığını paylaşma.</span><span class="sxs-lookup"><span data-stu-id="8b61e-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="8b61e-115">Avantajı. NET ' in performans, güvenilirlik ve güvenlik.</span><span class="sxs-lookup"><span data-stu-id="8b61e-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="8b61e-116">Windows, Linux ve macOS 'ta [Visual Studio](https://visualstudio.microsoft.com) ile üretken olun.</span><span class="sxs-lookup"><span data-stu-id="8b61e-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="8b61e-117">Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b61e-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="8b61e-118">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="8b61e-118">Components</span></span>

<span data-ttu-id="8b61e-119">:::no-loc(Blazor)::: uygulamalar *bileşenleri* temel alır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-119">:::no-loc(Blazor)::: apps are based on *components* .</span></span> <span data-ttu-id="8b61e-120">İçindeki bir bileşeni :::no-loc(Blazor)::: , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-120">A component in :::no-loc(Blazor)::: is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="8b61e-121">Bileşenler, [.NET derlemeleri](/dotnet/standard/assembly/) yerleşik olarak bulunan .NET C# sınıflarıdır:</span><span class="sxs-lookup"><span data-stu-id="8b61e-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="8b61e-122">Esnek kullanıcı arabirimi işleme mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="8b61e-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="8b61e-123">Kullanıcı olaylarını işleyin.</span><span class="sxs-lookup"><span data-stu-id="8b61e-123">Handle user events.</span></span>
* <span data-ttu-id="8b61e-124">İç içe ve yeniden kullanılabilir olabilir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-124">Can be nested and reused.</span></span>
* <span data-ttu-id="8b61e-125">, [ :::no-loc(Razor)::: Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-125">Can be shared and distributed as [:::no-loc(Razor)::: class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="8b61e-126">Bileşen sınıfı, genellikle [:::no-loc(Razor):::](xref:mvc/views/razor) bir dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır `.razor` .</span><span class="sxs-lookup"><span data-stu-id="8b61e-126">The component class is usually written in the form of a [:::no-loc(Razor):::](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="8b61e-127">İçindeki bileşenler :::no-loc(Blazor)::: , resmi olarak *:::no-loc(Razor)::: bileşen* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-127">Components in :::no-loc(Blazor)::: are formally referred to as *:::no-loc(Razor)::: components* .</span></span> <span data-ttu-id="8b61e-128">:::no-loc(Razor)::: , HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-128">:::no-loc(Razor)::: is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="8b61e-129">:::no-loc(Razor)::: Visual Studio 'da [IntelliSense](/visualstudio/ide/using-intellisense) programlama desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="8b61e-129">:::no-loc(Razor)::: allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="8b61e-130">:::no-loc(Razor)::: Sayfalar ve MVC de kullanır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b61e-130">:::no-loc(Razor)::: Pages and MVC also use :::no-loc(Razor):::.</span></span> <span data-ttu-id="8b61e-131">:::no-loc(Razor):::Bir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-131">Unlike :::no-loc(Razor)::: Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="8b61e-132">:::no-loc(Blazor)::: UI bileşimi için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-132">:::no-loc(Blazor)::: uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="8b61e-133">Aşağıdaki :::no-loc(Razor)::: biçimlendirme `Dialog.razor` bir iletişim kutusu görüntüleyen ve Kullanıcı bir düğme seçtiğinde bir olayı işleyen bir bileşeni () gösterir:</span><span class="sxs-lookup"><span data-stu-id="8b61e-133">The following :::no-loc(Razor)::: markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="8b61e-134">Yukarıdaki örnekte, `OnYes` düğme olayının tetiklediği bir C# yöntemidir `onclick` .</span><span class="sxs-lookup"><span data-stu-id="8b61e-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="8b61e-135">İletişim kutusunun Text ( `ChildContent` ) ve title ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan aşağıdaki bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="8b61e-136">`Dialog`Bileşen, BIR HTML etiketi kullanılarak başka bir bileşen içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="8b61e-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="8b61e-137">Aşağıdaki örnekte, `Index` bileşen ( `Pages/Index.razor` ) önceki `Dialog` bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="8b61e-138">Etiketin özniteliği, `Title` bileşen özelliğine bir başlık değeri geçirir `Dialog` `Title` .</span><span class="sxs-lookup"><span data-stu-id="8b61e-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="8b61e-139">`Dialog`Bileşenin metni (), `ChildContent` öğesinin içeriği tarafından ayarlanır `<Dialog>` .</span><span class="sxs-lookup"><span data-stu-id="8b61e-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="8b61e-140">`Dialog`Bileşen `Index` bileşene eklendiğinde, [Visual Studio 'da IntelliSense](/visualstudio/ide/using-intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about :::no-loc(Blazor):::?
</Dialog>
```

<span data-ttu-id="8b61e-141">İletişim kutusu, `Index` bileşene bir tarayıcıda erişildiğinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="8b61e-142">Düğme Kullanıcı tarafından seçildiğinde, tarayıcının geliştirici araçları konsolunda yöntemi tarafından yazılan ileti görüntülenir `OnYes` :</span><span class="sxs-lookup"><span data-stu-id="8b61e-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![İletişim kutusu bileşeni, Dizin bileşeninin içinde iç içe geçmiş tarayıcıda işlendi.](index/_static/dialog.png)

<span data-ttu-id="8b61e-146">Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı* adlı, tarayıcı [belge nesne modeli (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) ' ın bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree* , which is used to update the UI in a flexible and efficient way.</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="8b61e-147">:::no-loc(Blazor WebAssembly)::: , .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik [tek sayfalı uygulama (Spa) çerçevesidir](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) .</span><span class="sxs-lookup"><span data-stu-id="8b61e-147">:::no-loc(Blazor WebAssembly)::: is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="8b61e-148">:::no-loc(Blazor WebAssembly)::: , eklentiler olmadan açık Web standartları kullanır veya kodu diğer dillere yeniden derler.</span><span class="sxs-lookup"><span data-stu-id="8b61e-148">:::no-loc(Blazor WebAssembly)::: uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="8b61e-149">:::no-loc(Blazor WebAssembly)::: mobil tarayıcılar dahil tüm modern web tarayıcılarında çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-149">:::no-loc(Blazor WebAssembly)::: works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="8b61e-150">Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) (kısaltılmış) tarafından mümkün hale getirilir `wasm` .</span><span class="sxs-lookup"><span data-stu-id="8b61e-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="8b61e-151">WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="8b61e-152">WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="8b61e-153">WebAssembly Code, JavaScript birlikte *çalışabilirliği* olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir, genellikle *JavaScript birlikte çalışma* veya *js birlikte çalışma* olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* , often shortened to *JavaScript interop* or *JS interop* .</span></span> <span data-ttu-id="8b61e-154">Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (Blazor WebAssembly)::: .NET kodunu WebAssembly ile tarayıcıda çalıştırır.](index/_static/blazor-webassembly.png)

<span data-ttu-id="8b61e-156">Bir :::no-loc(Blazor WebAssembly)::: uygulama bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="8b61e-156">When a :::no-loc(Blazor WebAssembly)::: app is built and run in a browser:</span></span>

* <span data-ttu-id="8b61e-157">C# kod dosyaları ve :::no-loc(Razor)::: dosyaları .net Derlemeleriyle derlenir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-157">C# code files and :::no-loc(Razor)::: files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="8b61e-158">Derlemeler ve [.NET çalışma zamanı](/dotnet/framework/get-started/overview) tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="8b61e-159">:::no-loc(Blazor WebAssembly)::: .NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-159">:::no-loc(Blazor WebAssembly)::: bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="8b61e-160">:::no-loc(Blazor WebAssembly):::Çalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-160">The :::no-loc(Blazor WebAssembly)::: runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="8b61e-161">Yayınlanan uygulamanın boyutu, *Yük boyutu* , uygulamanın useyeteneğinin önemli bir performans etkendir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-161">The size of the published app, its *payload size* , is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="8b61e-162">Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek.</span><span class="sxs-lookup"><span data-stu-id="8b61e-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="8b61e-163">:::no-loc(Blazor WebAssembly)::: yükleme sürelerini azaltmak için yük boyutunu iyileştirir:</span><span class="sxs-lookup"><span data-stu-id="8b61e-163">:::no-loc(Blazor WebAssembly)::: optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="8b61e-164">Kullanılmayan kod, [ara dil (IL) ayarlayıcısı](xref:blazor/host-and-deploy/configure-trimmer)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="8b61e-165">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="8b61e-166">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="8b61e-167">Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:blazor/host-and-deploy/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="8b61e-168">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="8b61e-169">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## :::no-loc(Blazor Server):::

<span data-ttu-id="8b61e-170">:::no-loc(Blazor)::: Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-170">:::no-loc(Blazor)::: decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="8b61e-171">*:::no-loc(Blazor Server):::* ASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="8b61e-171">*:::no-loc(Blazor Server):::* provides support for hosting :::no-loc(Razor)::: components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="8b61e-172">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [:::no-loc(SignalR):::](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="8b61e-172">UI updates are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="8b61e-173">Çalışma zamanı tutamaçları:</span><span class="sxs-lookup"><span data-stu-id="8b61e-173">The runtime handles:</span></span>

* <span data-ttu-id="8b61e-174">Tarayıcıdan sunucusuna kullanıcı arabirimi olayları gönderiliyor.</span><span class="sxs-lookup"><span data-stu-id="8b61e-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="8b61e-175">Sunucu tarafından geri gönderilen işlenen bileşene Kullanıcı Arabirimi güncelleştirmeleri uygulanıyor.</span><span class="sxs-lookup"><span data-stu-id="8b61e-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="8b61e-176">:::no-loc(Blazor Server):::Tarayıcıyla iletişim kurmak için tarafından kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-176">The connection used by :::no-loc(Blazor Server)::: to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (Blazor Server)::: sunucuda .NET kodu çalıştırır ve şu şekilde istemcisinde Belge Nesne Modeli:: No-Loc (SignalR)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="8b61e-178">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="8b61e-178">JavaScript interop</span></span>

<span data-ttu-id="8b61e-179">Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="8b61e-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="8b61e-180">Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="8b61e-181">C# kodu [JavaScript kodunu çağırabilir](xref:blazor/call-javascript-from-dotnet)ve JavaScript kodu [c# koduna çağrı](xref:blazor/call-dotnet-from-javascript)yapabilir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="8b61e-182">Kod paylaşımı ve .NET Standard</span><span class="sxs-lookup"><span data-stu-id="8b61e-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="8b61e-183">:::no-loc(Blazor):::[.NET Standard](/dotnet/standard/net-standard) :::no-loc(Blazor)::: projelerin .NET Standard belirtimlerine uygun kitaplıklara başvurmalarını sağlayan .NET Standard uygular.</span><span class="sxs-lookup"><span data-stu-id="8b61e-183">:::no-loc(Blazor)::: implements the [.NET Standard](/dotnet/standard/net-standard), which enables :::no-loc(Blazor)::: projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="8b61e-184">.NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="8b61e-185">.NET Standard sınıf kitaplıkları :::no-loc(Blazor)::: , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="8b61e-185">.NET Standard class libraries can be shared across different .NET platforms, such as :::no-loc(Blazor):::, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="8b61e-186">Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="8b61e-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b61e-187">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8b61e-187">Additional resources</span></span>

* [<span data-ttu-id="8b61e-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8b61e-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="8b61e-189">C# Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="8b61e-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="8b61e-190">HTML</span><span class="sxs-lookup"><span data-stu-id="8b61e-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="8b61e-191">[Başar :::no-loc(Blazor)::: ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları</span><span class="sxs-lookup"><span data-stu-id="8b61e-191">[Awesome :::no-loc(Blazor):::](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
