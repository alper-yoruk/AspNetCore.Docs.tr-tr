---
title: 'Hata ayıklama ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Uygulamalarda hata ayıklamayı öğrenin :::no-loc(Blazor)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/debug
ms.openlocfilehash: 990882c03ddc14c664aa8da0518fb36087199aca
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550523"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="738be-103">Hata ayıklama ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="738be-103">Debug ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

[<span data-ttu-id="738be-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="738be-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="738be-105">:::no-loc(Blazor WebAssembly)::: uygulamalar, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak ayıklanamaz.</span><span class="sxs-lookup"><span data-stu-id="738be-105">:::no-loc(Blazor WebAssembly)::: apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="738be-106">Ayrıca, aşağıdaki tümleşik geliştirme ortamlarını (IDEs) kullanarak uygulamanızda hata ayıklaması yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="738be-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="738be-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="738be-107">Visual Studio</span></span>
* <span data-ttu-id="738be-108">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="738be-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="738be-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="738be-109">Visual Studio Code</span></span>

<span data-ttu-id="738be-110">Kullanılabilir senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="738be-110">Available scenarios include:</span></span>

* <span data-ttu-id="738be-111">Kesme noktaları ayarlayın ve kaldırın.</span><span class="sxs-lookup"><span data-stu-id="738be-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="738be-112">Uygulamaları, hata ayıklama desteğiyle birlikte çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="738be-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="738be-113">Kodda tek adımlı.</span><span class="sxs-lookup"><span data-stu-id="738be-113">Single-step through the code.</span></span>
* <span data-ttu-id="738be-114">Ides 'teki bir klavye kısayoluyla kod yürütmeyi sürdürür.</span><span class="sxs-lookup"><span data-stu-id="738be-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="738be-115">*Yereller* penceresinde, yerel değişkenlerin değerlerini gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="738be-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="738be-116">JavaScript ve .NET arasındaki çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.</span><span class="sxs-lookup"><span data-stu-id="738be-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="738be-117">Şimdilik şunları *yapamazsınız* :</span><span class="sxs-lookup"><span data-stu-id="738be-117">For now, you *can't* :</span></span>

* <span data-ttu-id="738be-118">İşlenmemiş özel durumların üzerine bölün.</span><span class="sxs-lookup"><span data-stu-id="738be-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="738be-119">Hata ayıklama proxy 'si çalışmadan önce uygulama başlatılırken kesme noktaları isabet edin.</span><span class="sxs-lookup"><span data-stu-id="738be-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="738be-120">Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="738be-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="738be-121">Yerel olmayan senaryolarda hata ayıklama (örneğin, [Linux Için Windows alt sistemi (WSL)](/windows/wsl/) veya [Visual Studio codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span><span class="sxs-lookup"><span data-stu-id="738be-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="738be-122">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="738be-122">Prerequisites</span></span>

<span data-ttu-id="738be-123">Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="738be-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="738be-124">Google Chrome (sürüm 70 veya üzeri) (varsayılan)</span><span class="sxs-lookup"><span data-stu-id="738be-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="738be-125">Microsoft Edge (sürüm 80 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="738be-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="738be-126">Güvenlik duvarları veya proxy 'lerin hata ayıklama proxy 'si (işlem) ile iletişimi engellemediğinden emin olun `NodeJS` .</span><span class="sxs-lookup"><span data-stu-id="738be-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="738be-127">Daha fazla bilgi için [güvenlik duvarı yapılandırması](#firewall-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="738be-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="738be-128">Mac için Visual Studio Sürüm 8,8 (derleme 1532) veya üzeri gerektirir:</span><span class="sxs-lookup"><span data-stu-id="738be-128">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="738be-129">Microsoft 'ta **Mac için Visual Studio indir** düğmesini seçerek en son Mac için Visual Studio sürümünü yükleyin [: Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="738be-129">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="738be-130">Visual Studio içinden *Önizleme* kanalını seçin.</span><span class="sxs-lookup"><span data-stu-id="738be-130">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="738be-131">Daha fazla bilgi için bkz. [Mac için Visual Studio Preview sürümü yüklemesi](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="738be-131">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="738be-132">MacOS üzerinde Apple Safari Şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="738be-132">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="738be-133">Hata ayıklamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="738be-133">Enable debugging</span></span>

<span data-ttu-id="738be-134">Mevcut bir uygulamada hata ayıklamayı etkinleştirmek için :::no-loc(Blazor WebAssembly)::: , `launchSettings.json` Başlangıç projesindeki dosyayı her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :</span><span class="sxs-lookup"><span data-stu-id="738be-134">To enable debugging for an existing :::no-loc(Blazor WebAssembly)::: app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="738be-135">Dosya güncelleştirildikten sonra, `launchSettings.json` aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="738be-135">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="738be-136">`inspectUri`Özelliği:</span><span class="sxs-lookup"><span data-stu-id="738be-136">The `inspectUri` property:</span></span>

* <span data-ttu-id="738be-137">IDE 'nin uygulamanın bir uygulama olduğunu algılamasını sağlar :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="738be-137">Enables the IDE to detect that the app is a :::no-loc(Blazor WebAssembly)::: app.</span></span>
* <span data-ttu-id="738be-138">Betik hata ayıklama altyapısına, :::no-loc(Blazor)::: hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="738be-138">Instructs the script debugging infrastructure to connect to the browser through :::no-loc(Blazor):::'s debugging proxy.</span></span>

<span data-ttu-id="738be-139">`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="738be-139">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="738be-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="738be-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="738be-141">:::no-loc(Blazor WebAssembly):::Visual Studio 'da bir uygulamada hata ayıklamak için:</span><span class="sxs-lookup"><span data-stu-id="738be-141">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio:</span></span>

1. <span data-ttu-id="738be-142">Yeni ASP.NET Core barındırılan bir :::no-loc(Blazor WebAssembly)::: uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="738be-142">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="738be-143">Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="738be-143">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="738be-144">**Hata ayıklama olmadan Başlat** ( <kbd>CTRL</kbd> + <kbd>F5</kbd>) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="738be-144">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="738be-145">Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="738be-145">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="738be-146">`*Client*`Uygulamada, içindeki satırda bir kesme noktası ayarlayın `currentCount++;` `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="738be-146">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="738be-147">Tarayıcıda `Counter` sayfasına gidin ve kesme noktasına ulaşmak için **bana tıklayın** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="738be-147">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="738be-148">Visual Studio 'da, `currentCount` **Yereller** penceresindeki alanın değerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="738be-148">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="738be-149">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="738be-149">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="738be-150">Bir uygulamada hata ayıklarken :::no-loc(Blazor WebAssembly)::: , sunucu kodunda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="738be-150">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="738be-151">İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="738be-151">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="738be-152">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="738be-152">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="738be-153">`Fetch Data` `FetchData` Sunucuya bir http isteği vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sayfaya gidin.</span><span class="sxs-lookup"><span data-stu-id="738be-153">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="738be-154">Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve içindeki sunucudaki kesme noktasına gidin `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="738be-154">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="738be-155">Yürütmenin devam etmesine izin vermek için <kbd>F5</kbd> tuşuna basın ve tarayıcıda işlenen Hava durumu tahmin tablosunu görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="738be-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="738be-156">Hata ayıklama proxy 'si çalışmadan önce uygulama başlatma sırasında kesme noktaları isabet **etmez** .</span><span class="sxs-lookup"><span data-stu-id="738be-156">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="738be-157">Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="738be-157">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="738be-158">Uygulama, farklı bir [uygulama temel yolunda](xref:blazor/host-and-deploy/index#app-base-path) barındırılıyorsa `/` , `Properties/launchSettings.json` uygulamasının temel yolunu yansıtmak için ' de aşağıdaki özellikleri güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="738be-158">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="738be-159">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="738be-159">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="738be-160">`inspectUri` Her profilin:</span><span class="sxs-lookup"><span data-stu-id="738be-160">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="738be-161">Önceki ayarlarda yer tutucular:</span><span class="sxs-lookup"><span data-stu-id="738be-161">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="738be-162">`{INSECURE PORT}`: Güvensiz bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="738be-162">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="738be-163">Rastgele bir değer varsayılan olarak sağlanır, ancak özel bir bağlantı noktasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="738be-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="738be-164">`{APP BASE PATH}`: Uygulamanın temel yolu.</span><span class="sxs-lookup"><span data-stu-id="738be-164">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="738be-165">`{SECURE PORT}`: Güvenli bağlantı noktası.</span><span class="sxs-lookup"><span data-stu-id="738be-165">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="738be-166">Rastgele bir değer varsayılan olarak sağlanır, ancak özel bir bağlantı noktasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="738be-166">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="738be-167">`{PROFILE 1, 2, ... N}`: Ayarlar profillerini başlatın.</span><span class="sxs-lookup"><span data-stu-id="738be-167">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="738be-168">Genellikle, bir uygulama varsayılan olarak birden fazla profil belirtir (örneğin, IIS Express için bir profil ve Kestrel Server tarafından kullanılan bir proje profili).</span><span class="sxs-lookup"><span data-stu-id="738be-168">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="738be-169">Aşağıdaki örneklerde, uygulama şu `/OAT` şekilde yapılandırılmış bir uygulama temel yolu ile konumunda barındırılır `wwwroot/index.html` `<base href="/OAT/">` :</span><span class="sxs-lookup"><span data-stu-id="738be-169">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="738be-170">Uygulamalar için özel bir uygulama temel yolu kullanma hakkında daha fazla bilgi için :::no-loc(Blazor WebAssembly)::: bkz <xref:blazor/host-and-deploy/index#app-base-path> ..</span><span class="sxs-lookup"><span data-stu-id="738be-170">For information on using a custom app base path for :::no-loc(Blazor WebAssembly)::: apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="738be-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="738be-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="738be-172">Tek başına hata ayıkla :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="738be-172">Debug standalone :::no-loc(Blazor WebAssembly):::</span></span></h2>

1. <span data-ttu-id="738be-173">Tek başına :::no-loc(Blazor WebAssembly)::: uygulamayı vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="738be-173">Open the standalone :::no-loc(Blazor WebAssembly)::: app in VS Code.</span></span>

   <span data-ttu-id="738be-174">Hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu belirten bir bildirim alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="738be-174">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="738be-175">Uygulamalarda hata ayıklamak için ek kurulum gereklidir :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="738be-175">Additional setup is required to debug :::no-loc(Blazor WebAssembly)::: applications.</span></span>

   <span data-ttu-id="738be-176">Bildirimi alırsanız:</span><span class="sxs-lookup"><span data-stu-id="738be-176">If you receive the notification:</span></span>

   * <span data-ttu-id="738be-177">[Visual Studio Code uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) en son C# ' nin yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="738be-177">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="738be-178">Yüklü uzantıları denetlemek için, menü çubuğundan **uzantıları görüntüle** ' yi açın  >  **Extensions** veya **etkinlik** kenar çubuğunda **Uzantılar** simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="738be-178">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="738be-179">JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="738be-179">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="738be-180">Menü çubuğundan ( **Dosya**  >  **tercihleri**  >  **ayarları** ) ayarları açın.</span><span class="sxs-lookup"><span data-stu-id="738be-180">Open the settings from the menu bar ( **File** > **Preferences** > **Settings** ).</span></span> <span data-ttu-id="738be-181">Anahtar sözcüklerini kullanarak arama yapın `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="738be-181">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="738be-182">Arama sonuçlarında, **hata ayıkla > JavaScript: önizleme kullan** onay kutusunun işaretli olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="738be-182">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="738be-183">Önizleme hata ayıklamayı etkinleştirme seçeneği yoksa, VS Code en son sürümüne yükseltin veya [JavaScript hata ayıklayıcı uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 'nı (vs Code 1,46 veya önceki bir sürümü) yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="738be-183">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="738be-184">Pencereyi yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="738be-184">Reload the window.</span></span>

1. <span data-ttu-id="738be-185"><kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="738be-185">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="738be-186">**Hata ayıklama olmadan Başlat** ( <kbd>CTRL</kbd> + <kbd>F5</kbd>) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="738be-186">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="738be-187">Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="738be-187">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="738be-188">İstendiğinde, hata ayıklamayı başlatmak için **:::no-loc(Blazor WebAssembly)::: Hata Ayıkla** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="738be-188">When prompted, select the **:::no-loc(Blazor WebAssembly)::: Debug** option to start debugging.</span></span>

1. <span data-ttu-id="738be-189">Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.</span><span class="sxs-lookup"><span data-stu-id="738be-189">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="738be-190">`*Client*`Uygulamada, içindeki satırda bir kesme noktası ayarlayın `currentCount++;` `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="738be-190">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="738be-191">Tarayıcıda `Counter` sayfasına gidin ve kesme noktasına ulaşmak için **bana tıklayın** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="738be-191">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="738be-192">Hata ayıklama proxy 'si çalışmadan önce uygulama başlatma sırasında kesme noktaları isabet **etmez** .</span><span class="sxs-lookup"><span data-stu-id="738be-192">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="738be-193">Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="738be-193">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="738be-194">Barındırılan hata ayıklama :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="738be-194">Debug hosted :::no-loc(Blazor WebAssembly):::</span></span>

1. <span data-ttu-id="738be-195">Barındırılan :::no-loc(Blazor WebAssembly)::: uygulamanın çözüm klasörünü vs Code açın.</span><span class="sxs-lookup"><span data-stu-id="738be-195">Open the hosted :::no-loc(Blazor WebAssembly)::: app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="738be-196">Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="738be-196">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="738be-197">**Evet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="738be-197">Select **Yes**.</span></span>

   > <span data-ttu-id="738be-198">' {APPLICATION NAME} ' içindeki derleme ve hata ayıklama için gerekli varlıklar eksik.</span><span class="sxs-lookup"><span data-stu-id="738be-198">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="738be-199">Bunları ekleyin mi?</span><span class="sxs-lookup"><span data-stu-id="738be-199">Add them?</span></span>

1. <span data-ttu-id="738be-200">Pencerenin üst kısmındaki komut paletinde barındırılan çözüm içindeki *sunucu* projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="738be-200">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="738be-201">`launch.json`Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir dosya oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="738be-201">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="738be-202">Varolan bir hata ayıklama oturumuna Ekle</span><span class="sxs-lookup"><span data-stu-id="738be-202">Attach to an existing debugging session</span></span>

<span data-ttu-id="738be-203">Çalışan bir uygulamaya eklemek için :::no-loc(Blazor)::: `launch.json` aşağıdaki yapılandırmaya sahip bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="738be-203">To attach to a running :::no-loc(Blazor)::: app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing :::no-loc(Blazor WebAssembly)::: Application"
}
```

> [!NOTE]
> <span data-ttu-id="738be-204">Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="738be-204">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="738be-205">Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="738be-205">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="738be-206">Yapılandırma seçeneklerini Başlat</span><span class="sxs-lookup"><span data-stu-id="738be-206">Launch configuration options</span></span>

<span data-ttu-id="738be-207">`blazorwasm`Hata ayıklama türü () için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `.vscode/launch.json` .</span><span class="sxs-lookup"><span data-stu-id="738be-207">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="738be-208">Seçenek</span><span class="sxs-lookup"><span data-stu-id="738be-208">Option</span></span>    | <span data-ttu-id="738be-209">Açıklama</span><span class="sxs-lookup"><span data-stu-id="738be-209">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="738be-210">`launch`Bir uygulamaya hata ayıklama oturumu başlatmak ve eklemek :::no-loc(Blazor WebAssembly)::: veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="738be-210">Use `launch` to launch and attach a debugging session to a :::no-loc(Blazor WebAssembly)::: app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="738be-211">Hata ayıklanırken tarayıcıda açılacak URL.</span><span class="sxs-lookup"><span data-stu-id="738be-211">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="738be-212">Varsayılan olarak olur `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="738be-212">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="738be-213">Hata ayıklama oturumu için başlatılacak tarayıcı.</span><span class="sxs-lookup"><span data-stu-id="738be-213">The browser to launch for the debugging session.</span></span> <span data-ttu-id="738be-214">Ayarlanan `edge` veya `chrome`.</span><span class="sxs-lookup"><span data-stu-id="738be-214">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="738be-215">Varsayılan olarak olur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="738be-215">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="738be-216">JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="738be-216">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="738be-217">`true`Günlük oluşturmak için olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="738be-217">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="738be-218">`true`Barındırılan bir uygulama başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="738be-218">Must be set to `true` if launching and debugging a hosted :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| `webRoot` | <span data-ttu-id="738be-219">Web sunucusunun mutlak yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="738be-219">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="738be-220">Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="738be-220">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="738be-221">Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı.</span><span class="sxs-lookup"><span data-stu-id="738be-221">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="738be-222">Varsayılan değer 30.000 milisaniyedir (30 saniye).</span><span class="sxs-lookup"><span data-stu-id="738be-222">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="738be-223">Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru.</span><span class="sxs-lookup"><span data-stu-id="738be-223">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="738be-224">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="738be-224">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="738be-225">Üzerinde uygulamayı başlatmak için çalışma dizini.</span><span class="sxs-lookup"><span data-stu-id="738be-225">The working directory to launch the app under.</span></span> <span data-ttu-id="738be-226">`hosted`İse ayarlanmalıdır `true` .</span><span class="sxs-lookup"><span data-stu-id="738be-226">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="738be-227">Başlatılan işleme sağlanacak ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="738be-227">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="738be-228">Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` .</span><span class="sxs-lookup"><span data-stu-id="738be-228">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="738be-229">Örnek başlatma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="738be-229">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="738be-230">Tek başına bir uygulamayı başlatma ve hata ayıklama :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="738be-230">Launch and debug a standalone :::no-loc(Blazor WebAssembly)::: app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="738be-231">Belirtilen URL 'de çalışan bir uygulamaya iliştirme</span><span class="sxs-lookup"><span data-stu-id="738be-231">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="738be-232">Microsoft Edge ile barındırılan bir uygulamayı başlatma ve hata ayıklama :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="738be-232">Launch and debug a hosted :::no-loc(Blazor WebAssembly)::: app with Microsoft Edge</span></span>

<span data-ttu-id="738be-233">Tarayıcı yapılandırması varsayılan olarak Google Chrome olarak belirlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="738be-233">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="738be-234">Hata ayıklama için Microsoft Edge kullanılırken, `browser` olarak ayarlayın `edge` .</span><span class="sxs-lookup"><span data-stu-id="738be-234">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="738be-235">Google Chrome 'ı kullanmak için `browser` seçeneği ayarlamayın veya seçeneğin değerini olarak ayarlayın `chrome` .</span><span class="sxs-lookup"><span data-stu-id="738be-235">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

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

<span data-ttu-id="738be-236">Yukarıdaki örnekte, `MyHostedApp.Server.dll` *sunucu* uygulamasının derlemesi olur.</span><span class="sxs-lookup"><span data-stu-id="738be-236">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="738be-237">`.vscode`Klasörü `Client` ,, `Server` ve klasörlerinin yanında çözüm klasöründe bulunur `Shared` .</span><span class="sxs-lookup"><span data-stu-id="738be-237">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="738be-238">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="738be-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="738be-239">Mac için Visual Studio bir uygulamada hata ayıklamak için :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="738be-239">To debug a :::no-loc(Blazor WebAssembly)::: app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="738be-240">Yeni ASP.NET Core barındırılan bir :::no-loc(Blazor WebAssembly)::: uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="738be-240">Create a new ASP.NET Core hosted :::no-loc(Blazor WebAssembly)::: app.</span></span>
1. <span data-ttu-id="738be-241"><kbd>&#8984;</kbd> + Uygulamayı hata ayıklayıcıda çalıştırmak için&#8984;<kbd>&#8617;</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="738be-241">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="738be-242">**Hata ayıklama olmadan Başlat** ( <kbd>&#8997;</kbd> + <kbd>&#8984;</kbd> + <kbd>&#8617;</kbd>) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="738be-242">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="738be-243">Uygulama hata ayıklama yapılandırmasında çalıştırıldığında, hata ayıklama ek yükü her zaman küçük bir performans azalmasıyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="738be-243">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="738be-244">Google Chrome veya Microsoft Edge, hata ayıklama oturumunun seçili tarayıcısı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="738be-244">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="738be-245">`*Client*`Uygulamada, içindeki satırda bir kesme noktası ayarlayın `currentCount++;` `Pages/Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="738be-245">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="738be-246">Tarayıcıda `Counter` sayfasına gidin ve kesme noktasına ulaşmak için **bana tıklayın** düğmesini seçin:</span><span class="sxs-lookup"><span data-stu-id="738be-246">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="738be-247">Visual Studio 'da, `currentCount` **Yereller** penceresindeki alanın değerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="738be-247">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="738be-248"><kbd>&#8984;</kbd> + Yürütmeye devam etmek için&#8984;<kbd>&#8617;</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="738be-248">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="738be-249">Bir uygulamada hata ayıklarken :::no-loc(Blazor WebAssembly)::: , sunucu kodunda hata ayıklama de yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="738be-249">While debugging a :::no-loc(Blazor WebAssembly)::: app, you can also debug server code:</span></span>

1. <span data-ttu-id="738be-250">İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="738be-250">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="738be-251">Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .</span><span class="sxs-lookup"><span data-stu-id="738be-251">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="738be-252">`Fetch Data` `FetchData` Sunucuya bir http isteği vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sayfaya gidin.</span><span class="sxs-lookup"><span data-stu-id="738be-252">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="738be-253"><kbd>&#8984;</kbd> + Yürütmeye devam etmek için&#8984;<kbd>&#8617;</kbd> tuşuna basın ve ardından içindeki sunucudaki kesme noktasına gidin `WeatherForecastController` .</span><span class="sxs-lookup"><span data-stu-id="738be-253">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="738be-254"><kbd>&#8984;</kbd> + Yürütmenin devam etmesine izin vermek ve tarayıcıda işlenen Hava durumu tahmin tablosuna bakmak için&#8984;<kbd>&#8617;</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="738be-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="738be-255">Hata ayıklama proxy 'si çalışmadan önce uygulama başlatma sırasında kesme noktaları isabet **etmez** .</span><span class="sxs-lookup"><span data-stu-id="738be-255">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="738be-256">Bu, `Program.Main` ( `Program.cs` ) ve uygulamadan istenen ilk sayfa tarafından yüklenen bileşen [ `OnInitialized{Async}` yöntemlerinde](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="738be-256">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="738be-257">Daha fazla bilgi için bkz. [Mac için Visual Studio Ile hata ayıklama](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="738be-257">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="738be-258">Tarayıcıda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="738be-258">Debug in the browser</span></span>

<span data-ttu-id="738be-259">*Bu bölümdeki kılavuz, Google Chrome ve Windows üzerinde çalışan Microsoft Edge için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="738be-259">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="738be-260">Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="738be-260">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="738be-261">Bir tarayıcı başlatın ve uygulamanın URL 'sine gidin (örneğin, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="738be-261">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="738be-262">Tarayıcıda <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>d</kbd>tuşlarına basarak uzaktan hata ayıklamayı yorum yapmaya çalışın.</span><span class="sxs-lookup"><span data-stu-id="738be-262">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="738be-263">Tarayıcı, varsayılan olmayan bir uzaktan hata ayıklama etkinken çalışıyor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="738be-263">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="738be-264">Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası, tarayıcıyı hata ayıklama bağlantı noktası açık olarak başlatma yönergeleriyle birlikte işlenir.</span><span class="sxs-lookup"><span data-stu-id="738be-264">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="738be-265">Tarayıcınızla ilgili yönergeleri izleyerek yeni bir tarayıcı penceresi açılır.</span><span class="sxs-lookup"><span data-stu-id="738be-265">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="738be-266">Önceki tarayıcı penceresini kapatın.</span><span class="sxs-lookup"><span data-stu-id="738be-266">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="738be-267">Tarayıcı uzaktan hata ayıklama etkinken çalışmaya başladıktan sonra, önceki adımda hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar.</span><span class="sxs-lookup"><span data-stu-id="738be-267">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="738be-268">Bir süre sonra, **kaynaklar** sekmesi, uygulama içindeki .net derlemelerinin bir listesini gösterir `file://` .</span><span class="sxs-lookup"><span data-stu-id="738be-268">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="738be-269">Bileşen kodunda ( `.razor` Dosyalar) ve C# kod dosyalarında ( `.cs` ), kod yürütüldüğünde ayarladığınız kesme noktaları isabet edilir.</span><span class="sxs-lookup"><span data-stu-id="738be-269">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="738be-270">Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.</span><span class="sxs-lookup"><span data-stu-id="738be-270">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="738be-271">:::no-loc(Blazor):::[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler.</span><span class="sxs-lookup"><span data-stu-id="738be-271">:::no-loc(Blazor)::: provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="738be-272">Klavye kısayoluna hata ayıklama basıldığında, :::no-loc(Blazor)::: Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder.</span><span class="sxs-lookup"><span data-stu-id="738be-272">When debugging keyboard shortcut is pressed, :::no-loc(Blazor)::: points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="738be-273">Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).</span><span class="sxs-lookup"><span data-stu-id="738be-273">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="738be-274">Tarayıcı kaynağı eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="738be-274">Browser source maps</span></span>

<span data-ttu-id="738be-275">Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="738be-275">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="738be-276">Ancak, :::no-loc(Blazor)::: Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez.</span><span class="sxs-lookup"><span data-stu-id="738be-276">However, :::no-loc(Blazor)::: doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="738be-277">Bunun yerine, :::no-loc(Blazor)::: tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.</span><span class="sxs-lookup"><span data-stu-id="738be-277">Instead, :::no-loc(Blazor)::: does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="738be-278">Güvenlik duvarı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="738be-278">Firewall configuration</span></span>

<span data-ttu-id="738be-279">Bir güvenlik duvarı hata ayıklama proxy 'si ile iletişimi engelliyorsa, tarayıcı ve işlem arasında iletişime izin veren bir güvenlik duvarı özel durumu kuralı oluşturun `NodeJS` .</span><span class="sxs-lookup"><span data-stu-id="738be-279">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="738be-280">Güvenlik güvenliği güvenlik açıklarını oluşturmaktan kaçınmak için bir güvenlik duvarı yapılandırmasının değiştirilmesi dikkatli yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="738be-280">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="738be-281">Güvenlik kılavuzunu dikkatle uygulayın, en iyi güvenlik uygulamalarını izleyin ve güvenlik duvarının üreticisi tarafından verilen uyarıları dikkate edin.</span><span class="sxs-lookup"><span data-stu-id="738be-281">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="738be-282">İşlemle iletişimi açmaya izin verme `NodeJS` :</span><span class="sxs-lookup"><span data-stu-id="738be-282">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="738be-283">Güvenlik duvarının özelliklerine ve yapılandırmasına bağlı olarak düğüm sunucusunu herhangi bir bağlantıda açar.</span><span class="sxs-lookup"><span data-stu-id="738be-283">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="738be-284">Ağınıza bağlı olarak riskli olabilir.</span><span class="sxs-lookup"><span data-stu-id="738be-284">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="738be-285">**Yalnızca geliştirici makinelerinde önerilir.**</span><span class="sxs-lookup"><span data-stu-id="738be-285">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="738be-286">Mümkünse, yalnızca `NodeJS` **Güvenilen veya özel ağlardaki** işlemle açık iletişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="738be-286">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="738be-287">[Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) yapılandırma kılavuzu için bkz. [bir gelen program veya hizmet kuralı oluşturma](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="738be-287">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="738be-288">Daha fazla bilgi için Windows Güvenlik Duvarı belge kümesindeki [Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarı](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) ve ilgili makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="738be-288">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="738be-289">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="738be-289">Troubleshoot</span></span>

<span data-ttu-id="738be-290">Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="738be-290">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="738be-291">**Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın.</span><span class="sxs-lookup"><span data-stu-id="738be-291">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="738be-292">Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.</span><span class="sxs-lookup"><span data-stu-id="738be-292">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="738be-293">ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="738be-293">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="738be-294">Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="738be-294">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="738be-295">Visual Studio, **ASP.NET için JavaScript hata ayıklamasını etkinleştir (Chrome, Edge ve IE)** seçeneği için **araç**  >  **seçeneklerinde**  >  **hata ayıklama**  >  **genel** ' de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="738be-295">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="738be-296">Bu, Visual Studio için varsayılan ayardır.</span><span class="sxs-lookup"><span data-stu-id="738be-296">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="738be-297">Hata ayıklama çalışmıyorsa, seçeneğinin seçili olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="738be-297">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="738be-298">Ortamınız bir HTTP proxy kullanıyorsa, `localhost` proxy atlama ayarlarına dahil edildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="738be-298">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="738be-299">Bu, `NO_PROXY` ortam değişkeni şu şekilde ayarlanarak yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="738be-299">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="738be-300">`launchSettings.json`Projenin dosyası.</span><span class="sxs-lookup"><span data-stu-id="738be-300">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="738be-301">Kullanıcı veya sistem ortamı değişkenleri düzeyinde tüm uygulamalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="738be-301">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="738be-302">Bir ortam değişkeni kullanırken, değişikliğin etkili olması için Visual Studio 'Yu yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="738be-302">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="738be-303">Güvenlik duvarları veya proxy 'lerin hata ayıklama proxy 'si (işlem) ile iletişimi engellemediğinden emin olun `NodeJS` .</span><span class="sxs-lookup"><span data-stu-id="738be-303">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="738be-304">Daha fazla bilgi için [güvenlik duvarı yapılandırması](#firewall-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="738be-304">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="738be-305">`OnInitialized{Async}`İsabet bulunmayan kesme noktaları</span><span class="sxs-lookup"><span data-stu-id="738be-305">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="738be-306">:::no-loc(Blazor):::Çerçevenin hata ayıklama proxy 'sinin başlatılması kısa bir süre sürer, bu nedenle [ `OnInitialized{Async}` yaşam döngüsü yöntemindeki](xref:blazor/components/lifecycle#component-initialization-methods) kesme noktaları isabet edemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="738be-306">The :::no-loc(Blazor)::: framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="738be-307">Hata ayıklama proxy 'sine, kesme noktasından önce başlamak için bir süre önce, Yöntem gövdesinin başlangıcında bir gecikme eklemeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="738be-307">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="738be-308">Uygulamanın yayın derlemesi için gecikmeye izin olmadığından emin olmak için bir [ `if` derleyici yönergesine](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) göre gecikmeyi dahil edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="738be-308">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="738be-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="738be-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="738be-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="738be-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="738be-311">Visual Studio (Windows) zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="738be-311">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="738be-312">Visual Studio, zaman aşımına ulaşılmış olduğunu belirten hata ayıklama bağdaştırıcısının başarısız bir özel durum oluşturursa, zaman aşımını bir kayıt defteri ayarıyla ayarlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="738be-312">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging ":::no-loc(Blazor):::TimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="738be-313">`{TIMEOUT}`Önceki komutta yer tutucu milisaniyedir.</span><span class="sxs-lookup"><span data-stu-id="738be-313">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="738be-314">Örneğin, bir dakika olarak atanır `60000` .</span><span class="sxs-lookup"><span data-stu-id="738be-314">For example, one minute is assigned as `60000`.</span></span>
