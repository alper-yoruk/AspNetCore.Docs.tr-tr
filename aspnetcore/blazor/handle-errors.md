---
title: ASP.NET Core Blazor uygulamalardaki hataları işleme
author: guardrex
description: İşlenmemiş özel durumları Blazor nasıl Blazor yönettiğini ve hataları algılayan ve işleyen uygulamalar geliştirme ASP.NET Core nasıl yapılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 7601e448a52be5e1064326929281e72ad28a0e65
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967161"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="158ef-103">ASP.NET Core Blazor uygulamalardaki hataları işleme</span><span class="sxs-lookup"><span data-stu-id="158ef-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="158ef-104">[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="158ef-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="158ef-105">Bu makalede, işlenmemiş Blazor özel durumların nasıl yönetildiği ve hataları algılayan ve işleyen uygulamalar geliştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="158ef-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="158ef-106">Geliştirme sırasında ayrıntılı hatalar</span><span class="sxs-lookup"><span data-stu-id="158ef-106">Detailed errors during development</span></span>

<span data-ttu-id="158ef-107">Geliştirme sırasında Blazor bir uygulama düzgün çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerini alma sorunu gidermeye ve soruna yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="158ef-108">Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:</span><span class="sxs-lookup"><span data-stu-id="158ef-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="158ef-109">Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="158ef-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="158ef-110">Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.</span><span class="sxs-lookup"><span data-stu-id="158ef-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="158ef-111">Bu hata işleme deneyimi için Kullanıcı arabirimi, Blazor proje şablonlarının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="158ef-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="158ef-112">Blazor Webassembly uygulamasında, *Wwwroot/index.html* dosyasındaki deneyimi özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="158ef-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="158ef-113">Bir Blazor sunucu uygulamasında, *Pages/_Host. cshtml* dosyasındaki deneyimi özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="158ef-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="158ef-114">`blazor-error-ui` Öğe, Blazor şablonlara dahil edilen stillerle (*Wwwroot/CSS/site. css*) gizlenir ve bir hata oluştuğunda gösterilir:</span><span class="sxs-lookup"><span data-stu-id="158ef-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="158ef-115">Blazor Sunucu uygulamasının işlenmemiş özel durumlara nasıl yeniden davranması</span><span class="sxs-lookup"><span data-stu-id="158ef-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="158ef-116">Sunucu, durum bilgisi olan bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="158ef-116"> Server is a stateful framework.</span></span> <span data-ttu-id="158ef-117">Kullanıcılar bir uygulamayla etkileşim kurarken, *devre*olarak bilinen sunucuya bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="158ef-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="158ef-118">Devre, etkin bileşen örneklerini ve diğer birçok durum düzeyini barındırır; örneğin:</span><span class="sxs-lookup"><span data-stu-id="158ef-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="158ef-119">Bileşenlerin en son işlenmiş çıktısı.</span><span class="sxs-lookup"><span data-stu-id="158ef-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="158ef-120">İstemci tarafı olayları tarafından tetiklenebilecek geçerli olay işleme temsilcileri kümesi.</span><span class="sxs-lookup"><span data-stu-id="158ef-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="158ef-121">Bir Kullanıcı uygulamayı birden çok tarayıcı sekmelerinde açarsa, birden çok bağımsız devreler vardır.</span><span class="sxs-lookup"><span data-stu-id="158ef-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="158ef-122">işlenmemiş özel durumları, nerede gerçekleştikleri devreye göre önemli olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="158ef-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="158ef-123">İşlenmeyen bir özel durum nedeniyle devre sonlandırılırsa, Kullanıcı yalnızca yeni bir bağlantı oluşturmak için sayfayı yeniden yükleyerek uygulamayla etkileşime geçerek devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="158ef-124">Diğer kullanıcılar veya diğer tarayıcı sekmeleri için devre dışı bırakılmış olan Sonlandırıcı dışındaki devreleri etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="158ef-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="158ef-125">Bu senaryo, kilitlenen&mdash;uygulamayı engelleyen bir masaüstü uygulamasına benzer, ancak diğer uygulamalar etkilenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="158ef-126">Aşağıdaki nedenlerden dolayı işlenmeyen bir özel durum oluştuğunda devre sonlandırılır:</span><span class="sxs-lookup"><span data-stu-id="158ef-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="158ef-127">İşlenmeyen bir özel durum, genellikle devre dışı bir durumda bırakır.</span><span class="sxs-lookup"><span data-stu-id="158ef-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="158ef-128">İşlenmemiş bir özel durumdan sonra uygulamanın normal işlemi garanti edilemez.</span><span class="sxs-lookup"><span data-stu-id="158ef-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="158ef-129">Devre devam ederse uygulamada güvenlik açıkları görünebilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="158ef-130">Geliştirici kodundaki işlenmemiş özel durumları yönetme</span><span class="sxs-lookup"><span data-stu-id="158ef-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="158ef-131">Bir uygulamanın bir hatadan sonra devam edebilmesi için, uygulamanın hata işleme mantığı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="158ef-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="158ef-132">Bu makalenin sonraki bölümlerinde işlenmemiş özel durumların olası kaynakları açıklanır.</span><span class="sxs-lookup"><span data-stu-id="158ef-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="158ef-133">Üretimde, çerçeve özel durum iletilerini veya yığın izlemelerini Kullanıcı arabiriminde işlemez.</span><span class="sxs-lookup"><span data-stu-id="158ef-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="158ef-134">Özel durum iletilerini veya yığın izlemelerini işleme şu şekilde olabilir:</span><span class="sxs-lookup"><span data-stu-id="158ef-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="158ef-135">Hassas bilgileri son kullanıcılara açıklayın.</span><span class="sxs-lookup"><span data-stu-id="158ef-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="158ef-136">Kötü niyetli bir kullanıcının uygulama, sunucu veya ağ güvenliğini tehlikeye atabilecek bir uygulamada zayıf yanları bulmasına yardımcı olun.</span><span class="sxs-lookup"><span data-stu-id="158ef-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="158ef-137">Kalıcı bir sağlayıcıyla hataları günlüğe kaydet</span><span class="sxs-lookup"><span data-stu-id="158ef-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="158ef-138">İşlenmeyen bir özel durum oluşursa, özel durum hizmet kapsayıcısında yapılandırılan <xref:Microsoft.Extensions.Logging.ILogger> örneklere kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="158ef-139">Varsayılan olarak, Blazor uygulamalar konsol günlüğü sağlayıcısı ile konsol çıktısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="158ef-140">Günlük boyutunu ve günlük döndürmesini yöneten bir sağlayıcı ile daha kalıcı bir konuma oturum açmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="158ef-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="158ef-141">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="158ef-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="158ef-142">Geliştirme sırasında, Blazor hata ayıklamaya yardımcı olması için genellikle özel durumların tüm ayrıntılarını tarayıcı konsoluna gönderir.</span><span class="sxs-lookup"><span data-stu-id="158ef-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="158ef-143">Üretimde, tarayıcı konsolundaki ayrıntılı hatalar varsayılan olarak devre dışıdır. Bu, hataların istemcilere gönderilmediği, ancak özel durumun tam ayrıntılarının hala sunucu tarafında günlüğe kaydedildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="158ef-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="158ef-144">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="158ef-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="158ef-145">Hangi olayların günlüğe kaydedileceğini ve günlüğe kaydedilen olayların önem düzeyi düzeyini karar vermelisiniz.</span><span class="sxs-lookup"><span data-stu-id="158ef-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="158ef-146">Saldırgan kullanıcılar hataları kasıtlı olarak tetikleyebiliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="158ef-147">Örneğin, ürün ayrıntılarını görüntüleyen bir bileşenin URL 'sinde, bilinmeyen `ProductId` bir hatadan olay günlüğe kaydetme.</span><span class="sxs-lookup"><span data-stu-id="158ef-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="158ef-148">Tüm hatalar günlüğe kaydetme için yüksek önem derecesine sahip olaylar olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="158ef-149">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="158ef-149">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="158ef-150">Hataların gerçekleşebileceği yerleri</span><span class="sxs-lookup"><span data-stu-id="158ef-150">Places where errors may occur</span></span>

<span data-ttu-id="158ef-151">Çerçeve ve uygulama kodu aşağıdaki konumlardan herhangi birinde işlenmeyen özel durumları tetikleyebiliriz:</span><span class="sxs-lookup"><span data-stu-id="158ef-151">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="158ef-152">Bileşen örneği oluşturma</span><span class="sxs-lookup"><span data-stu-id="158ef-152">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="158ef-153">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="158ef-153">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="158ef-154">İşleme mantığı</span><span class="sxs-lookup"><span data-stu-id="158ef-154">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="158ef-155">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="158ef-155">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="158ef-156">Bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="158ef-156">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="158ef-157">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="158ef-157">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="158ef-158">[BlazorSunucu rerendering](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="158ef-158">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="158ef-159">Önceki işlenmemiş özel durumlar, bu makalenin aşağıdaki bölümlerinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="158ef-159">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="158ef-160">Bileşen örneği oluşturma</span><span class="sxs-lookup"><span data-stu-id="158ef-160">Component instantiation</span></span>

<span data-ttu-id="158ef-161">Bir Blazor bileşenin örneğini oluşturduğunda:</span><span class="sxs-lookup"><span data-stu-id="158ef-161">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="158ef-162">Bileşenin Oluşturucusu çağrılır.</span><span class="sxs-lookup"><span data-stu-id="158ef-162">The component's constructor is invoked.</span></span>
* <span data-ttu-id="158ef-163">Bileşen Oluşturucu ya da [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) özniteliği aracılığıyla bileşen oluşturucusuna sağlanan tek başına olmayan hizmetlerin oluşturucuları çağırılır.</span><span class="sxs-lookup"><span data-stu-id="158ef-163">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="158ef-164">Herhangi Blazor `[Inject]` bir özellik için yürütülen herhangi bir Oluşturucu veya ayarlayıcı işlenmeyen bir özel durum oluşturduğunda sunucu devresi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-164">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="158ef-165">Framework bileşeni örneklemediğinden özel durum önemlidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-165">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="158ef-166">Oluşturucu mantığı özel durumlar oluşturmayabilir, uygulama hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumları yakalemelidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-166">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="158ef-167">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="158ef-167">Lifecycle methods</span></span>

<span data-ttu-id="158ef-168">Bir bileşenin ömrü boyunca aşağıdaki Blazor [yaşam döngüsü yöntemlerini](xref:blazor/lifecycle)çağırır:</span><span class="sxs-lookup"><span data-stu-id="158ef-168">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="158ef-169">Herhangi bir yaşam döngüsü yöntemi, zaman uyumlu veya zaman uyumsuz olarak bir özel durum oluşturursa, özel Blazor durum sunucu devresi için önemli olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-169">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="158ef-170">Bileşenler için yaşam döngüsü yöntemlerinde hatalarla başa çıkmak için hata işleme mantığı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="158ef-170">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="158ef-171">Aşağıdaki örnekte, bir ürünü `OnParametersSetAsync` elde etmek için bir yöntemi çağırır:</span><span class="sxs-lookup"><span data-stu-id="158ef-171">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="158ef-172">`ProductRepository.GetProductByIdAsync` Yönteminde oluşan bir özel durum, bir `try-catch` ifadesiyle işlenir.</span><span class="sxs-lookup"><span data-stu-id="158ef-172">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="158ef-173">`catch` Blok yürütüldüğünde:</span><span class="sxs-lookup"><span data-stu-id="158ef-173">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="158ef-174">`loadFailed`, kullanıcıya bir `true`hata iletisi göstermek için kullanılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="158ef-174">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="158ef-175">Hata günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-175">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="158ef-176">İşleme mantığı</span><span class="sxs-lookup"><span data-stu-id="158ef-176">Rendering logic</span></span>

<span data-ttu-id="158ef-177">Bir `.razor` bileşen dosyasındaki bildirim temelli biçimlendirme adlı `BuildRenderTree`bir C# yöntemine derlenir.</span><span class="sxs-lookup"><span data-stu-id="158ef-177">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="158ef-178">Bir bileşen oluşturduğunda, `BuildRenderTree` oluşturulan bileşenin öğelerini, metnini ve alt bileşenlerini açıklayan bir veri yapısını yürütür ve oluşturur.</span><span class="sxs-lookup"><span data-stu-id="158ef-178">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="158ef-179">İşleme mantığı bir özel durum oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-179">Rendering logic can throw an exception.</span></span> <span data-ttu-id="158ef-180">Bu senaryonun bir örneği değerlendirilir, ancak `@someObject.PropertyName` `@someObject` ise `null`oluşur.</span><span class="sxs-lookup"><span data-stu-id="158ef-180">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="158ef-181">Oluşturma mantığı tarafından oluşturulan işlenmeyen bir özel durum, Blazor sunucu devresi için önemli bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="158ef-181">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="158ef-182">Oluşturma mantığındaki null başvuru özel durumunu engellemek için, üyelerine erişmeden önce `null` bir nesne denetleyin.</span><span class="sxs-lookup"><span data-stu-id="158ef-182">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="158ef-183">Aşağıdaki örnekte, `person.Address` şu `null`ise `person.Address` Özellikler erişilmez:</span><span class="sxs-lookup"><span data-stu-id="158ef-183">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="158ef-184">Önceki kod olmadığını `person` varsayar `null`.</span><span class="sxs-lookup"><span data-stu-id="158ef-184">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="158ef-185">Genellikle, kodun yapısı, bileşenin işlendiği sırada bir nesnenin var olmasını garanti eder.</span><span class="sxs-lookup"><span data-stu-id="158ef-185">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="158ef-186">Bu durumlarda, işleme mantığını denetlemek `null` gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="158ef-186">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="158ef-187">Önceki örnekte, bileşen örneklendiği `person` `person` zaman oluşturulduğu için, mevcut olması garantisini alabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-187">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="158ef-188">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="158ef-188">Event handlers</span></span>

<span data-ttu-id="158ef-189">İstemci tarafı kod, kullanarak olay işleyicileri oluşturulurken C# kodu etkinleştirmeleri tetikler:</span><span class="sxs-lookup"><span data-stu-id="158ef-189">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="158ef-190">Diğer `@on...` öznitelikler</span><span class="sxs-lookup"><span data-stu-id="158ef-190">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="158ef-191">Olay işleyici kodu, bu senaryolarda işlenmeyen bir özel durum oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-191">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="158ef-192">Bir olay işleyicisi işlenmeyen bir özel durum oluşturursa (örneğin, bir veritabanı sorgusu başarısız olursa), özel durum Blazor sunucu devresi için önemli olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-192">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="158ef-193">Uygulama, dış nedenlerle başarısız olabilecek kodu çağırırsa, hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumlar yakalayın.</span><span class="sxs-lookup"><span data-stu-id="158ef-193">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="158ef-194">Kullanıcı kodu yakalanmazsa ve özel durumu işlemezse çerçeve özel durumu günlüğe kaydeder ve devre sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="158ef-194">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="158ef-195">Bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="158ef-195">Component disposal</span></span>

<span data-ttu-id="158ef-196">Örneğin, Kullanıcı başka bir sayfaya gezindiği için, bir bileşen kullanıcı arabiriminden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-196">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="158ef-197">Uygulayan <xref:System.IDisposable?displayProperty=fullName> bir bileşen kullanıcı arabiriminden kaldırıldığında, çerçeve bileşenin <xref:System.IDisposable.Dispose%2A> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="158ef-197">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="158ef-198">Bileşenin `Dispose` yöntemi işlenmeyen bir özel durum oluşturursa, özel durum Blazor sunucu devresi için önemli olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-198">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="158ef-199">Çıkarma mantığı özel durumlar oluşturmayabilir, uygulama hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesini kullanarak özel durumları yakalemelidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-199">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="158ef-200">Bileşen elden çıkarma hakkında daha fazla bilgi için <xref:blazor/lifecycle#component-disposal-with-idisposable>bkz..</span><span class="sxs-lookup"><span data-stu-id="158ef-200">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="158ef-201">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="158ef-201">JavaScript interop</span></span>

<span data-ttu-id="158ef-202">`IJSRuntime.InvokeAsync<T>`.NET kodunun Kullanıcı tarayıcısında JavaScript çalışma zamanına zaman uyumsuz çağrılar yapmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="158ef-202">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="158ef-203">Aşağıdaki koşullar ile `InvokeAsync<T>`hata işleme için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="158ef-203">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="158ef-204">Bir çağrı zaman uyumlu `InvokeAsync<T>` başarısız olursa, .NET özel durumu oluşur.</span><span class="sxs-lookup"><span data-stu-id="158ef-204">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="158ef-205">Sağlanan bağımsız değişkenler `InvokeAsync<T>` seri hale getirilemediğinden, bir çağrı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-205">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="158ef-206">Geliştirici kodu özel durumu yakalamalı.</span><span class="sxs-lookup"><span data-stu-id="158ef-206">Developer code must catch the exception.</span></span> <span data-ttu-id="158ef-207">Bir olay işleyicisindeki veya bileşen yaşam döngüsü yöntemindeki uygulama kodu bir özel durumu işlemezse, ortaya çıkan özel durum Blazor sunucu devresi için önemli olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-207">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="158ef-208">Bir çağrı zaman uyumsuz `InvokeAsync<T>` olarak başarısız olursa, .net <xref:System.Threading.Tasks.Task> başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-208">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="158ef-209">Örneğin, JavaScript `InvokeAsync<T>` tarafı kodu bir özel durum oluşturduğundan veya olarak `Promise` `rejected`tamamlanan bir döndürürse, ' a çağrı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-209">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="158ef-210">Geliştirici kodu özel durumu yakalamalı.</span><span class="sxs-lookup"><span data-stu-id="158ef-210">Developer code must catch the exception.</span></span> <span data-ttu-id="158ef-211">[Await](/dotnet/csharp/language-reference/keywords/await) işleci kullanılıyorsa, yöntem çağrısını hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesinde sarmalamalı olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="158ef-211">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="158ef-212">Aksi takdirde, hata veren kod, bir Blazor sunucu devresi için önemli olan işlenmemiş bir özel durumla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="158ef-212">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="158ef-213">Varsayılan olarak, `InvokeAsync<T>` çağrısı belirli bir süre içinde tamamlanmalıdır veya çağrı zaman aşımına uğrar. Varsayılan zaman aşımı süresi bir dakikadır.</span><span class="sxs-lookup"><span data-stu-id="158ef-213">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="158ef-214">Zaman aşımı, kodu ağ bağlantısında veya hiçbir zaman bir tamamlanma iletisi göndermeme JavaScript kodundaki bir kaybına karşı korur.</span><span class="sxs-lookup"><span data-stu-id="158ef-214">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="158ef-215">Çağrı zaman aşımına uğrarsa sonuç `Task` bir <xref:System.OperationCanceledException>ile başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-215">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="158ef-216">Günlüğe kaydetme ile özel durumu yakalar ve işleyin.</span><span class="sxs-lookup"><span data-stu-id="158ef-216">Trap and process the exception with logging.</span></span>

<span data-ttu-id="158ef-217">Benzer şekilde, JavaScript kodu [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) özniteliği tarafından gösterilen .net yöntemlerine çağrıları başlatabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-217">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="158ef-218">Bu .NET yöntemleri işlenmeyen bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="158ef-218">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="158ef-219">Özel durum, bir Blazor sunucu devresi için önemli olarak değerlendirilmez.</span><span class="sxs-lookup"><span data-stu-id="158ef-219">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="158ef-220">JavaScript tarafı `Promise` reddedilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-220">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="158ef-221">.NET tarafında ya da yöntem çağrısının JavaScript tarafında hata işleme kodu kullanma seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="158ef-221">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="158ef-222">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="158ef-222">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor<span data-ttu-id="158ef-223">Sunucu prerendering</span><span class="sxs-lookup"><span data-stu-id="158ef-223"> Server prerendering</span></span>

Blazor<span data-ttu-id="158ef-224">bileşenler, işlenen HTML işaretlemesi kullanıcının ilk HTTP isteğinin bir parçası olarak döndürüldüğünden, [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) kullanılarak önceden uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-224"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="158ef-225">Bu şu şekilde geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="158ef-225">This works by:</span></span>

* <span data-ttu-id="158ef-226">Aynı sayfanın parçası olan tüm ön işlenmiş bileşenler için yeni bir devre oluşturma.</span><span class="sxs-lookup"><span data-stu-id="158ef-226">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="158ef-227">İlk HTML oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="158ef-227">Generating the initial HTML.</span></span>
* <span data-ttu-id="158ef-228">Kullanıcı tarayıcısının aynı sunucuya `disconnected` geri bir SignalR bağlantı kuruncaya kadar devreyi kabul etmek.</span><span class="sxs-lookup"><span data-stu-id="158ef-228">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="158ef-229">Bağlantı oluşturulduğunda, devre üzerindeki etkileşim sürdürülür ve bileşenlerin HTML işaretlemesi güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-229">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="158ef-230">Herhangi bir bileşen prerendering sırasında, örneğin bir yaşam döngüsü yöntemi veya işleme mantığı sırasında işlenmeyen bir özel durum oluşturursa:</span><span class="sxs-lookup"><span data-stu-id="158ef-230">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="158ef-231">Bu, devre için önemli bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="158ef-231">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="158ef-232">Özel durum, `Component` etiket yardımcısından çağrı yığınını ortaya atılır.</span><span class="sxs-lookup"><span data-stu-id="158ef-232">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="158ef-233">Bu nedenle, özel durum geliştirici kodu tarafından açıkça yakalanmadığı takdirde tüm HTTP isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-233">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="158ef-234">Normal koşullarda, prerendering başarısız olduğunda bileşeni oluşturma ve işleme devam etmek, çalışan bir bileşen işlenemediği için mantıklı değildir.</span><span class="sxs-lookup"><span data-stu-id="158ef-234">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="158ef-235">Prerendering sırasında oluşabilecek hatalara tolerans sağlamak için hata işleme mantığı özel durum oluşturabilecek bir bileşenin içine yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-235">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="158ef-236">[Try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimlerini hata işleme ve günlüğe kaydetme ile kullanın.</span><span class="sxs-lookup"><span data-stu-id="158ef-236">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="158ef-237">`Component` Etiket yardımcısını bir `try-catch` deyime sarmalama yerine, `Component` etiket Yardımcısı tarafından işlenen bileşene hata işleme mantığını koyun.</span><span class="sxs-lookup"><span data-stu-id="158ef-237">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="158ef-238">Gelişmiş senaryolar</span><span class="sxs-lookup"><span data-stu-id="158ef-238">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="158ef-239">Özyinelemeli işleme</span><span class="sxs-lookup"><span data-stu-id="158ef-239">Recursive rendering</span></span>

<span data-ttu-id="158ef-240">Bileşenler yinelemeli olarak iç içe olabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-240">Components can be nested recursively.</span></span> <span data-ttu-id="158ef-241">Bu, özyinelemeli veri yapılarını temsil etmek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="158ef-241">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="158ef-242">Örneğin, bir `TreeNode` bileşen düğüm alt öğelerinin her `TreeNode` biri için daha fazla bileşen işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-242">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="158ef-243">Yinelemeli olarak işlenirken, sonsuz özyineleme sonucu veren kodlama desenlerinden kaçının:</span><span class="sxs-lookup"><span data-stu-id="158ef-243">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="158ef-244">Bir döngüyü içeren bir veri yapısını yinelemeli olarak işlemez.</span><span class="sxs-lookup"><span data-stu-id="158ef-244">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="158ef-245">Örneğin, alt öğeleri içeren bir ağaç düğümünü işlemez.</span><span class="sxs-lookup"><span data-stu-id="158ef-245">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="158ef-246">Bir döngüyü içeren bir düzen zinciri oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="158ef-246">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="158ef-247">Örneğin, düzeni kendisi olan bir düzen oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="158ef-247">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="158ef-248">Son kullanıcının, kötü amaçlı veri girişi veya JavaScript birlikte çalışabilirlik çağrıları aracılığıyla özyineleme/varyantları (kurallar) ihlal etmemenizi izin verme.</span><span class="sxs-lookup"><span data-stu-id="158ef-248">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="158ef-249">Oluşturma sırasında sonsuz döngüler:</span><span class="sxs-lookup"><span data-stu-id="158ef-249">Infinite loops during rendering:</span></span>

* <span data-ttu-id="158ef-250">İşleme işleminin süresiz olarak devam etmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="158ef-250">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="158ef-251">Sonlandırılmamış bir döngü oluşturmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="158ef-251">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="158ef-252">Bu senaryolarda, etkilenen Blazor sunucu devresi başarısız olur ve iş parçacığı genellikle şunları yapmayı dener:</span><span class="sxs-lookup"><span data-stu-id="158ef-252">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="158ef-253">Süresiz olarak işletim sisteminin izin verdiği CPU süresini çok fazla kullanın.</span><span class="sxs-lookup"><span data-stu-id="158ef-253">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="158ef-254">Sınırsız miktarda sunucu belleği tükettin.</span><span class="sxs-lookup"><span data-stu-id="158ef-254">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="158ef-255">Sınırsız bellek tüketme, Sonlandırılmamış bir döngünün her yinelemede bir koleksiyona giriş eklediği senaryoya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="158ef-255">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="158ef-256">Sonsuz özyineleme desenlerinin önüne geçmek için, özyinelemeli işleme kodunun uygun durdurma koşullarını içerdiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="158ef-256">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="158ef-257">Özel işleme ağacı mantığı</span><span class="sxs-lookup"><span data-stu-id="158ef-257">Custom render tree logic</span></span>

<span data-ttu-id="158ef-258">Çoğu Blazor bileşen *. Razor* dosyaları olarak uygulanır ve çıktısını işlemek için bir `RenderTreeBuilder` üzerinde çalışan Logic üretmek için derlenir.</span><span class="sxs-lookup"><span data-stu-id="158ef-258">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="158ef-259">Bir geliştirici, yordamsal C# `RenderTreeBuilder` kodu kullanarak Logic el ile uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-259">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="158ef-260">Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="158ef-260">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="158ef-261">El ile işleme ağacı Oluşturucu mantığının kullanımı, genel bileşen geliştirme için önerilmeyen gelişmiş ve güvenli olmayan bir senaryo olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-261">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="158ef-262">`RenderTreeBuilder` Kod yazılmışsa, geliştirici kodun doğruluğunu garanti etmelidir.</span><span class="sxs-lookup"><span data-stu-id="158ef-262">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="158ef-263">Örneğin, geliştirici şunları sağlamalıdır:</span><span class="sxs-lookup"><span data-stu-id="158ef-263">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="158ef-264">`OpenElement` Ve `CloseElement` çağrıları doğru şekilde dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="158ef-264">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="158ef-265">Öznitelikler yalnızca doğru yerlere eklenir.</span><span class="sxs-lookup"><span data-stu-id="158ef-265">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="158ef-266">Hatalı elle işleme ağacı Oluşturucu mantığı kilitlenmeler, sunucu askıda kalma ve güvenlik açıkları dahil rastgele tanımsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="158ef-266">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="158ef-267">El ile işleme ağacı Oluşturucu mantığını aynı karmaşıklık düzeyinde ve aynı *tehlike* düzeyiyle, el ile derleme kodu veya MSIL yönergeleri yazarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="158ef-267">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
