---
title: Hata Ayıklama Blazor ASP.NET Çekirdek WebAssembly
author: guardrex
description: Uygulamaları nasıl hata Blazor ayıklamakonusunda öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7273ae3d240de0b59a58069fdcc1880247379751
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661602"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="00c5a-103">Hata Ayıklama Blazor ASP.NET Çekirdek WebAssembly</span><span class="sxs-lookup"><span data-stu-id="00c5a-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="00c5a-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="00c5a-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="00c5a-105">WebAssembly uygulamaları Krom tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak debugged olabilir.</span><span class="sxs-lookup"><span data-stu-id="00c5a-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="00c5a-106">Alternatif olarak Visual Studio veya Visual Studio Code'u kullanarak uygulamanızı hata ayıklayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="00c5a-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="00c5a-107">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="00c5a-107">Available scenarios include:</span></span>

* <span data-ttu-id="00c5a-108">Kesme noktalarını ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="00c5a-109">Visual Studio ve Visual Studio Code<kbd>(F5</kbd> desteği) hata ayıklama desteği ile uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="00c5a-110">Kod aracılığıyla tek adımlı<kbd>(F10).</kbd></span><span class="sxs-lookup"><span data-stu-id="00c5a-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="00c5a-111">Visual Studio veya Visual Studio Code'da bir tarayıcıda <kbd>F8</kbd> veya <kbd>F5</kbd> ile kod yürütmeye devam edin.</span><span class="sxs-lookup"><span data-stu-id="00c5a-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="00c5a-112">Yerel *ekranda,* yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="00c5a-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="00c5a-113">JavaScript'ten .NET'e ve .NET'ten JavaScript'e giden çağrı zincirleri de dahil olmak üzere arama yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="00c5a-114">Şimdilik, *yapamam:*</span><span class="sxs-lookup"><span data-stu-id="00c5a-114">For now, you *can't*:</span></span>

* <span data-ttu-id="00c5a-115">Dizileri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="00c5a-115">Inspect arrays.</span></span>
* <span data-ttu-id="00c5a-116">Üyeleri incelemek için hover.</span><span class="sxs-lookup"><span data-stu-id="00c5a-116">Hover to inspect members.</span></span>
* <span data-ttu-id="00c5a-117">Yönetilen koda hata ayıklama adımı veya çıkan.</span><span class="sxs-lookup"><span data-stu-id="00c5a-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="00c5a-118">Değer türlerini incelemek için tam desteğe sahip.</span><span class="sxs-lookup"><span data-stu-id="00c5a-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="00c5a-119">Işlenmemiş özel durumlara son verin.</span><span class="sxs-lookup"><span data-stu-id="00c5a-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="00c5a-120">Uygulama nın başlatılması sırasında kesme noktalarına vurun.</span><span class="sxs-lookup"><span data-stu-id="00c5a-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="00c5a-121">Bir servis çalışanı olan bir uygulamayı hata ayıklama.</span><span class="sxs-lookup"><span data-stu-id="00c5a-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="00c5a-122">Biz yaklaşan sürümlerde hata ayıklama deneyimini geliştirmeye devam edecektir.</span><span class="sxs-lookup"><span data-stu-id="00c5a-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="00c5a-123">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="00c5a-123">Prerequisites</span></span>

<span data-ttu-id="00c5a-124">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="00c5a-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="00c5a-125">Microsoft Edge (sürüm 80 veya sonraki sürüm)</span><span class="sxs-lookup"><span data-stu-id="00c5a-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="00c5a-126">Google Chrome (sürüm 70 veya sonrası)</span><span class="sxs-lookup"><span data-stu-id="00c5a-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="00c5a-127">Visual Studio ve Visual Studio Code için hata ayıklama etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="00c5a-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="00c5a-128">Hata ayıklama, ASP.NET Core 3.2 Preview 3 veya daha sonraki Blazor WebAssembly proje şablonu kullanılarak oluşturulan yeni projeler için otomatik olarak[etkinleştirilir (geçerli sürüm 3,2 Önizleme 4'tür).](xref:blazor/get-started)</span><span class="sxs-lookup"><span data-stu-id="00c5a-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="00c5a-129">Varolan Blazor bir WebAssembly uygulaması için hata ayıklamayı etkinleştirmek için, başlangıç projesindeki `inspectUri` *launchSettings.json* dosyasını her başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="00c5a-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="00c5a-130">Bir kez güncelleştirildikten sonra *launchSettings.json* dosyası aşağıdaki örneğe benzer olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="00c5a-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="00c5a-131">Özellik: `inspectUri`</span><span class="sxs-lookup"><span data-stu-id="00c5a-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="00c5a-132">IDE'nin uygulamanın bir Blazor WebAssembly uygulaması olduğunu algılamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="00c5a-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="00c5a-133">Komut dosyası hata ayıklama altyapısının 'hata Blazorayıklama proxy' aracılığıyla tarayıcıya bağlanmasını bildirir.</span><span class="sxs-lookup"><span data-stu-id="00c5a-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="00c5a-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="00c5a-134">Visual Studio</span></span>

<span data-ttu-id="00c5a-135">Visual Studio'da bir Blazor WebAssembly uygulamasını hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="00c5a-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="00c5a-136">[Visual Studio 2019 16.6'nın (Önizleme](https://visualstudio.com/preview) 2 veya sonraki sürüm) en son önizleme sürümüne sahip olduğunuzdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="00c5a-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="00c5a-137">Core barındırılan Blazor yeni bir ASP.NET WebAssembly uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="00c5a-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="00c5a-138">Uygulamayı hata ayıklamada çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="00c5a-139">`IncrementCount` Yöntemde *Counter.razor'da* bir kırılma noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="00c5a-140">**Sayaç** sekmesine göz atın ve kesme noktasına basmak için düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="00c5a-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Hata Ayıklama Sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="00c5a-142">Yerel penceredeki `currentCount` alanın değerine göz atın:</span><span class="sxs-lookup"><span data-stu-id="00c5a-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerel halkları görüntüleyin](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="00c5a-144">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="00c5a-145">WebAssembly uygulamanızı Blazor hata ayıklarken sunucu kodunuzu da hata ayıklayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="00c5a-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="00c5a-146">*FetchData.razor* sayfasında bir kesme noktası `OnInitializedAsync`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="00c5a-147">`Get` Eylem yönteminde `WeatherForecastController` bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="00c5a-148">Sunucuya bir **Fetch Data** HTTP isteği vermeden hemen önce `FetchData` bileşendeki ilk kesme noktasına basmak için Verileri Getir sekmesine göz atın:</span><span class="sxs-lookup"><span data-stu-id="00c5a-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Hata Ayıklama Verisi](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="00c5a-150">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın `WeatherForecastController`ve ardından sunucudaki kesme noktasına şu şekilde vurdu:</span><span class="sxs-lookup"><span data-stu-id="00c5a-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="00c5a-152">Yürütmenin devam etmesini ve hava tahmini tablosunun işlenmesini görmek için <kbd>F5</kbd> tuşuna tekrar basın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="00c5a-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="00c5a-153">Visual Studio Code</span></span>

<span data-ttu-id="00c5a-154">Visual Studio Blazor Code'ta bir WebAssembly uygulamasını hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="00c5a-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="00c5a-155">[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript Hata Ayıklama (Gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` ' olarak `true`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Uzantıları](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS önizleme hata ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="00c5a-158">Hata ayıklama etkin olan varolan Blazor bir WebAssembly uygulamasını açın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="00c5a-159">Hata ayıklamayı etkinleştirmek için ek kurulum gerektiğine dair aşağıdaki bildirimi alırsanız, doğru uzantıların yüklü olduğunu ve JavaScript önizleme hata ayıklamanın etkin olduğunu onaylayın ve ardından pencereyi yeniden yükleyin:</span><span class="sxs-lookup"><span data-stu-id="00c5a-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Ek kurulum requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="00c5a-161">Bir bildirim, bina ve hata ayıklama için gerekli varlıkları uygulamaya eklemeyi teklif ediyor.</span><span class="sxs-lookup"><span data-stu-id="00c5a-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="00c5a-162">**Evet'i**seçin :</span><span class="sxs-lookup"><span data-stu-id="00c5a-162">Select **Yes**:</span></span>

     ![Gerekli varlıkları ekleme](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="00c5a-164">Uygulamayı hata ayıklamada başlatmak iki aşamalı bir işlemdir:</span><span class="sxs-lookup"><span data-stu-id="00c5a-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="00c5a-165">1\.</span><span class="sxs-lookup"><span data-stu-id="00c5a-165">1\.</span></span> <span data-ttu-id="00c5a-166">**İlk olarak**, **.NET Core LaunchBlazor (Bağımsız)** başlatma yapılandırmasını kullanarak uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="00c5a-167">2\.</span><span class="sxs-lookup"><span data-stu-id="00c5a-167">2\.</span></span> <span data-ttu-id="00c5a-168">**Uygulama başladıktan sonra**Chrome başlatma **yapılandırmasında .NET Blazor Core Hata Ayıklama Web Derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerektirir).</span><span class="sxs-lookup"><span data-stu-id="00c5a-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="00c5a-169">Chrome yerine Edge'i kullanmak `type` için *.vscode/launch.json'daki* `pwa-chrome` başlatma `pwa-msedge`yapılandırmasını .</span><span class="sxs-lookup"><span data-stu-id="00c5a-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="00c5a-170">`Counter` Bileşendeki `IncrementCount` yöntemde bir kesme noktası ayarlayın ve ardından kesme noktasına basmak için düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="00c5a-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Kodunda Hata Ayıklama Sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="00c5a-172">Tarayıcıda hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="00c5a-172">Debug in the browser</span></span>

1. <span data-ttu-id="00c5a-173">Geliştirme ortamında uygulamanın hata ayıklama oluşturma sını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="00c5a-174">+Shift <kbd>Shift</kbd><kbd>Alt</kbd>+<kbd>D</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="00c5a-175">Tarayıcı uzaktan hata ayıklama etkin çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00c5a-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="00c5a-176">Uzaktan hata ayıklama devre dışı bırakılırsa, **hata ayıklanabilir tarayıcı sekmesi** hata sayfası bulunamaz.</span><span class="sxs-lookup"><span data-stu-id="00c5a-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="00c5a-177">Hata sayfası, hata ayıklama proxy'sinin uygulamaya bağlanabilmesi için Blazor hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmak için yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="00c5a-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="00c5a-178">*Tüm tarayıcı örneklerini kapatın* ve tarayıcıyı söylendiği gibi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="00c5a-179">Tarayıcı uzaktan hata ayıklama etkinken çalıştırDıktan sonra, hata ayıklama klavyesi kısayolu yeni bir hata ayıklama sekmesini açar. Bir süre **sonra, Kaynaklar** sekmesi uygulamadaki .NET derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="00c5a-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="00c5a-180">Her derlemeyi genişletin ve hata ayıklama için kullanılabilir *.cs*/*.razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="00c5a-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="00c5a-181">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktalarına vurulun.</span><span class="sxs-lookup"><span data-stu-id="00c5a-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="00c5a-182">Bir kesme noktası vurulduktan sonra, tek adımlı<kbd>(F10)</kbd>kodu veya devam<kbd>(F8</kbd>) kod yürütme normal üzerinden.</span><span class="sxs-lookup"><span data-stu-id="00c5a-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="00c5a-183">[Chrome DevTools Protokolü'nü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolü ' yle artıran bir hata ayıklama proxy'si sağlar. NET'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="00c5a-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="00c5a-184">Klavye kısayolu hata ayıklama basıldığında, Blazor Chrome DevTools'u proxy'ye doğru işaret edin.</span><span class="sxs-lookup"><span data-stu-id="00c5a-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="00c5a-185">Proxy hata ayıklamak istediğiniz tarayıcı penceresine bağlanır (dolayısıyla uzaktan hata ayıklama etkinleştirme gereksinimi).</span><span class="sxs-lookup"><span data-stu-id="00c5a-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="00c5a-186">Tarayıcı kaynak haritaları</span><span class="sxs-lookup"><span data-stu-id="00c5a-186">Browser source maps</span></span>

<span data-ttu-id="00c5a-187">Tarayıcı kaynak haritaları, tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına eşlemesine olanak sağlar ve genellikle istemci tarafı hata ayıklama için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="00c5a-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="00c5a-188">Ancak, Blazor şu anda C# ile doğrudan JavaScript/WASM eşlemiyor.</span><span class="sxs-lookup"><span data-stu-id="00c5a-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="00c5a-189">Bunun Blazor yerine, tarayıcı içinde IL yorumu yapar, bu nedenle kaynak haritalar ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="00c5a-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="00c5a-190">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="00c5a-190">Troubleshoot</span></span>

<span data-ttu-id="00c5a-191">Hatalarla karşınıza çıktıysanız, aşağıdaki ipucu size yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="00c5a-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="00c5a-192">Hata **Ayıklama** sekmesinde, tarayıcınızdaki geliştirici araçlarını açın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="00c5a-193">Konsolda, herhangi `localStorage.clear()` bir kesme noktalarını kaldırmak için çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="00c5a-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
