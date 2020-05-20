---
<span data-ttu-id="188da-101">Başlık: ' Debug ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' uygulamalarda hata ayıklamayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="188da-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="188da-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="188da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="188da-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="188da-103">'Blazor'</span></span>
- <span data-ttu-id="188da-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="188da-104">'Identity'</span></span>
- <span data-ttu-id="188da-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="188da-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="188da-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="188da-106">'Razor'</span></span>
- <span data-ttu-id="188da-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="188da-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="188da-108">Hata ayıklama ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="188da-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="188da-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="188da-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="188da-110">Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak WebAssembly uygulamalarına hata ayıklanabilir.</span><span class="sxs-lookup"><span data-stu-id="188da-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="188da-111">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="188da-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="188da-112">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="188da-112">Available scenarios include:</span></span>

* <span data-ttu-id="188da-113">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="188da-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="188da-114">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="188da-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="188da-115">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="188da-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="188da-116">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="188da-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="188da-117">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="188da-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="188da-118">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="188da-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="188da-119">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="188da-119">For now, you *can't*:</span></span>

* <span data-ttu-id="188da-120">Dizileri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="188da-120">Inspect arrays.</span></span>
* <span data-ttu-id="188da-121">Üyeleri incelemek için üzerine gelin.</span><span class="sxs-lookup"><span data-stu-id="188da-121">Hover to inspect members.</span></span>
* <span data-ttu-id="188da-122">Yönetilen kodun içine veya dışına hata ayıklama adımı.</span><span class="sxs-lookup"><span data-stu-id="188da-122">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="188da-123">Değer türlerini incelemek için tam destek vardır.</span><span class="sxs-lookup"><span data-stu-id="188da-123">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="188da-124">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="188da-124">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="188da-125">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="188da-125">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="188da-126">Hizmet çalışanıyla bir uygulamada hata ayıklama.</span><span class="sxs-lookup"><span data-stu-id="188da-126">Debug an app with a service worker.</span></span>

<span data-ttu-id="188da-127">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="188da-127">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="188da-128">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="188da-128">Prerequisites</span></span>

<span data-ttu-id="188da-129">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="188da-129">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="188da-130">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="188da-130">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="188da-131">Google Chrome (sürüm 70 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="188da-131">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="188da-132">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="188da-132">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="188da-133">Mevcut bir weelsembly uygulamasında hata ayıklamayı etkinleştirmek için Blazor , başlangıç projesindeki *launchsettings. JSON* dosyasını her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :</span><span class="sxs-lookup"><span data-stu-id="188da-133">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="188da-134">' Yi güncelleştirdikten sonra, *Launchsettings. JSON* dosyası aşağıdaki örneğe benzer şekilde görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="188da-134">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="188da-135">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="188da-135">The `inspectUri` property:</span></span>

* <span data-ttu-id="188da-136">IDE 'nin uygulamanın bir Blazor webassembly uygulaması olduğunu algılamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="188da-136">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="188da-137">Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="188da-137">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="188da-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="188da-138">Visual Studio</span></span>

<span data-ttu-id="188da-139">BlazorVisual Studio 'da bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="188da-139">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="188da-140">Yeni bir ASP.NET Core barındırılan Blazor webassembly uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="188da-140">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="188da-141">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="188da-141">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="188da-142">Yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="188da-142">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="188da-143">**Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="188da-143">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="188da-145">`currentCount`Yereller penceresindeki alanın değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="188da-145">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="188da-147">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="188da-147">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="188da-148">BlazorWebassembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="188da-148">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="188da-149">İçindeki *Fetchdata. Razor* sayfasında bir kesme noktası ayarlayın `OnInitializedAsync` .</span><span class="sxs-lookup"><span data-stu-id="188da-149">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="188da-150">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="188da-150">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="188da-151">Bir HTTP isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına gitmek için **verileri getir** sekmesine gidin `FetchData` :</span><span class="sxs-lookup"><span data-stu-id="188da-151">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="188da-153">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="188da-153">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="188da-155">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="188da-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="188da-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="188da-156">Visual Studio Code</span></span>

<span data-ttu-id="188da-157">BlazorVisual Studio Code bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="188da-157">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="188da-158">[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` olarak ayarlandığı şekilde yükler `true` .</span><span class="sxs-lookup"><span data-stu-id="188da-158">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Uzantılar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="188da-161">BlazorHata ayıklama etkinken mevcut bir webassembly uygulamasını açın.</span><span class="sxs-lookup"><span data-stu-id="188da-161">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="188da-162">Hata ayıklamayı etkinleştirmek için ek kurulum gerekli olduğunda aşağıdaki bildirime sahipseniz, doğru uzantıların yüklü olduğunu ve JavaScript önizlemesi hata ayıklamanın etkin olduğunu doğrulayın ve ardından pencereyi yeniden yükleyin:</span><span class="sxs-lookup"><span data-stu-id="188da-162">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="188da-164">Derleme ve hata ayıklama için gerekli varlıkları uygulamaya eklemek için bir bildirim sunulur.</span><span class="sxs-lookup"><span data-stu-id="188da-164">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="188da-165">**Evet**' i seçin:</span><span class="sxs-lookup"><span data-stu-id="188da-165">Select **Yes**:</span></span>

     ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="188da-167">Uygulamanın hata ayıklayıcıda başlatılması iki adımlı bir işlemdir:</span><span class="sxs-lookup"><span data-stu-id="188da-167">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="188da-168">1 \.</span><span class="sxs-lookup"><span data-stu-id="188da-168">1\.</span></span> <span data-ttu-id="188da-169">**İlk**olarak, **.NET Core başlatma ( Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="188da-169">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="188da-170">2 \.</span><span class="sxs-lookup"><span data-stu-id="188da-170">2\.</span></span> <span data-ttu-id="188da-171">**Uygulama başladıktan sonra**, Chrome başlatma yapılandırmasındaki **.NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir).</span><span class="sxs-lookup"><span data-stu-id="188da-171">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="188da-172">Chrome yerine Edge kullanmak için, `type` *. vscode/Launch. JSON* içindeki başlatma yapılandırmasının öğesini `pwa-chrome` olarak değiştirin `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="188da-172">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="188da-173">Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="188da-173">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="188da-175">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="188da-175">Debug in the browser</span></span>

1. <span data-ttu-id="188da-176">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="188da-176">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="188da-177"><kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="188da-177">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="188da-178">Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="188da-178">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="188da-179">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="188da-179">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="188da-180">Hata sayfası, hata ayıklama Blazor proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="188da-180">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="188da-181">*Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="188da-181">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="188da-182">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="188da-182">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="188da-183">Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs* / *. Razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="188da-183">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="188da-184">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="188da-184">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="188da-185">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="188da-185">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="188da-186">[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="188da-186"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="188da-187">Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="188da-187">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="188da-188">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="188da-188">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="188da-189">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="188da-189">Browser source maps</span></span>

<span data-ttu-id="188da-190">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="188da-190">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="188da-191">Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="188da-191">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="188da-192">Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="188da-192">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="188da-193">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="188da-193">Troubleshoot</span></span>

<span data-ttu-id="188da-194">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipucu yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="188da-194">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="188da-195">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="188da-195">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="188da-196">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="188da-196">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
