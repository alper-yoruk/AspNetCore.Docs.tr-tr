---
<span data-ttu-id="adaab-101">Başlık: ' Debug ASP.NET Core Blazor webassembly ' Author: guardrex açıklaması: ' uygulamalarda hata ayıklamayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="adaab-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="adaab-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/29/2020 No-loc:</span><span class="sxs-lookup"><span data-stu-id="adaab-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/29/2020 no-loc:</span></span>
- <span data-ttu-id="adaab-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="adaab-103">'Blazor'</span></span>
- <span data-ttu-id="adaab-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="adaab-104">'Identity'</span></span>
- <span data-ttu-id="adaab-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="adaab-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="adaab-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="adaab-106">'Razor'</span></span>
- <span data-ttu-id="adaab-107">' SignalR ' uid: blazor/Debug</span><span class="sxs-lookup"><span data-stu-id="adaab-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="adaab-108">Hata ayıklama ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="adaab-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="adaab-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="adaab-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="adaab-110">Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak WebAssembly uygulamalarına hata ayıklanabilir.</span><span class="sxs-lookup"><span data-stu-id="adaab-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="adaab-111">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="adaab-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="adaab-112">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="adaab-112">Available scenarios include:</span></span>

* <span data-ttu-id="adaab-113">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="adaab-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="adaab-114">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="adaab-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="adaab-115">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="adaab-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="adaab-116">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="adaab-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="adaab-117">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="adaab-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="adaab-118">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="adaab-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="adaab-119">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="adaab-119">For now, you *can't*:</span></span>

* <span data-ttu-id="adaab-120">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="adaab-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="adaab-121">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="adaab-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="adaab-122">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="adaab-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="adaab-123">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="adaab-123">Prerequisites</span></span>

<span data-ttu-id="adaab-124">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="adaab-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="adaab-125">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="adaab-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="adaab-126">Google Chrome (sürüm 70 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="adaab-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="adaab-127">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="adaab-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="adaab-128">Mevcut bir weelsembly uygulamasında hata ayıklamayı etkinleştirmek için Blazor , başlangıç projesindeki *launchsettings. JSON* dosyasını her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :</span><span class="sxs-lookup"><span data-stu-id="adaab-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="adaab-129">' Yi güncelleştirdikten sonra, *Launchsettings. JSON* dosyası aşağıdaki örneğe benzer şekilde görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="adaab-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="adaab-130">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="adaab-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="adaab-131">IDE 'nin uygulamanın bir Blazor webassembly uygulaması olduğunu algılamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="adaab-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="adaab-132">Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="adaab-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="adaab-133">`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="adaab-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="adaab-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="adaab-134">Visual Studio</span></span>

<span data-ttu-id="adaab-135">BlazorVisual Studio 'da bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="adaab-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="adaab-136">Yeni bir ASP.NET Core barındırılan Blazor webassembly uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="adaab-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="adaab-137">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="adaab-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="adaab-138">Yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="adaab-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="adaab-139">**Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="adaab-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="adaab-141">`currentCount`Yereller penceresindeki alanın değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="adaab-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="adaab-143">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="adaab-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="adaab-144">BlazorWebassembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="adaab-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="adaab-145">İçindeki *Fetchdata. Razor* sayfasında bir kesme noktası ayarlayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="adaab-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="adaab-146">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="adaab-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="adaab-147">Bir HTTP isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına gitmek için **verileri getir** sekmesine gidin `FetchData` :</span><span class="sxs-lookup"><span data-stu-id="adaab-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="adaab-149">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="adaab-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="adaab-151">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="adaab-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="adaab-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="adaab-152">Visual Studio Code</span></span>

<span data-ttu-id="adaab-153">BlazorVisual Studio Code bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="adaab-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="adaab-154">[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` olarak ayarlandığı şekilde yükler `true` .</span><span class="sxs-lookup"><span data-stu-id="adaab-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Uzantılar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="adaab-157">BlazorHata ayıklama etkinken mevcut bir webassembly uygulamasını açın.</span><span class="sxs-lookup"><span data-stu-id="adaab-157">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="adaab-158">Hata ayıklamayı etkinleştirmek için ek kurulum gerekli olduğunda aşağıdaki bildirime sahipseniz, doğru uzantıların yüklü olduğunu ve JavaScript önizlemesi hata ayıklamanın etkin olduğunu doğrulayın ve ardından pencereyi yeniden yükleyin:</span><span class="sxs-lookup"><span data-stu-id="adaab-158">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="adaab-160">Derleme ve hata ayıklama için gerekli varlıkları uygulamaya eklemek için bir bildirim sunulur.</span><span class="sxs-lookup"><span data-stu-id="adaab-160">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="adaab-161">**Evet**' i seçin:</span><span class="sxs-lookup"><span data-stu-id="adaab-161">Select **Yes**:</span></span>

     ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="adaab-163">Uygulamanın hata ayıklayıcıda başlatılması iki adımlı bir işlemdir:</span><span class="sxs-lookup"><span data-stu-id="adaab-163">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="adaab-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="adaab-164">1\.</span></span> <span data-ttu-id="adaab-165">**İlk**olarak, **.NET Core başlatma ( Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="adaab-165">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="adaab-166">2 \.</span><span class="sxs-lookup"><span data-stu-id="adaab-166">2\.</span></span> <span data-ttu-id="adaab-167">**Uygulama başladıktan sonra**, Chrome başlatma yapılandırmasındaki **.NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir).</span><span class="sxs-lookup"><span data-stu-id="adaab-167">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="adaab-168">Chrome yerine Edge kullanmak için, `type` *. vscode/Launch. JSON* içindeki başlatma yapılandırmasının öğesini `pwa-chrome` olarak değiştirin `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="adaab-168">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="adaab-169">Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="adaab-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="adaab-171">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="adaab-171">Debug in the browser</span></span>

1. <span data-ttu-id="adaab-172">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="adaab-172">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="adaab-173"><kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="adaab-173">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="adaab-174">Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="adaab-174">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="adaab-175">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="adaab-175">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="adaab-176">Hata sayfası, hata ayıklama Blazor proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="adaab-176">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="adaab-177">*Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="adaab-177">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="adaab-178">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="adaab-178">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="adaab-179">Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs* / *. Razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="adaab-179">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="adaab-180">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="adaab-180">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="adaab-181">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="adaab-181">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="adaab-182">[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="adaab-182"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="adaab-183">Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="adaab-183">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="adaab-184">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="adaab-184">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="adaab-185">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="adaab-185">Browser source maps</span></span>

<span data-ttu-id="adaab-186">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="adaab-186">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="adaab-187">Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="adaab-187">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="adaab-188">Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="adaab-188">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="adaab-189">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="adaab-189">Troubleshoot</span></span>

<span data-ttu-id="adaab-190">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="adaab-190">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="adaab-191">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="adaab-191">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="adaab-192">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="adaab-192">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="adaab-193">ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="adaab-193">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="adaab-194">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="adaab-194">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
