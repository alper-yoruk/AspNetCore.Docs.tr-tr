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
ms.openlocfilehash: 174a8e561701bb3ebd68ed05e42dfc3d70a9b450
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176235"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="1aac7-103">BlazorYapılacaklar listesi uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="1aac7-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="1aac7-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="1aac7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1aac7-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="1aac7-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="1aac7-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="1aac7-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1aac7-107">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1aac7-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="1aac7-108">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="1aac7-108">Modify Razor components</span></span>
> * <span data-ttu-id="1aac7-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="1aac7-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="1aac7-110">Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="1aac7-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="1aac7-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="1aac7-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="1aac7-112">BlazorKomut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="1aac7-113">Yukarıdaki komut, uygulamayı tutmak için adlı bir klasör oluşturur `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="1aac7-114">`TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1aac7-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="1aac7-115">`Todo` Razor Aşağıdaki komutu kullanarak klasördeki uygulamaya yeni bir bileşen ekleyin `Pages` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor<span data-ttu-id="1aac7-116">bileşen dosyası adları, büyük harfle bir ilk harf gerektirir, bu nedenle `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-116"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="1aac7-117">İçinde `Pages/Todo.razor` bileşen için ilk biçimlendirmeyi belirtin:</span><span class="sxs-lookup"><span data-stu-id="1aac7-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="1aac7-118">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1aac7-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="1aac7-119">`NavMenu`Bileşen ( `Shared/NavMenu.razor` ) uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1aac7-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="1aac7-120">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="1aac7-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="1aac7-121">`<NavLink>` `Todo` Aşağıdaki liste öğesi işaretlemesini, dosyadaki var olan liste öğelerinin altına ekleyerek bileşen için bir öğe ekleyin `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="1aac7-122">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1aac7-122">Rebuild and run the app.</span></span> <span data-ttu-id="1aac7-123">Bileşen bağlantısının çalıştığından emin olmak için yeni Todo sayfasını ziyaret edin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="1aac7-124">`TodoItem.cs`Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin köküne bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1aac7-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="1aac7-125">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="1aac7-126">Bileşene geri dön `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="1aac7-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="1aac7-127">Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="1aac7-128">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="1aac7-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="1aac7-129">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="1aac7-130">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1aac7-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="1aac7-131">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="1aac7-132">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1aac7-132">Rebuild and run the app.</span></span> <span data-ttu-id="1aac7-133">**`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="1aac7-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="1aac7-134">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="1aac7-135">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="1aac7-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="1aac7-136">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="1aac7-137">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="1aac7-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="1aac7-138">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="1aac7-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="1aac7-139">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1aac7-139">Rebuild and run the app.</span></span> <span data-ttu-id="1aac7-140">Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1aac7-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="1aac7-141">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="1aac7-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="1aac7-142">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="1aac7-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="1aac7-143">Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="1aac7-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="1aac7-144">Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).</span><span class="sxs-lookup"><span data-stu-id="1aac7-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="1aac7-145">Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="1aac7-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="1aac7-146">Uygulamayı yeniden derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1aac7-146">Rebuild and run the app.</span></span> <span data-ttu-id="1aac7-147">Yeni kodu test etmek için Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1aac7-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1aac7-148">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1aac7-148">Next steps</span></span>

<span data-ttu-id="1aac7-149">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="1aac7-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1aac7-150">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1aac7-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="1aac7-151">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="1aac7-151">Modify Razor components</span></span>
> * <span data-ttu-id="1aac7-152">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="1aac7-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="1aac7-153">Bir uygulamada bağımlılık ekleme (dı) ve yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="1aac7-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="1aac7-154">ASP.NET Core araçları hakkında bilgi edinin Blazor :</span><span class="sxs-lookup"><span data-stu-id="1aac7-154">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
