---
title: ASP.NET Core giriş Blazor
author: guardrex
description: ASP.NET Core uygulamasında Blazor .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 79c225a0714562a01afe67bf8e59f3b3f98a6265
ms.sourcegitcommit: e9b8835a02f75b6378b766edb8bab23b14a4192b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97666865"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="60def-103">ASP.NET Core giriş Blazor</span><span class="sxs-lookup"><span data-stu-id="60def-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="60def-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="60def-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="60def-105">*Hoş geldiniz Blazor !*</span><span class="sxs-lookup"><span data-stu-id="60def-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="60def-106">Blazor , [.net](/dotnet/standard/tour)ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="60def-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="60def-107">[JavaScript](https://www.javascript.com)yerine [C#](/dotnet/csharp/) kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="60def-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="60def-108">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="60def-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="60def-109">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="60def-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="60def-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="60def-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="60def-111">İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="60def-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="60def-112">JavaScript yerine C# dilinde kod yazın.</span><span class="sxs-lookup"><span data-stu-id="60def-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="60def-113">[.Net kitaplıklarının](/dotnet/standard/class-libraries)mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="60def-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="60def-114">Sunucu ve istemci arasında uygulama mantığını paylaşma.</span><span class="sxs-lookup"><span data-stu-id="60def-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="60def-115">Avantajı. NET ' in performans, güvenilirlik ve güvenlik.</span><span class="sxs-lookup"><span data-stu-id="60def-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="60def-116">Windows, Linux ve macOS 'ta [Visual Studio](https://visualstudio.microsoft.com) ile üretken olun.</span><span class="sxs-lookup"><span data-stu-id="60def-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="60def-117">Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="60def-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="60def-118">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="60def-118">Components</span></span>

<span data-ttu-id="60def-119">Blazor uygulamalar *bileşenleri* temel alır.</span><span class="sxs-lookup"><span data-stu-id="60def-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="60def-120">İçindeki bir bileşeni Blazor , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.</span><span class="sxs-lookup"><span data-stu-id="60def-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="60def-121">Bileşenler, [.NET derlemeleri](/dotnet/standard/assembly/) yerleşik olarak bulunan .NET C# sınıflarıdır:</span><span class="sxs-lookup"><span data-stu-id="60def-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="60def-122">Esnek kullanıcı arabirimi işleme mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="60def-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="60def-123">Kullanıcı olaylarını işleyin.</span><span class="sxs-lookup"><span data-stu-id="60def-123">Handle user events.</span></span>
* <span data-ttu-id="60def-124">İç içe ve yeniden kullanılabilir olabilir.</span><span class="sxs-lookup"><span data-stu-id="60def-124">Can be nested and reused.</span></span>
* <span data-ttu-id="60def-125">, [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="60def-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="60def-126">Bileşen sınıfı, genellikle [Razor](xref:mvc/views/razor) bir dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır `.razor` .</span><span class="sxs-lookup"><span data-stu-id="60def-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="60def-127">İçindeki bileşenler Blazor , resmi olarak *Razor bileşen* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="60def-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="60def-128">Razor , HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="60def-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="60def-129">Razor Visual Studio 'da [IntelliSense](/visualstudio/ide/using-intellisense) programlama desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="60def-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="60def-130">Razor Sayfalar ve MVC de kullanır Razor .</span><span class="sxs-lookup"><span data-stu-id="60def-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="60def-131">RazorBir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60def-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="60def-132">Blazor UI bileşimi için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="60def-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="60def-133">Aşağıdaki Razor biçimlendirme `Dialog.razor` bir iletişim kutusu görüntüleyen ve Kullanıcı bir düğme seçtiğinde bir olayı işleyen bir bileşeni () gösterir:</span><span class="sxs-lookup"><span data-stu-id="60def-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

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

<span data-ttu-id="60def-134">Yukarıdaki örnekte, `OnYes` düğme olayının tetiklediği bir C# yöntemidir `onclick` .</span><span class="sxs-lookup"><span data-stu-id="60def-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="60def-135">İletişim kutusunun Text ( `ChildContent` ) ve title ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan aşağıdaki bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="60def-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="60def-136">`Dialog`Bileşen, BIR HTML etiketi kullanılarak başka bir bileşen içinde iç içe geçmiş.</span><span class="sxs-lookup"><span data-stu-id="60def-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="60def-137">Aşağıdaki örnekte, `Index` bileşen ( `Pages/Index.razor` ) önceki `Dialog` bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="60def-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="60def-138">Etiketin özniteliği, `Title` bileşen özelliğine bir başlık değeri geçirir `Dialog` `Title` .</span><span class="sxs-lookup"><span data-stu-id="60def-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="60def-139">`Dialog`Bileşenin metni (), `ChildContent` öğesinin içeriği tarafından ayarlanır `<Dialog>` .</span><span class="sxs-lookup"><span data-stu-id="60def-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="60def-140">`Dialog`Bileşen `Index` bileşene eklendiğinde, [Visual Studio 'da IntelliSense](/visualstudio/ide/using-intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.</span><span class="sxs-lookup"><span data-stu-id="60def-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="60def-141">İletişim kutusu, `Index` bileşene bir tarayıcıda erişildiğinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="60def-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="60def-142">Düğme Kullanıcı tarafından seçildiğinde, tarayıcının geliştirici araçları konsolunda yöntemi tarafından yazılan ileti görüntülenir `OnYes` :</span><span class="sxs-lookup"><span data-stu-id="60def-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![İletişim kutusu bileşeni, Dizin bileşeninin içinde iç içe geçmiş tarayıcıda işlendi.](index/_static/dialog.png)

<span data-ttu-id="60def-146">Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı* adlı, tarayıcı [belge nesne modeli (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) ' ın bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="60def-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="60def-147">Blazor WebAssembly , .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik [tek sayfalı uygulama (Spa) çerçevesidir](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) .</span><span class="sxs-lookup"><span data-stu-id="60def-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="60def-148">Blazor WebAssembly , eklentiler olmadan açık Web standartları kullanır veya kodu diğer dillere yeniden derler.</span><span class="sxs-lookup"><span data-stu-id="60def-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="60def-149">Blazor WebAssembly mobil tarayıcılar dahil tüm modern web tarayıcılarında çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="60def-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="60def-150">Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) (kısaltılmış) tarafından mümkün hale getirilir `wasm` .</span><span class="sxs-lookup"><span data-stu-id="60def-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="60def-151">WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir.</span><span class="sxs-lookup"><span data-stu-id="60def-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="60def-152">WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="60def-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="60def-153">WebAssembly Code, JavaScript birlikte *çalışabilirliği* olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir, genellikle *JavaScript birlikte çalışma* veya *js birlikte çalışma* olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="60def-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="60def-154">Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="60def-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (Blazor WebAssembly)::: .NET kodunu WebAssembly ile tarayıcıda çalıştırır.](index/_static/blazor-webassembly.png)

<span data-ttu-id="60def-156">Bir Blazor WebAssembly uygulama bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="60def-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="60def-157">C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.</span><span class="sxs-lookup"><span data-stu-id="60def-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="60def-158">Derlemeler ve [.NET çalışma zamanı](/dotnet/framework/get-started/overview) tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="60def-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="60def-159">Blazor WebAssembly .NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="60def-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="60def-160">Blazor WebAssemblyÇalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="60def-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="60def-161">Yayınlanan uygulamanın boyutu, *Yük boyutu*, bir uygulamanın kullanılabilirliği için kritik bir performans etkendir.</span><span class="sxs-lookup"><span data-stu-id="60def-161">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="60def-162">Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek.</span><span class="sxs-lookup"><span data-stu-id="60def-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="60def-163">Blazor WebAssembly yükleme sürelerini azaltmak için yük boyutunu iyileştirir:</span><span class="sxs-lookup"><span data-stu-id="60def-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="60def-164">Kullanılmayan kod, [ara dil (IL) ayarlayıcısı](xref:blazor/host-and-deploy/configure-trimmer)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="60def-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="60def-165">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="60def-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="60def-166">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="60def-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="60def-167">Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:blazor/host-and-deploy/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="60def-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="60def-168">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="60def-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="60def-169">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="60def-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="60def-170">Blazor Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="60def-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="60def-171">*Blazor Server* ASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="60def-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="60def-172">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="60def-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="60def-173">Çalışma zamanı tutamaçları:</span><span class="sxs-lookup"><span data-stu-id="60def-173">The runtime handles:</span></span>

* <span data-ttu-id="60def-174">Tarayıcıdan sunucusuna kullanıcı arabirimi olayları gönderiliyor.</span><span class="sxs-lookup"><span data-stu-id="60def-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="60def-175">Sunucu tarafından geri gönderilen işlenen bileşene Kullanıcı Arabirimi güncelleştirmeleri uygulanıyor.</span><span class="sxs-lookup"><span data-stu-id="60def-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="60def-176">Blazor ServerTarayıcıyla iletişim kurmak için tarafından kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="60def-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (Blazor Server)::: sunucuda .NET kodu çalıştırır ve şu şekilde istemcisinde Belge Nesne Modeli:: No-Loc (SignalR)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="60def-178">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="60def-178">JavaScript interop</span></span>

<span data-ttu-id="60def-179">Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="60def-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="60def-180">Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="60def-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="60def-181">C# kodu [JavaScript kodunu çağırabilir](xref:blazor/call-javascript-from-dotnet)ve JavaScript kodu [c# koduna çağrı](xref:blazor/call-dotnet-from-javascript)yapabilir.</span><span class="sxs-lookup"><span data-stu-id="60def-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="60def-182">Kod paylaşımı ve .NET Standard</span><span class="sxs-lookup"><span data-stu-id="60def-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="60def-183">Blazor[](/dotnet/standard/net-standard) Blazor projelerin .NET Standard belirtimlerine uygun kitaplıklara başvurmalarını sağlayan .NET Standard uygular.</span><span class="sxs-lookup"><span data-stu-id="60def-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="60def-184">.NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="60def-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="60def-185">.NET Standard sınıf kitaplıkları Blazor , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="60def-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="60def-186">Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="60def-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60def-187">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="60def-187">Additional resources</span></span>

* [<span data-ttu-id="60def-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="60def-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="60def-189">C# Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="60def-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="60def-190">HTML</span><span class="sxs-lookup"><span data-stu-id="60def-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="60def-191">[Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları</span><span class="sxs-lookup"><span data-stu-id="60def-191">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
