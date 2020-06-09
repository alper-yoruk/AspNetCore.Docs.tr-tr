---
title: İlk uygulamanızı oluşturma Blazor
author: guardrex
description: Bir Blazor uygulamayı adım adım oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: ce3f20f7ee4ccfa73afc5f80a4429d9f4fe05591
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507263"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="ee98f-103">İlk uygulamanızı oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="ee98f-103">Build your first Blazor app</span></span>

<span data-ttu-id="ee98f-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="ee98f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ee98f-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="ee98f-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="ee98f-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ee98f-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ee98f-107">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="ee98f-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ee98f-108">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="ee98f-108">Modify Razor components</span></span>
> * <span data-ttu-id="ee98f-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="ee98f-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ee98f-110">Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="ee98f-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="ee98f-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="ee98f-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="ee98f-112">Derleme bileşenleri</span><span class="sxs-lookup"><span data-stu-id="ee98f-112">Build components</span></span>

1. <span data-ttu-id="ee98f-113"><xref:blazor/get-started>Bu öğretici için bir proje oluşturmak üzere makalesindeki yönergeleri izleyin Blazor .</span><span class="sxs-lookup"><span data-stu-id="ee98f-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="ee98f-114">Projeyi *ToDoList*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-114">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="ee98f-115">*Sayfalar* klasöründe uygulamanın üç sayfasının her birine gidin: giriş, sayaç ve veri getirme.</span><span class="sxs-lookup"><span data-stu-id="ee98f-115">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="ee98f-116">Bu sayfalar, Razor *Dizin. Razor*, *Counter. Razor*ve *fetchdata. Razor*bileşen dosyaları tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-116">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="ee98f-117">Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-117">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ee98f-118">Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="ee98f-119">İle Blazor bunun yerine C# yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ee98f-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="ee98f-120">`Counter` *Counter. Razor* dosyasındaki bileşenin uygulamasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-120">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="ee98f-121">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee98f-121">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="ee98f-122">Bileşenin kullanıcı arabirimi `Counter` HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="ee98f-123">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="ee98f-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="ee98f-124">HTML biçimlendirme ve C# işleme mantığı, derleme zamanında bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="ee98f-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="ee98f-125">Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="ee98f-126">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="ee98f-127">`@code`Bloğunda, bileşen durumu (özellikler, alanlar) ve yöntemler olay işleme için veya diğer bileşen mantığını tanımlamak için belirtilir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="ee98f-128">Bu Üyeler daha sonra bileşenin işleme mantığının bir parçası olarak ve olayları işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="ee98f-129">**Bana tıklama** düğmesi seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="ee98f-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="ee98f-130">`Counter`Bileşenin kayıtlı `onclick` işleyicisine ( `IncrementCount` yöntemi) denir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="ee98f-131">`Counter`Bileşen, işleme ağacını yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ee98f-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="ee98f-132">Yeni işleme ağacı öncekiyle karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="ee98f-133">Yalnızca Belge Nesne Modeli (DOM) üzerinde yapılan değişiklikler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="ee98f-134">Görünen sayı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="ee98f-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="ee98f-135">`Counter`Sayıyı bir yerine iki ile artırmak için bileşenin C# mantığını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="ee98f-136">Değişiklikleri görmek için uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="ee98f-137">**Ben tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-137">Select the **Click me** button.</span></span> <span data-ttu-id="ee98f-138">Sayaç iki olarak artar.</span><span class="sxs-lookup"><span data-stu-id="ee98f-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="ee98f-139">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="ee98f-139">Use components</span></span>

<span data-ttu-id="ee98f-140">Bir bileşeni, bir HTML söz dizimini kullanarak başka bir bileşene ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="ee98f-141">Bileşene `Counter` `Index` bir öğe ekleyerek bileşeni uygulamanın bileşenine ekleyin `<Counter />` `Index` (*Index. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ee98f-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="ee98f-142">BlazorBu deneyim için webassembly kullanıyorsanız bileşen `SurveyPrompt` tarafından bir bileşen kullanılır `Index` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="ee98f-143">`<SurveyPrompt>`Öğesini bir `<Counter />` öğesiyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="ee98f-144">BlazorBu deneyim için bir sunucu uygulaması kullanıyorsanız, `<Counter />` öğesini `Index` bileşene ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ee98f-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="ee98f-145">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee98f-145">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="ee98f-146">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-146">Rebuild and run the app.</span></span> <span data-ttu-id="ee98f-147">`Index`Bileşenin kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="ee98f-148">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="ee98f-148">Component parameters</span></span>

<span data-ttu-id="ee98f-149">Bileşenler de parametrelere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-149">Components can also have parameters.</span></span> <span data-ttu-id="ee98f-150">Bileşen parametreleri, bileşen sınıfında özniteliğiyle birlikte ortak özellikler kullanılarak tanımlanır [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="ee98f-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="ee98f-151">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="ee98f-152">Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ee98f-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="ee98f-153">`IncrementAmount`Özniteliği ile ortak bir özellik ekleyin [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="ee98f-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="ee98f-154">`IncrementCount` `IncrementAmount` Değerini artırdığınızda özelliğini kullanmak için yöntemini değiştirin `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="ee98f-155">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee98f-155">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="ee98f-156">`IncrementAmount` `Index` Öznitelik kullanarak bileşenin öğesinde bir parametre belirtin `<Counter>` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="ee98f-157">Sayacı on olarak artırmak için değeri ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="ee98f-158">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee98f-158">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="ee98f-159">Bileşeni yeniden yükleyin `Index` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-159">Reload the `Index` component.</span></span> <span data-ttu-id="ee98f-160">**Beni tıklama** düğmesi seçildiğinde sayaç on bir kez artar.</span><span class="sxs-lookup"><span data-stu-id="ee98f-160">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ee98f-161">`Counter`Bileşendeki sayaç bir artırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="ee98f-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="ee98f-162">Bileşenlere yönlendir</span><span class="sxs-lookup"><span data-stu-id="ee98f-162">Route to components</span></span>

<span data-ttu-id="ee98f-163">`@page` *Counter. Razor* dosyasının en üstündeki yönerge, `Counter` bileşenin bir yönlendirme uç noktası olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-163">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="ee98f-164">`Counter`Bileşen öğesine gönderilen istekleri işler `/counter` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="ee98f-165">Yönerge olmadan `@page` , bileşen yönlendirilmiş istekleri işlemez, ancak bileşen diğer bileşenler tarafından hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="ee98f-166">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="ee98f-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="ee98f-167">Sunucu deneyimi</span><span class="sxs-lookup"><span data-stu-id="ee98f-167"> Server experience</span></span>

<span data-ttu-id="ee98f-168">Sunucu uygulamasıyla çalışıyorsanız Blazor , `WeatherForecastService` hizmet [tek](xref:fundamentals/dependency-injection#service-lifetimes) bir olarak kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee98f-169">Uygulamanın tamamında [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)yoluyla hizmetin bir örneği mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="ee98f-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="ee98f-170">[`@inject`](xref:mvc/views/razor#inject)Yönergesi, hizmet örneğini bileşene eklemek için kullanılır `WeatherForecastService` `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="ee98f-171">*Pages/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee98f-171">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="ee98f-172">`FetchData`Bileşen, `ForecastService` nesne dizisini almak için olarak eklenen hizmeti kullanır `WeatherForecast` :</span><span class="sxs-lookup"><span data-stu-id="ee98f-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="ee98f-173">WebAssembly deneyimi</span><span class="sxs-lookup"><span data-stu-id="ee98f-173"> WebAssembly experience</span></span>

<span data-ttu-id="ee98f-174">BlazorWebassembly uygulamasıyla çalışıyorsanız, <xref:System.Net.Http.HttpClient> *Wwwroot/Sample-Data* klasöründeki *Hava durumu. JSON* dosyasından Hava durumu tahmin verileri almak için eklenir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="ee98f-175">*Pages/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ee98f-175">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="ee98f-176">Bir [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngü, her tahmin örneğini Hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ee98f-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="ee98f-177">Yapılacaklar listesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="ee98f-177">Build a todo list</span></span>

<span data-ttu-id="ee98f-178">Uygulamaya basit bir yapılacaklar listesi uygulayan yeni bir bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="ee98f-179">`Todo` Razor *Sayfalar* klasöründe uygulamaya yeni bir bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-179">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="ee98f-180">Visual Studio kullanıyorsanız, **Sayfalar** klasörüne sağ tıklayın ve **Add**  >  **Yeni öğe** Ekle  >  \*\* Razor bileşeni\*\*' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-180">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="ee98f-181">Bileşenin dosya *Todo. Razor*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-181">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="ee98f-182">Diğer geliştirme ortamlarında, *Todo. Razor*adlı **Sayfalar** klasörüne boş bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-182">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="ee98f-183">Bileşen için ilk biçimlendirmeyi belirtin:</span><span class="sxs-lookup"><span data-stu-id="ee98f-183">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="ee98f-184">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-184">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="ee98f-185">`NavMenu`Bileşen (*Shared/navmenu. Razor*) uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-185">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="ee98f-186">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-186">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="ee98f-187">`<NavLink>` `Todo` *Paylaşılan/navmenu. Razor* dosyasındaki mevcut liste öğelerinin altına aşağıdaki liste öğesi işaretlemesini ekleyerek bileşen için bir öğe ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ee98f-187">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="ee98f-188">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-188">Rebuild and run the app.</span></span> <span data-ttu-id="ee98f-189">Bileşen bağlantısının çalıştığından emin olmak için yeni Todo sayfasını ziyaret edin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-189">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="ee98f-190">Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir *TodoItem.cs* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-190">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="ee98f-191">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="ee98f-191">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="ee98f-192">Bileşene geri dön `Todo` (*Pages/Todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ee98f-192">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="ee98f-193">Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-193">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="ee98f-194">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="ee98f-194">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="ee98f-195">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-195">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="ee98f-196">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-196">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="ee98f-197">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="ee98f-197">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="ee98f-198">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-198">Rebuild and run the app.</span></span> <span data-ttu-id="ee98f-199">**Todo Ekle** düğmesi seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="ee98f-199">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="ee98f-200">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-200">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="ee98f-201">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="ee98f-201">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="ee98f-202">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :</span><span class="sxs-lookup"><span data-stu-id="ee98f-202">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="ee98f-203">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="ee98f-203">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="ee98f-204">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="ee98f-204">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="ee98f-205">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-205">Rebuild and run the app.</span></span> <span data-ttu-id="ee98f-206">Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-206">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="ee98f-207">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="ee98f-207">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="ee98f-208">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-208">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="ee98f-209">Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="ee98f-209">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="ee98f-210">Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).</span><span class="sxs-lookup"><span data-stu-id="ee98f-210">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="ee98f-211">Tamamlanan `Todo` bileşen (*sayfa/Todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ee98f-211">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="ee98f-212">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ee98f-212">Rebuild and run the app.</span></span> <span data-ttu-id="ee98f-213">Yeni kodu test etmek için Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ee98f-213">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ee98f-214">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="ee98f-214">Next steps</span></span>

<span data-ttu-id="ee98f-215">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="ee98f-215">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ee98f-216">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="ee98f-216">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ee98f-217">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="ee98f-217">Modify Razor components</span></span>
> * <span data-ttu-id="ee98f-218">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="ee98f-218">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ee98f-219">Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="ee98f-219">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="ee98f-220">Bileşenleri derlemeyi ve kullanmayı öğrenin:</span><span class="sxs-lookup"><span data-stu-id="ee98f-220">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
