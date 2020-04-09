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
# <a name="build-your-first-opno-locblazor-app"></a>İlk Blazor uygulamanızı oluşturun

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu öğretici, bir Blazor uygulamayı nasıl oluşturup değiştirdiğinizi gösterir.

## <a name="build-components"></a>Bileşenler oluşturma

1. Bu öğretici için <xref:blazor/get-started> bir Blazor proje oluşturmak için makaledeki kılavuzu izleyin. Proje *ToDoList*adını.

1. *Uygulamanın Sayfalar* klasöründeki üç sayfasının her birine göz atın: Ana Sayfa, Sayaç ve Getir verileri. Bu sayfalar Razor bileşen dosyaları *Index.razor,* *Counter.razor*ve *FetchData.razor*tarafından uygulanır.

1. Sayaç sayfasında, sayfa yenilemeden sayacı niçin artıya doğru artıya tıklayın **düğmesini** seçin. Bir web sayfasında ki sayaç ları artıya yazmak normalde JavaScript yazmayı gerektirir. Ile, Blazorbunun yerine C# yazabilirsiniz.

1. `Counter` *Counter.razor* dosyasındaki bileşenin uygulanmasını inceleyin.

   *Sayfalar/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   `Counter` Bileşenin UI'si HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullular, ifadeler) [Razor](xref:mvc/views/razor)adlı katıştırılmış c# sözdizimi kullanılarak eklenir. HTML biçimlendirmesi ve C# oluşturma mantığı, oluşturma zamanında bileşen sınıfına dönüştürülür. Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.

   Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır. Blokta, `@code` olay işleme veya diğer bileşen mantığını tanımlamak için bileşen durumu (özellikler, alanlar) ve yöntemler belirtilir. Bu üyeler daha sonra bileşenin oluşturma mantığının bir parçası olarak ve olayları işlemek için kullanılır.

   Beni **Tıklatın** düğmesi seçildiğinde:

   * Bileşenin `Counter` kayıtlı `onclick` işleyicisi `IncrementCount` (yöntem) olarak adlandırılır.
   * Bileşen, `Counter` render ağacını yeniden oluşturur.
   * Yeni render ağacı öncekiyle karşılaştırılır.
   * Yalnızca Belge Nesnesi Modelinde (DOM) değişiklikler uygulanır. Görüntülenen sayı güncelleştirilir.

1. Bileşenin `Counter` C# mantığını değiştirerek sayım artışını bir yerine iki yapın.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Değişiklikleri görmek için uygulamayı yeniden oluşturve çalıştırın. Beni **Tıklatın** düğmesini seçin. Sayaç iki şer kademeli olarak artıyor.

## <a name="use-components"></a>Bileşenleri kullanma

HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.

1. `Counter` Bileşene `Index` bir `<Counter />` öğe ekleyerek bileşeni uygulamanın bileşenine `Index` ekleyin *(Index.razor).*

   Bu deneyim için Blazor WebAssembly kullanıyorsanız, `SurveyPrompt` `Index` bileşen tarafından bir bileşen kullanılır. Öğeyi `<SurveyPrompt>` bir `<Counter />` öğeyle değiştirin. Bu deneyim için Blazor bir Sunucu uygulaması kullanıyorsanız, öğeyi `<Counter />` `Index` bileşene ekleyin:

   *Sayfalar/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Uygulamayı yeniden oluşturve çalıştırın. Bileşenin `Index` kendi sayacı vardır.

## <a name="component-parameters"></a>Bileşen parametreleri

Bileşenlerin parametreleri de olabilir. Bileşen parametreleri öznitelik ile `[Parameter]` bileşen sınıfında ortak özellikleri kullanılarak tanımlanır. Biçimlendirmede bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

1. Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:

   * Öznitelik `IncrementAmount` içeren bir kamu malı ekleyin. `[Parameter]`
   * Değerini `IncrementCount` artırırken `IncrementAmount` özelliği kullanmak için yöntemi `currentCount`değiştirin.

   *Sayfalar/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Bir `IncrementAmount` öznitelik kullanarak `Index` bileşenin `<Counter>` öğesinde bir parametre belirtin. Sayacı on yla artıya göre değere ayarlayın.

   *Sayfalar/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Bileşeni yeniden `Index` yükleyin. **Bana Tıkla** düğmesi seçildiğinde sayaç on oranında artarak tamamlanır. Bileşendeki `Counter` sayaç bir oranında artmaya devam ediyor.

## <a name="route-to-components"></a>Bileşenlere rota

*Counter.razor* dosyasının üst kısmındaki `Counter` `@page` yönerge, bileşenin bir yönlendirme bitiş noktası olduğunu belirtir. Bileşen, `Counter` ''ye `/counter`gönderilen istekleri işler. Yönerge `@page` olmadan, bir bileşen yönlendirilen istekleri işlemez, ancak bileşen yine de diğer bileşenler tarafından kullanılabilir.

## <a name="dependency-injection"></a>Bağımlılık ekleme

### <a name="opno-locblazor-server-experience"></a>BlazorSunucu deneyimi

Bir Blazor Server uygulamasıyla çalışıyorsanız, `WeatherForecastService` hizmet [singleton](xref:fundamentals/dependency-injection#service-lifetimes) `Startup.ConfigureServices`singleton olarak kaydedilir. Hizmetin bir örneği [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)yoluyla uygulama boyunca kullanılabilir:

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Yönerge, `@inject` `WeatherForecastService` hizmet örneğini `FetchData` bileşene enjekte etmek için kullanılır.

*Sayfalar/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Bileşen, `FetchData` `ForecastService`bir dizi `WeatherForecast` nesne almak için enjekte edilen hizmeti kullanır:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>BlazorWebAssembly deneyimi

Bir Blazor WebAssembly uygulamasıyla `HttpClient` çalışıyorsanız, *wwwroot/sample-data* klasöründeki *weather.json* dosyasından hava tahmini verileri elde etmek için enjekte edilir.

*Sayfalar/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Döngü, [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) her tahmin örneğini hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Yapılacaklar listesi oluşturma

Basit bir yapılacaklar listesi uygulayan uygulamaya yeni bir bileşen ekleyin.

1. `Todo` *Sayfalar* klasöründeki uygulamaya yeni bir Razor bileşeni ekleyin. Visual Studio'da, **Sayfalar** klasörüne sağ tıklayın ve**Yeni Öğe** > **Jilet Bileşeni** **Ekle'yi** > seçin. Bileşenin dosyasını *Todo.razor*olarak adlandırın. Diğer geliştirme ortamlarında, *Todo.razor*adlı **Sayfalar** klasörüne boş bir dosya ekleyin.

1. Bileşen için ilk biçimlendirmeyi sağlayın:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. `Todo` Bileşeni gezinti çubuğuna ekleyin.

   Bileşen `NavMenu` *(Paylaşılan/NavMenu.razor)* uygulamanın düzeninde kullanılır. Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak tanıyan bileşenlerdir.

   `<NavLink>` *Paylaşılan/NavMenu.razor* dosyasındaki `Todo` varolan liste öğelerinin altına aşağıdaki liste öğesi biçimlendirmesini ekleyerek bileşen için bir öğe ekleyin:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Uygulamayı yeniden oluşturve çalıştırın. Bileşene bağlantının çalıştığını doğrulamak için yeni `Todo` Todo sayfasını ziyaret edin.

1. Bir todo öğesini temsil eden bir sınıf tutmak için projenin köküne *bir TodoItem.cs* dosyası ekleyin. `TodoItem` Sınıf için aşağıdaki C# kodunu kullanın:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Bileşene `Todo` dön (*Pages/Todo.razor):*

   * Bir bloktaki todo öğeleri `@code` için bir alan ekleyin. Bileşen, `Todo` yapılacaklar listesinin durumunu korumak için bu alanı kullanır.
   * Her bir todo öğesini `foreach` liste öğesi olarak işlemek için sıralanmamış liste biçimlendirmesi ve döngü ekleyin (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Uygulama, listeye todo öğeleri eklemek için UI öğeleri gerektirir. Sıralanmamış listenin`<input>`altına metin girişi`<button>`( ) ve bir`<ul>...</ul>`düğme ( ) ekleyin ( ):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Uygulamayı yeniden oluşturve çalıştırın. **Ekle** düğmesi seçildiğinde, olay işleyicisi düğmeye bağlı olmadığından hiçbir şey olmaz.

1. Bileşene `AddTodo` bir yöntem ekleyin ve özniteliği kullanarak düğme seçimlerine kaydedin. `@onclick` `Todo` Düğme `AddTodo` seçildiğinde C# yöntemi çağrılır:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Yeni yapılacak lar öğesinin başlığını almak `newTodo` için bloğun üst `@code` kısmındabir dize alanı ekleyin ve `bind` `<input>` öğedeki özniteliği kullanarak metin girişinin değerine bağlayın:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Belirtilen `AddTodo` başlığı listeye `TodoItem` eklemek için yöntemi güncelleştirin. Boş bir dize `newTodo` ayarlayarak metin girişinin değerini temizleyin:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Uygulamayı yeniden oluşturve çalıştırın. Yeni kodu test etmek için yapılacaklar listesine bazı yapılacaklar öğeleri ekleyin.

1. Her bir yapılacak öğenin başlık metni değiştirilebilir hale getirilebilir ve onay kutusu kullanıcının tamamlanan öğeleri izlemesine yardımcı olabilir. Her todo öğesi için bir onay kutusu girişi ekleyin `IsDone` ve değerini özelliğe bağlayın. Bağlı `@todo.Title` bir `<input>` öğeye `@todo.Title`değiştirin:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Bu değerlerin bağlı olduğunu doğrulamak `<h3>` için, tamamlanmamış todo öğeleri sayısının sayısını göstermek için`IsDone` `false`üstbilgigünceli güncelleştirin ( is).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Tamamlanan `Todo` bileşen (*Pages/Todo.razor):*

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Uygulamayı yeniden oluşturve çalıştırın. Yeni kodu sınamak için todo öğeleri ekleyin.

> [!div class="nextstepaction"]
> <xref:blazor/components>
