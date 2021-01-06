---
title: ASP.NET Core uygulamalardaki hataları işleme Blazor
author: guardrex
description: Blazor Blazor İşlenmemiş özel durumları nasıl yönettiğini ve hataları algılayan ve işleyen uygulamalar geliştirme ASP.NET Core nasıl yapılacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: c789928252417ef1cf95c60deb7edef24d58126e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056003"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a><span data-ttu-id="86100-103">ASP.NET Core uygulamalardaki hataları işleme Blazor</span><span class="sxs-lookup"><span data-stu-id="86100-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="86100-104">[Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="86100-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="86100-105">Bu makalede Blazor , işlenmemiş özel durumların nasıl yönetildiği ve hataları algılayan ve işleyen uygulamalar geliştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="86100-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="86100-106">Geliştirme sırasında ayrıntılı hatalar</span><span class="sxs-lookup"><span data-stu-id="86100-106">Detailed errors during development</span></span>

<span data-ttu-id="86100-107">BlazorGeliştirme sırasında bir uygulama düzgün çalışmadığı zaman, uygulamanın ayrıntılı hata bilgilerini alma sorunu gidermeye ve soruna yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="86100-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="86100-108">Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında altın bir çubuk görüntüler:</span><span class="sxs-lookup"><span data-stu-id="86100-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="86100-109">Geliştirme sırasında altın çubuk, özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="86100-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="86100-110">Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirir ve tarayıcıyı yenilemeyi önerir.</span><span class="sxs-lookup"><span data-stu-id="86100-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="86100-111">Bu hata işleme deneyimi için Kullanıcı arabirimi, Blazor proje şablonlarının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="86100-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="86100-112">Bir Blazor WebAssembly uygulamada, dosyadaki deneyimi özelleştirin `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="86100-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="86100-113">Bir Blazor Server uygulamada, dosyadaki deneyimi özelleştirin `Pages/_Host.cshtml` :</span><span class="sxs-lookup"><span data-stu-id="86100-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

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

<span data-ttu-id="86100-114">`blazor-error-ui`Öğe, şablonlara dahil edilen stillerle Blazor ( `wwwroot/css/app.css` veya) gizlenir `wwwroot/css/site.css` ve bir hata oluştuğunda gösterilir:</span><span class="sxs-lookup"><span data-stu-id="86100-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

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

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="86100-115">Bir Blazor Server uygulamanın işlenmemiş özel durumlara nasıl yeniden davranması</span><span class="sxs-lookup"><span data-stu-id="86100-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="86100-116">Blazor Server durum bilgisi olan bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="86100-116">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="86100-117">Kullanıcılar bir uygulamayla etkileşim kurarken, *devre* olarak bilinen sunucuya bir bağlantı sağlar.</span><span class="sxs-lookup"><span data-stu-id="86100-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="86100-118">Devre, etkin bileşen örneklerini ve diğer birçok durum düzeyini barındırır; örneğin:</span><span class="sxs-lookup"><span data-stu-id="86100-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="86100-119">Bileşenlerin en son işlenmiş çıktısı.</span><span class="sxs-lookup"><span data-stu-id="86100-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="86100-120">İstemci tarafı olayları tarafından tetiklenebilecek geçerli olay işleme temsilcileri kümesi.</span><span class="sxs-lookup"><span data-stu-id="86100-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="86100-121">Bir Kullanıcı uygulamayı birden çok tarayıcı sekmelerinde açarsa, birden çok bağımsız devreler vardır.</span><span class="sxs-lookup"><span data-stu-id="86100-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="86100-122">Blazor işlenmemiş özel durumları, nerede gerçekleştikleri devreye göre önemli olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="86100-122">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="86100-123">İşlenmeyen bir özel durum nedeniyle devre sonlandırılırsa, Kullanıcı yalnızca yeni bir bağlantı oluşturmak için sayfayı yeniden yükleyerek uygulamayla etkileşime geçerek devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="86100-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="86100-124">Diğer kullanıcılar veya diğer tarayıcı sekmeleri için devre dışı bırakılmış olan Sonlandırıcı dışındaki devreleri etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="86100-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="86100-125">Bu senaryo, çöktüğü bir masaüstü uygulamasına benzerdir.</span><span class="sxs-lookup"><span data-stu-id="86100-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="86100-126">Kilitlenen uygulamanın yeniden başlatılması gerekiyor, ancak diğer uygulamalar etkilenmedi.</span><span class="sxs-lookup"><span data-stu-id="86100-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="86100-127">Aşağıdaki nedenlerden dolayı işlenmeyen bir özel durum oluştuğunda devre sonlandırılır:</span><span class="sxs-lookup"><span data-stu-id="86100-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="86100-128">İşlenmeyen bir özel durum, genellikle devre dışı bir durumda bırakır.</span><span class="sxs-lookup"><span data-stu-id="86100-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="86100-129">İşlenmemiş bir özel durumdan sonra uygulamanın normal işlemi garanti edilemez.</span><span class="sxs-lookup"><span data-stu-id="86100-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="86100-130">Devre devam ederse uygulamada güvenlik açıkları görünebilir.</span><span class="sxs-lookup"><span data-stu-id="86100-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="86100-131">Geliştirici kodundaki işlenmemiş özel durumları yönetme</span><span class="sxs-lookup"><span data-stu-id="86100-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="86100-132">Bir uygulamanın bir hatadan sonra devam edebilmesi için, uygulamanın hata işleme mantığı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="86100-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="86100-133">Bu makalenin sonraki bölümlerinde işlenmemiş özel durumların olası kaynakları açıklanır.</span><span class="sxs-lookup"><span data-stu-id="86100-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="86100-134">Üretimde, çerçeve özel durum iletilerini veya yığın izlemelerini Kullanıcı arabiriminde işlemez.</span><span class="sxs-lookup"><span data-stu-id="86100-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="86100-135">Özel durum iletilerini veya yığın izlemelerini işleme şu şekilde olabilir:</span><span class="sxs-lookup"><span data-stu-id="86100-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="86100-136">Hassas bilgileri son kullanıcılara açıklayın.</span><span class="sxs-lookup"><span data-stu-id="86100-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="86100-137">Kötü niyetli bir kullanıcının uygulama, sunucu veya ağ güvenliğini tehlikeye atabilecek bir uygulamada zayıf yanları bulmasına yardımcı olun.</span><span class="sxs-lookup"><span data-stu-id="86100-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="86100-138">Kalıcı bir sağlayıcıyla hataları günlüğe kaydet</span><span class="sxs-lookup"><span data-stu-id="86100-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="86100-139">İşlenmeyen bir özel durum oluşursa, özel durum <xref:Microsoft.Extensions.Logging.ILogger> hizmet kapsayıcısında yapılandırılan örneklere kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="86100-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="86100-140">Varsayılan olarak, Blazor uygulamalar konsol günlüğü sağlayıcısı ile konsol çıktısına kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="86100-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="86100-141">Günlük boyutunu ve günlük döndürmesini yöneten bir sağlayıcı ile daha kalıcı bir konuma oturum açmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="86100-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="86100-142">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="86100-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="86100-143">Geliştirme sırasında, Blazor hata ayıklamaya yardımcı olması için genellikle özel durumların tüm ayrıntılarını tarayıcı konsoluna gönderir.</span><span class="sxs-lookup"><span data-stu-id="86100-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="86100-144">Üretimde, tarayıcı konsolundaki ayrıntılı hatalar varsayılan olarak devre dışıdır. Bu, hataların istemcilere gönderilmediği, ancak özel durumun tam ayrıntılarının hala sunucu tarafında günlüğe kaydedildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="86100-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="86100-145">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="86100-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="86100-146">Hangi olayların günlüğe kaydedileceğini ve günlüğe kaydedilen olayların önem düzeyi düzeyini karar vermelisiniz.</span><span class="sxs-lookup"><span data-stu-id="86100-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="86100-147">Saldırgan kullanıcılar hataları kasıtlı olarak tetikleyebiliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="86100-148">Örneğin, `ProductId` ürün ayrıntılarını görüntüleyen bir BILEŞENIN URL 'sinde, bilinmeyen bir hatadan olay günlüğe kaydetme.</span><span class="sxs-lookup"><span data-stu-id="86100-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="86100-149">Tüm hatalar günlüğe kaydetme için yüksek önem derecesine sahip olaylar olarak değerlendirilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="86100-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="86100-150">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="86100-150">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="86100-151">Hataların gerçekleşebileceği yerleri</span><span class="sxs-lookup"><span data-stu-id="86100-151">Places where errors may occur</span></span>

<span data-ttu-id="86100-152">Çerçeve ve uygulama kodu aşağıdaki konumlardan herhangi birinde işlenmeyen özel durumları tetikleyebiliriz:</span><span class="sxs-lookup"><span data-stu-id="86100-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="86100-153">Bileşen örneği oluşturma</span><span class="sxs-lookup"><span data-stu-id="86100-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="86100-154">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="86100-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="86100-155">İşleme mantığı</span><span class="sxs-lookup"><span data-stu-id="86100-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="86100-156">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="86100-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="86100-157">Bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="86100-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="86100-158">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="86100-158">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="86100-159">Blazor Server rerendering</span><span class="sxs-lookup"><span data-stu-id="86100-159">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="86100-160">Önceki işlenmemiş özel durumlar, bu makalenin aşağıdaki bölümlerinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="86100-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="86100-161">Bileşen örneği oluşturma</span><span class="sxs-lookup"><span data-stu-id="86100-161">Component instantiation</span></span>

<span data-ttu-id="86100-162">BlazorBir bileşenin örneğini oluşturduğunda:</span><span class="sxs-lookup"><span data-stu-id="86100-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="86100-163">Bileşenin Oluşturucusu çağrılır.</span><span class="sxs-lookup"><span data-stu-id="86100-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="86100-164">Bileşen Oluşturucu ya da özniteliği aracılığıyla bileşen oluşturucusuna sağlanan tek başına olmayan hizmetlerin oluşturucuları [`@inject`](xref:mvc/views/razor#inject) [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) çağırılır.</span><span class="sxs-lookup"><span data-stu-id="86100-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="86100-165">Blazor ServerHerhangi bir özellik için yürütülen herhangi bir Oluşturucu veya ayarlayıcı işlenmeyen bir `[Inject]` özel durum oluşturduğunda devre dışı olur.</span><span class="sxs-lookup"><span data-stu-id="86100-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="86100-166">Framework bileşeni örneklemediğinden özel durum önemlidir.</span><span class="sxs-lookup"><span data-stu-id="86100-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="86100-167">Oluşturucu mantığı özel durumlar oluşturmayabilir, uygulama [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) hata işleme ve günlüğe kaydetme içeren bir ifade kullanarak özel durumları yakalemelidir.</span><span class="sxs-lookup"><span data-stu-id="86100-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="86100-168">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="86100-168">Lifecycle methods</span></span>

<span data-ttu-id="86100-169">Bir bileşenin ömrü boyunca Blazor aşağıdaki [yaşam döngüsü yöntemlerini](xref:blazor/components/lifecycle)çağırır:</span><span class="sxs-lookup"><span data-stu-id="86100-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="86100-170">Herhangi bir yaşam döngüsü yöntemi, zaman uyumlu veya zaman uyumsuz olarak bir özel durum oluşturursa, özel durum bir devre için önemlidir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="86100-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="86100-171">Bileşenler için yaşam döngüsü yöntemlerinde hatalarla başa çıkmak için hata işleme mantığı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="86100-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="86100-172">Aşağıdaki örnekte, bir <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ürünü elde etmek için bir yöntemi çağırır:</span><span class="sxs-lookup"><span data-stu-id="86100-172">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="86100-173">Yönteminde oluşan bir özel durum, `ProductRepository.GetProductByIdAsync` bir [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle işlenir.</span><span class="sxs-lookup"><span data-stu-id="86100-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="86100-174">`catch`Blok yürütüldüğünde:</span><span class="sxs-lookup"><span data-stu-id="86100-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="86100-175">`loadFailed``true`, kullanıcıya bir hata iletisi göstermek için kullanılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="86100-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="86100-176">Hata günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="86100-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="86100-177">İşleme mantığı</span><span class="sxs-lookup"><span data-stu-id="86100-177">Rendering logic</span></span>

<span data-ttu-id="86100-178">Bir bileşen dosyasındaki bildirim temelli biçimlendirme `.razor` adlı bir C# yöntemine derlenir <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="86100-178">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="86100-179">Bir bileşen oluşturduğunda, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> oluşturulan bileşenin öğelerini, metnini ve alt bileşenlerini açıklayan bir veri yapısını yürütür ve oluşturur.</span><span class="sxs-lookup"><span data-stu-id="86100-179">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="86100-180">İşleme mantığı bir özel durum oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="86100-181">Bu senaryonun bir örneği `@someObject.PropertyName` değerlendirilir, ancak `@someObject` ise oluşur `null` .</span><span class="sxs-lookup"><span data-stu-id="86100-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="86100-182">İşleme mantığı tarafından oluşturulan işlenmemiş bir özel durum, bir Blazor Server devrende önemlidir.</span><span class="sxs-lookup"><span data-stu-id="86100-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="86100-183">Oluşturma mantığındaki null başvuru özel durumunu engellemek için, `null` üyelerine erişmeden önce bir nesne denetleyin.</span><span class="sxs-lookup"><span data-stu-id="86100-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="86100-184">Aşağıdaki örnekte, şu ise `person.Address` Özellikler erişilmez `person.Address` `null` :</span><span class="sxs-lookup"><span data-stu-id="86100-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="86100-185">Önceki kod olmadığını varsayar `person` `null` .</span><span class="sxs-lookup"><span data-stu-id="86100-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="86100-186">Genellikle, kodun yapısı, bileşenin işlendiği sırada bir nesnenin var olmasını garanti eder.</span><span class="sxs-lookup"><span data-stu-id="86100-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="86100-187">Bu durumlarda, işleme mantığını denetlemek gerekli değildir `null` .</span><span class="sxs-lookup"><span data-stu-id="86100-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="86100-188">Önceki örnekte, `person` `person` bileşen örneklendiği zaman oluşturulduğu için, mevcut olması garantisini alabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="86100-189">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="86100-189">Event handlers</span></span>

<span data-ttu-id="86100-190">İstemci tarafı kod, kullanarak olay işleyicileri oluşturulurken C# kodu etkinleştirmeleri tetikler:</span><span class="sxs-lookup"><span data-stu-id="86100-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="86100-191">Diğer `@on...` öznitelikler</span><span class="sxs-lookup"><span data-stu-id="86100-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="86100-192">Olay işleyici kodu, bu senaryolarda işlenmeyen bir özel durum oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="86100-193">Bir olay işleyicisi işlenmeyen bir özel durum oluşturursa (örneğin, bir veritabanı sorgusu başarısız olursa), özel durum bir devre için önemli olur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="86100-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="86100-194">Uygulama, dış nedenlerle başarısız olabilecek kodu çağırırsa, [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) hata işleme ve günlüğe kaydetme içeren bir ifade kullanarak özel durumlar yakalar.</span><span class="sxs-lookup"><span data-stu-id="86100-194">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="86100-195">Kullanıcı kodu yakalanmazsa ve özel durumu işlemezse çerçeve özel durumu günlüğe kaydeder ve devre sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="86100-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="86100-196">Bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="86100-196">Component disposal</span></span>

<span data-ttu-id="86100-197">Örneğin, Kullanıcı başka bir sayfaya gezindiği için, bir bileşen kullanıcı arabiriminden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="86100-198">Uygulayan bir bileşen <xref:System.IDisposable?displayProperty=fullName> kullanıcı arabiriminden kaldırıldığında, çerçeve bileşenin <xref:System.IDisposable.Dispose%2A> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="86100-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="86100-199">Bileşenin `Dispose` yöntemi işlenmeyen bir özel durum oluşturursa, özel durum bir devre için önemli olur Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="86100-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="86100-200">Çıkarma mantığı özel durumlar oluşturmayabilir, uygulama [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) hata işleme ve günlüğe kaydetme içeren bir ifade kullanarak özel durumları yakalemelidir.</span><span class="sxs-lookup"><span data-stu-id="86100-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="86100-201">Bileşen elden çıkarma hakkında daha fazla bilgi için bkz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> ..</span><span class="sxs-lookup"><span data-stu-id="86100-201">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="86100-202">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="86100-202">JavaScript interop</span></span>

<span data-ttu-id="86100-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .NET kodunun Kullanıcı tarayıcısında JavaScript çalışma zamanına zaman uyumsuz çağrılar yapmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="86100-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="86100-204">Aşağıdaki koşullar ile hata işleme için geçerlidir <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="86100-204">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="86100-205">Bir çağrı <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> zaman uyumlu başarısız olursa, .NET özel durumu oluşur.</span><span class="sxs-lookup"><span data-stu-id="86100-205">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="86100-206"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>Sağlanan bağımsız değişkenler seri hale getirilemediğinden, bir çağrı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-206">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="86100-207">Geliştirici kodu özel durumu yakalamalı.</span><span class="sxs-lookup"><span data-stu-id="86100-207">Developer code must catch the exception.</span></span> <span data-ttu-id="86100-208">Bir olay işleyicisindeki veya bileşen yaşam döngüsü yöntemindeki uygulama kodu bir özel durumu işlemezse, ortaya çıkan özel durum bir bağlantı Blazor Server devrenidir.</span><span class="sxs-lookup"><span data-stu-id="86100-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="86100-209">Bir çağrı <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> zaman uyumsuz olarak başarısız olursa, .net <xref:System.Threading.Tasks.Task> başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="86100-209">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="86100-210"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>Örneğin, JavaScript tarafı kodu bir özel durum oluşturduğundan veya olarak tamamlanan bir döndürürse, ' a çağrı başarısız olabilir `Promise` `rejected` .</span><span class="sxs-lookup"><span data-stu-id="86100-210">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="86100-211">Geliştirici kodu özel durumu yakalamalı.</span><span class="sxs-lookup"><span data-stu-id="86100-211">Developer code must catch the exception.</span></span> <span data-ttu-id="86100-212">[`await`](/dotnet/csharp/language-reference/keywords/await)İşlecini kullanıyorsanız, yöntem çağrısını [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) hata işleme ve günlüğe kaydetme ile bir deyime sarmalama seçeneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="86100-212">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="86100-213">Aksi takdirde, başarısız kod, bir devreniz için önemli olan işlenmemiş bir özel durumla sonuçlanır Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="86100-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="86100-214">Varsayılan olarak, çağrısı <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> belirli bir süre içinde tamamlanmalıdır veya çağrı zaman aşımına uğrar. Varsayılan zaman aşımı süresi bir dakikadır.</span><span class="sxs-lookup"><span data-stu-id="86100-214">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="86100-215">Zaman aşımı, kodu ağ bağlantısında veya hiçbir zaman bir tamamlanma iletisi göndermeme JavaScript kodundaki bir kaybına karşı korur.</span><span class="sxs-lookup"><span data-stu-id="86100-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="86100-216">Çağrı zaman aşımına uğrarsa sonuç <xref:System.Threading.Tasks> bir ile başarısız olur <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="86100-216">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="86100-217">Günlüğe kaydetme ile özel durumu yakalar ve işleyin.</span><span class="sxs-lookup"><span data-stu-id="86100-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="86100-218">Benzer şekilde, JavaScript kodu özniteliği tarafından gösterilen .NET yöntemlerine çağrıları başlatabilir [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) .</span><span class="sxs-lookup"><span data-stu-id="86100-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="86100-219">Bu .NET yöntemleri işlenmeyen bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="86100-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="86100-220">Özel durum, bir devre için önemli olarak değerlendirilmez Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="86100-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="86100-221">JavaScript tarafı `Promise` reddedilir.</span><span class="sxs-lookup"><span data-stu-id="86100-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="86100-222">.NET tarafında ya da yöntem çağrısının JavaScript tarafında hata işleme kodu kullanma seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="86100-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="86100-223">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="86100-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a><span data-ttu-id="86100-224">Blazor Server prerendering</span><span class="sxs-lookup"><span data-stu-id="86100-224">Blazor Server prerendering</span></span>

<span data-ttu-id="86100-225">Blazor bileşenler, işlenen HTML işaretlemesi kullanıcının ilk HTTP isteğinin bir parçası olarak döndürüldüğünden, [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) kullanılarak önceden uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-225">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="86100-226">Bu şu şekilde geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="86100-226">This works by:</span></span>

* <span data-ttu-id="86100-227">Aynı sayfanın parçası olan tüm ön işlenmiş bileşenler için yeni bir devre oluşturma.</span><span class="sxs-lookup"><span data-stu-id="86100-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="86100-228">İlk HTML oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="86100-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="86100-229">`disconnected`Kullanıcı tarayıcısının aynı sunucuya geri bir bağlantı kuruncaya kadar devreyi kabul etmek SignalR .</span><span class="sxs-lookup"><span data-stu-id="86100-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="86100-230">Bağlantı oluşturulduğunda, devre üzerindeki etkileşim sürdürülür ve bileşenlerin HTML işaretlemesi güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="86100-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="86100-231">Herhangi bir bileşen prerendering sırasında, örneğin bir yaşam döngüsü yöntemi veya işleme mantığı sırasında işlenmeyen bir özel durum oluşturursa:</span><span class="sxs-lookup"><span data-stu-id="86100-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="86100-232">Bu, devre için önemli bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="86100-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="86100-233">Özel durum, etiket yardımcısından çağrı yığınını ortaya atılır <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> .</span><span class="sxs-lookup"><span data-stu-id="86100-233">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="86100-234">Bu nedenle, özel durum geliştirici kodu tarafından açıkça yakalanmadığı takdirde tüm HTTP isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="86100-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="86100-235">Normal koşullarda, prerendering başarısız olduğunda bileşeni oluşturma ve işleme devam etmek, çalışan bir bileşen işlenemediği için mantıklı değildir.</span><span class="sxs-lookup"><span data-stu-id="86100-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="86100-236">Prerendering sırasında oluşabilecek hatalara tolerans sağlamak için hata işleme mantığı özel durum oluşturabilecek bir bileşenin içine yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="86100-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="86100-237">[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)Hata işleme ve günlüğe kaydetme ile deyimleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="86100-237">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="86100-238"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>Etiket yardımcısını bir deyime sarmalama yerine [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) , etiket Yardımcısı tarafından işlenen bileşene hata işleme mantığını koyun <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> .</span><span class="sxs-lookup"><span data-stu-id="86100-238">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="86100-239">Gelişmiş senaryolar</span><span class="sxs-lookup"><span data-stu-id="86100-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="86100-240">Özyinelemeli işleme</span><span class="sxs-lookup"><span data-stu-id="86100-240">Recursive rendering</span></span>

<span data-ttu-id="86100-241">Bileşenler yinelemeli olarak iç içe olabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-241">Components can be nested recursively.</span></span> <span data-ttu-id="86100-242">Bu, özyinelemeli veri yapılarını temsil etmek için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="86100-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="86100-243">Örneğin, bir `TreeNode` bileşen `TreeNode` düğüm alt öğelerinin her biri için daha fazla bileşen işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="86100-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="86100-244">Yinelemeli olarak işlenirken, sonsuz özyineleme sonucu veren kodlama desenlerinden kaçının:</span><span class="sxs-lookup"><span data-stu-id="86100-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="86100-245">Bir döngüyü içeren bir veri yapısını yinelemeli olarak işlemez.</span><span class="sxs-lookup"><span data-stu-id="86100-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="86100-246">Örneğin, alt öğeleri içeren bir ağaç düğümünü işlemez.</span><span class="sxs-lookup"><span data-stu-id="86100-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="86100-247">Bir döngüyü içeren bir düzen zinciri oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="86100-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="86100-248">Örneğin, düzeni kendisi olan bir düzen oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="86100-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="86100-249">Son kullanıcının, kötü amaçlı veri girişi veya JavaScript birlikte çalışabilirlik çağrıları aracılığıyla özyineleme/varyantları (kurallar) ihlal etmemenizi izin verme.</span><span class="sxs-lookup"><span data-stu-id="86100-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="86100-250">Oluşturma sırasında sonsuz döngüler:</span><span class="sxs-lookup"><span data-stu-id="86100-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="86100-251">İşleme işleminin süresiz olarak devam etmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="86100-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="86100-252">Sonlandırılmamış bir döngü oluşturmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="86100-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="86100-253">Bu senaryolarda, etkilenen bir Blazor Server devre başarısız olur ve iş parçacığı genellikle şunları yapmayı dener:</span><span class="sxs-lookup"><span data-stu-id="86100-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="86100-254">Süresiz olarak işletim sisteminin izin verdiği CPU süresini çok fazla kullanın.</span><span class="sxs-lookup"><span data-stu-id="86100-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="86100-255">Sınırsız miktarda sunucu belleği tükettin.</span><span class="sxs-lookup"><span data-stu-id="86100-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="86100-256">Sınırsız bellek tüketme, Sonlandırılmamış bir döngünün her yinelemede bir koleksiyona giriş eklediği senaryoya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="86100-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="86100-257">Sonsuz özyineleme desenlerinin önüne geçmek için, özyinelemeli işleme kodunun uygun durdurma koşullarını içerdiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="86100-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="86100-258">Özel işleme ağacı mantığı</span><span class="sxs-lookup"><span data-stu-id="86100-258">Custom render tree logic</span></span>

<span data-ttu-id="86100-259">Çoğu Blazor Bileşen dosya olarak uygulanır `.razor` ve çıktılarını işlemek için bir üzerinde çalışan Logic üretmek üzere derlenir <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> .</span><span class="sxs-lookup"><span data-stu-id="86100-259">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="86100-260">Bir geliştirici, <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> yordamsal C# kodu kullanarak Logic el ile uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-260">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="86100-261">Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="86100-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="86100-262">El ile işleme ağacı Oluşturucu mantığının kullanımı, genel bileşen geliştirme için önerilmeyen gelişmiş ve güvenli olmayan bir senaryo olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="86100-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="86100-263"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>Kod yazılmışsa, geliştirici kodun doğruluğunu garanti etmelidir.</span><span class="sxs-lookup"><span data-stu-id="86100-263">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="86100-264">Örneğin, geliştirici şunları sağlamalıdır:</span><span class="sxs-lookup"><span data-stu-id="86100-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="86100-265">Ve çağrıları <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> doğru şekilde dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="86100-265">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="86100-266">Öznitelikler yalnızca doğru yerlere eklenir.</span><span class="sxs-lookup"><span data-stu-id="86100-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="86100-267">Hatalı elle işleme ağacı Oluşturucu mantığı kilitlenmeler, sunucu askıda kalma ve güvenlik açıkları dahil rastgele tanımsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="86100-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="86100-268">El ile işleme ağacı Oluşturucu mantığını aynı karmaşıklık düzeyinde ve aynı *tehlike* düzeyiyle, el ile derleme kodu veya MSIL yönergeleri yazarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="86100-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
