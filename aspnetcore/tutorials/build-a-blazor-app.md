---
title: BlazorYapılacaklar listesi uygulaması oluşturma
author: guardrex
description: Bir Blazor uygulamayı adım adım oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 106e1119db777074b5eae24f5d7e216e6127ca13
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238302"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>BlazorYapılacaklar listesi uygulaması oluşturma

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor . Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Yapılacaklar listesi Blazor uygulama projesi oluşturma
> * RazorBileşenleri Değiştir
> * Bileşenlerde olay işleme ve veri bağlamayı kullanma
> * Bir uygulamada yönlendirmeyi kullanma Blazor

Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.

## <a name="prerequisites"></a>Önkoşullar

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>Yapılacaklar listesi uygulaması oluşturma Blazor

1. BlazorKomut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Yukarıdaki komut, `TodoList` uygulamayı tutma seçeneğiyle adlı bir klasör oluşturur `-o|--output` . `TodoList`Klasör, projenin *kök klasörüdür* . `TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:

   ```dotnetcli
   cd TodoList
   ```

1. `Todo` Razor Aşağıdaki komutu kullanarak uygulamaya yeni bir bileşen ekleyin:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   `-n|--name`Yukarıdaki komutta seçeneği, yeni bileşenin adını belirtir Razor . Yeni bileşen, `Pages` seçeneğiyle birlikte proje klasöründe oluşturulur `-o|--output` .

   > [!IMPORTANT]
   > Razor bileşen dosyası adları, büyük harfle bir ilk harf gerektirir. Klasörünü açın `Pages` ve `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` . Dosya adı olmalıdır `Todo.razor` .

1. `Todo`Bileşeni herhangi bir dosya düzenleyicisinde açın ve `@page` Razor göreli URL 'si ile dosyanın en üstüne bir yönerge ekleyin `/todo` .

   `Pages/Todo.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   `Pages/Todo.razor` dosyasını kaydedin.

1. `Todo`Bileşeni gezinti çubuğuna ekleyin.

   `NavMenu`Bileşen, uygulamanın düzeninde kullanılır. Düzenler, bir uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir. Bileşen, `NavLink` uygulama tarafından bileşen URL 'si yüklendiğinde uygulamanın kullanıcı arabiriminde bir ipucu sağlar.

   Bileşenin sırasız listesinde ( `<ul>...</ul>` ) `NavMenu` , bileşen için aşağıdaki liste öğesini ( `<li>...</li>` ) ve `NavLink` bileşenini ekleyin `Todo` .

   `Shared/NavMenu.razor` içinde:

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   `Shared/NavMenu.razor` dosyasını kaydedin.

1. [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)Klasörden komut kabuğu 'ndaki komutu yürüterek uygulamayı derleyin ve çalıştırın `TodoList` . Uygulama çalıştırıldıktan sonra, **`Todo`** uygulamanın gezinti çubuğundaki bağlantıyı seçerek yeni Todo sayfasını ziyaret edin `/todo` .

   Uygulamadan komut kabuğunu çalıştırın. Bir dosya her kaydedildiğinde, uygulama otomatik olarak yeniden oluşturulur. Tarayıcı, derleme ve yeniden başlatma sırasında uygulamayla bağlantısını geçici olarak kaybeder. Bağlantı yeniden oluşturulduğunda Tarayıcıdaki sayfa otomatik olarak yeniden yüklenir.

1. `TodoItem.cs` `TodoList` Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin (klasör) köküne bir dosya ekleyin. Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` .

   `TodoItem.cs`:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > Dosyayı ve sınıfı oluşturmak için Visual Studio kullanıyorsanız `ToDoItem.cs` `ToDoItem` aşağıdaki yaklaşımlardan birini kullanın:
   >
   > * Sınıf için Visual Studio 'Nun oluşturduğu ad alanını kaldırın.
   > * Yukarıdaki kod bloğundaki **Kopyala** düğmesini kullanın ve Visual Studio 'nun oluşturduğu dosyanın tüm içeriğini değiştirin.

1. `Todo`Bileşene dönün ve aşağıdaki görevleri gerçekleştirin:

   * Bloktaki Todo öğeleri için bir alan ekleyin `@code` . `Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.
   * `foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .

   `Pages/Todo.razor`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir. Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. `TodoItem.cs`Dosyayı ve güncelleştirilmiş `Pages/Todo.razor` dosyayı kaydedin. Komut kabuğunda, dosyalar kaydedildiğinde uygulama otomatik olarak yeniden oluşturulur. Tarayıcı, uygulamanın bağlantısını geçici olarak kaybeder ve bağlantı yeniden oluşturulduğunda sayfayı yeniden yükler.

1. **`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye eklenmediğinden hiçbir şey olmaz.

1. Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğmenin metodunu kaydedin `@onclick` . `AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. Yeni Todo öğesinin başlığını almak için, `newTodo` bloğun üst kısmına bir dize alanı ekleyin `@code` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   `<input>`Özniteliği ile bağlanacak metin öğesini değiştirin `newTodo` `@bind` :

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. `AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` . Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. `Pages/Todo.razor` dosyasını kaydedin. Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur. Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.

1. Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir. Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın. `@todo.Title` `<input>` İle bağlantılı bir öğeye geçin `todo.Title` `@bind` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. `<h3>`Üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının (yani) sayısını gösterecek şekilde güncelleştirin `IsDone` `false` .

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. `Pages/Todo.razor` dosyasını kaydedin. Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur. Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.

1. Bileşeni test etmek için öğe ekleyin, öğeleri düzenleyin ve Todo öğelerini işaretleyin.

1. İşiniz bittiğinde, komut kabuğu 'nda uygulamayı kapatın. Birçok komut kabuklarını, <kbd></kbd> + bir uygulamayı durdurmak için CTRL<kbd>c</kbd> klavye komutunu kabul eder.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yapılacaklar listesi Blazor uygulama projesi oluşturma
> * RazorBileşenleri Değiştir
> * Bileşenlerde olay işleme ve veri bağlamayı kullanma
> * Bir uygulamada yönlendirmeyi kullanma Blazor

ASP.NET Core araçları hakkında bilgi edinin Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
