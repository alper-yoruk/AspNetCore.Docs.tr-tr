---
title: Hata ayıklama ASP.NET Core Blazor WebAssembly
author: guardrex
description: Uygulamalarda hata ayıklamayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
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
uid: blazor/debug
ms.openlocfilehash: 838ed1a10ab3312e449782a29c305a976265550c
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227598"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="7ae37-103">Hata ayıklama ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ae37-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="7ae37-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="7ae37-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="7ae37-105">Blazor WebAssembly uygulamalar, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak ayıklanamaz.</span><span class="sxs-lookup"><span data-stu-id="7ae37-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="7ae37-106">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7ae37-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="7ae37-107">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7ae37-107">Available scenarios include:</span></span>

* <span data-ttu-id="7ae37-108">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7ae37-109">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="7ae37-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="7ae37-110">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="7ae37-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="7ae37-111">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7ae37-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="7ae37-112">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="7ae37-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="7ae37-113">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7ae37-114">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="7ae37-114">For now, you *can't*:</span></span>

* <span data-ttu-id="7ae37-115">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="7ae37-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7ae37-116">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="7ae37-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="7ae37-117">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="7ae37-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7ae37-118">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7ae37-118">Prerequisites</span></span>

<span data-ttu-id="7ae37-119">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="7ae37-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7ae37-120">Google Chrome (sürüm 70 veya üzeri) (varsayılan)</span><span class="sxs-lookup"><span data-stu-id="7ae37-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="7ae37-121">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="7ae37-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="7ae37-122">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="7ae37-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="7ae37-123">Mevcut bir uygulamada hata ayıklamayı etkinleştirmek için Blazor WebAssembly , `launchSettings.json` Başlangıç projesindeki dosyayı her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :</span><span class="sxs-lookup"><span data-stu-id="7ae37-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7ae37-124">Dosya güncelleştirildikten sonra, `launchSettings.json` aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="7ae37-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7ae37-125">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="7ae37-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="7ae37-126">IDE 'nin uygulamanın bir uygulama olduğunu algılamasını sağlar Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7ae37-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7ae37-127">Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="7ae37-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="7ae37-128">`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="7ae37-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ae37-129">Visual Studio</span></span>

<span data-ttu-id="7ae37-130">Blazor WebAssemblyVisual Studio 'da bir uygulamada hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="7ae37-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7ae37-131">Yeni ASP.NET Core barındırılan bir Blazor WebAssembly uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7ae37-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7ae37-132">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7ae37-133">**Hata ayıklama olmadan Başlat** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7ae37-133">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="7ae37-134">Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-134">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="7ae37-135">Metodunda bir kesme noktası ayarlayın `Pages/Counter.razor` `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-135">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="7ae37-136">**`Counter`** Sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="7ae37-136">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="7ae37-138">`currentCount`Yereller penceresindeki alanın değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="7ae37-138">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="7ae37-140">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-140">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7ae37-141">Uygulamanızda hata ayıklarken Blazor WebAssembly , sunucu kodunuzda hata ayıklaması de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="7ae37-141">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="7ae37-142">İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7ae37-142">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7ae37-143">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-143">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7ae37-144">**`Fetch Data`** `FetchData` Bir http isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sekmeye gidin:</span><span class="sxs-lookup"><span data-stu-id="7ae37-144">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="7ae37-146">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="7ae37-146">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="7ae37-148">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="7ae37-148">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="7ae37-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7ae37-149">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="7ae37-150">Tek başına hata ayıkla Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ae37-150">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="7ae37-151">Tek başına Blazor WebAssembly uygulamayı vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-151">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="7ae37-152">Hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğu aşağıdaki bildirimi alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="7ae37-152">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="7ae37-154">Bildirimi alırsanız:</span><span class="sxs-lookup"><span data-stu-id="7ae37-154">If you receive the notification:</span></span>

   * <span data-ttu-id="7ae37-155">[Visual Studio Code uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) en son C# ' nin yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-155">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="7ae37-156">Yüklü uzantıları denetlemek için, menü çubuğundan **uzantıları görüntüle**' yi açın  >  **Extensions** veya **etkinlik** kenar çubuğunda **Uzantılar** simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7ae37-156">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="7ae37-157">JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-157">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="7ae37-158">Menü çubuğundan (**Dosya**  >  **tercihleri**  >  **ayarları**) ayarları açın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-158">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="7ae37-159">Anahtar sözcüklerini kullanarak arama yapın `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-159">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="7ae37-160">Arama sonuçlarında, **hata ayıkla > JavaScript: önizleme kullan** onay kutusunun işaretli olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="7ae37-160">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="7ae37-161">Önizleme hata ayıklamayı etkinleştirme seçeneği yoksa, VS Code en son sürümüne yükseltin veya [JavaScript hata ayıklayıcı uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 'nı (vs Code 1,46 veya önceki bir sürümü) yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="7ae37-161">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="7ae37-162">Pencereyi yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7ae37-162">Reload the window.</span></span>

1. <span data-ttu-id="7ae37-163"><kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-163">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7ae37-164">**Hata ayıklama olmadan Başlat** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7ae37-164">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="7ae37-165">Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-165">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="7ae37-166">İstendiğinde, hata ayıklamayı başlatmak için \*\* Blazor WebAssembly Hata Ayıkla\*\* seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="7ae37-166">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="7ae37-168">Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-168">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="7ae37-169">Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="7ae37-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="7ae37-171">Barındırılan hata ayıklama Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ae37-171">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="7ae37-172">Barındırılan Blazor WebAssembly uygulamanın çözüm klasörünü vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-172">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="7ae37-173">Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-173">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="7ae37-174">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="7ae37-174">Select **Yes**.</span></span>

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="7ae37-176">Pencerenin üst kısmındaki komut paletinde barındırılan çözüm içindeki *sunucu* projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7ae37-176">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="7ae37-177">`launch.json`Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir dosya oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7ae37-177">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7ae37-178">Varolan bir hata ayıklama oturumuna Ekle</span><span class="sxs-lookup"><span data-stu-id="7ae37-178">Attach to an existing debugging session</span></span>

<span data-ttu-id="7ae37-179">Çalışan bir uygulamaya eklemek için Blazor `launch.json` aşağıdaki yapılandırmaya sahip bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7ae37-179">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7ae37-180">Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-180">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="7ae37-181">Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-181">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="7ae37-182">Yapılandırma seçeneklerini Başlat</span><span class="sxs-lookup"><span data-stu-id="7ae37-182">Launch configuration options</span></span>

<span data-ttu-id="7ae37-183">`blazorwasm`Hata ayıklama türü () için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `.vscode/launch.json` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-183">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="7ae37-184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="7ae37-184">Option</span></span>    | <span data-ttu-id="7ae37-185">Açıklama</span><span class="sxs-lookup"><span data-stu-id="7ae37-185">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7ae37-186">`launch`Bir uygulamaya hata ayıklama oturumu başlatmak ve eklemek Blazor WebAssembly veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-186">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="7ae37-187">Hata ayıklanırken tarayıcıda açılacak URL.</span><span class="sxs-lookup"><span data-stu-id="7ae37-187">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7ae37-188">Varsayılan olarak olur `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-188">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="7ae37-189">Hata ayıklama oturumu için başlatılacak tarayıcı.</span><span class="sxs-lookup"><span data-stu-id="7ae37-189">The browser to launch for the debugging session.</span></span> <span data-ttu-id="7ae37-190">Ayarlanan `edge` veya `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7ae37-190">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7ae37-191">Varsayılan olarak olur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-191">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="7ae37-192">JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-192">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7ae37-193">`true`Günlük oluşturmak için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-193">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="7ae37-194">`true`Barındırılan bir uygulama başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7ae37-194">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="7ae37-195">Web sunucusunun mutlak yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-195">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="7ae37-196">Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-196">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="7ae37-197">Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı.</span><span class="sxs-lookup"><span data-stu-id="7ae37-197">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="7ae37-198">Varsayılan değer 30.000 milisaniyedir (30 saniye).</span><span class="sxs-lookup"><span data-stu-id="7ae37-198">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="7ae37-199">Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru.</span><span class="sxs-lookup"><span data-stu-id="7ae37-199">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="7ae37-200">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-200">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="7ae37-201">Üzerinde uygulamayı başlatmak için çalışma dizini.</span><span class="sxs-lookup"><span data-stu-id="7ae37-201">The working directory to launch the app under.</span></span> <span data-ttu-id="7ae37-202">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-202">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="7ae37-203">Başlatılan işleme sağlanacak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="7ae37-203">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="7ae37-204">Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-204">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="7ae37-205">Örnek başlatma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7ae37-205">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="7ae37-206">Tek başına bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ae37-206">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7ae37-207">Belirtilen URL 'de çalışan bir uygulamaya iliştirme</span><span class="sxs-lookup"><span data-stu-id="7ae37-207">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="7ae37-208">Microsoft Edge ile barındırılan bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ae37-208">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="7ae37-209">Tarayıcı yapılandırması varsayılan olarak Google Chrome olarak belirlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-209">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="7ae37-210">Hata ayıklama için Microsoft Edge kullanılırken, `browser` olarak ayarlayın `edge` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-210">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="7ae37-211">Google Chrome 'ı kullanmak için `browser` seçeneği ayarlamayın veya seçeneğin değerini olarak ayarlayın `chrome` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-211">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="7ae37-212">Yukarıdaki örnekte, `MyHostedApp.Server.dll` *sunucu* uygulamasının derlemesi olur.</span><span class="sxs-lookup"><span data-stu-id="7ae37-212">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="7ae37-213">`.vscode`Klasörü `Client` ,, `Server` ve klasörlerinin yanında çözüm klasöründe bulunur `Shared` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-213">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="7ae37-214">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="7ae37-214">Debug in the browser</span></span>

1. <span data-ttu-id="7ae37-215">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-215">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7ae37-216">Bir tarayıcı başlatın ve uygulamanın URL 'sine gidin (örneğin, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="7ae37-216">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="7ae37-217">Tarayıcıda <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşlarına basarak uzaktan hata ayıklamayı yorum yapmaya çalışın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-217">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="7ae37-218">Tarayıcı, varsayılan olmayan bir uzaktan hata ayıklama etkinken çalışıyor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-218">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="7ae37-219">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası, tarayıcıyı hata ayıklama bağlantı noktası açık olarak başlatma yönergeleriyle birlikte işlenir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-219">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="7ae37-220">Tarayıcınızla ilgili yönergeleri izleyerek yeni bir tarayıcı penceresi açılır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-220">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="7ae37-221">Önceki tarayıcı penceresini kapatın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-221">Close the previous browser window.</span></span>

1. <span data-ttu-id="7ae37-222">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu (<kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>) yeni bir hata ayıklayıcı sekmesi açar.</span><span class="sxs-lookup"><span data-stu-id="7ae37-222">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="7ae37-223">Bir süre sonra, **kaynaklar** sekmesi, uygulama içindeki .net derlemelerinin bir listesini gösterir `file://` .</span><span class="sxs-lookup"><span data-stu-id="7ae37-223">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="7ae37-224">Bileşen kodunda ( `.razor` Dosyalar) ve C# kod dosyalarında ( `.cs` ), kod yürütüldüğünde ayarladığınız kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-224">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="7ae37-225">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-225">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="7ae37-226">Blazor[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="7ae37-226">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7ae37-227">Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="7ae37-227">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7ae37-228">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="7ae37-228">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7ae37-229">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="7ae37-229">Browser source maps</span></span>

<span data-ttu-id="7ae37-230">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-230">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7ae37-231">Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="7ae37-231">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7ae37-232">Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-232">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7ae37-233">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7ae37-233">Troubleshoot</span></span>

<span data-ttu-id="7ae37-234">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="7ae37-234">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="7ae37-235">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-235">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7ae37-236">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="7ae37-236">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="7ae37-237">ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7ae37-237">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="7ae37-238">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="7ae37-238">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="7ae37-239">Visual Studio, **ASP.NET için JavaScript hata ayıklamasını etkinleştir (Chrome, Edge ve IE)** seçeneği için **araç**  >  **seçeneklerinde**  >  **hata ayıklama**  >  **genel**' de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-239">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="7ae37-240">Bu, Visual Studio için varsayılan ayardır.</span><span class="sxs-lookup"><span data-stu-id="7ae37-240">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="7ae37-241">Hata ayıklama çalışmıyorsa, seçeneğinin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="7ae37-241">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="7ae37-242">`OnInitialized{Async}`İsabet bulunmayan kesme noktaları</span><span class="sxs-lookup"><span data-stu-id="7ae37-242">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="7ae37-243">BlazorÇerçevenin hata ayıklama proxy 'sinin başlatılması kısa bir süre sürer, bu nedenle [ `OnInitialized{Async}` yaşam döngüsü yöntemindeki](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları isabet edemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-243">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="7ae37-244">Hata ayıklama proxy 'sine, kesme noktasından önce başlamak için bir süre önce, Yöntem gövdesinin başlangıcında bir gecikme eklemeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="7ae37-244">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="7ae37-245">Uygulamanın yayın derlemesi için gecikmeye izin olmadığından emin olmak için bir [ `if` derleyici yönergesine](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) göre gecikmeyi dahil edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7ae37-245">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="7ae37-246"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="7ae37-246"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="7ae37-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="7ae37-247"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```
