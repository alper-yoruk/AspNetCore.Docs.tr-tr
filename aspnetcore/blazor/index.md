---
title: ASP.NET Çekirdeğine GirişBlazor
author: guardrex
description: ASP.NET Core Blazoruygulamasında .NET ile etkileşimli istemci tarafı web UI oluşturmanın bir yolu olan ASP.NET Core'u keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405950"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="20357-103">ASP.NET Çekirdeğine GirişBlazor</span><span class="sxs-lookup"><span data-stu-id="20357-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="20357-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="20357-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="20357-105">*Hoş Blazorgeldiniz!*</span><span class="sxs-lookup"><span data-stu-id="20357-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="20357-106">is a framework for building interactive client-side web UI with .NET:</span><span class="sxs-lookup"><span data-stu-id="20357-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="20357-107">JavaScript yerine C# kullanarak zengin etkileşimli web'ler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20357-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="20357-108">.NET'te yazılı sunucu ve istemci tarafı uygulama mantığını paylaşın.</span><span class="sxs-lookup"><span data-stu-id="20357-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="20357-109">Mobil tarayıcılar da dahil olmak üzere geniş tarayıcı desteği için UI'yi HTML ve CSS olarak işleyin.</span><span class="sxs-lookup"><span data-stu-id="20357-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="20357-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformları ile entegre edin.</span><span class="sxs-lookup"><span data-stu-id="20357-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="20357-111">İstemci tarafı web geliştirme için .NET'i kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="20357-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="20357-112">JavaScript yerine C# kodu yazın.</span><span class="sxs-lookup"><span data-stu-id="20357-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="20357-113">.NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="20357-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="20357-114">Uygulama mantığını sunucu ve istemci arasında paylaşın.</span><span class="sxs-lookup"><span data-stu-id="20357-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="20357-115">Yararlanın . NET'in performansı, güvenilirliği ve güvenliği.</span><span class="sxs-lookup"><span data-stu-id="20357-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="20357-116">Windows, Linux ve macOS'ta Visual Studio ile üretken kalın.</span><span class="sxs-lookup"><span data-stu-id="20357-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="20357-117">Kararlı, zengin özelliklere sahip ve kullanımı kolay ortak bir dil, çerçeve ve araç kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20357-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="20357-118">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="20357-118">Components</span></span>

Blazor<span data-ttu-id="20357-119">uygulamalar *bileşenleri*ne lerdir.</span><span class="sxs-lookup"><span data-stu-id="20357-119"> apps are based on *components*.</span></span> <span data-ttu-id="20357-120">Bir Blazor bileşen, sayfa, iletişim kutusu veya veri giriş formu gibi ui'nin bir öğesidir.</span><span class="sxs-lookup"><span data-stu-id="20357-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="20357-121">Bileşenler .NET derlemeleri yerleşik .NET sınıfları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="20357-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="20357-122">Esnek UI oluşturma mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="20357-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="20357-123">Kullanıcı olaylarını işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="20357-123">Handle user events.</span></span>
* <span data-ttu-id="20357-124">Iç içe ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="20357-124">Can be nested and reused.</span></span>
* <span data-ttu-id="20357-125">[Razor sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="20357-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="20357-126">Bileşen sınıfı genellikle *.razor* dosya uzantısı olan [bir Jilet](xref:mvc/views/razor) biçimlendirme sayfası biçiminde yazılır.</span><span class="sxs-lookup"><span data-stu-id="20357-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="20357-127">Bileşenler Blazor resmen *Razor bileşenleri*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="20357-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="20357-128">Razor, HTML biçimlendirmesini geliştirici üretkenliği için tasarlanmış C# koduyla birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="20357-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="20357-129">Razor, [IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML biçimlendirmesi ve C# arasında geçiş yapmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="20357-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="20357-130">Razor Pages ve MVC de Razor kullanın.</span><span class="sxs-lookup"><span data-stu-id="20357-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="20357-131">Bir istek/yanıt modeli etrafında oluşturulmuş Olan Razor Pages ve MVC'nin aksine, bileşenler özellikle istemci tarafındaki Kullanıcı Arabirimi mantığı ve kompozisyonu için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20357-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="20357-132">Aşağıdaki Jilet biçimlendirmesi, başka bir bileşenin içinde iç içe yapabilen bir bileşeni *(Dialog.razor)* gösterir:</span><span class="sxs-lookup"><span data-stu-id="20357-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="20357-133">İletişim kutusunun gövde içeriği`ChildContent`( )`Title`ve başlık ( ) kullanıcı ui'sinde bu bileşeni kullanan bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="20357-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="20357-134">`OnYes`düğmenin olayı tarafından tetiklenen bir `onclick` C# yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="20357-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="20357-135">UI kompozisyonu için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="20357-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="20357-136">HTML öğeleri bileşenleri belirtir ve etiketin öznitelikleri değerleri bir bileşenin özelliklerine geçirir.</span><span class="sxs-lookup"><span data-stu-id="20357-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="20357-137">Aşağıdaki örnekte, `Index` bileşen `Dialog` bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="20357-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="20357-138">`ChildContent`ve `Title` öğenin öznitelikleri ve `<Dialog>` içeriği tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="20357-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="20357-139">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="20357-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="20357-140">İletişim, üst öğeye *(Index.razor)* bir tarayıcıda erişildiğinde işlenir:</span><span class="sxs-lookup"><span data-stu-id="20357-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Tarayıcıda işlenen iletişim bileşeni](index/_static/dialog.png)

<span data-ttu-id="20357-142">Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) ve Visual [Studio Code'daki](https://code.visualstudio.com/docs/editor/intellisense) IntelliSense sözdizimi ve parametre tamamlama ile geliştirmeyi hızlandırAr.</span><span class="sxs-lookup"><span data-stu-id="20357-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="20357-143">Bileşenler, kullanıcı arasını esnek ve verimli bir şekilde güncelleştirmek için kullanılan *render ağacı*olarak adlandırılan tarayıcının Belge Nesnesi Modeli'nin (DOM) bellek içi gösterimine dönüşür.</span><span class="sxs-lookup"><span data-stu-id="20357-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="20357-144">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="20357-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="20357-145">WebAssembly, .NET ile etkileşimli istemci tarafı web uygulamaları oluşturmak için tek sayfalık bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="20357-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="20357-146">WebAssembly eklentileri veya kod transpilasyonu olmadan açık web standartları kullanır ve mobil tarayıcılar da dahil olmak üzere tüm modern web tarayıcılarında çalışır.</span><span class="sxs-lookup"><span data-stu-id="20357-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="20357-147">Web tarayıcılarında .NET kodunun çalıştırılmasının [WebAssembly](https://webassembly.org) (kısaltılmış *wasm)* tarafından yürütülmesi mümkün kılındı.</span><span class="sxs-lookup"><span data-stu-id="20357-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="20357-148">WebAssembly, hızlı indirme ve maksimum yürütme hızı için optimize edilmiş kompakt bir bytecode biçimidir.</span><span class="sxs-lookup"><span data-stu-id="20357-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="20357-149">WebAssembly açık bir web standardıdır ve eklentileri olmadan web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="20357-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="20357-150">WebAssembly kodu *JavaScript birlikte çalışabilirlik* (veya *JavaScript interop)* olarak adlandırılan JavaScript üzerinden tarayıcının tam işlevselliğine erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20357-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="20357-151">.NET kodu tarayıcıda WebAssembly üzerinden yürütülen sandbox istemci makineüzerinde kötü amaçlı eylemlere karşı sağladığı korumaları ile tarayıcının JavaScript sandbox çalışır.</span><span class="sxs-lookup"><span data-stu-id="20357-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="20357-152">![BlazorWebAssembly, WebAssembly ile tarayıcıda .NET kodunu çalıştırın.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="20357-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="20357-153">Bir Blazor WebAssembly uygulaması bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="20357-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="20357-154">C# kod dosyaları ve Razor dosyaları .NET derlemeleri halinde derlenir.</span><span class="sxs-lookup"><span data-stu-id="20357-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="20357-155">Derlemeler ve .NET çalışma süresi tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="20357-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="20357-156">WebAssembly .NET çalışma saatini yakalar ve uygulamanın derlemelerini yüklemek için çalışma süresini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="20357-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="20357-157">Blazor WebAssembly çalışma süresi, DOM manipülasyonu ve tarayıcı API çağrılarını işlemek için JavaScript interop'u kullanır.</span><span class="sxs-lookup"><span data-stu-id="20357-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="20357-158">Yayınlanan uygulamanın boyutu, *yük boyutu,* bir uygulamanın kullanılabilirliği için kritik bir performans faktörüdür.</span><span class="sxs-lookup"><span data-stu-id="20357-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="20357-159">Büyük bir uygulamanın tarayıcıya indirilen kullanıcı deneyimini azaltan nispeten uzun bir zaman alır.</span><span class="sxs-lookup"><span data-stu-id="20357-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="20357-160">WebAssembly, indirme sürelerini azaltmak için yük boyutunu optimize eder:</span><span class="sxs-lookup"><span data-stu-id="20357-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="20357-161">Kullanılmayan [kod, Ara Dil (IL) Bağlantı Aracı](xref:host-and-deploy/blazor/configure-linker)tarafından yayınlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="20357-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="20357-162">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="20357-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="20357-163">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="20357-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="20357-164">Sunucu</span><span class="sxs-lookup"><span data-stu-id="20357-164"> Server</span></span>

Blazor<span data-ttu-id="20357-165">ui güncelleştirmelerinin nasıl uygulandığından bileşen oluşturma mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="20357-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="20357-166">Sunucu, ASP.NET Core uygulamasında Razor bileşenlerini sunucuda barındırma desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="20357-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="20357-167">UI güncelleştirmeleri bir [SignalR](xref:signalr/introduction) bağlantı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="20357-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="20357-168">Çalışma zamanı, tarayıcıdan sunucuya UI olayları göndermeyi işler ve bileşenleri çalıştırdıktan sonra sunucu tarafından gönderilen UI güncelleştirmelerini tarayıcıya uygular.</span><span class="sxs-lookup"><span data-stu-id="20357-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="20357-169">Sunucu tarafından Blazor tarayıcıyla iletişim kurmak için kullanılan bağlantı, JavaScript interop çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20357-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="20357-170">![BlazorSunucu sunucuda .NET kodunu çalıştırıyor ve bağlantı SignalR üzerinden istemcideki Belge Nesnesi Modeli ile etkileşime giriyor](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="20357-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="20357-171">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="20357-171">JavaScript interop</span></span>

<span data-ttu-id="20357-172">Üçüncü taraf JavaScript kitaplıkları ve tarayıcı API'lerine erişim gerektiren uygulamalar için bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="20357-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="20357-173">Bileşenler, JavaScript'in kullanabildiği herhangi bir kitaplık veya API'yi kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="20357-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="20357-174">C# kodu JavaScript koduna çağrıyapabilir ve JavaScript kodu C# koduna çağrıyapabilir.</span><span class="sxs-lookup"><span data-stu-id="20357-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="20357-175">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="20357-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="20357-176">Kod paylaşımı ve .NET Standardı</span><span class="sxs-lookup"><span data-stu-id="20357-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="20357-177">uygular [.NET Standart 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="20357-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="20357-178">.NET Standardı, .NET uygulamalarında yaygın olan .NET API'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="20357-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="20357-179">.NET Standart sınıf kitaplıkları , .NET BlazorFramework, .NET Core, Xamarin, Mono ve Unity gibi farklı .NET platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="20357-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="20357-180">Bir web tarayıcısının içinde geçerli olmayan API'ler (örneğin, dosya sistemine erişim, bir soket açma ve iş parçacığı) bir <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="20357-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20357-181">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="20357-181">Additional resources</span></span>

* [<span data-ttu-id="20357-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="20357-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="20357-183">C# Rehberi</span><span class="sxs-lookup"><span data-stu-id="20357-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="20357-184">HTML</span><span class="sxs-lookup"><span data-stu-id="20357-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="20357-185">[Awesome Blazor ](https://github.com/AdrienTorris/awesome-blazor) topluluk bağlantıları</span><span class="sxs-lookup"><span data-stu-id="20357-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
