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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: f791dae5915c87d4c36f23419961e3c53e888743
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409078"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="255e1-103">İlk uygulamanızı oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="255e1-103">Build your first Blazor app</span></span>

<span data-ttu-id="255e1-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="255e1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="255e1-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="255e1-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="255e1-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="255e1-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="255e1-107">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="255e1-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="255e1-108">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="255e1-108">Modify Razor components</span></span>
> * <span data-ttu-id="255e1-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="255e1-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="255e1-110">Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="255e1-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="255e1-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="255e1-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="255e1-112">Derleme bileşenleri</span><span class="sxs-lookup"><span data-stu-id="255e1-112">Build components</span></span>

1. <span data-ttu-id="255e1-113"><xref:blazor/get-started>Bu öğretici için bir proje oluşturmak üzere makalesindeki yönergeleri izleyin Blazor .</span><span class="sxs-lookup"><span data-stu-id="255e1-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="255e1-114">Projeyi adlandırın `ToDoList` .</span><span class="sxs-lookup"><span data-stu-id="255e1-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="255e1-115">Şu klasördeki her bir uygulamanın üç sayfasına gidin `Pages` : `Home` , `Counter` ve `Fetch data` .</span><span class="sxs-lookup"><span data-stu-id="255e1-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="255e1-116">Bu sayfalar, Razor ve bileşen dosyaları tarafından uygulanır `Index.razor` `Counter.razor` `FetchData.razor` .</span><span class="sxs-lookup"><span data-stu-id="255e1-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="255e1-117">Sayfada, `Counter` Sayfa yenileme olmadan sayacı artırmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="255e1-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="255e1-118">Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="255e1-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="255e1-119">İle Blazor bunun yerine C# yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="255e1-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="255e1-120">`Counter`Dosyadaki bileşen uygulamasını inceleyin `Counter.razor` .</span><span class="sxs-lookup"><span data-stu-id="255e1-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="255e1-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="255e1-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="255e1-122">Bileşenin kullanıcı arabirimi `Counter` HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="255e1-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="255e1-123">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="255e1-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="255e1-124">HTML biçimlendirme ve C# işleme mantığı, derleme zamanında bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="255e1-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="255e1-125">Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="255e1-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="255e1-126">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="255e1-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="255e1-127">`@code`Bloğunda, bileşen durumu (özellikler, alanlar) ve yöntemler olay işleme için veya diğer bileşen mantığını tanımlamak için belirtilir.</span><span class="sxs-lookup"><span data-stu-id="255e1-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="255e1-128">Bu Üyeler daha sonra bileşenin işleme mantığının bir parçası olarak ve olayları işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="255e1-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="255e1-129">Sayaç artışı düğmesi seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="255e1-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="255e1-130">`Counter`Bileşenin kayıtlı `onclick` işleyicisine ( `IncrementCount` yöntemi) denir.</span><span class="sxs-lookup"><span data-stu-id="255e1-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="255e1-131">`Counter`Bileşen, işleme ağacını yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="255e1-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="255e1-132">Yeni işleme ağacı öncekiyle karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="255e1-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="255e1-133">Yalnızca Belge Nesne Modeli (DOM) üzerinde yapılan değişiklikler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="255e1-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="255e1-134">Görünen sayı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="255e1-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="255e1-135">`Counter`Sayıyı bir yerine iki ile artırmak için bileşenin C# mantığını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="255e1-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="255e1-136">Değişiklikleri görmek için uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="255e1-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="255e1-137">Düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="255e1-137">Select the button.</span></span> <span data-ttu-id="255e1-138">Sayaç iki olarak artar.</span><span class="sxs-lookup"><span data-stu-id="255e1-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="255e1-139">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="255e1-139">Use components</span></span>

<span data-ttu-id="255e1-140">Bir bileşeni, bir HTML söz dizimini kullanarak başka bir bileşene ekleyin.</span><span class="sxs-lookup"><span data-stu-id="255e1-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="255e1-141">Bileşene `Counter` `Index` bir öğe ekleyerek bileşeni uygulamanın bileşenine ekleyin `<Counter />` `Index` ( `Index.razor` ).</span><span class="sxs-lookup"><span data-stu-id="255e1-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="255e1-142">Blazor WebAssemblyBu deneyim için kullanıyorsanız, bileşen `SurveyPrompt` tarafından bir bileşen kullanılır `Index` .</span><span class="sxs-lookup"><span data-stu-id="255e1-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="255e1-143">`<SurveyPrompt>`Öğesini bir `<Counter />` öğesiyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="255e1-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="255e1-144">Blazor ServerBu deneyim için bir uygulama kullanıyorsanız, `<Counter />` öğesini `Index` bileşene ekleyin:</span><span class="sxs-lookup"><span data-stu-id="255e1-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="255e1-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="255e1-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="255e1-146">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="255e1-146">Rebuild and run the app.</span></span> <span data-ttu-id="255e1-147">`Index`Bileşenin kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="255e1-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="255e1-148">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="255e1-148">Component parameters</span></span>

<span data-ttu-id="255e1-149">Bileşenler de parametrelere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="255e1-149">Components can also have parameters.</span></span> <span data-ttu-id="255e1-150">Bileşen parametreleri, bileşen sınıfında özniteliğiyle birlikte ortak özellikler kullanılarak tanımlanır [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="255e1-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="255e1-151">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="255e1-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="255e1-152">Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="255e1-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="255e1-153">`IncrementAmount`Özniteliği ile ortak bir özellik ekleyin [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .</span><span class="sxs-lookup"><span data-stu-id="255e1-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="255e1-154">`IncrementCount` `IncrementAmount` Değerini artırdığınızda özelliğini kullanmak için yöntemini değiştirin `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="255e1-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="255e1-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="255e1-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="255e1-156">`IncrementAmount` `Index` Öznitelik kullanarak bileşenin öğesinde bir parametre belirtin `<Counter>` .</span><span class="sxs-lookup"><span data-stu-id="255e1-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="255e1-157">Sayacı on olarak artırmak için değeri ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="255e1-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="255e1-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="255e1-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="255e1-159">Bileşeni yeniden yükleyin `Index` .</span><span class="sxs-lookup"><span data-stu-id="255e1-159">Reload the `Index` component.</span></span> <span data-ttu-id="255e1-160">Her düğme seçildiğinde sayaç on olarak artar.</span><span class="sxs-lookup"><span data-stu-id="255e1-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="255e1-161">`Counter`Bileşendeki sayaç bir artırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="255e1-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="255e1-162">Bileşenlere yönlendir</span><span class="sxs-lookup"><span data-stu-id="255e1-162">Route to components</span></span>

<span data-ttu-id="255e1-163">`@page`Dosyanın en üstündeki yönerge `Counter.razor` , `Counter` bileşenin bir yönlendirme uç noktası olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="255e1-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="255e1-164">`Counter`Bileşen öğesine gönderilen istekleri işler `/counter` .</span><span class="sxs-lookup"><span data-stu-id="255e1-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="255e1-165">Yönerge olmadan `@page` , bileşen yönlendirilmiş istekleri işlemez, ancak bileşen diğer bileşenler tarafından hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="255e1-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="255e1-166">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="255e1-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor Server<span data-ttu-id="255e1-167">deneyimleri</span><span class="sxs-lookup"><span data-stu-id="255e1-167"> experience</span></span>

<span data-ttu-id="255e1-168">Bir uygulamayla birlikte çalışıyorsa Blazor Server , `WeatherForecastService` hizmet [tek](xref:fundamentals/dependency-injection#service-lifetimes) bir olarak kaydedilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="255e1-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="255e1-169">Uygulamanın tamamında [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)yoluyla hizmetin bir örneği mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="255e1-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="255e1-170">[`@inject`](xref:mvc/views/razor#inject)Yönergesi, hizmet örneğini bileşene eklemek için kullanılır `WeatherForecastService` `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="255e1-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="255e1-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="255e1-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="255e1-172">`FetchData`Bileşen, `ForecastService` nesne dizisini almak için olarak eklenen hizmeti kullanır `WeatherForecast` :</span><span class="sxs-lookup"><span data-stu-id="255e1-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor WebAssembly<span data-ttu-id="255e1-173">deneyimleri</span><span class="sxs-lookup"><span data-stu-id="255e1-173"> experience</span></span>

<span data-ttu-id="255e1-174">Bir uygulamayla birlikte çalışıyorsa Blazor WebAssembly , <xref:System.Net.Http.HttpClient> klasördeki dosyadan Hava durumu tahmin verileri almak için eklenmiş olur `weather.json` `wwwroot/sample-data` .</span><span class="sxs-lookup"><span data-stu-id="255e1-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="255e1-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="255e1-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="255e1-176">Bir [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngü, her tahmin örneğini Hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="255e1-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="255e1-177">Yapılacaklar listesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="255e1-177">Build a todo list</span></span>

<span data-ttu-id="255e1-178">Uygulamaya basit bir yapılacaklar listesi uygulayan yeni bir bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="255e1-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="255e1-179">`Todo` Razor Klasördeki uygulamaya yeni bir bileşen ekleyin `Pages` .</span><span class="sxs-lookup"><span data-stu-id="255e1-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="255e1-180">Visual Studio kullanıyorsanız, klasöre sağ tıklayın `Pages` ve **Add**  >  **Yeni öğe** Ekle  >  \*\* Razor bileşeni\*\*' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="255e1-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="255e1-181">Bileşenin dosyasını adlandırın `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="255e1-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="255e1-182">Diğer geliştirme ortamlarında adlı klasöre boş bir dosya ekleyin `Pages` `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="255e1-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> Razor<span data-ttu-id="255e1-183">bileşen dosyası adları, büyük harfle bir ilk harf gerektirir, bu nedenle `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .</span><span class="sxs-lookup"><span data-stu-id="255e1-183"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="255e1-184">Bileşen için ilk biçimlendirmeyi belirtin:</span><span class="sxs-lookup"><span data-stu-id="255e1-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="255e1-185">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="255e1-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="255e1-186">`NavMenu`Bileşen ( `Shared/NavMenu.razor` ) uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="255e1-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="255e1-187">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="255e1-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="255e1-188">`<NavLink>` `Todo` Aşağıdaki liste öğesi işaretlemesini, dosyadaki var olan liste öğelerinin altına ekleyerek bileşen için bir öğe ekleyin `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="255e1-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="255e1-189">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="255e1-189">Rebuild and run the app.</span></span> <span data-ttu-id="255e1-190">Bileşen bağlantısının çalıştığından emin olmak için yeni Todo sayfasını ziyaret edin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="255e1-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="255e1-191">`TodoItem.cs`Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="255e1-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="255e1-192">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="255e1-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="255e1-193">Bileşene geri dön `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="255e1-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="255e1-194">Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="255e1-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="255e1-195">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="255e1-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="255e1-196">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="255e1-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="255e1-197">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="255e1-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="255e1-198">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="255e1-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="255e1-199">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="255e1-199">Rebuild and run the app.</span></span> <span data-ttu-id="255e1-200">**`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="255e1-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="255e1-201">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="255e1-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="255e1-202">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="255e1-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="255e1-203">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :</span><span class="sxs-lookup"><span data-stu-id="255e1-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="255e1-204">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="255e1-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="255e1-205">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="255e1-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="255e1-206">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="255e1-206">Rebuild and run the app.</span></span> <span data-ttu-id="255e1-207">Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="255e1-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="255e1-208">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="255e1-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="255e1-209">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="255e1-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="255e1-210">Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="255e1-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="255e1-211">Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).</span><span class="sxs-lookup"><span data-stu-id="255e1-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="255e1-212">Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="255e1-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="255e1-213">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="255e1-213">Rebuild and run the app.</span></span> <span data-ttu-id="255e1-214">Yeni kodu test etmek için Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="255e1-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="255e1-215">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="255e1-215">Next steps</span></span>

<span data-ttu-id="255e1-216">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="255e1-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="255e1-217">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="255e1-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="255e1-218">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="255e1-218">Modify Razor components</span></span>
> * <span data-ttu-id="255e1-219">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="255e1-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="255e1-220">Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="255e1-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="255e1-221">Bileşenleri derlemeyi ve kullanmayı öğrenin:</span><span class="sxs-lookup"><span data-stu-id="255e1-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
