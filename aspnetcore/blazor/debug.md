---
title: Hata ayıklama ASP.NET Core Blazor webassembly
author: guardrex
description: Uygulamalarda hata ayıklamayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84239224"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7b1c5-103">Hata ayıklama ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="7b1c5-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="7b1c5-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="7b1c5-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="7b1c5-105">Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak WebAssembly uygulamalarına hata ayıklanabilir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="7b1c5-106">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="7b1c5-107">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-107">Available scenarios include:</span></span>

* <span data-ttu-id="7b1c5-108">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7b1c5-109">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="7b1c5-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="7b1c5-110">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="7b1c5-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="7b1c5-111">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="7b1c5-112">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="7b1c5-113">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7b1c5-114">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-114">For now, you *can't*:</span></span>

* <span data-ttu-id="7b1c5-115">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7b1c5-116">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="7b1c5-117">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b1c5-118">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="7b1c5-118">Prerequisites</span></span>

<span data-ttu-id="7b1c5-119">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7b1c5-120">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="7b1c5-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="7b1c5-121">Google Chrome (sürüm 70 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="7b1c5-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="7b1c5-122">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="7b1c5-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="7b1c5-123">Varolan bir weelsembly uygulamasında hata ayıklamayı etkinleştirmek için Blazor , başlangıç projesindeki dosya *launchSettings.js* `inspectUri` her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-123">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7b1c5-124">Güncelleştirildikten sonra, dosyadaki *launchSettings.js* aşağıdaki örneğe benzer şekilde görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-124">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7b1c5-125">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="7b1c5-126">IDE 'nin uygulamanın bir Blazor webassembly uygulaması olduğunu algılamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7b1c5-127">Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="7b1c5-128">`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="7b1c5-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b1c5-129">Visual Studio</span></span>

<span data-ttu-id="7b1c5-130">BlazorVisual Studio 'da bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7b1c5-131">Yeni bir ASP.NET Core barındırılan Blazor webassembly uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7b1c5-132">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="7b1c5-133">Yönteminde *Counter. Razor* içinde bir kesme noktası ayarlayın `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-133">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="7b1c5-134">**Sayaç** sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-134">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="7b1c5-136">`currentCount`Yereller penceresindeki alanın değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="7b1c5-138">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7b1c5-139">BlazorWebassembly uygulamanızda hata ayıklarken, sunucu kodunuzda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="7b1c5-140">İçindeki *Fetchdata. Razor* sayfasında bir kesme noktası ayarlayın <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-140">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7b1c5-141">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7b1c5-142">Bir HTTP isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına gitmek için **verileri getir** sekmesine gidin `FetchData` :</span><span class="sxs-lookup"><span data-stu-id="7b1c5-142">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="7b1c5-144">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="7b1c5-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="7b1c5-146">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="7b1c5-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7b1c5-147">Visual Studio Code</span></span>

<span data-ttu-id="7b1c5-148">BlazorVisual Studio Code bir webassembly uygulamasında hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="7b1c5-149">[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` olarak ayarlandığı şekilde yükler `true` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Uzantılar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="7b1c5-152">Tek başına Blazor webassembly hatalarını ayıkla</span><span class="sxs-lookup"><span data-stu-id="7b1c5-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="7b1c5-153">BlazorVs Code ' de tek başına webassembly uygulamasını açın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="7b1c5-154">Aşağıdaki bildirimi, hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="7b1c5-155">Doğru uzantıların yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="7b1c5-156">JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="7b1c5-157">Pencereyi yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-157">Reload the window.</span></span>

   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="7b1c5-159"><kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="7b1c5-160">İstendiğinde, hata ayıklamayı başlatmak için \*\* Blazor webassembly hata ayıklama\*\* seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="7b1c5-162">Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="7b1c5-163">Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="7b1c5-165">Barındırılan Blazor webassembly hata ayıklaması</span><span class="sxs-lookup"><span data-stu-id="7b1c5-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="7b1c5-166">Barındırılan Blazor webassembly uygulamasını vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="7b1c5-167">Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="7b1c5-168">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-168">Select **Yes**.</span></span>

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="7b1c5-170">Seçim penceresinde barındırılan çözüm içinde *sunucu* projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="7b1c5-171">Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile dosyada bir *launch.js* oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-171">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7b1c5-172">Varolan bir hata ayıklama oturumuna Ekle</span><span class="sxs-lookup"><span data-stu-id="7b1c5-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="7b1c5-173">Çalışan bir uygulamaya eklemek için Blazor aşağıdaki yapılandırmaya sahip bir *launch.js* dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-173">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7b1c5-174">Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="7b1c5-175">Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="7b1c5-176">Yapılandırma seçeneklerini Başlat</span><span class="sxs-lookup"><span data-stu-id="7b1c5-176">Launch configuration options</span></span>

<span data-ttu-id="7b1c5-177">Hata ayıklama türü için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `blazorwasm` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="7b1c5-178">Seçenek</span><span class="sxs-lookup"><span data-stu-id="7b1c5-178">Option</span></span>    | <span data-ttu-id="7b1c5-179">Açıklama</span><span class="sxs-lookup"><span data-stu-id="7b1c5-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7b1c5-180">Bir `launch` webassembly uygulamasına bir hata ayıklama oturumu başlatmak Blazor veya eklemek veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="7b1c5-181">Hata ayıklanırken tarayıcıda açılacak URL.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7b1c5-182">Varsayılan olarak olur `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="7b1c5-183">Hata ayıklama oturumu için başlatılacak tarayıcı.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="7b1c5-184">Ayarlanan `edge` veya `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7b1c5-185">Varsayılan olarak olur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="7b1c5-186">JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7b1c5-187">`true`Günlük oluşturmak için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="7b1c5-188">`true`Barındırılan webassembly uygulaması başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır Blazor .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="7b1c5-189">Web sunucusunun mutlak yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="7b1c5-190">Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="7b1c5-191">Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="7b1c5-192">Varsayılan değer 30.000 milisaniyedir (30 saniye).</span><span class="sxs-lookup"><span data-stu-id="7b1c5-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="7b1c5-193">Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="7b1c5-194">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="7b1c5-195">Üzerinde uygulamayı başlatmak için çalışma dizini.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-195">The working directory to launch the app under.</span></span> <span data-ttu-id="7b1c5-196">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="7b1c5-197">Başlatılan işleme sağlanacak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="7b1c5-198">Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` .</span><span class="sxs-lookup"><span data-stu-id="7b1c5-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="7b1c5-199">Örnek başlatma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7b1c5-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="7b1c5-200">Tek başına Blazor webassembly uygulamasını başlatma ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="7b1c5-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7b1c5-201">Belirtilen URL 'de çalışan bir uygulamaya iliştirme</span><span class="sxs-lookup"><span data-stu-id="7b1c5-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="7b1c5-202">Barındırılan Blazor webassembly uygulamasını başlatma ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="7b1c5-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="7b1c5-203">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="7b1c5-203">Debug in the browser</span></span>

1. <span data-ttu-id="7b1c5-204">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7b1c5-205"><kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="7b1c5-206">Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="7b1c5-207">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="7b1c5-208">Hata sayfası, hata ayıklama Blazor proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="7b1c5-209">*Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="7b1c5-210">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="7b1c5-211">Her bir derlemeyi genişletin ve hata ayıklama için kullanılabilen *. cs* / *. Razor* kaynak dosyalarını bulun.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-211">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="7b1c5-212">Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="7b1c5-213">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="7b1c5-214">[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7b1c5-215">Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7b1c5-216">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="7b1c5-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7b1c5-217">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="7b1c5-217">Browser source maps</span></span>

<span data-ttu-id="7b1c5-218">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7b1c5-219">Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7b1c5-220">Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7b1c5-221">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7b1c5-221">Troubleshoot</span></span>

<span data-ttu-id="7b1c5-222">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="7b1c5-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="7b1c5-223">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7b1c5-224">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="7b1c5-225">ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="7b1c5-226">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="7b1c5-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
