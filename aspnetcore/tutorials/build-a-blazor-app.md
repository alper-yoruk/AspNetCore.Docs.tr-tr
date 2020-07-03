---
title: BlazorYapılacaklar listesi uygulaması oluşturma
author: guardrex
description: Bir Blazor uygulamayı adım adım oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 726380c42c952f47d6fdff09a811f35a20462d96
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944970"
---
# <a name="build-a-blazor-todo-list-app"></a>BlazorYapılacaklar listesi uygulaması oluşturma

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor . Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Yapılacaklar listesi Blazor uygulama projesi oluşturma
> * RazorBileşenleri Değiştir
> * Bileşenlerde olay işleme ve veri bağlamayı kullanma
> * Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor

Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.

1. BlazorKomut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Yukarıdaki komut, uygulamayı tutmak için adlı bir klasör oluşturur `TodoList` . `TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:

   ```dotnetcli
   cd TodoList
   ```

1. `Todo` Razor Aşağıdaki komutu kullanarak klasördeki uygulamaya yeni bir bileşen ekleyin `Pages` :

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razorbileşen dosyası adları, büyük harfle bir ilk harf gerektirir, bu nedenle `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .

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

1. Uygulamayı yeniden derleyin ve çalıştırın. Bileşen bağlantısının çalıştığından emin olmak için yeni Todo sayfasını ziyaret edin `Todo` .

1. `TodoItem.cs`Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir dosya ekleyin. Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Bileşene geri dön `Todo` ( `Pages/Todo.razor` ):

   * Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` . `Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.
   * `foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir. Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Uygulamayı yeniden derleyin ve çalıştırın. **`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.

1. Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` . `AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. `AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` . Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Uygulamayı yeniden derleyin ve çalıştırın. Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.

1. Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir. Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın. Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Uygulamayı yeniden derleyin ve çalıştırın. Yeni kodu test etmek için Todo öğeleri ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yapılacaklar listesi Blazor uygulama projesi oluşturma
> * RazorBileşenleri Değiştir
> * Bileşenlerde olay işleme ve veri bağlamayı kullanma
> * Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor

Proje şablonları hakkında bilgi edinin Blazor :

> [!div class="nextstepaction"]
> <xref:blazor/templates>
