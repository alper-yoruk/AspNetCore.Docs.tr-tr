---
title: ASP.NET Core Blazor uygulamalarındaki hataları işleme
author: guardrex
description: ASP.NET Blazor Core'un Blazor işlenmemiş özel durumları nasıl yönettiğini ve hataları algılayan ve işleyen uygulamaların nasıl geliştirilebildiğini keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382281"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="47f53-103">ASP.NET Core Blazor uygulamalarındaki hataları işleme</span><span class="sxs-lookup"><span data-stu-id="47f53-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="47f53-104">Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="47f53-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="47f53-105">Bu makalede, Blazor işlenmemiş özel durumları nasıl yönetildiği ve hataları algılayan ve işleyen uygulamaların nasıl geliştirilen anlatılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="47f53-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="47f53-106">Geliştirme sırasında ayrıntılı hatalar</span><span class="sxs-lookup"><span data-stu-id="47f53-106">Detailed errors during development</span></span>

<span data-ttu-id="47f53-107">Bir Blazor uygulama geliştirme sırasında düzgün çalışmadığında, uygulamadan ayrıntılı hata bilgileri almak sorun giderme ve sorunu gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="47f53-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="47f53-108">Bir hata oluştuğunda, Blazor uygulamalar ekranın alt kısmında bir altın çubuk görüntüler:</span><span class="sxs-lookup"><span data-stu-id="47f53-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="47f53-109">Geliştirme sırasında, gold bar sizi özel durumu görebileceğiniz tarayıcı konsoluna yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="47f53-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="47f53-110">Üretimde, altın çubuk kullanıcıya bir hata oluştuğunu bildirer ve tarayıcının yenilenmesini önerir.</span><span class="sxs-lookup"><span data-stu-id="47f53-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="47f53-111">Bu hata işleme deneyimi için UI Blazor proje şablonlarının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="47f53-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="47f53-112">Bir Blazor WebAssembly uygulamasında, *wwwroot/index.html* dosyasındaki deneyimi özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="47f53-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="47f53-113">Sunucu Blazor uygulamasında, *Pages/_Host.cshtml* dosyasındaki deneyimi özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="47f53-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="47f53-114">Öğe `blazor-error-ui` şablonlarda yer alan Blazor stiller tarafından gizlenir *(wwwroot/css/site.css)* ve bir hata oluştuğunda gösterilir:</span><span class="sxs-lookup"><span data-stu-id="47f53-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="47f53-115">Sunucu Blazor uygulaması işlenmemiş özel durumlara nasıl tepki verir?</span><span class="sxs-lookup"><span data-stu-id="47f53-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="47f53-116">Sunucu durumsal bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="47f53-116"> Server is a stateful framework.</span></span> <span data-ttu-id="47f53-117">Kullanıcılar bir uygulamayla etkileşimde iken, *devre*olarak bilinen sunucuya bağlantı sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="47f53-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="47f53-118">Devre etkin bileşen örnekleri, artı durum, örneğin diğer birçok yönlerini tutar:</span><span class="sxs-lookup"><span data-stu-id="47f53-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="47f53-119">Bileşenlerin en son işlenmiş çıktısı.</span><span class="sxs-lookup"><span data-stu-id="47f53-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="47f53-120">İstemci tarafı olayları tarafından tetiklenebilen geçerli olay işleme temsilcileri kümesi.</span><span class="sxs-lookup"><span data-stu-id="47f53-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="47f53-121">Bir kullanıcı uygulamayı birden çok tarayıcı sekmesinde açarsa, birden çok bağımsız devresi vardır.</span><span class="sxs-lookup"><span data-stu-id="47f53-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="47f53-122">işlenmemiş çoğu özel durumu, oluştukları devre için ölümcül olarak ele alır.</span><span class="sxs-lookup"><span data-stu-id="47f53-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="47f53-123">Bir devre işlenmemiş bir özel durum nedeniyle sonlandırılırsa, kullanıcı yalnızca yeni bir devre oluşturmak için sayfayı yeniden yükleyerek uygulamayla etkileşime devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="47f53-124">Diğer kullanıcılar veya diğer tarayıcı sekmeleri için devreler olan sonlandırılan devrelerin dışındaki devreler etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="47f53-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="47f53-125">Bu senaryo, çöken uygulamayı çökerten bir masaüstü&mdash;uygulamasına benzer, ancak diğer uygulamalar etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="47f53-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="47f53-126">İşlenmemiş bir özel durum aşağıdaki nedenlerle oluştuğunda bir devre sonlandırılır:</span><span class="sxs-lookup"><span data-stu-id="47f53-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="47f53-127">İşlenmemiş bir özel durum genellikle devreyi tanımlanmamış bir durumda bırakır.</span><span class="sxs-lookup"><span data-stu-id="47f53-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="47f53-128">Uygulamanın normal çalışması işlenmemiş bir özel durum sonrasında garanti edilemez.</span><span class="sxs-lookup"><span data-stu-id="47f53-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="47f53-129">Devre devam ederse uygulamada güvenlik açıkları görünebilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="47f53-130">Geliştirici kodunda işlenmemiş özel durumları yönetme</span><span class="sxs-lookup"><span data-stu-id="47f53-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="47f53-131">Bir uygulamanın hatadan sonra devam edeabilmesi için uygulamanın hata işleme mantığına sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="47f53-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="47f53-132">Bu makalenin sonraki bölümleri, işlenmemiş özel durumların olası kaynaklarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="47f53-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="47f53-133">Üretimde, oluşturma aracında çerçeve özel durum iletileri veya yığın izlemeleri oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="47f53-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="47f53-134">Özel durum iletileri veya yığın izlemeleri oluşturma:</span><span class="sxs-lookup"><span data-stu-id="47f53-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="47f53-135">Hassas bilgileri son kullanıcılara ifşa edin.</span><span class="sxs-lookup"><span data-stu-id="47f53-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="47f53-136">Kötü niyetli bir kullanıcının uygulamanın, sunucunun veya ağın güvenliğini tehlikeye atabilecek bir uygulamadaki zayıflıkları keşfetmesinde yardımcı olun.</span><span class="sxs-lookup"><span data-stu-id="47f53-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="47f53-137">Kalıcı bir sağlayıcıyla hataları günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="47f53-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="47f53-138">İşlenmemiş bir özel durum oluşursa, <xref:Microsoft.Extensions.Logging.ILogger> özel durum hizmet kapsayıcısında yapılandırılan örneklere günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="47f53-139">Varsayılan olarak, Blazor uygulamalar Konsol Günlük Sağlayıcısı ile çıktı konsolu için günlük.</span><span class="sxs-lookup"><span data-stu-id="47f53-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="47f53-140">Günlük boyutunu ve günlük döndürmeyi yöneten bir sağlayıcıyla daha kalıcı bir konuma günlüğe kaydetmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="47f53-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="47f53-141">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="47f53-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="47f53-142">Geliştirme sırasında, Blazor genellikle hata ayıklama yardımcı olmak için tarayıcının konsoluna özel durumların tüm ayrıntılarını gönderir.</span><span class="sxs-lookup"><span data-stu-id="47f53-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="47f53-143">Üretimde, tarayıcının konsolundaki ayrıntılı hatalar varsayılan olarak devre dışı bırakılır, bu da hataların istemcilere gönderilmediği, ancak özel durum ayrıntılarının hala sunucu tarafında günlüğe kaydedildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="47f53-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="47f53-144">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="47f53-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="47f53-145">Hangi olayların günlüğe kaydolacağına ve günlüğe kaydedilen olayların önem düzeyine karar vermelisiniz.</span><span class="sxs-lookup"><span data-stu-id="47f53-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="47f53-146">Düşman kullanıcılar hataları kasıtlı olarak tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="47f53-147">Örneğin, ürün ayrıntılarını görüntüleyen bir bileşenin `ProductId` URL'sinde bilinmeyenin sağlandığı bir hatadan olay kaydetmeyin.</span><span class="sxs-lookup"><span data-stu-id="47f53-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="47f53-148">Tüm hatalar günlüğe kaydetme için yüksek öneme yönelik olaylar olarak ele alınmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="47f53-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="47f53-149">Hataların oluşabileceği yerler</span><span class="sxs-lookup"><span data-stu-id="47f53-149">Places where errors may occur</span></span>

<span data-ttu-id="47f53-150">Çerçeve ve uygulama kodu, aşağıdaki konumlardan herhangi birinde işlenmemiş özel durumları tetikleyebilir:</span><span class="sxs-lookup"><span data-stu-id="47f53-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="47f53-151">Bileşen anında</span><span class="sxs-lookup"><span data-stu-id="47f53-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="47f53-152">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="47f53-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="47f53-153">Oluşturma mantığı</span><span class="sxs-lookup"><span data-stu-id="47f53-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="47f53-154">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="47f53-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="47f53-155">Bileşen imha</span><span class="sxs-lookup"><span data-stu-id="47f53-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="47f53-156">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="47f53-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="47f53-157">[BlazorSunucu yeniden işleme](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="47f53-157">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="47f53-158">Önceki işlenmemiş özel durumlar bu makalenin aşağıdaki bölümlerinde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="47f53-158">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="47f53-159">Bileşen anında</span><span class="sxs-lookup"><span data-stu-id="47f53-159">Component instantiation</span></span>

<span data-ttu-id="47f53-160">Bir Blazor bileşen örneğini oluşturduğunda:</span><span class="sxs-lookup"><span data-stu-id="47f53-160">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="47f53-161">Bileşenin oluşturucusu çağrılır.</span><span class="sxs-lookup"><span data-stu-id="47f53-161">The component's constructor is invoked.</span></span>
* <span data-ttu-id="47f53-162">[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) Bileşenin oluşturucuya yönerge veya [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) öznitelik yoluyla sağlanan singleton olmayan DI hizmetlerinin üreticileri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="47f53-162">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="47f53-163">Herhangi Blazor bir `[Inject]` özellik için çalıştırılan herhangi bir kurucu veya ayarlayıcı işlenmemiş bir özel durum attığında Sunucu devresi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="47f53-163">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="47f53-164">Çerçeve bileşeni anında atamadığı için özel durum ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-164">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="47f53-165">Oluşturucu mantığı özel durumlar oluşturabılırsa, uygulama hata işleme ve günlüğe kaydetme içeren bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi kullanarak özel durumları yakalamalıdır.</span><span class="sxs-lookup"><span data-stu-id="47f53-165">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="47f53-166">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="47f53-166">Lifecycle methods</span></span>

<span data-ttu-id="47f53-167">Bir bileşenin ömrü Blazor boyunca, aşağıdaki [yaşam döngüsü yöntemlerini](xref:blazor/lifecycle)çağırır:</span><span class="sxs-lookup"><span data-stu-id="47f53-167">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="47f53-168">Herhangi bir yaşam döngüsü yöntemi eşzamanlı veya eşzamanlı olarak bir özel durum atarsa, özel durum bir Blazor Sunucu devresi için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-168">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="47f53-169">Bileşenlerin yaşam döngüsü yöntemlerindeki hatalarla başa çıkabilmek için hata işleme mantığı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="47f53-169">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="47f53-170">Aşağıdaki örnekte, `OnParametersSetAsync` bir ürünü elde etmek için bir yöntem çağırır:</span><span class="sxs-lookup"><span data-stu-id="47f53-170">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="47f53-171">`ProductRepository.GetProductByIdAsync` Yöntemde atılan bir özel durum `try-catch` bir deyim tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="47f53-171">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="47f53-172">`catch` Blok yürütüldüğünde:</span><span class="sxs-lookup"><span data-stu-id="47f53-172">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="47f53-173">`loadFailed`kullanıcıya `true`bir hata iletisi görüntülemek için kullanılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="47f53-173">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="47f53-174">Hata günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-174">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="47f53-175">Oluşturma mantığı</span><span class="sxs-lookup"><span data-stu-id="47f53-175">Rendering logic</span></span>

<span data-ttu-id="47f53-176">Bileşen dosyasındaki `.razor` bildirimbiçimlendirmecif olarak adlandırılan `BuildRenderTree`c# yönteminde derlenir.</span><span class="sxs-lookup"><span data-stu-id="47f53-176">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="47f53-177">Bir bileşen, işlenen `BuildRenderTree` bileşenin öğelerini, metni ve alt bileşenlerini açıklayan bir veri yapısını işler, yürütür ve oluşturur.</span><span class="sxs-lookup"><span data-stu-id="47f53-177">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="47f53-178">Oluşturma mantığı bir özel durum atabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-178">Rendering logic can throw an exception.</span></span> <span data-ttu-id="47f53-179">Bu senaryonun bir örneği `@someObject.PropertyName` değerlendirildiğinde `@someObject` oluşur, ancak . `null`</span><span class="sxs-lookup"><span data-stu-id="47f53-179">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="47f53-180">Rendermantığı tarafından atılan işlenmemiş bir özel Blazor durum, bir Sunucu devresi için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-180">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="47f53-181">Oluşturma mantığında geçersiz bir başvuru özel durum `null` larını önlemek için, üyelerine erişmeden önce bir nesneyi denetleyin.</span><span class="sxs-lookup"><span data-stu-id="47f53-181">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="47f53-182">Aşağıdaki örnekte, `person.Address` aşağıdaki durumlarda özelliklere `person.Address` erişimez: `null`</span><span class="sxs-lookup"><span data-stu-id="47f53-182">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="47f53-183">Önceki kod, `person` `null`'.</span><span class="sxs-lookup"><span data-stu-id="47f53-183">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="47f53-184">Genellikle, kodun yapısı bileşenin işlendiği anda bir nesnenin var olduğunu garanti eder.</span><span class="sxs-lookup"><span data-stu-id="47f53-184">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="47f53-185">Bu gibi durumlarda, oluşturma mantığı `null` için kontrol etmek gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="47f53-185">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="47f53-186">Önceki örnekte, `person` bileşen anında oluşturulduğunda `person` oluşturulduğu için var olacağı garanti edilebilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-186">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="47f53-187">Olay işleyicileri</span><span class="sxs-lookup"><span data-stu-id="47f53-187">Event handlers</span></span>

<span data-ttu-id="47f53-188">İstemci tarafı kodu, olay işleyicileri kullanılarak oluşturulduğunda C# kodunun çağrılarını tetikler:</span><span class="sxs-lookup"><span data-stu-id="47f53-188">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="47f53-189">Diğer `@on...` öznitelikler</span><span class="sxs-lookup"><span data-stu-id="47f53-189">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="47f53-190">Olay işleyicisi kodu bu senaryolarda işlenmemiş bir özel durum atabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-190">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="47f53-191">Bir olay işleyicisi işlenmemiş bir özel durum atarsa (örneğin, bir Blazor veritabanı sorgusu başarısız olursa), özel durum bir Sunucu devresi için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-191">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="47f53-192">Uygulama dış nedenlerle başarısız olabilecek kodu çağırırsa, hata işleme ve günlüğe kaydetme içeren bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi kullanarak özel durumları yakalayın.</span><span class="sxs-lookup"><span data-stu-id="47f53-192">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="47f53-193">Kullanıcı kodu özel durumu tutup işlemiyorsa, çerçeve özel durumu kaydeder ve devreyi sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="47f53-193">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="47f53-194">Bileşen imha</span><span class="sxs-lookup"><span data-stu-id="47f53-194">Component disposal</span></span>

<span data-ttu-id="47f53-195">Kullanıcı başka bir sayfada gezinme yaptığı için, örneğin bir bileşen Kullanıcı Arabirimi'nden kaldırılabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-195">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="47f53-196">Uygulayan <xref:System.IDisposable?displayProperty=fullName> bir bileşen UI'den kaldırıldığında, çerçeve bileşenin <xref:System.IDisposable.Dispose*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="47f53-196">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="47f53-197">Bileşenin `Dispose` yöntemi işlenmemiş bir özel durum atarsa, özel Blazor durum bir Sunucu devresi için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-197">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="47f53-198">Elden çıkarma mantığı özel durumlar atayabı olabilirse, uygulama hata işleme ve günlüğe kaydetme içeren bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi kullanarak özel durumları yakalamalıdır.</span><span class="sxs-lookup"><span data-stu-id="47f53-198">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="47f53-199">Bileşen imhası hakkında daha <xref:blazor/lifecycle#component-disposal-with-idisposable>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="47f53-199">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="47f53-200">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="47f53-200">JavaScript interop</span></span>

<span data-ttu-id="47f53-201">`IJSRuntime.InvokeAsync<T>`.NET kodunun kullanıcının tarayıcısında JavaScript çalışma süresine eşzamanlı arama lar yapmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="47f53-201">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="47f53-202">Aşağıdaki koşullar aşağıdaki `InvokeAsync<T>`ile hata işleme için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="47f53-202">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="47f53-203">Bir çağrı `InvokeAsync<T>` eşzamanlı olarak başarısız olursa, bir .NET özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="47f53-203">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="47f53-204">Örneğin, `InvokeAsync<T>` sağlanan bağımsız değişkenler seri hale getirilemedığından, bir çağrı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-204">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="47f53-205">Geliştirici kodu özel durumu yakalamak gerekir.</span><span class="sxs-lookup"><span data-stu-id="47f53-205">Developer code must catch the exception.</span></span> <span data-ttu-id="47f53-206">Olay işleyicisi veya bileşen yaşam döngüsü yöntemindeki uygulama kodu bir özel durum Blazor işlemiyorsa, ortaya çıkan özel durum bir Sunucu devresi için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-206">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="47f53-207">Bir çağrı `InvokeAsync<T>` eşzamanlı olarak başarısız olursa, <xref:System.Threading.Tasks.Task> .NET başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="47f53-207">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="47f53-208">Örneğin, `InvokeAsync<T>` JavaScript yan kodu bir özel durum attığından veya 'da `Promise` `rejected`tamamlanmış bir özel durum döndürür' den bir çağrı başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-208">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="47f53-209">Geliştirici kodu özel durumu yakalamak gerekir.</span><span class="sxs-lookup"><span data-stu-id="47f53-209">Developer code must catch the exception.</span></span> <span data-ttu-id="47f53-210">[Bekleme](/dotnet/csharp/language-reference/keywords/await) işleci kullanıyorsanız, yöntem çağrısını hata işleme ve günlüğe kaydetme yle [deneme yakalama](/dotnet/csharp/language-reference/keywords/try-catch) deyiminde sarmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="47f53-210">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="47f53-211">Aksi takdirde, başarısız kod, bir Blazor Sunucu devresi için ölümcül olan işlenmemiş bir özel durumla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="47f53-211">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="47f53-212">Varsayılan olarak, `InvokeAsync<T>` çağrıların belirli bir süre içinde tamamlanması veya arama saatleri nin tamamlanması gerekir. Varsayılan zaman açıksırılım dönüşü</span><span class="sxs-lookup"><span data-stu-id="47f53-212">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="47f53-213">Zaman açıkışığı kodu, tamamlanma iletisi geri gününü asla geri gününüolmayan ağbağlantısı veya JavaScript kodundaki bir kayba karşı korur.</span><span class="sxs-lookup"><span data-stu-id="47f53-213">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="47f53-214">Arama zamanları dışarı çıkarsa, `Task` ortaya <xref:System.OperationCanceledException>çıkan bir .</span><span class="sxs-lookup"><span data-stu-id="47f53-214">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="47f53-215">Bindirme ve günlük ile özel durum işleme.</span><span class="sxs-lookup"><span data-stu-id="47f53-215">Trap and process the exception with logging.</span></span>

<span data-ttu-id="47f53-216">Benzer şekilde, JavaScript kodu öznitelik tarafından [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) belirtilen .NET yöntemlerine çağrı lar başlatabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-216">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="47f53-217">Bu .NET yöntemleri işlenmemiş bir özel durum atarsa:</span><span class="sxs-lookup"><span data-stu-id="47f53-217">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="47f53-218">Özel durum, bir Blazor Sunucu devresi için ölümcül olarak kabul edilmez.</span><span class="sxs-lookup"><span data-stu-id="47f53-218">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="47f53-219">JavaScript tarafı `Promise` reddedildi.</span><span class="sxs-lookup"><span data-stu-id="47f53-219">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="47f53-220">Yöntem çağrısının .NET tarafında veya JavaScript tarafında hata işleme kodunu kullanma seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="47f53-220">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="47f53-221">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="47f53-221">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="47f53-222">Sunucu önişleme</span><span class="sxs-lookup"><span data-stu-id="47f53-222"> Server prerendering</span></span>

Blazor<span data-ttu-id="47f53-223">[bileşenleri,](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) kullanıcının ilk HTTP isteğinin bir parçası olarak işlenen HTML biçimlendirmesinin döndürülecek şekilde Bileşen Etiket Yardımcısı kullanılarak önceden oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-223"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="47f53-224">Bu çalışır:</span><span class="sxs-lookup"><span data-stu-id="47f53-224">This works by:</span></span>

* <span data-ttu-id="47f53-225">Aynı sayfanın parçası olan önceden işlenmiş bileşenlerin tümü için yeni bir devre oluşturma.</span><span class="sxs-lookup"><span data-stu-id="47f53-225">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="47f53-226">İlk HTML oluşturma.</span><span class="sxs-lookup"><span data-stu-id="47f53-226">Generating the initial HTML.</span></span>
* <span data-ttu-id="47f53-227">Kullanıcının tarayıcısı `disconnected` aynı sunucuya bir SignalR bağlantı kurana kadar devreyi ele alır.</span><span class="sxs-lookup"><span data-stu-id="47f53-227">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="47f53-228">Bağlantı kurulduğunda, devredeki etkileşim devam eder ve bileşenlerin HTML biçimlendirmesi güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-228">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="47f53-229">Herhangi bir bileşen, örneğin, bir yaşam döngüsü yöntemi sırasında veya oluşturma mantığı nda, ön işleme sırasında işlenmemiş bir özel durum atarsa:</span><span class="sxs-lookup"><span data-stu-id="47f53-229">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="47f53-230">Özel durum devre için ölümcüldür.</span><span class="sxs-lookup"><span data-stu-id="47f53-230">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="47f53-231">Özel durum, Etiket Yardımcısı'ndan `Component` çağrı yığınına atılır.</span><span class="sxs-lookup"><span data-stu-id="47f53-231">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="47f53-232">Bu nedenle, özel durum geliştirici kodu tarafından açıkça yakalanmadığı sürece TÜM HTTP isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="47f53-232">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="47f53-233">Ön işleme başarısız olduğunda normal koşullar altında, çalışan bir bileşen oluşturulamadığından bileşeni oluşturmaya ve işlemeye devam etmek mantıklı değildir.</span><span class="sxs-lookup"><span data-stu-id="47f53-233">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="47f53-234">Ön işleme sırasında oluşabilecek hataları tolere etmek için, hata işleme mantığı özel durumlar atabilecek bir bileşenin içine yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="47f53-234">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="47f53-235">Hata işleme ve günlüğe kaydetme ile [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="47f53-235">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="47f53-236">Tag Helper'ı `Component` bir `try-catch` deyimde sarmak yerine, `Component` Tag Helper tarafından işlenen bileşene hata işleme mantığını yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="47f53-236">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="47f53-237">Gelişmiş senaryolar</span><span class="sxs-lookup"><span data-stu-id="47f53-237">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="47f53-238">Özyinelemeli görüntüleme</span><span class="sxs-lookup"><span data-stu-id="47f53-238">Recursive rendering</span></span>

<span data-ttu-id="47f53-239">Bileşenler özyinelemeli olarak iç içe olabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-239">Components can be nested recursively.</span></span> <span data-ttu-id="47f53-240">Bu, özyinelemeli veri yapılarını temsil etmek için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="47f53-240">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="47f53-241">Örneğin, bir `TreeNode` bileşen düğümün her bir çocuk için daha fazla `TreeNode` bileşen işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-241">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="47f53-242">Özyinelemeli olarak işleme yaparken, sonsuz özyinelemeyle sonuçlanan kodlama desenlerinden kaçının:</span><span class="sxs-lookup"><span data-stu-id="47f53-242">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="47f53-243">Döngü içeren bir veri yapısını özyinelemeli olarak işlemeyin.</span><span class="sxs-lookup"><span data-stu-id="47f53-243">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="47f53-244">Örneğin, çocukları kendisini içeren bir ağaç düğümü oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="47f53-244">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="47f53-245">Döngü içeren bir düzenler zinciri oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="47f53-245">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="47f53-246">Örneğin, düzeni kendisi olan bir düzen oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="47f53-246">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="47f53-247">Son kullanıcının kötü amaçlı veri girişi veya JavaScript interop çağrıları yoluyla özyineleme değişmezlerini (kuralları) ihlal etmesine izin vermeyin.</span><span class="sxs-lookup"><span data-stu-id="47f53-247">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="47f53-248">İşleme sırasında sonsuz döngüler:</span><span class="sxs-lookup"><span data-stu-id="47f53-248">Infinite loops during rendering:</span></span>

* <span data-ttu-id="47f53-249">İşleme işleminin sonsuza kadar devam etmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="47f53-249">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="47f53-250">Belirsiz bir döngü oluşturmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="47f53-250">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="47f53-251">Bu senaryolarda, etkilenen Blazor bir Sunucu devresi başarısız olur ve iş parçacığı genellikle aşağıdakileri yapmaya çalışır:</span><span class="sxs-lookup"><span data-stu-id="47f53-251">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="47f53-252">İşletim sisteminin izin verdiği kadar CPU süresini süresiz olarak tüketin.</span><span class="sxs-lookup"><span data-stu-id="47f53-252">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="47f53-253">Sınırsız miktarda sunucu belleği tüketin.</span><span class="sxs-lookup"><span data-stu-id="47f53-253">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="47f53-254">Sınırsız bellek tüketmek, sonlanmamış bir döngünün her yinelemede bir koleksiyona girişler eklediği senaryoya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="47f53-254">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="47f53-255">Sonsuz özyineleme desenlerinden kaçınmak için, özyinelemeli işleme kodunun uygun durdurma koşulları içerdiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="47f53-255">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="47f53-256">Özel render ağacı mantığı</span><span class="sxs-lookup"><span data-stu-id="47f53-256">Custom render tree logic</span></span>

<span data-ttu-id="47f53-257">Bileşenlerin çoğu Blazor *.razor* dosyaları olarak uygulanır ve çıktılarını işlemek `RenderTreeBuilder` için a üzerinde çalışan mantık üretmek için derlenir.</span><span class="sxs-lookup"><span data-stu-id="47f53-257">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="47f53-258">Bir geliştirici, yordamc# kodunu kullanarak mantığı el ile uygulayabilir. `RenderTreeBuilder`</span><span class="sxs-lookup"><span data-stu-id="47f53-258">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="47f53-259">Daha fazla bilgi için bkz. <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="47f53-259">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="47f53-260">El ile render ağacı oluşturucu mantığının kullanımı, genel bileşen geliştirme için önerilmez gelişmiş ve güvenli olmayan bir senaryo olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-260">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="47f53-261">`RenderTreeBuilder` Kod yazılmışsa, geliştirici kodun doğruluğunu garanti etmelidir.</span><span class="sxs-lookup"><span data-stu-id="47f53-261">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="47f53-262">Örneğin, geliştirici aşağıdakileri sağlamalıdır:</span><span class="sxs-lookup"><span data-stu-id="47f53-262">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="47f53-263">Aramalar `OpenElement` ve `CloseElement` doğru dengelenir.</span><span class="sxs-lookup"><span data-stu-id="47f53-263">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="47f53-264">Öznitelikler yalnızca doğru yerlerde eklenir.</span><span class="sxs-lookup"><span data-stu-id="47f53-264">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="47f53-265">Yanlış el ile işoluşturma ağacı oluşturucu mantığı, kilitlenmeler, sunucu askıları ve güvenlik açıkları gibi rasgele tanımlanmamış davranışlara neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="47f53-265">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="47f53-266">El ile ağaç oluşturucu mantığını aynı karmaşıklık düzeyinde ve montaj kodu veya MSIL yönergelerini elle yazmakla aynı *tehlike* düzeyinde düşünün.</span><span class="sxs-lookup"><span data-stu-id="47f53-266">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
