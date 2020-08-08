---
title: ASP.NET Core girişBlazor
author: guardrex
description: ASP.NET Core uygulamasında Blazor .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
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
ms.openlocfilehash: ad543087243658f09a23e4f6d957d0c6aa77b361
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014184"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="ff130-103">ASP.NET Core girişBlazor</span><span class="sxs-lookup"><span data-stu-id="ff130-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="ff130-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="ff130-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ff130-105">*Hoş geldiniz Blazor !*</span><span class="sxs-lookup"><span data-stu-id="ff130-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="ff130-106">Blazor, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="ff130-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="ff130-107">JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff130-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="ff130-108">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ff130-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="ff130-109">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="ff130-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="ff130-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="ff130-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="ff130-111">İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="ff130-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="ff130-112">JavaScript yerine C# dilinde kod yazın.</span><span class="sxs-lookup"><span data-stu-id="ff130-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="ff130-113">.NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="ff130-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="ff130-114">Sunucu ve istemci arasında uygulama mantığını paylaşma.</span><span class="sxs-lookup"><span data-stu-id="ff130-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="ff130-115">Avantajı. NET ' in performans, güvenilirlik ve güvenlik.</span><span class="sxs-lookup"><span data-stu-id="ff130-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="ff130-116">Windows, Linux ve macOS 'ta Visual Studio ile üretken olun.</span><span class="sxs-lookup"><span data-stu-id="ff130-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="ff130-117">Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ff130-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="ff130-118">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="ff130-118">Components</span></span>

<span data-ttu-id="ff130-119">Blazoruygulamalar *bileşenleri*temel alır.</span><span class="sxs-lookup"><span data-stu-id="ff130-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="ff130-120">İçindeki bir bileşeni Blazor , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.</span><span class="sxs-lookup"><span data-stu-id="ff130-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="ff130-121">Bileşenler, .NET Derlemeleriyle yerleşik olarak bulunan .NET sınıflarıdır:</span><span class="sxs-lookup"><span data-stu-id="ff130-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="ff130-122">Esnek kullanıcı arabirimi işleme mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="ff130-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="ff130-123">Kullanıcı olaylarını işleyin.</span><span class="sxs-lookup"><span data-stu-id="ff130-123">Handle user events.</span></span>
* <span data-ttu-id="ff130-124">İç içe ve yeniden kullanılabilir olabilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-124">Can be nested and reused.</span></span>
* <span data-ttu-id="ff130-125">, [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="ff130-126">Bileşen sınıfı, genellikle [Razor](xref:mvc/views/razor) bir dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır `.razor` .</span><span class="sxs-lookup"><span data-stu-id="ff130-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="ff130-127">İçindeki bileşenler Blazor , resmi olarak \* Razor bileşen\*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ff130-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="ff130-128">Razor, HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="ff130-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="ff130-129">Razor[IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ff130-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="ff130-130">RazorSayfalar ve MVC de kullanır Razor .</span><span class="sxs-lookup"><span data-stu-id="ff130-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="ff130-131">RazorBir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff130-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="ff130-132">Aşağıdaki Razor biçimlendirme, `Dialog.razor` başka bir bileşen içinde iç içe yerleştirilebileceğini bir bileşeni () gösterir:</span><span class="sxs-lookup"><span data-stu-id="ff130-132">The following Razor markup demonstrates a component (`Dialog.razor`), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="ff130-133">İletişim kutusunun gövde içeriği ( `ChildContent` ) ve başlığı ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ff130-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="ff130-134">`OnYes`, düğme olayının tetiklediği bir C# yöntemidir `onclick` .</span><span class="sxs-lookup"><span data-stu-id="ff130-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="ff130-135">BlazorUI bileşimi için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="ff130-135">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="ff130-136">HTML öğeleri, bileşenleri belirtir ve bir etiketin öznitelikleri değerleri bir bileşenin özelliklerine iletir.</span><span class="sxs-lookup"><span data-stu-id="ff130-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="ff130-137">Aşağıdaki örnekte `Index` bileşen `Dialog` bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="ff130-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="ff130-138">`ChildContent`ve `Title` öğesi öznitelikleri ve içeriği tarafından ayarlanır `<Dialog>` .</span><span class="sxs-lookup"><span data-stu-id="ff130-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="ff130-139">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="ff130-139">`Pages/Index.razor`:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="ff130-140">Üst öğeye ( `Pages/Index.razor` ) bir tarayıcıda erişildiğinde iletişim kutusu işlenir:</span><span class="sxs-lookup"><span data-stu-id="ff130-140">The dialog is rendered when the parent (`Pages/Index.razor`) is accessed in a browser:</span></span>

![Tarayıcıda işlenen iletişim kutusu bileşeni](index/_static/dialog.png)

<span data-ttu-id="ff130-142">Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) 'da ıntellisense ve [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.</span><span class="sxs-lookup"><span data-stu-id="ff130-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="ff130-143">Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı*adlı, tarayıcı belge nesne MODELI (DOM) ' ın bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="ff130-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="ff130-144">Blazor WebAssembly, .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik tek sayfalı bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="ff130-144">Blazor WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="ff130-145">Blazor WebAssembly, eklentiler veya kod transpilation olmadan açık Web standartları kullanır ve mobil tarayıcılar dahil tüm modern web tarayıcılarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-145">Blazor WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="ff130-146">Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) (kısaltılmış) tarafından mümkün hale getirilir `wasm` .</span><span class="sxs-lookup"><span data-stu-id="ff130-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="ff130-147">WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir.</span><span class="sxs-lookup"><span data-stu-id="ff130-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="ff130-148">WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ff130-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="ff130-149">WebAssembly Code, JavaScript ile *birlikte çalışabilirlik* (veya *JavaScript birlikte çalışma*) olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="ff130-150">Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="ff130-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (Blazor WebAssembly)::: .NET kodunu WebAssembly ile tarayıcıda çalıştırır.](index/_static/blazor-webassembly.png)

<span data-ttu-id="ff130-152">Bir Blazor WebAssembly uygulama bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="ff130-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="ff130-153">C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.</span><span class="sxs-lookup"><span data-stu-id="ff130-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="ff130-154">Derlemeler ve .NET çalışma zamanı tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="ff130-155">Blazor WebAssembly.NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ff130-155">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="ff130-156">Blazor WebAssemblyÇalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="ff130-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="ff130-157">Yayınlanan uygulamanın boyutu, *Yük boyutu*, uygulamanın useyeteneğinin önemli bir performans etkendir.</span><span class="sxs-lookup"><span data-stu-id="ff130-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="ff130-158">Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek.</span><span class="sxs-lookup"><span data-stu-id="ff130-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="ff130-159">Blazor WebAssemblyyükleme sürelerini azaltmak için yük boyutunu iyileştirir:</span><span class="sxs-lookup"><span data-stu-id="ff130-159">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="ff130-160">Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:blazor/host-and-deploy/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="ff130-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="ff130-161">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="ff130-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="ff130-162">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="ff130-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## Blazor Server

<span data-ttu-id="ff130-163">BlazorKullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="ff130-163">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="ff130-164">Blazor ServerASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="ff130-164">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="ff130-165">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="ff130-165">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="ff130-166">Çalışma zamanı, tarayıcıdan sunucuya kullanıcı arabirimi olayları göndermeyi ve bileşenleri çalıştırdıktan sonra sunucu tarafından tarayıcıya geri gönderilen Kullanıcı arabirimi güncelleştirmelerini uygular.</span><span class="sxs-lookup"><span data-stu-id="ff130-166">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="ff130-167">Blazor ServerTarayıcıyla iletişim kurmak için tarafından kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ff130-167">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (Blazor Server)::: sunucuda .NET kodu çalıştırır ve şu şekilde istemcisinde Belge Nesne Modeli:: No-Loc (SignalR)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="ff130-169">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="ff130-169">JavaScript interop</span></span>

<span data-ttu-id="ff130-170">Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="ff130-170">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="ff130-171">Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ff130-171">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="ff130-172">C# kodu JavaScript kodunu çağırabilir ve JavaScript kodu C# koduna çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-172">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="ff130-173">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ff130-173">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="ff130-174">Kod paylaşımı ve .NET Standard</span><span class="sxs-lookup"><span data-stu-id="ff130-174">Code sharing and .NET Standard</span></span>

<span data-ttu-id="ff130-175">Blazor[.NET Standard 2,1](/dotnet/standard/net-standard)uygular ve bu sayede Blazor Projeler .NET Standard 2,1 veya daha önceki belirtimlere uygun kitaplıklara başvurabilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-175">Blazor implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="ff130-176">.NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="ff130-176">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="ff130-177">.NET Standard sınıf kitaplıkları Blazor , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="ff130-177">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="ff130-178">Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="ff130-178">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff130-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ff130-179">Additional resources</span></span>

* [<span data-ttu-id="ff130-180">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ff130-180">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="ff130-181">C# Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="ff130-181">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="ff130-182">HTML</span><span class="sxs-lookup"><span data-stu-id="ff130-182">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="ff130-183">[Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları</span><span class="sxs-lookup"><span data-stu-id="ff130-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
