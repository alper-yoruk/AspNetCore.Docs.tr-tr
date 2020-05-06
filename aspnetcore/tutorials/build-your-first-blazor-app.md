---
title: İlk Blazor uygulamanızı oluşturma
author: guardrex
description: Bir Blazor uygulamayı adım adım oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 5a5a56ee646cba21a883df2cf686cb1ccb18d7f9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776610"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="1b456-103">İlk Blazor uygulamanızı oluşturma</span><span class="sxs-lookup"><span data-stu-id="1b456-103">Build your first Blazor app</span></span>

<span data-ttu-id="1b456-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="1b456-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1b456-105">Bu öğreticide, bir Blazor uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="1b456-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="1b456-106">Derleme bileşenleri</span><span class="sxs-lookup"><span data-stu-id="1b456-106">Build components</span></span>

1. <span data-ttu-id="1b456-107">Bu öğretici için bir <xref:blazor/get-started> Blazor proje oluşturmak üzere makalesindeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="1b456-108">Projeyi *ToDoList*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="1b456-109">*Sayfalar* klasöründe uygulamanın üç sayfasının her birine gidin: giriş, sayaç ve veri getirme.</span><span class="sxs-lookup"><span data-stu-id="1b456-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="1b456-110">Bu sayfalar, Razor *Dizin. Razor*, *Counter. Razor*ve *fetchdata. Razor*bileşen dosyaları tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1b456-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="1b456-111">Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1b456-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="1b456-112">Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1b456-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="1b456-113">İle Blazorbunun yerine C# yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1b456-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="1b456-114">`Counter` *Counter. Razor* dosyasındaki bileşenin uygulamasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="1b456-115">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1b456-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="1b456-116">`Counter` Bileşenin kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1b456-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="1b456-117">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı [Razor](xref:mvc/views/razor)gömülü C# sözdizimi kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="1b456-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="1b456-118">HTML biçimlendirme ve C# işleme mantığı, derleme zamanında bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="1b456-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="1b456-119">Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1b456-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="1b456-120">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1b456-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="1b456-121">`@code` Bloğunda, bileşen durumu (özellikler, alanlar) ve yöntemler olay işleme için veya diğer bileşen mantığını tanımlamak için belirtilir.</span><span class="sxs-lookup"><span data-stu-id="1b456-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="1b456-122">Bu Üyeler daha sonra bileşenin işleme mantığının bir parçası olarak ve olayları işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1b456-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="1b456-123">**Bana tıklama** düğmesi seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="1b456-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="1b456-124">`Counter` Bileşenin kayıtlı `onclick` işleyicisine ( `IncrementCount` yöntemi) denir.</span><span class="sxs-lookup"><span data-stu-id="1b456-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="1b456-125">`Counter` Bileşen, işleme ağacını yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1b456-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="1b456-126">Yeni işleme ağacı öncekiyle karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="1b456-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="1b456-127">Yalnızca Belge Nesne Modeli (DOM) üzerinde yapılan değişiklikler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1b456-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="1b456-128">Görünen sayı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="1b456-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="1b456-129">Sayıyı bir yerine iki ile artırmak `Counter` Için bileşenin C# mantığını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1b456-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="1b456-130">Değişiklikleri görmek için uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="1b456-131">**Ben tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1b456-131">Select the **Click me** button.</span></span> <span data-ttu-id="1b456-132">Sayaç iki olarak artar.</span><span class="sxs-lookup"><span data-stu-id="1b456-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="1b456-133">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="1b456-133">Use components</span></span>

<span data-ttu-id="1b456-134">Bir bileşeni, bir HTML söz dizimini kullanarak başka bir bileşene ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="1b456-135">Bileşene bir `Counter` `Index` `<Counter />` öğe `Index` ekleyerek bileşeni uygulamanın bileşenine ekleyin (*Index. Razor*).</span><span class="sxs-lookup"><span data-stu-id="1b456-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="1b456-136">Bu deneyim için webassembly kullanıyorsanız Blazor bileşen tarafından `SurveyPrompt` `Index` bir bileşen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1b456-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="1b456-137">`<SurveyPrompt>` Öğesini bir `<Counter />` öğesiyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1b456-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="1b456-138">Bu deneyim için bir Blazor sunucu uygulaması kullanıyorsanız, `<Counter />` öğesini `Index` bileşene ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1b456-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="1b456-139">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1b456-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="1b456-140">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-140">Rebuild and run the app.</span></span> <span data-ttu-id="1b456-141">`Index` Bileşenin kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="1b456-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="1b456-142">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="1b456-142">Component parameters</span></span>

<span data-ttu-id="1b456-143">Bileşenler de parametrelere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="1b456-143">Components can also have parameters.</span></span> <span data-ttu-id="1b456-144">Bileşen parametreleri, bileşen sınıfında `[Parameter]` özniteliğiyle birlikte ortak özellikler kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1b456-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="1b456-145">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="1b456-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="1b456-146">Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1b456-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="1b456-147">`[Parameter]` Özniteliği ile ortak `IncrementAmount` bir özellik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="1b456-148">Değerini artırdığınızda `IncrementCount` `IncrementAmount` özelliğini kullanmak için yöntemini değiştirin `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="1b456-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="1b456-149">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1b456-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="1b456-150">Öznitelik kullanarak `IncrementAmount` `Index` bileşenin `<Counter>` öğesinde bir parametre belirtin.</span><span class="sxs-lookup"><span data-stu-id="1b456-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="1b456-151">Sayacı on olarak artırmak için değeri ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1b456-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="1b456-152">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1b456-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="1b456-153">`Index` Bileşeni yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-153">Reload the `Index` component.</span></span> <span data-ttu-id="1b456-154">**Beni tıklama** düğmesi seçildiğinde sayaç on bir kez artar.</span><span class="sxs-lookup"><span data-stu-id="1b456-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="1b456-155">`Counter` Bileşendeki sayaç bir artırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="1b456-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="1b456-156">Bileşenlere yönlendir</span><span class="sxs-lookup"><span data-stu-id="1b456-156">Route to components</span></span>

<span data-ttu-id="1b456-157">*Counter. Razor* dosyasının en üstündeki `Counter` `@page` yönerge, bileşenin bir yönlendirme uç noktası olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="1b456-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="1b456-158">`Counter` Bileşen öğesine `/counter`gönderilen istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="1b456-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="1b456-159">`@page` Yönerge olmadan, bileşen yönlendirilmiş istekleri işlemez, ancak bileşen diğer bileşenler tarafından hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1b456-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="1b456-160">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="1b456-160">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="1b456-161">Sunucu deneyimi</span><span class="sxs-lookup"><span data-stu-id="1b456-161"> Server experience</span></span>

<span data-ttu-id="1b456-162">Blazor Sunucu uygulamasıyla çalışıyorsanız, `WeatherForecastService` hizmet [tek](xref:fundamentals/dependency-injection#service-lifetimes) bir olarak kaydedilir `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1b456-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1b456-163">Uygulamanın tamamında [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)yoluyla hizmetin bir örneği mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="1b456-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="1b456-164">`@inject` Yönergesi, `WeatherForecastService` hizmet örneğini `FetchData` bileşene eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1b456-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="1b456-165">*Pages/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1b456-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="1b456-166">`FetchData` Bileşen, `WeatherForecast` nesne dizisini almak için olarak `ForecastService`eklenen hizmeti kullanır:</span><span class="sxs-lookup"><span data-stu-id="1b456-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="1b456-167">WebAssembly deneyimi</span><span class="sxs-lookup"><span data-stu-id="1b456-167"> WebAssembly experience</span></span>

<span data-ttu-id="1b456-168">Blazor Webassembly uygulamasıyla çalışıyorsanız `HttpClient` , *Wwwroot/Sample-Data* klasöründeki *Hava durumu. JSON* dosyasından Hava durumu tahmin verileri almak için eklenir.</span><span class="sxs-lookup"><span data-stu-id="1b456-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="1b456-169">*Pages/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1b456-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="1b456-170">Bir [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngü, her tahmin örneğini Hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1b456-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="1b456-171">Yapılacaklar listesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1b456-171">Build a todo list</span></span>

<span data-ttu-id="1b456-172">Uygulamaya basit bir yapılacaklar listesi uygulayan yeni bir bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="1b456-173">*Sayfalar* klasöründe uygulamaya `Todo` Razor yeni bir bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="1b456-174">Visual Studio 'da **Sayfalar** klasörüne sağ tıklayın ve**Yeni öğe**  >  \*\* Razor \*\* **Ekle** > bileşeni ' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="1b456-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="1b456-175">Bileşenin dosya *Todo. Razor*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="1b456-176">Diğer geliştirme ortamlarında, *Todo. Razor*adlı **Sayfalar** klasörüne boş bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="1b456-177">Bileşen için ilk biçimlendirmeyi belirtin:</span><span class="sxs-lookup"><span data-stu-id="1b456-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="1b456-178">`Todo` Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="1b456-179">`NavMenu` Bileşen (*Shared/navmenu. Razor*) uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1b456-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="1b456-180">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="1b456-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="1b456-181">`<NavLink>` *Paylaşılan/navmenu. Razor* dosyasındaki mevcut liste öğelerinin altına aşağıdaki liste öğesi işaretlemesini ekleyerek `Todo` bileşen için bir öğe ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1b456-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="1b456-182">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-182">Rebuild and run the app.</span></span> <span data-ttu-id="1b456-183">`Todo` Bileşen bağlantısının çalıştığından emin olmak Için yeni Todo sayfasını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="1b456-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="1b456-184">Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir *TodoItem.cs* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="1b456-185">`TodoItem` Sınıfı Için aşağıdaki C# kodunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="1b456-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="1b456-186">`Todo` Bileşene geri dön (*Pages/Todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1b456-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="1b456-187">Bir `@code` bloktaki Todo öğeleri için bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="1b456-188">`Todo` Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b456-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="1b456-189">Her Todo öğesini bir liste öğesi `foreach` (`<li>`) olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="1b456-190">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1b456-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="1b456-191">Sıralanmamış listenin`<ul>...</ul>`() altına`<input>`bir metin girişi ()`<button>`ve bir düğme () ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1b456-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="1b456-192">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-192">Rebuild and run the app.</span></span> <span data-ttu-id="1b456-193">**Todo Ekle** düğmesi seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="1b456-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="1b456-194">Bileşene bir `AddTodo` yöntem ekleyin ve `@onclick` özniteliği kullanarak düğme seçimleri için kaydedin. `Todo`</span><span class="sxs-lookup"><span data-stu-id="1b456-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="1b456-195">`AddTodo` C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="1b456-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="1b456-196">Yeni Todo öğesinin başlığını `newTodo` almak için, `@code` bloğunun üst kısmına bir dize alanı ekleyin ve bunu, `bind` `<input>` öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın:</span><span class="sxs-lookup"><span data-stu-id="1b456-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="1b456-197">Belirtilen başlığa `AddTodo` `TodoItem` sahip öğesini listeye eklemek için yöntemini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="1b456-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="1b456-198">Metin girişinin değerini boş bir dizeye ayarlayarak `newTodo` temizleyin:</span><span class="sxs-lookup"><span data-stu-id="1b456-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="1b456-199">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-199">Rebuild and run the app.</span></span> <span data-ttu-id="1b456-200">Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="1b456-201">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="1b456-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="1b456-202">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="1b456-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="1b456-203">`@todo.Title` Öğesine göre bir `<input>` öğeye geç `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="1b456-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="1b456-204">Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin (`IsDone` yani `false`).</span><span class="sxs-lookup"><span data-stu-id="1b456-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="1b456-205">Tamamlanan `Todo` bileşen (*sayfa/Todo. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1b456-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="1b456-206">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1b456-206">Rebuild and run the app.</span></span> <span data-ttu-id="1b456-207">Yeni kodu test etmek için Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b456-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
