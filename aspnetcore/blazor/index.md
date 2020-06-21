---
title: ASP.NET Core girişBlazor
author: guardrex
description: ASP.NET Core uygulamasında Blazor .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol ASP.NET Core keşfedelim.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: 086629f88ad04faed812bd16683e7d073f3e8ede
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122080"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="5b9b2-103">ASP.NET Core girişBlazor</span><span class="sxs-lookup"><span data-stu-id="5b9b2-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="5b9b2-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="5b9b2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5b9b2-105">*Hoş geldiniz Blazor !*</span><span class="sxs-lookup"><span data-stu-id="5b9b2-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="5b9b2-106">, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="5b9b2-107">JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="5b9b2-108">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="5b9b2-109">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="5b9b2-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="5b9b2-111">İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="5b9b2-112">JavaScript yerine C# dilinde kod yazın.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="5b9b2-113">.NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="5b9b2-114">Sunucu ve istemci arasında uygulama mantığını paylaşma.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="5b9b2-115">Avantajı. NET ' in performans, güvenilirlik ve güvenlik.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="5b9b2-116">Windows, Linux ve macOS 'ta Visual Studio ile üretken olun.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="5b9b2-117">Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="5b9b2-118">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="5b9b2-118">Components</span></span>

Blazor<span data-ttu-id="5b9b2-119">uygulamalar *bileşenleri*temel alır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-119"> apps are based on *components*.</span></span> <span data-ttu-id="5b9b2-120">İçindeki bir bileşeni Blazor , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="5b9b2-121">Bileşenler, .NET Derlemeleriyle yerleşik olarak bulunan .NET sınıflarıdır:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="5b9b2-122">Esnek kullanıcı arabirimi işleme mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="5b9b2-123">Kullanıcı olaylarını işleyin.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-123">Handle user events.</span></span>
* <span data-ttu-id="5b9b2-124">İç içe ve yeniden kullanılabilir olabilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-124">Can be nested and reused.</span></span>
* <span data-ttu-id="5b9b2-125">, [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="5b9b2-126">Bileşen sınıfı genellikle [Razor](xref:mvc/views/razor) *. Razor* dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="5b9b2-127">İçindeki bileşenler Blazor , resmi olarak \* Razor bileşen\*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-127">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="5b9b2-128">, HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-128"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="5b9b2-129">[IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-129"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="5b9b2-130">Sayfalar ve MVC de kullanır Razor .</span><span class="sxs-lookup"><span data-stu-id="5b9b2-130"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="5b9b2-131">RazorBir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="5b9b2-132">Aşağıdaki Razor biçimlendirme, başka bir bileşen içinde iç içe yerleştirilebileceğini bir bileşeni (*iletişim kutusu. Razor*) gösterir:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="5b9b2-133">İletişim kutusunun gövde içeriği ( `ChildContent` ) ve başlığı ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="5b9b2-134">`OnYes`, düğme olayının tetiklediği bir C# yöntemidir `onclick` .</span><span class="sxs-lookup"><span data-stu-id="5b9b2-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="5b9b2-135">UI bileşimi için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="5b9b2-136">HTML öğeleri, bileşenleri belirtir ve bir etiketin öznitelikleri değerleri bir bileşenin özelliklerine iletir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="5b9b2-137">Aşağıdaki örnekte `Index` bileşen `Dialog` bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="5b9b2-138">`ChildContent`ve `Title` öğesi öznitelikleri ve içeriği tarafından ayarlanır `<Dialog>` .</span><span class="sxs-lookup"><span data-stu-id="5b9b2-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="5b9b2-139">*Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="5b9b2-140">Üst öğeye (*Index. Razor*) bir tarayıcıda erişildiğinde iletişim kutusu işlenir:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Tarayıcıda işlenen iletişim kutusu bileşeni](index/_static/dialog.png)

<span data-ttu-id="5b9b2-142">Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) 'da ıntellisense ve [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="5b9b2-143">Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı*adlı, tarayıcı belge nesne MODELI (DOM) ' ın bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="5b9b2-144">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5b9b2-144"> WebAssembly</span></span>

Blazor<span data-ttu-id="5b9b2-145">WebAssembly, .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik tek sayfalı bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="5b9b2-146">WebAssembly, eklentiler veya Code transpilation olmadan açık Web standartları kullanır ve mobil tarayıcılar dahil tüm modern web tarayıcılarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="5b9b2-147">Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) *(kısaltılmış)* tarafından mümkün hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="5b9b2-148">WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="5b9b2-149">WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="5b9b2-150">WebAssembly Code, JavaScript ile *birlikte çalışabilirlik* (veya *JavaScript birlikte çalışma*) olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="5b9b2-151">Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="5b9b2-152">![BlazorWebAssembly, WebAssembly ile tarayıcıda .NET kodu çalıştırır.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="5b9b2-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="5b9b2-153">Bir Blazor weelsembly uygulaması bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="5b9b2-154">C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="5b9b2-155">Derlemeler ve .NET çalışma zamanı tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="5b9b2-156">WebAssembly, .NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="5b9b2-157">BlazorWebassembly çalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="5b9b2-158">Yayınlanan uygulamanın boyutu, *Yük boyutu*, uygulamanın useyeteneğinin önemli bir performans etkendir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="5b9b2-159">Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="5b9b2-160">WebAssembly indirme sürelerini azaltmak için yük boyutunu iyileştirir:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="5b9b2-161">Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:blazor/host-and-deploy/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="5b9b2-162">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="5b9b2-163">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="5b9b2-164">Server</span><span class="sxs-lookup"><span data-stu-id="5b9b2-164"> Server</span></span>

Blazor<span data-ttu-id="5b9b2-165">Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="5b9b2-166">Sunucu Razor , bir ASP.NET Core uygulamasındaki sunucuda bileşenleri barındırmak için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="5b9b2-167">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="5b9b2-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="5b9b2-168">Çalışma zamanı, tarayıcıdan sunucuya kullanıcı arabirimi olayları göndermeyi ve bileşenleri çalıştırdıktan sonra sunucu tarafından tarayıcıya geri gönderilen Kullanıcı arabirimi güncelleştirmelerini uygular.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="5b9b2-169">BlazorSunucu tarafından tarayıcıyla iletişim kurmak için kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="5b9b2-170">![BlazorSunucu, sunucuda .NET kodu çalıştırır ve bir bağlantı üzerinden istemcideki Belge Nesne Modeli etkileşime girer SignalR](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="5b9b2-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="5b9b2-171">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="5b9b2-171">JavaScript interop</span></span>

<span data-ttu-id="5b9b2-172">Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="5b9b2-173">Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="5b9b2-174">C# kodu JavaScript kodunu çağırabilir ve JavaScript kodu C# koduna çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="5b9b2-175">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="5b9b2-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="5b9b2-176">Kod paylaşımı ve .NET Standard</span><span class="sxs-lookup"><span data-stu-id="5b9b2-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="5b9b2-177">[.NET Standard 2,1](/dotnet/standard/net-standard)uygular ve bu sayede Blazor Projeler .NET Standard 2,1 veya daha önceki belirtimlere uygun kitaplıklara başvurabilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-177"> implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="5b9b2-178">.NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="5b9b2-179">.NET Standard sınıf kitaplıkları Blazor , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="5b9b2-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="5b9b2-180">Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="5b9b2-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b9b2-181">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5b9b2-181">Additional resources</span></span>

* [<span data-ttu-id="5b9b2-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="5b9b2-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="5b9b2-183">C# Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="5b9b2-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="5b9b2-184">HTML</span><span class="sxs-lookup"><span data-stu-id="5b9b2-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="5b9b2-185">[Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları</span><span class="sxs-lookup"><span data-stu-id="5b9b2-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
