---
<span data-ttu-id="33e78-101">Başlık: ' ASP.NET Core giriş Blazor ' Yazar: Açıklama: ' ASP.NET Core araştırma Blazor , bir ASP.NET Core uygulamasında .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmanın bir yoludur. '</span><span class="sxs-lookup"><span data-stu-id="33e78-101">title: 'Introduction to ASP.NET Core Blazor' author: description: 'Explore ASP.NET Core Blazor, a way to build interactive client-side web UI with .NET in an ASP.NET Core app.'</span></span>
<span data-ttu-id="33e78-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="33e78-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="33e78-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="33e78-103">'Blazor'</span></span>
- <span data-ttu-id="33e78-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="33e78-104">'Identity'</span></span>
- <span data-ttu-id="33e78-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="33e78-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="33e78-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="33e78-106">'Razor'</span></span>
- <span data-ttu-id="33e78-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="33e78-107">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="33e78-108">ASP.NET Core girişBlazor</span><span class="sxs-lookup"><span data-stu-id="33e78-108">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="33e78-109">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="33e78-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="33e78-110">*Hoş geldiniz Blazor !*</span><span class="sxs-lookup"><span data-stu-id="33e78-110">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="33e78-111">, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="33e78-111"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="33e78-112">JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="33e78-112">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="33e78-113">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="33e78-113">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="33e78-114">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="33e78-114">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="33e78-115">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)gibi modern barındırma platformlarıyla tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="33e78-115">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="33e78-116">İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="33e78-116">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="33e78-117">JavaScript yerine C# dilinde kod yazın.</span><span class="sxs-lookup"><span data-stu-id="33e78-117">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="33e78-118">.NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="33e78-118">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="33e78-119">Sunucu ve istemci arasında uygulama mantığını paylaşma.</span><span class="sxs-lookup"><span data-stu-id="33e78-119">Share app logic across server and client.</span></span>
* <span data-ttu-id="33e78-120">Avantajı. NET ' in performans, güvenilirlik ve güvenlik.</span><span class="sxs-lookup"><span data-stu-id="33e78-120">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="33e78-121">Windows, Linux ve macOS 'ta Visual Studio ile üretken olun.</span><span class="sxs-lookup"><span data-stu-id="33e78-121">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="33e78-122">Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="33e78-122">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="33e78-123">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="33e78-123">Components</span></span>

Blazor<span data-ttu-id="33e78-124">uygulamalar *bileşenleri*temel alır.</span><span class="sxs-lookup"><span data-stu-id="33e78-124"> apps are based on *components*.</span></span> <span data-ttu-id="33e78-125">İçindeki bir bileşeni Blazor , bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.</span><span class="sxs-lookup"><span data-stu-id="33e78-125">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="33e78-126">Bileşenler, .NET Derlemeleriyle yerleşik olarak bulunan .NET sınıflarıdır:</span><span class="sxs-lookup"><span data-stu-id="33e78-126">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="33e78-127">Esnek kullanıcı arabirimi işleme mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="33e78-127">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="33e78-128">Kullanıcı olaylarını işleyin.</span><span class="sxs-lookup"><span data-stu-id="33e78-128">Handle user events.</span></span>
* <span data-ttu-id="33e78-129">İç içe ve yeniden kullanılabilir olabilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-129">Can be nested and reused.</span></span>
* <span data-ttu-id="33e78-130">, [ Razor Sınıf kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-130">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="33e78-131">Bileşen sınıfı genellikle [Razor](xref:mvc/views/razor) *. Razor* dosya uzantısına sahip bir biçimlendirme sayfası biçiminde yazılır.</span><span class="sxs-lookup"><span data-stu-id="33e78-131">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="33e78-132">İçindeki bileşenler Blazor , resmi olarak \* Razor bileşen\*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="33e78-132">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="33e78-133">, HTML işaretlemesini geliştirici üretkenliği için tasarlanan C# kodu ile birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="33e78-133"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="33e78-134">[IntelliSense](/visualstudio/ide/using-intellisense) desteğiyle aynı dosyada HTML Işaretlemesi ile C# arasında geçiş yapmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="33e78-134"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="33e78-135">Sayfalar ve MVC de kullanır Razor .</span><span class="sxs-lookup"><span data-stu-id="33e78-135"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="33e78-136">RazorBir istek/yanıt modeli etrafında oluşturulan sayfaların ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="33e78-136">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="33e78-137">Aşağıdaki Razor biçimlendirme, başka bir bileşen içinde iç içe yerleştirilebileceğini bir bileşeni (*iletişim kutusu. Razor*) gösterir:</span><span class="sxs-lookup"><span data-stu-id="33e78-137">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="33e78-138">İletişim kutusunun gövde içeriği ( `ChildContent` ) ve başlığı ( `Title` ), bu bileşeni Kullanıcı arabiriminde kullanan bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="33e78-138">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="33e78-139">`OnYes`, düğme olayının tetiklediği bir C# yöntemidir `onclick` .</span><span class="sxs-lookup"><span data-stu-id="33e78-139">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="33e78-140">UI bileşimi için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="33e78-140"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="33e78-141">HTML öğeleri, bileşenleri belirtir ve bir etiketin öznitelikleri değerleri bir bileşenin özelliklerine iletir.</span><span class="sxs-lookup"><span data-stu-id="33e78-141">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="33e78-142">Aşağıdaki örnekte `Index` bileşen `Dialog` bileşeni kullanır.</span><span class="sxs-lookup"><span data-stu-id="33e78-142">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="33e78-143">`ChildContent`ve `Title` öğesi öznitelikleri ve içeriği tarafından ayarlanır `<Dialog>` .</span><span class="sxs-lookup"><span data-stu-id="33e78-143">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="33e78-144">*Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="33e78-144">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="33e78-145">Üst öğeye (*Index. Razor*) bir tarayıcıda erişildiğinde iletişim kutusu işlenir:</span><span class="sxs-lookup"><span data-stu-id="33e78-145">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Tarayıcıda işlenen iletişim kutusu bileşeni](index/_static/dialog.png)

<span data-ttu-id="33e78-147">Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) 'da ıntellisense ve [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.</span><span class="sxs-lookup"><span data-stu-id="33e78-147">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="33e78-148">Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı*adlı, tarayıcı belge nesne MODELI (DOM) ' ın bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="33e78-148">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="33e78-149">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="33e78-149"> WebAssembly</span></span>

Blazor<span data-ttu-id="33e78-150">WebAssembly, .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik tek sayfalı bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="33e78-150"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="33e78-151">WebAssembly, eklentiler veya Code transpilation olmadan açık Web standartları kullanır ve mobil tarayıcılar dahil tüm modern web tarayıcılarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-151"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="33e78-152">Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) *(kısaltılmış)* tarafından mümkün hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-152">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="33e78-153">WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir.</span><span class="sxs-lookup"><span data-stu-id="33e78-153">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="33e78-154">WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="33e78-154">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="33e78-155">WebAssembly Code, JavaScript ile *birlikte çalışabilirlik* (veya *JavaScript birlikte çalışma*) olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-155">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="33e78-156">Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="33e78-156">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="33e78-157">![BlazorWebAssembly, WebAssembly ile tarayıcıda .NET kodu çalıştırır.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="33e78-157">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="33e78-158">Bir Blazor weelsembly uygulaması bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="33e78-158">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="33e78-159">C# kod dosyaları ve Razor dosyaları .net Derlemeleriyle derlenir.</span><span class="sxs-lookup"><span data-stu-id="33e78-159">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="33e78-160">Derlemeler ve .NET çalışma zamanı tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-160">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="33e78-161">WebAssembly, .NET çalışma zamanı önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="33e78-161"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="33e78-162">BlazorWebassembly çalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek Için JavaScript birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="33e78-162">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="33e78-163">Yayınlanan uygulamanın boyutu, *Yük boyutu*, uygulamanın useyeteneğinin önemli bir performans etkendir.</span><span class="sxs-lookup"><span data-stu-id="33e78-163">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="33e78-164">Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek.</span><span class="sxs-lookup"><span data-stu-id="33e78-164">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="33e78-165">WebAssembly indirme sürelerini azaltmak için yük boyutunu iyileştirir:</span><span class="sxs-lookup"><span data-stu-id="33e78-165"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="33e78-166">Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:host-and-deploy/blazor/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="33e78-166">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="33e78-167">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="33e78-167">HTTP responses are compressed.</span></span>
* <span data-ttu-id="33e78-168">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="33e78-168">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="33e78-169">Server</span><span class="sxs-lookup"><span data-stu-id="33e78-169"> Server</span></span>

Blazor<span data-ttu-id="33e78-170">Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="33e78-170"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="33e78-171">Sunucu Razor , bir ASP.NET Core uygulamasındaki sunucuda bileşenleri barındırmak için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="33e78-171"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="33e78-172">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="33e78-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="33e78-173">Çalışma zamanı, tarayıcıdan sunucuya kullanıcı arabirimi olayları göndermeyi ve bileşenleri çalıştırdıktan sonra sunucu tarafından tarayıcıya geri gönderilen Kullanıcı arabirimi güncelleştirmelerini uygular.</span><span class="sxs-lookup"><span data-stu-id="33e78-173">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="33e78-174">BlazorSunucu tarafından tarayıcıyla iletişim kurmak için kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="33e78-174">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="33e78-175">![BlazorSunucu, sunucuda .NET kodu çalıştırır ve bir bağlantı üzerinden istemcideki Belge Nesne Modeli etkileşime girer SignalR](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="33e78-175">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="33e78-176">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="33e78-176">JavaScript interop</span></span>

<span data-ttu-id="33e78-177">Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="33e78-177">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="33e78-178">Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="33e78-178">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="33e78-179">C# kodu JavaScript kodunu çağırabilir ve JavaScript kodu C# koduna çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-179">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="33e78-180">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="33e78-180">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="33e78-181">Kod paylaşımı ve .NET Standard</span><span class="sxs-lookup"><span data-stu-id="33e78-181">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="33e78-182">[2,0 .NET Standard](/dotnet/standard/net-standard)uygular.</span><span class="sxs-lookup"><span data-stu-id="33e78-182"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="33e78-183">.NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="33e78-183">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="33e78-184">.NET Standard sınıf kitaplıkları Blazor , .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .net platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="33e78-184">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="33e78-185">Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) bir oluşturur <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="33e78-185">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33e78-186">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="33e78-186">Additional resources</span></span>

* [<span data-ttu-id="33e78-187">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="33e78-187">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="33e78-188">C# Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="33e78-188">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="33e78-189">HTML</span><span class="sxs-lookup"><span data-stu-id="33e78-189">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="33e78-190">[Başar Blazor ](https://github.com/AdrienTorris/awesome-blazor) Topluluk bağlantıları</span><span class="sxs-lookup"><span data-stu-id="33e78-190">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
