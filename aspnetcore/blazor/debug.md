---
title: 'Hata ayıklama ASP.NET Core:::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Uygulamalarda hata ayıklamayı öğrenin :::no-loc(Blazor)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 14943b9f7847ac9144addfdf16a003f6fc8c340c
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159704"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="a2d4d-103">Hata ayıklama ASP.NET Core:::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a2d4d-103">Debug ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

[<span data-ttu-id="a2d4d-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="a2d4d-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="a2d4d-105">:::no-loc(Blazor WebAssembly):::uygulamalar, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak ayıklanamaz.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-105">:::no-loc(Blazor WebAssembly)::: apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="a2d4d-106">Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="a2d4d-107">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-107">Available scenarios include:</span></span>

* <span data-ttu-id="a2d4d-108">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="a2d4d-109">Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).</span><span class="sxs-lookup"><span data-stu-id="a2d4d-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="a2d4d-110">Kod üzerinden tek adımlı (<kbd>F10</kbd>).</span><span class="sxs-lookup"><span data-stu-id="a2d4d-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="a2d4d-111">Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="a2d4d-112">*Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="a2d4d-113">JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="a2d4d-114">Şimdilik şunları *yapamazsınız*:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-114">For now, you *can't*:</span></span>

* <span data-ttu-id="a2d4d-115">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="a2d4d-116">Uygulamanın başlatılması sırasında isabet kesme noktaları.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="a2d4d-117">Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a2d4d-118">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a2d4d-118">Prerequisites</span></span>

<span data-ttu-id="a2d4d-119">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="a2d4d-120">Google Chrome (sürüm 70 veya üzeri) (varsayılan)</span><span class="sxs-lookup"><span data-stu-id="a2d4d-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="a2d4d-121">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="a2d4d-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="a2d4d-122">Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="a2d4d-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="a2d4d-123">Mevcut bir uygulamada hata ayıklamayı etkinleştirmek için :::no-loc(Blazor WebAssembly)::: , `launchSettings.json` Başlangıç projesindeki dosyayı her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :</span><span class="sxs-lookup"><span data-stu-id="a2d4d-123">To enable debugging for an existing :::no-loc(Blazor WebAssembly)::: app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="a2d4d-124">Dosya güncelleştirildikten sonra, `launchSettings.json` aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="a2d4d-125">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="a2d4d-126">IDE 'nin uygulamanın bir uygulama olduğunu algılamasını sağlar :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-126">Enables the IDE to detect that the app is a :::no-loc(Blazor WebAssembly)::: app.</span></span>
* <span data-ttu-id="a2d4d-127">Betik hata ayıklama altyapısına, :::no-loc(Blazor)::: hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-127">Instructs the script debugging infrastructure to connect to the browser through :::no-loc(Blazor):::'s debugging proxy.</span></span>

<span data-ttu-id="a2d4d-128">`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="a2d4d-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2d4d-129">Visual Studio</span></span>

<span data-ttu-id="a2d4d-130">:::no-loc(Blazor WebAssembly):::Visual Studio 'da bir uygulamada hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-130">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span></span>

1. <span data-ttu-id="a2d4d-131">Yeni ASP.NET Core barındırılan bir :::no-loc(Blazor WebAssembly)::: uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-131">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="a2d4d-132">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="a2d4d-133">Metodunda bir kesme noktası ayarlayın `Pages/Counter.razor` `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="a2d4d-134">**`Counter`** Sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="a2d4d-136">`currentCount`Yereller penceresindeki alanın değerini gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="a2d4d-138">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="a2d4d-139">Uygulamanızda hata ayıklarken :::no-loc(Blazor WebAssembly)::: , sunucu kodunuzda hata ayıklaması de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-139">While debugging your :::no-loc(Blazor WebAssembly)::: app, you can also debug your server code:</span></span>

1. <span data-ttu-id="a2d4d-140">İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="a2d4d-141">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="a2d4d-142">**`Fetch Data`** `FetchData` Bir http isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sekmeye gidin:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="a2d4d-144">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="a2d4d-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="a2d4d-146">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="a2d4d-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a2d4d-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="a2d4d-148">Tek başına hata ayıkla:::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a2d4d-148">Debug standalone :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="a2d4d-149">Tek başına :::no-loc(Blazor WebAssembly)::: uygulamayı vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-149">Open the standalone :::no-loc(Blazor WebAssembly)::: app in VS Code.</span></span>

   <span data-ttu-id="a2d4d-150">Aşağıdaki bildirimi, hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-150">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="a2d4d-151">Doğru uzantıların yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-151">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="a2d4d-152">JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-152">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="a2d4d-153">Pencereyi yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-153">Reload the window.</span></span>

   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="a2d4d-155"><kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-155">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="a2d4d-156">İstendiğinde, hata ayıklamayı başlatmak için \*\* :::no-loc(Blazor WebAssembly)::: Hata Ayıkla\*\* seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-156">When prompted, select the **:::no-loc(Blazor WebAssembly)::: Debug** option to start debugging.</span></span>

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="a2d4d-158">Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-158">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="a2d4d-159">Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-159">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="a2d4d-161">Barındırılan hata ayıklama:::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a2d4d-161">Debug hosted :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="a2d4d-162">Barındırılan :::no-loc(Blazor WebAssembly)::: uygulamanın çözüm klasörünü vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-162">Open the hosted :::no-loc(Blazor WebAssembly)::: app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="a2d4d-163">Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-163">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="a2d4d-164">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-164">Select **Yes**.</span></span>

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="a2d4d-166">Pencerenin üst kısmındaki komut paletinde barındırılan çözüm içindeki *sunucu* projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-166">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="a2d4d-167">`launch.json`Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir dosya oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-167">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="a2d4d-168">Varolan bir hata ayıklama oturumuna Ekle</span><span class="sxs-lookup"><span data-stu-id="a2d4d-168">Attach to an existing debugging session</span></span>

<span data-ttu-id="a2d4d-169">Çalışan bir uygulamaya eklemek için :::no-loc(Blazor)::: `launch.json` aşağıdaki yapılandırmaya sahip bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-169">To attach to a running :::no-loc(Blazor)::: app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> <span data-ttu-id="a2d4d-170">Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-170">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="a2d4d-171">Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-171">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="a2d4d-172">Yapılandırma seçeneklerini Başlat</span><span class="sxs-lookup"><span data-stu-id="a2d4d-172">Launch configuration options</span></span>

<span data-ttu-id="a2d4d-173">`blazorwasm`Hata ayıklama türü () için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `.vscode/launch.json` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-173">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="a2d4d-174">Seçenek</span><span class="sxs-lookup"><span data-stu-id="a2d4d-174">Option</span></span>    | <span data-ttu-id="a2d4d-175">Açıklama</span><span class="sxs-lookup"><span data-stu-id="a2d4d-175">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="a2d4d-176">`launch`Bir uygulamaya hata ayıklama oturumu başlatmak ve eklemek :::no-loc(Blazor WebAssembly)::: veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-176">Use `launch` to launch and attach a debugging session to a :::no-loc(Blazor WebAssembly)::: app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="a2d4d-177">Hata ayıklanırken tarayıcıda açılacak URL.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-177">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="a2d4d-178">Varsayılan olarak olur `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-178">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="a2d4d-179">Hata ayıklama oturumu için başlatılacak tarayıcı.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-179">The browser to launch for the debugging session.</span></span> <span data-ttu-id="a2d4d-180">Ayarlanan `edge` veya `chrome`.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-180">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="a2d4d-181">Varsayılan olarak olur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-181">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="a2d4d-182">JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-182">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="a2d4d-183">`true`Günlük oluşturmak için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-183">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="a2d4d-184">`true`Barındırılan bir uygulama başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-184">Must be set to `true` if launching and debugging a hosted :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| `webRoot` | <span data-ttu-id="a2d4d-185">Web sunucusunun mutlak yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-185">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="a2d4d-186">Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-186">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="a2d4d-187">Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-187">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="a2d4d-188">Varsayılan değer 30.000 milisaniyedir (30 saniye).</span><span class="sxs-lookup"><span data-stu-id="a2d4d-188">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="a2d4d-189">Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-189">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="a2d4d-190">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-190">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="a2d4d-191">Üzerinde uygulamayı başlatmak için çalışma dizini.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-191">The working directory to launch the app under.</span></span> <span data-ttu-id="a2d4d-192">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-192">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="a2d4d-193">Başlatılan işleme sağlanacak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-193">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="a2d4d-194">Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-194">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="a2d4d-195">Örnek başlatma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="a2d4d-195">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="a2d4d-196">Tek başına bir uygulamayı başlatma ve hata ayıklama :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a2d4d-196">Launch and debug a standalone :::no-loc(Blazor WebAssembly)::: app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="a2d4d-197">Belirtilen URL 'de çalışan bir uygulamaya iliştirme</span><span class="sxs-lookup"><span data-stu-id="a2d4d-197">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="a2d4d-198">Microsoft Edge ile barındırılan bir uygulamayı başlatma ve hata ayıklama :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="a2d4d-198">Launch and debug a hosted :::no-loc(Blazor WebAssembly)::: app with Microsoft Edge</span></span>

<span data-ttu-id="a2d4d-199">Tarayıcı yapılandırması varsayılan olarak Google Chrome olarak belirlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-199">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="a2d4d-200">Hata ayıklama için Microsoft Edge kullanılırken, `browser` olarak ayarlayın `edge` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-200">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="a2d4d-201">Google Chrome 'ı kullanmak için `browser` seçeneği ayarlamayın veya seçeneğin değerini olarak ayarlayın `chrome` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-201">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted :::no-loc(Blazor WebAssembly)::: App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="a2d4d-202">Yukarıdaki örnekte, `MyHostedApp.Server.dll` *sunucu* uygulamasının derlemesi olur.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-202">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="a2d4d-203">`.vscode`Klasörü `Client` ,, `Server` ve klasörlerinin yanında çözüm klasöründe bulunur `Shared` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-203">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="a2d4d-204">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="a2d4d-204">Debug in the browser</span></span>

1. <span data-ttu-id="a2d4d-205">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-205">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="a2d4d-206">Bir tarayıcı başlatın ve uygulamanın URL 'sine gidin (örneğin, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="a2d4d-206">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="a2d4d-207">Tarayıcıda <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşlarına basarak uzaktan hata ayıklamayı yorum yapmaya çalışın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-207">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="a2d4d-208">Tarayıcı, varsayılan olmayan bir uzaktan hata ayıklama etkinken çalışıyor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-208">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="a2d4d-209">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası, tarayıcıyı hata ayıklama bağlantı noktası açık olarak başlatma yönergeleriyle birlikte işlenir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-209">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="a2d4d-210">Tarayıcınızla ilgili yönergeleri izleyerek yeni bir tarayıcı penceresi açılır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-210">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="a2d4d-211">Önceki tarayıcı penceresini kapatın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-211">Close the previous browser window.</span></span>

1. <span data-ttu-id="a2d4d-212">Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu (<kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>) yeni bir hata ayıklayıcı sekmesi açar.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-212">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="a2d4d-213">Bir süre sonra, **kaynaklar** sekmesi, uygulama içindeki .net derlemelerinin bir listesini gösterir `file://` .</span><span class="sxs-lookup"><span data-stu-id="a2d4d-213">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="a2d4d-214">Bileşen kodunda ( `.razor` Dosyalar) ve C# kod dosyalarında ( `.cs` ), kod yürütüldüğünde ayarladığınız kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-214">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="a2d4d-215">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-215">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="a2d4d-216">:::no-loc(Blazor):::[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-216">:::no-loc(Blazor)::: provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="a2d4d-217">Klavye kısayoluna hata ayıklama basıldığında, :::no-loc(Blazor)::: Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-217">When debugging keyboard shortcut is pressed, :::no-loc(Blazor)::: points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="a2d4d-218">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="a2d4d-218">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="a2d4d-219">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="a2d4d-219">Browser source maps</span></span>

<span data-ttu-id="a2d4d-220">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-220">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="a2d4d-221">Ancak, :::no-loc(Blazor)::: Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-221">However, :::no-loc(Blazor)::: doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="a2d4d-222">Bunun yerine, :::no-loc(Blazor)::: tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-222">Instead, :::no-loc(Blazor)::: does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a2d4d-223">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="a2d4d-223">Troubleshoot</span></span>

<span data-ttu-id="a2d4d-224">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="a2d4d-224">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="a2d4d-225">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-225">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="a2d4d-226">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-226">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="a2d4d-227">ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-227">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="a2d4d-228">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-228">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="a2d4d-229">Visual Studio, **ASP.NET için JavaScript hata ayıklamasını etkinleştir (Chrome, Edge ve IE)** seçeneği için **araç**  >  **seçeneklerinde**  >  **hata ayıklama**  >  **genel**' de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-229">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="a2d4d-230">Bu, Visual Studio için varsayılan ayardır.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-230">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="a2d4d-231">Hata ayıklama çalışmıyorsa, seçeneğinin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="a2d4d-231">If debugging isn't working, confirm that the option is selected.</span></span>
