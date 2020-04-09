---
title: İlk Blazor uygulamanızı oluşturun
author: guardrex
description: Adım Blazor adım bir uygulama oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989429"
---
# <a name="build-your-first-opno-locblazor-app"></a><span data-ttu-id="a805d-103">İlk Blazor uygulamanızı oluşturun</span><span class="sxs-lookup"><span data-stu-id="a805d-103">Build your first Blazor app</span></span>

<span data-ttu-id="a805d-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a805d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a805d-105">Bu öğretici, bir Blazor uygulamayı nasıl oluşturup değiştirdiğinizi gösterir.</span><span class="sxs-lookup"><span data-stu-id="a805d-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="a805d-106">Bileşenler oluşturma</span><span class="sxs-lookup"><span data-stu-id="a805d-106">Build components</span></span>

1. <span data-ttu-id="a805d-107">Bu öğretici için <xref:blazor/get-started> bir Blazor proje oluşturmak için makaledeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="a805d-108">Proje *ToDoList*adını.</span><span class="sxs-lookup"><span data-stu-id="a805d-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="a805d-109">*Uygulamanın Sayfalar* klasöründeki üç sayfasının her birine göz atın: Ana Sayfa, Sayaç ve Getir verileri.</span><span class="sxs-lookup"><span data-stu-id="a805d-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="a805d-110">Bu sayfalar Razor bileşen dosyaları *Index.razor,* *Counter.razor*ve *FetchData.razor*tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="a805d-111">Sayaç sayfasında, sayfa yenilemeden sayacı niçin artıya doğru artıya tıklayın **düğmesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="a805d-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a805d-112">Bir web sayfasında ki sayaç ları artıya yazmak normalde JavaScript yazmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a805d-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="a805d-113">Ile, Blazorbunun yerine C# yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a805d-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="a805d-114">`Counter` *Counter.razor* dosyasındaki bileşenin uygulanmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="a805d-115">*Sayfalar/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a805d-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="a805d-116">`Counter` Bileşenin UI'si HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="a805d-117">Dinamik işleme mantığı (örneğin, döngüler, koşullular, ifadeler) [Razor](xref:mvc/views/razor)adlı katıştırılmış c# sözdizimi kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="a805d-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="a805d-118">HTML biçimlendirmesi ve C# oluşturma mantığı, oluşturma zamanında bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="a805d-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="a805d-119">Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a805d-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="a805d-120">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="a805d-121">Blokta, `@code` olay işleme veya diğer bileşen mantığını tanımlamak için bileşen durumu (özellikler, alanlar) ve yöntemler belirtilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="a805d-122">Bu üyeler daha sonra bileşenin oluşturma mantığının bir parçası olarak ve olayları işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a805d-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="a805d-123">Beni **Tıklatın** düğmesi seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="a805d-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="a805d-124">Bileşenin `Counter` kayıtlı `onclick` işleyicisi `IncrementCount` (yöntem) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="a805d-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="a805d-125">Bileşen, `Counter` render ağacını yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a805d-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="a805d-126">Yeni render ağacı öncekiyle karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="a805d-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="a805d-127">Yalnızca Belge Nesnesi Modelinde (DOM) değişiklikler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="a805d-128">Görüntülenen sayı güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="a805d-129">Bileşenin `Counter` C# mantığını değiştirerek sayım artışını bir yerine iki yapın.</span><span class="sxs-lookup"><span data-stu-id="a805d-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="a805d-130">Değişiklikleri görmek için uygulamayı yeniden oluşturve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="a805d-131">Beni **Tıklatın** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a805d-131">Select the **Click me** button.</span></span> <span data-ttu-id="a805d-132">Sayaç iki şer kademeli olarak artıyor.</span><span class="sxs-lookup"><span data-stu-id="a805d-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="a805d-133">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="a805d-133">Use components</span></span>

<span data-ttu-id="a805d-134">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="a805d-135">`Counter` Bileşene `Index` bir `<Counter />` öğe ekleyerek bileşeni uygulamanın bileşenine `Index` ekleyin *(Index.razor).*</span><span class="sxs-lookup"><span data-stu-id="a805d-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="a805d-136">Bu deneyim için Blazor WebAssembly kullanıyorsanız, `SurveyPrompt` `Index` bileşen tarafından bir bileşen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a805d-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="a805d-137">Öğeyi `<SurveyPrompt>` bir `<Counter />` öğeyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="a805d-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="a805d-138">Bu deneyim için Blazor bir Sunucu uygulaması kullanıyorsanız, öğeyi `<Counter />` `Index` bileşene ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a805d-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="a805d-139">*Sayfalar/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a805d-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="a805d-140">Uygulamayı yeniden oluşturve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-140">Rebuild and run the app.</span></span> <span data-ttu-id="a805d-141">Bileşenin `Index` kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="a805d-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="a805d-142">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="a805d-142">Component parameters</span></span>

<span data-ttu-id="a805d-143">Bileşenlerin parametreleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-143">Components can also have parameters.</span></span> <span data-ttu-id="a805d-144">Bileşen parametreleri öznitelik ile `[Parameter]` bileşen sınıfında ortak özellikleri kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="a805d-145">Biçimlendirmede bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="a805d-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="a805d-146">Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="a805d-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="a805d-147">Öznitelik `IncrementAmount` içeren bir kamu malı ekleyin. `[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="a805d-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="a805d-148">Değerini `IncrementCount` artırırken `IncrementAmount` özelliği kullanmak için yöntemi `currentCount`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="a805d-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="a805d-149">*Sayfalar/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a805d-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="a805d-150">Bir `IncrementAmount` öznitelik kullanarak `Index` bileşenin `<Counter>` öğesinde bir parametre belirtin.</span><span class="sxs-lookup"><span data-stu-id="a805d-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="a805d-151">Sayacı on yla artıya göre değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a805d-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="a805d-152">*Sayfalar/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a805d-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="a805d-153">Bileşeni yeniden `Index` yükleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-153">Reload the `Index` component.</span></span> <span data-ttu-id="a805d-154">**Bana Tıkla** düğmesi seçildiğinde sayaç on oranında artarak tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="a805d-155">Bileşendeki `Counter` sayaç bir oranında artmaya devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="a805d-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="a805d-156">Bileşenlere rota</span><span class="sxs-lookup"><span data-stu-id="a805d-156">Route to components</span></span>

<span data-ttu-id="a805d-157">*Counter.razor* dosyasının üst kısmındaki `Counter` `@page` yönerge, bileşenin bir yönlendirme bitiş noktası olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="a805d-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="a805d-158">Bileşen, `Counter` ''ye `/counter`gönderilen istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="a805d-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="a805d-159">Yönerge `@page` olmadan, bir bileşen yönlendirilen istekleri işlemez, ancak bileşen yine de diğer bileşenler tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="a805d-160">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="a805d-160">Dependency injection</span></span>

### <a name="opno-locblazor-server-experience"></a>Blazor<span data-ttu-id="a805d-161">Sunucu deneyimi</span><span class="sxs-lookup"><span data-stu-id="a805d-161"> Server experience</span></span>

<span data-ttu-id="a805d-162">Bir Blazor Server uygulamasıyla çalışıyorsanız, `WeatherForecastService` hizmet [singleton](xref:fundamentals/dependency-injection#service-lifetimes) `Startup.ConfigureServices`singleton olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a805d-163">Hizmetin bir örneği [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)yoluyla uygulama boyunca kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="a805d-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="a805d-164">Yönerge, `@inject` `WeatherForecastService` hizmet örneğini `FetchData` bileşene enjekte etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a805d-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="a805d-165">*Sayfalar/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="a805d-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="a805d-166">Bileşen, `FetchData` `ForecastService`bir dizi `WeatherForecast` nesne almak için enjekte edilen hizmeti kullanır:</span><span class="sxs-lookup"><span data-stu-id="a805d-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Blazor<span data-ttu-id="a805d-167">WebAssembly deneyimi</span><span class="sxs-lookup"><span data-stu-id="a805d-167"> WebAssembly experience</span></span>

<span data-ttu-id="a805d-168">Bir Blazor WebAssembly uygulamasıyla `HttpClient` çalışıyorsanız, *wwwroot/sample-data* klasöründeki *weather.json* dosyasından hava tahmini verileri elde etmek için enjekte edilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="a805d-169">*Sayfalar/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="a805d-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="a805d-170">Döngü, [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) her tahmin örneğini hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="a805d-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="a805d-171">Yapılacaklar listesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="a805d-171">Build a todo list</span></span>

<span data-ttu-id="a805d-172">Basit bir yapılacaklar listesi uygulayan uygulamaya yeni bir bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="a805d-173">`Todo` *Sayfalar* klasöründeki uygulamaya yeni bir Razor bileşeni ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="a805d-174">Visual Studio'da, **Sayfalar** klasörüne sağ tıklayın ve**Yeni Öğe** > **Jilet Bileşeni** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="a805d-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="a805d-175">Bileşenin dosyasını *Todo.razor*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="a805d-176">Diğer geliştirme ortamlarında, *Todo.razor*adlı **Sayfalar** klasörüne boş bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="a805d-177">Bileşen için ilk biçimlendirmeyi sağlayın:</span><span class="sxs-lookup"><span data-stu-id="a805d-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="a805d-178">`Todo` Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a805d-179">Bileşen `NavMenu` *(Paylaşılan/NavMenu.razor)* uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a805d-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="a805d-180">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak tanıyan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="a805d-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="a805d-181">`<NavLink>` *Paylaşılan/NavMenu.razor* dosyasındaki `Todo` varolan liste öğelerinin altına aşağıdaki liste öğesi biçimlendirmesini ekleyerek bileşen için bir öğe ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a805d-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="a805d-182">Uygulamayı yeniden oluşturve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-182">Rebuild and run the app.</span></span> <span data-ttu-id="a805d-183">Bileşene bağlantının çalıştığını doğrulamak için yeni `Todo` Todo sayfasını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="a805d-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="a805d-184">Bir todo öğesini temsil eden bir sınıf tutmak için projenin köküne *bir TodoItem.cs* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="a805d-185">`TodoItem` Sınıf için aşağıdaki C# kodunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="a805d-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="a805d-186">Bileşene `Todo` dön (*Pages/Todo.razor):*</span><span class="sxs-lookup"><span data-stu-id="a805d-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="a805d-187">Bir bloktaki todo öğeleri `@code` için bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="a805d-188">Bileşen, `Todo` yapılacaklar listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a805d-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a805d-189">Her bir todo öğesini `foreach` liste öğesi olarak işlemek için sıralanmamış liste biçimlendirmesi ve döngü ekleyin (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="a805d-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="a805d-190">Uygulama, listeye todo öğeleri eklemek için UI öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a805d-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a805d-191">Sıralanmamış listenin`<input>`altına metin girişi`<button>`( ) ve bir`<ul>...</ul>`düğme ( ) ekleyin ( ):</span><span class="sxs-lookup"><span data-stu-id="a805d-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="a805d-192">Uygulamayı yeniden oluşturve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-192">Rebuild and run the app.</span></span> <span data-ttu-id="a805d-193">**Ekle** düğmesi seçildiğinde, olay işleyicisi düğmeye bağlı olmadığından hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="a805d-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="a805d-194">Bileşene `AddTodo` bir yöntem ekleyin ve özniteliği kullanarak düğme seçimlerine kaydedin. `@onclick` `Todo`</span><span class="sxs-lookup"><span data-stu-id="a805d-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="a805d-195">Düğme `AddTodo` seçildiğinde C# yöntemi çağrılır:</span><span class="sxs-lookup"><span data-stu-id="a805d-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="a805d-196">Yeni yapılacak lar öğesinin başlığını almak `newTodo` için bloğun üst `@code` kısmındabir dize alanı ekleyin ve `bind` `<input>` öğedeki özniteliği kullanarak metin girişinin değerine bağlayın:</span><span class="sxs-lookup"><span data-stu-id="a805d-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a805d-197">Belirtilen `AddTodo` başlığı listeye `TodoItem` eklemek için yöntemi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="a805d-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a805d-198">Boş bir dize `newTodo` ayarlayarak metin girişinin değerini temizleyin:</span><span class="sxs-lookup"><span data-stu-id="a805d-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="a805d-199">Uygulamayı yeniden oluşturve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-199">Rebuild and run the app.</span></span> <span data-ttu-id="a805d-200">Yeni kodu test etmek için yapılacaklar listesine bazı yapılacaklar öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="a805d-201">Her bir yapılacak öğenin başlık metni değiştirilebilir hale getirilebilir ve onay kutusu kullanıcının tamamlanan öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="a805d-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a805d-202">Her todo öğesi için bir onay kutusu girişi ekleyin `IsDone` ve değerini özelliğe bağlayın.</span><span class="sxs-lookup"><span data-stu-id="a805d-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a805d-203">Bağlı `@todo.Title` bir `<input>` öğeye `@todo.Title`değiştirin:</span><span class="sxs-lookup"><span data-stu-id="a805d-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="a805d-204">Bu değerlerin bağlı olduğunu doğrulamak `<h3>` için, tamamlanmamış todo öğeleri sayısının sayısını göstermek için`IsDone` `false`üstbilgigünceli güncelleştirin ( is).</span><span class="sxs-lookup"><span data-stu-id="a805d-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a805d-205">Tamamlanan `Todo` bileşen (*Pages/Todo.razor):*</span><span class="sxs-lookup"><span data-stu-id="a805d-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="a805d-206">Uygulamayı yeniden oluşturve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a805d-206">Rebuild and run the app.</span></span> <span data-ttu-id="a805d-207">Yeni kodu sınamak için todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a805d-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
