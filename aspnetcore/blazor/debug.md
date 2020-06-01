---
<span data-ttu-id="f069d-101">Başlık: ' Debug ASP.NET Core Blazor webassembly ' Author: guardrex açıklaması: ' uygulamalarda hata ayıklamayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="f069d-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="f069d-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/31/2020 No-loc:</span><span class="sxs-lookup"><span data-stu-id="f069d-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:</span></span>
- <span data-ttu-id="f069d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f069d-103">'Blazor'</span></span>
- <span data-ttu-id="f069d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f069d-104">'Identity'</span></span>
- <span data-ttu-id="f069d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f069d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f069d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f069d-106">'Razor'</span></span>
- <span data-ttu-id="f069d-107">' SignalR ' uid: blazor/Debug</span><span class="sxs-lookup"><span data-stu-id="f069d-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="f069d-108">Hata ayıklama ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="f069d-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="f069d-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="f069d-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="f069d-110">Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak WebAssembly uygulamalarına hata ayıklanabilir.</span><span class="sxs-lookup"><span data-stu-id="f069d-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="f069d-111">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f069d-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="f069d-112">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f069d-112">Available scenarios include:</span></span>

* <span data-ttu-id="f069d-113">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f069d-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f069d-114">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="f069d-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="f069d-115">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="f069d-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="f069d-116">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f069d-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="f069d-117">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="f069d-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="f069d-118">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="f069d-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="f069d-119">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="f069d-119">For now, you *can't*:</span></span>

* <span data-ttu-id="f069d-120">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="f069d-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="f069d-121">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="f069d-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="f069d-122">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="f069d-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f069d-123">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="f069d-123">Prerequisites</span></span>

<span data-ttu-id="f069d-124">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="f069d-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f069d-125">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="f069d-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="f069d-126">Google Chrome (sürüm 70 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="f069d-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="f069d-127">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="f069d-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="f069d-128">Mevcut bir weelsembly uygulamasında hata ayıklamayı etkinleştirmek için Blazor , başlangıç projesindeki *launchsettings. JSON* dosyasını her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :</span><span class="sxs-lookup"><span data-stu-id="f069d-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="f069d-129">' Yi güncelleştirdikten sonra, *Launchsettings. JSON* dosyası aşağıdaki örneğe benzer şekilde görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="f069d-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="f069d-130">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="f069d-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="f069d-131">IDE 'nin uygulamanın bir Blazor webassembly uygulaması olduğunu algılamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f069d-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="f069d-132">Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="f069d-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="f069d-133">`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f069d-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="f069d-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f069d-134">Visual Studio</span></span>

<span data-ttu-id="f069d-135">BlazorVisual Studio 'da bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="f069d-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="f069d-136">Yeni bir ASP.NET Core barındırılan Blazor webassembly uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f069d-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f069d-137">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f069d-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="f069d-138">Yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="f069d-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="f069d-139">**Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="f069d-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="f069d-141">`currentCount`Yereller penceresindeki alanın değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="f069d-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="f069d-143">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f069d-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="f069d-144">BlazorWebassembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f069d-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="f069d-145">İçindeki *Fetchdata. Razor* sayfasında bir kesme noktası ayarlayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f069d-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f069d-146">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="f069d-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f069d-147">Bir HTTP isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına gitmek için **verileri getir** sekmesine gidin `FetchData` :</span><span class="sxs-lookup"><span data-stu-id="f069d-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="f069d-149">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="f069d-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="f069d-151">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="f069d-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="f069d-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f069d-152">Visual Studio Code</span></span>

<span data-ttu-id="f069d-153">BlazorVisual Studio Code bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="f069d-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="f069d-154">[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` olarak ayarlandığı şekilde yükler `true` .</span><span class="sxs-lookup"><span data-stu-id="f069d-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Uzantılar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="f069d-157">Tek başına Blazor webassembly hatalarını ayıkla</span><span class="sxs-lookup"><span data-stu-id="f069d-157">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="f069d-158">BlazorVs Code ' de tek başına webassembly uygulamasını açın.</span><span class="sxs-lookup"><span data-stu-id="f069d-158">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="f069d-159">Aşağıdaki bildirimi, hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="f069d-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="f069d-160">Doğru uzantıların yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f069d-160">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="f069d-161">JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f069d-161">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="f069d-162">Pencereyi yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f069d-162">Reload the window.</span></span>

   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="f069d-164"><kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="f069d-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="f069d-165">İstendiğinde, hata ayıklamayı başlatmak için \*\* Blazor webassembly hata ayıklama\*\* seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f069d-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="f069d-167">Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.</span><span class="sxs-lookup"><span data-stu-id="f069d-167">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="f069d-168">Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="f069d-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="f069d-170">Barındırılan Blazor webassembly hata ayıklaması</span><span class="sxs-lookup"><span data-stu-id="f069d-170">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="f069d-171">Barındırılan Blazor webassembly uygulamasını vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="f069d-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="f069d-172">Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f069d-172">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="f069d-173">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f069d-173">Select **Yes**.</span></span>

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="f069d-175">Seçim penceresinde barındırılan çözüm içinde *sunucu* projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f069d-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="f069d-176">Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir *Launch. JSON* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f069d-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="f069d-177">Varolan bir hata ayıklama oturumuna Ekle</span><span class="sxs-lookup"><span data-stu-id="f069d-177">Attach to an existing debugging session</span></span>

<span data-ttu-id="f069d-178">Çalışan bir uygulamaya eklemek için Blazor aşağıdaki yapılandırmaya sahip bir *Launch. JSON* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f069d-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="f069d-179">Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f069d-179">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="f069d-180">Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="f069d-180">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="f069d-181">Yapılandırma seçeneklerini Başlat</span><span class="sxs-lookup"><span data-stu-id="f069d-181">Launch configuration options</span></span>

<span data-ttu-id="f069d-182">Hata ayıklama türü için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="f069d-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="f069d-183">Seçenek</span><span class="sxs-lookup"><span data-stu-id="f069d-183">Option</span></span>    | <span data-ttu-id="f069d-184">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f069d-184">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="f069d-185">Bir `launch` webassembly uygulamasına bir hata ayıklama oturumu başlatmak Blazor veya eklemek veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f069d-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="f069d-186">Hata ayıklanırken tarayıcıda açılacak URL.</span><span class="sxs-lookup"><span data-stu-id="f069d-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="f069d-187">Varsayılan olarak olur `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="f069d-187">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="f069d-188">Hata ayıklama oturumu için başlatılacak tarayıcı.</span><span class="sxs-lookup"><span data-stu-id="f069d-188">The browser to launch for the debugging session.</span></span> <span data-ttu-id="f069d-189">Ayarlanan `edge` veya `chrome`.</span><span class="sxs-lookup"><span data-stu-id="f069d-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="f069d-190">Varsayılan olarak olur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="f069d-190">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="f069d-191">JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f069d-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="f069d-192">`true`Günlük oluşturmak için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f069d-192">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="f069d-193">`true`Barındırılan webassembly uygulaması başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır Blazor .</span><span class="sxs-lookup"><span data-stu-id="f069d-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="f069d-194">Web sunucusunun mutlak yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f069d-194">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="f069d-195">Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f069d-195">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="f069d-196">Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı.</span><span class="sxs-lookup"><span data-stu-id="f069d-196">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="f069d-197">Varsayılan değer 30.000 milisaniyedir (30 saniye).</span><span class="sxs-lookup"><span data-stu-id="f069d-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="f069d-198">Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru.</span><span class="sxs-lookup"><span data-stu-id="f069d-198">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="f069d-199">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="f069d-199">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="f069d-200">Üzerinde uygulamayı başlatmak için çalışma dizini.</span><span class="sxs-lookup"><span data-stu-id="f069d-200">The working directory to launch the app under.</span></span> <span data-ttu-id="f069d-201">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="f069d-201">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="f069d-202">Başlatılan işleme sağlanacak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f069d-202">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="f069d-203">Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` .</span><span class="sxs-lookup"><span data-stu-id="f069d-203">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="f069d-204">Örnek başlatma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f069d-204">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="f069d-205">Tek başına Blazor webassembly uygulamasını başlatma ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f069d-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="f069d-206">Belirtilen URL 'de çalışan bir uygulamaya iliştirme</span><span class="sxs-lookup"><span data-stu-id="f069d-206">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="f069d-207">Barındırılan Blazor webassembly uygulamasını başlatma ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="f069d-207">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="f069d-208">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="f069d-208">Debug in the browser</span></span>

1. <span data-ttu-id="f069d-209">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f069d-209">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="f069d-210"><kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f069d-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="f069d-211">Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f069d-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="f069d-212">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f069d-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="f069d-213">Hata sayfası, hata ayıklama Blazor proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="f069d-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="f069d-214">*Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f069d-214">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="f069d-215">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f069d-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="f069d-216">Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs* / *. Razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="f069d-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="f069d-217">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="f069d-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="f069d-218">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="f069d-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="f069d-219">[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="f069d-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f069d-220">Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="f069d-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f069d-221">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="f069d-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f069d-222">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="f069d-222">Browser source maps</span></span>

<span data-ttu-id="f069d-223">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="f069d-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f069d-224">Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="f069d-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f069d-225">Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="f069d-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f069d-226">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f069d-226">Troubleshoot</span></span>

<span data-ttu-id="f069d-227">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="f069d-227">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="f069d-228">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="f069d-228">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f069d-229">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="f069d-229">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="f069d-230">ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f069d-230">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="f069d-231">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="f069d-231">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
