---
title: BlazorYapılacaklar listesi uygulaması oluşturma
author: guardrex
description: Bir Blazor uygulamayı adım adım oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
ms.openlocfilehash: 4d5bd977b52dd20ffb62519720106ef0a4560914
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508142"
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

## <a name="prerequisites"></a>Ön koşullar

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

   Yukarıdaki komut, uygulamayı tutmak için adlı bir klasör oluşturur `TodoList` . `TodoList`Klasör, projenin *kök klasörüdür* . `TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:

   ```dotnetcli
   cd TodoList
   ```

1. `Todo` Razor Aşağıdaki komutu kullanarak klasördeki uygulamaya yeni bir bileşen ekleyin `Pages` :

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor bileşen dosyası adları, büyük harfle bir ilk harf gerektirir. Klasörünü açın `Pages` ve `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` . Dosya adı olmalıdır `Todo.razor` .

1. İçinde `Pages/Todo.razor` bileşen için ilk biçimlendirmeyi belirtin:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. `Todo`Bileşeni gezinti çubuğuna ekleyin.

   `NavMenu`Bileşen ( `Shared/NavMenu.razor` ) uygulamanın düzeninde kullanılır. Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.

   `<NavLink>` `Todo` Aşağıdaki liste öğesi işaretlemesini, dosyadaki var olan liste öğelerinin altına ekleyerek bileşen için bir öğe ekleyin `Shared/NavMenu.razor` :

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. `dotnet run`Klasörden komut kabuğu 'ndaki komutu yürüterek uygulamayı derleyin ve çalıştırın `TodoList` . `https://localhost:5001/todo`Bileşen bağlantısının çalıştığından emin olmak için adresindeki yeni Todo sayfasını ziyaret edin `Todo` .

1. `TodoItem.cs` `TodoList` Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin (klasör) köküne bir dosya ekleyin. Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Bileşene geri dön `Todo` ( `Pages/Todo.razor` ):

   * Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` . `Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.
   * `foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir. Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Komut kabuğundan çalışan uygulamayı durdurun. Birçok komut kabuklarını, <kbd>Ctrl</kbd> + bir uygulamayı durdurmak için CTRL<kbd>c</kbd> klavye komutunu kabul eder. Komutuyla uygulamayı yeniden derleyin ve çalıştırın `dotnet run` . **`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.

1. Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` . `AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. `AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` . Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Komut kabuğundan çalışan uygulamayı durdurun. Komutuyla uygulamayı yeniden derleyin ve çalıştırın `dotnet run` . Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.

1. Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir. Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın. Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Komut kabuğundan çalışan uygulamayı durdurun. Komutuyla uygulamayı yeniden derleyin ve çalıştırın `dotnet run` . Yeni kodu test etmek için Todo öğeleri ekleyin.

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
