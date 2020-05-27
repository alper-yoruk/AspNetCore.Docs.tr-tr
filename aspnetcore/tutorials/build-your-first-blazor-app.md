---
Başlık: ' ilk uygulamanızı derleme Blazor ' Yazar: Açıklama: ' Blazor uygulama oluşturma adım adım. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="build-your-first-blazor-app"></a>İlk uygulamanızı oluşturma Blazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor . Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Yapılacaklar listesi Blazor uygulama projesi oluşturma
> * RazorBileşenleri Değiştir
> * Bileşenlerde olay işleme ve veri bağlamayı kullanma
> * Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor

Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.

## <a name="build-components"></a>Derleme bileşenleri

1. <xref:blazor/get-started>Bu öğretici için bir proje oluşturmak üzere makalesindeki yönergeleri izleyin Blazor . Projeyi *ToDoList*olarak adlandırın.

1. *Sayfalar* klasöründe uygulamanın üç sayfasının her birine gidin: giriş, sayaç ve veri getirme. Bu sayfalar, Razor *Dizin. Razor*, *Counter. Razor*ve *fetchdata. Razor*bileşen dosyaları tarafından uygulanır.

1. Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin. Bir Web sayfasında normal olarak bir sayacı artırma, JavaScript yazmayı gerektirir. İle Blazor bunun yerine C# yazabilirsiniz.

1. `Counter` *Counter. Razor* dosyasındaki bileşenin uygulamasını inceleyin.

   *Pages/Counter. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Bileşenin kullanıcı arabirimi `Counter` HTML kullanılarak tanımlanır. Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) adlı gömülü C# sözdizimi kullanılarak eklenir [Razor](xref:mvc/views/razor) . HTML biçimlendirme ve C# işleme mantığı, derleme zamanında bir bileşen sınıfına dönüştürülür. Oluşturulan .NET sınıfının adı dosya adıyla eşleşir.

   Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır. `@code`Bloğunda, bileşen durumu (özellikler, alanlar) ve yöntemler olay işleme için veya diğer bileşen mantığını tanımlamak için belirtilir. Bu Üyeler daha sonra bileşenin işleme mantığının bir parçası olarak ve olayları işlemek için kullanılır.

   **Bana tıklama** düğmesi seçildiğinde:

   * `Counter`Bileşenin kayıtlı `onclick` işleyicisine ( `IncrementCount` yöntemi) denir.
   * `Counter`Bileşen, işleme ağacını yeniden oluşturur.
   * Yeni işleme ağacı öncekiyle karşılaştırılır.
   * Yalnızca Belge Nesne Modeli (DOM) üzerinde yapılan değişiklikler uygulanır. Görünen sayı güncelleştirildi.

1. `Counter`Sayıyı bir yerine iki ile artırmak için bileşenin C# mantığını değiştirin.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Değişiklikleri görmek için uygulamayı yeniden derleyin ve çalıştırın. **Ben tıklama** düğmesini seçin. Sayaç iki olarak artar.

## <a name="use-components"></a>Bileşenleri kullanma

Bir bileşeni, bir HTML söz dizimini kullanarak başka bir bileşene ekleyin.

1. Bileşene `Counter` `Index` bir öğe ekleyerek bileşeni uygulamanın bileşenine ekleyin `<Counter />` `Index` (*Index. Razor*).

   BlazorBu deneyim için webassembly kullanıyorsanız bileşen `SurveyPrompt` tarafından bir bileşen kullanılır `Index` . `<SurveyPrompt>`Öğesini bir `<Counter />` öğesiyle değiştirin. BlazorBu deneyim için bir sunucu uygulaması kullanıyorsanız, `<Counter />` öğesini `Index` bileşene ekleyin:

   *Pages/Index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Uygulamayı yeniden derleyin ve çalıştırın. `Index`Bileşenin kendi sayacı vardır.

## <a name="component-parameters"></a>Bileşen parametreleri

Bileşenler de parametrelere sahip olabilir. Bileşen parametreleri, bileşen sınıfında özniteliğiyle birlikte ortak özellikler kullanılarak tanımlanır [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) . Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.

1. Bileşenin `@code` C# kodunu aşağıdaki gibi güncelleştirin:

   * `IncrementAmount`Özniteliği ile ortak bir özellik ekleyin [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) .
   * `IncrementCount` `IncrementAmount` Değerini artırdığınızda özelliğini kullanmak için yöntemini değiştirin `currentCount` .

   *Pages/Counter. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. `IncrementAmount` `Index` Öznitelik kullanarak bileşenin öğesinde bir parametre belirtin `<Counter>` . Sayacı on olarak artırmak için değeri ayarlayın.

   *Pages/Index. Razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Bileşeni yeniden yükleyin `Index` . **Beni tıklama** düğmesi seçildiğinde sayaç on bir kez artar. `Counter`Bileşendeki sayaç bir artırmaya devam eder.

## <a name="route-to-components"></a>Bileşenlere yönlendir

`@page` *Counter. Razor* dosyasının en üstündeki yönerge, `Counter` bileşenin bir yönlendirme uç noktası olduğunu belirtir. `Counter`Bileşen öğesine gönderilen istekleri işler `/counter` . Yönerge olmadan `@page` , bileşen yönlendirilmiş istekleri işlemez, ancak bileşen diğer bileşenler tarafından hala kullanılabilir.

## <a name="dependency-injection"></a>Bağımlılık ekleme

### <a name="blazor-server-experience"></a>BlazorSunucu deneyimi

Sunucu uygulamasıyla çalışıyorsanız Blazor , `WeatherForecastService` hizmet [tek](xref:fundamentals/dependency-injection#service-lifetimes) bir olarak kaydedilir `Startup.ConfigureServices` . Uygulamanın tamamında [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)yoluyla hizmetin bir örneği mevcuttur:

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

[`@inject`](xref:mvc/views/razor#inject)Yönergesi, hizmet örneğini bileşene eklemek için kullanılır `WeatherForecastService` `FetchData` .

*Pages/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

`FetchData`Bileşen, `ForecastService` nesne dizisini almak için olarak eklenen hizmeti kullanır `WeatherForecast` :

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>BlazorWebAssembly deneyimi

BlazorWebassembly uygulamasıyla çalışıyorsanız, <xref:System.Net.Http.HttpClient> *Wwwroot/Sample-Data* klasöründeki *Hava durumu. JSON* dosyasından Hava durumu tahmin verileri almak için eklenir.

*Pages/FetchData. Razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

Bir [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) döngü, her tahmin örneğini Hava durumu verileri tablosunda bir satır olarak işlemek için kullanılır:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Yapılacaklar listesi oluşturma

Uygulamaya basit bir yapılacaklar listesi uygulayan yeni bir bileşen ekleyin.

1. `Todo` Razor *Sayfalar* klasöründe uygulamaya yeni bir bileşen ekleyin. Visual Studio 'da **Sayfalar** klasörüne sağ tıklayın ve **Add**  >  **Yeni öğe** Ekle  >  ** Razor bileşeni**' ni seçin. Bileşenin dosya *Todo. Razor*olarak adlandırın. Diğer geliştirme ortamlarında, *Todo. Razor*adlı **Sayfalar** klasörüne boş bir dosya ekleyin.

1. Bileşen için ilk biçimlendirmeyi belirtin:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. `Todo`Bileşeni gezinti çubuğuna ekleyin.

   `NavMenu`Bileşen (*Shared/navmenu. Razor*) uygulamanın düzeninde kullanılır. Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.

   `<NavLink>` `Todo` *Paylaşılan/navmenu. Razor* dosyasındaki mevcut liste öğelerinin altına aşağıdaki liste öğesi işaretlemesini ekleyerek bileşen için bir öğe ekleyin:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Uygulamayı yeniden derleyin ve çalıştırın. Bileşen bağlantısının çalıştığından emin olmak için yeni Todo sayfasını ziyaret edin `Todo` .

1. Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir *TodoItem.cs* dosyası ekleyin. Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Bileşene geri dön `Todo` (*Pages/Todo. Razor*):

   * Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` . `Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.
   * `foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir. Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Uygulamayı yeniden derleyin ve çalıştırın. **Todo Ekle** düğmesi seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.

1. Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` . `AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. `AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` . Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Uygulamayı yeniden derleyin ve çalıştırın. Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.

1. Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir. Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın. Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Tamamlanan `Todo` bileşen (*sayfa/Todo. Razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Uygulamayı yeniden derleyin ve çalıştırın. Yeni kodu test etmek için Todo öğeleri ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yapılacaklar listesi Blazor uygulama projesi oluşturma
> * RazorBileşenleri Değiştir
> * Bileşenlerde olay işleme ve veri bağlamayı kullanma
> * Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor

Bileşenleri derlemeyi ve kullanmayı öğrenin:

> [!div class="nextstepaction"]
> <xref:blazor/components>
