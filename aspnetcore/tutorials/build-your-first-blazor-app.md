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
# <a name="build-your-first-blazor-app"></a>İlk Blazor uygulamanızı oluşturma

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Bu öğreticide, bir Blazor uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir.

## <a name="build-components"></a>Derleme bileşenleri

1. Bu öğretici için bir <xref:blazor/get-started> Blazor proje oluşturmak üzere makalesindeki yönergeleri izleyin. Projeyi *ToDoList*olarak adlandırın.

1. *Sayfalar* klasöründe uygulamanın üç sayfasının her birine gidin: giriş, sayaç ve veri getirme. Bu sayfalar, Razor *Dizin. Razor*, *Counter. Razor*ve *fetchdata. Razor*bileşen dosyaları tarafından uygulanır.

1. Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin. Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir. İle Blazorbunun yerine C# yazabilirsiniz.

1. `Counter` *Counter. Razor* dosyasındaki bileşenin uygulamasını inceleyin.

   *Pages/Counter. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   `Counter` Bileşenin kullanıcı arabirimi HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı [Razor](xref:mvc/views/razor)gömülü C# sözdizimi kullanılarak eklenir. HTML biçimlendirme ve C# işleme mantığı, derleme zamanında bir bileşen sınıfına dönüştürülür. Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.

   Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır. `@code` Bloğunda, bileşen durumu (özellikler, alanlar) ve yöntemler olay işleme için veya diğer bileşen mantığını tanımlamak için belirtilir. Bu Üyeler daha sonra bileşenin işleme mantığının bir parçası olarak ve olayları işlemek için kullanılır.

   **Bana tıklama** düğmesi seçildiğinde:

   * `Counter` Bileşenin kayıtlı `onclick` işleyicisine ( `IncrementCount` yöntemi) denir.
   * `Counter` Bileşen, işleme ağacını yeniden oluşturur.
   * Yeni işleme ağacı öncekiyle karşılaştırılır.
   * Yalnızca Belge Nesne Modeli (DOM) üzerinde yapılan değişiklikler uygulanır. Görünen sayı güncelleştirildi.

1. Sayıyı bir yerine iki ile artırmak `Counter` Için bileşenin C# mantığını değiştirin.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Değişiklikleri görmek için uygulamayı yeniden derleyin ve çalıştırın. **Ben tıklama** düğmesini seçin. Sayaç iki olarak artar.

## <a name="use-components"></a>Bileşenleri kullanma

Bir bileşeni, bir HTML söz dizimini kullanarak başka bir bileşene ekleyin.

1. Bileşene bir `Counter` `Index` `<Counter />` öğe `Index` ekleyerek bileşeni uygulamanın bileşenine ekleyin (*Index. Razor*).

   Bu deneyim için webassembly kullanıyorsanız Blazor bileşen tarafından `SurveyPrompt` `Index` bir bileşen kullanılır. `<SurveyPrompt>` Öğesini bir `<Counter />` öğesiyle değiştirin. Bu deneyim için bir Blazor sunucu uygulaması kullanıyorsanız, `<Counter />` öğesini `Index` bileşene ekleyin:

   *Pages/Index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Uygulamayı yeniden derleyin ve çalıştırın. `Index` Bileşenin kendi sayacı vardır.

## <a name="component-parameters"></a>Bileşen parametreleri

Bileşenler de parametrelere sahip olabilir. Bileşen parametreleri, bileşen sınıfında `[Parameter]` özniteliğiyle birlikte ortak özellikler kullanılarak tanımlanır. Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

1. Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:

   * `[Parameter]` Özniteliği ile ortak `IncrementAmount` bir özellik ekleyin.
   * Değerini artırdığınızda `IncrementCount` `IncrementAmount` özelliğini kullanmak için yöntemini değiştirin `currentCount`.

   *Pages/Counter. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Öznitelik kullanarak `IncrementAmount` `Index` bileşenin `<Counter>` öğesinde bir parametre belirtin. Sayacı on olarak artırmak için değeri ayarlayın.

   *Pages/Index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. `Index` Bileşeni yeniden yükleyin. **Beni tıklama** düğmesi seçildiğinde sayaç on bir kez artar. `Counter` Bileşendeki sayaç bir artırmaya devam eder.

## <a name="route-to-components"></a>Bileşenlere yönlendir

*Counter. Razor* dosyasının en üstündeki `Counter` `@page` yönerge, bileşenin bir yönlendirme uç noktası olduğunu belirtir. `Counter` Bileşen öğesine `/counter`gönderilen istekleri işler. `@page` Yönerge olmadan, bileşen yönlendirilmiş istekleri işlemez, ancak bileşen diğer bileşenler tarafından hala kullanılabilir.

## <a name="dependency-injection"></a>Bağımlılık ekleme

### <a name="blazor-server-experience"></a>BlazorSunucu deneyimi

Blazor Sunucu uygulamasıyla çalışıyorsanız, `WeatherForecastService` hizmet [tek](xref:fundamentals/dependency-injection#service-lifetimes) bir olarak kaydedilir `Startup.ConfigureServices`. Uygulamanın tamamında [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)yoluyla hizmetin bir örneği mevcuttur:

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

`@inject` Yönergesi, `WeatherForecastService` hizmet örneğini `FetchData` bileşene eklemek için kullanılır.

*Pages/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

`FetchData` Bileşen, `WeatherForecast` nesne dizisini almak için olarak `ForecastService`eklenen hizmeti kullanır:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorWebAssembly deneyimi

Blazor Webassembly uygulamasıyla çalışıyorsanız `HttpClient` , *Wwwroot/Sample-Data* klasöründeki *Hava durumu. JSON* dosyasından Hava durumu tahmin verileri almak için eklenir.

*Pages/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Bir [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngü, her tahmin örneğini Hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Yapılacaklar listesi oluşturma

Uygulamaya basit bir yapılacaklar listesi uygulayan yeni bir bileşen ekleyin.

1. *Sayfalar* klasöründe uygulamaya `Todo` Razor yeni bir bileşen ekleyin. Visual Studio 'da **Sayfalar** klasörüne sağ tıklayın ve**Yeni öğe**  >  ** Razor ** **Ekle** > bileşeni ' ni seçin. Bileşenin dosya *Todo. Razor*olarak adlandırın. Diğer geliştirme ortamlarında, *Todo. Razor*adlı **Sayfalar** klasörüne boş bir dosya ekleyin.

1. Bileşen için ilk biçimlendirmeyi belirtin:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. `Todo` Bileşeni gezinti çubuğuna ekleyin.

   `NavMenu` Bileşen (*Shared/navmenu. Razor*) uygulamanın düzeninde kullanılır. Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.

   `<NavLink>` *Paylaşılan/navmenu. Razor* dosyasındaki mevcut liste öğelerinin altına aşağıdaki liste öğesi işaretlemesini ekleyerek `Todo` bileşen için bir öğe ekleyin:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Uygulamayı yeniden derleyin ve çalıştırın. `Todo` Bileşen bağlantısının çalıştığından emin olmak Için yeni Todo sayfasını ziyaret edin.

1. Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir *TodoItem.cs* dosyası ekleyin. `TodoItem` Sınıfı Için aşağıdaki C# kodunu kullanın:

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. `Todo` Bileşene geri dön (*Pages/Todo. Razor*):

   * Bir `@code` bloktaki Todo öğeleri için bir alan ekleyin. `Todo` Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.
   * Her Todo öğesini bir liste öğesi `foreach` (`<li>`) olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir. Sıralanmamış listenin`<ul>...</ul>`() altına`<input>`bir metin girişi ()`<button>`ve bir düğme () ekleyin:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Uygulamayı yeniden derleyin ve çalıştırın. **Todo Ekle** düğmesi seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.

1. Bileşene bir `AddTodo` yöntem ekleyin ve `@onclick` özniteliği kullanarak düğme seçimleri için kaydedin. `Todo` `AddTodo` C# yöntemi, düğme seçildiğinde çağrılır:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Yeni Todo öğesinin başlığını `newTodo` almak için, `@code` bloğunun üst kısmına bir dize alanı ekleyin ve bunu, `bind` `<input>` öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Belirtilen başlığa `AddTodo` `TodoItem` sahip öğesini listeye eklemek için yöntemini güncelleştirin. Metin girişinin değerini boş bir dizeye ayarlayarak `newTodo` temizleyin:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Uygulamayı yeniden derleyin ve çalıştırın. Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.

1. Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir. Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın. `@todo.Title` Öğesine göre bir `<input>` öğeye geç `@todo.Title`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin (`IsDone` yani `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Tamamlanan `Todo` bileşen (*sayfa/Todo. Razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Uygulamayı yeniden derleyin ve çalıştırın. Yeni kodu test etmek için Todo öğeleri ekleyin.

> [!div class="nextstepaction"]
> <xref:blazor/components>
