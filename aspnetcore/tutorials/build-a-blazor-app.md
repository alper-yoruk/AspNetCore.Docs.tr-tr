---
title: :::no-loc(Blazor):::Yapılacaklar listesi uygulaması oluşturma
author: guardrex
description: 'Bir :::no-loc(Blazor)::: uygulamayı adım adım oluşturun.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 68a38b82f5a89365e4f345a60f1f34b697c027ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060098"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="f0d2a-103">:::no-loc(Blazor):::Yapılacaklar listesi uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="f0d2a-103">Build a :::no-loc(Blazor)::: todo list app</span></span>

<span data-ttu-id="f0d2a-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="f0d2a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f0d2a-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-105">This tutorial shows you how to build and modify a :::no-loc(Blazor)::: app.</span></span> <span data-ttu-id="f0d2a-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f0d2a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0d2a-107">Yapılacaklar listesi :::no-loc(Blazor)::: uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f0d2a-107">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="f0d2a-108">:::no-loc(Razor):::Bileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="f0d2a-108">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="f0d2a-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="f0d2a-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f0d2a-110">Bir uygulamada yönlendirmeyi kullanma :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="f0d2a-110">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="f0d2a-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f0d2a-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="f0d2a-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="f0d2a-113">Yapılacaklar listesi uygulaması oluşturma :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="f0d2a-113">Create a todo list :::no-loc(Blazor)::: app</span></span>

1. <span data-ttu-id="f0d2a-114">:::no-loc(Blazor):::Komut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-114">Create a new :::no-loc(Blazor)::: app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="f0d2a-115">Yukarıdaki komut, uygulamayı tutmak için adlı bir klasör oluşturur `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="f0d2a-116">`TodoList`Klasör, projenin *kök klasörüdür* .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="f0d2a-117">`TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f0d2a-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="f0d2a-118">`Todo` :::no-loc(Razor)::: Aşağıdaki komutu kullanarak klasördeki uygulamaya yeni bir bileşen ekleyin `Pages` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-118">Add a new `Todo` :::no-loc(Razor)::: component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="f0d2a-119">:::no-loc(Razor)::: bileşen dosyası adları, büyük harfle bir ilk harf gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-119">:::no-loc(Razor)::: component file names require a capitalized first letter.</span></span> <span data-ttu-id="f0d2a-120">Klasörünü açın `Pages` ve `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="f0d2a-121">Dosya adı olmalıdır `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="f0d2a-122">İçinde `Pages/Todo.razor` bileşen için ilk biçimlendirmeyi belirtin:</span><span class="sxs-lookup"><span data-stu-id="f0d2a-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="f0d2a-123">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-123">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="f0d2a-124">`NavMenu`Bileşen ( `Shared/NavMenu.razor` ) uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-124">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="f0d2a-125">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-125">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="f0d2a-126">`<NavLink>` `Todo` Aşağıdaki liste öğesi işaretlemesini, dosyadaki var olan liste öğelerinin altına ekleyerek bileşen için bir öğe ekleyin `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-126">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="f0d2a-127">`dotnet run`Klasörden komut kabuğu 'ndaki komutu yürüterek uygulamayı derleyin ve çalıştırın `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-127">Build and run the app by executing the `dotnet run` command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="f0d2a-128">Bileşen bağlantısının çalıştığından emin olmak için yeni Todo sayfasını ziyaret edin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-128">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="f0d2a-129">`TodoItem.cs` `TodoList` Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin (klasör) köküne bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-129">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="f0d2a-130">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-130">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="f0d2a-131">Bileşene geri dön `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f0d2a-131">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="f0d2a-132">Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-132">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="f0d2a-133">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-133">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f0d2a-134">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-134">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="f0d2a-135">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-135">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="f0d2a-136">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-136">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="f0d2a-137">Komut kabuğundan çalışan uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-137">Stop the running app in the command shell.</span></span> <span data-ttu-id="f0d2a-138">Birçok komut kabuklarını, <kbd>Ctrl</kbd> + bir uygulamayı durdurmak için CTRL<kbd>c</kbd> klavye komutunu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-138">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span> <span data-ttu-id="f0d2a-139">Komutuyla uygulamayı yeniden derleyin ve çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-139">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="f0d2a-140">**`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye kablolu olmadığı için hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-140">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="f0d2a-141">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-141">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="f0d2a-142">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="f0d2a-142">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="f0d2a-143">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-143">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="f0d2a-144">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-144">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f0d2a-145">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="f0d2a-145">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="f0d2a-146">Komut kabuğundan çalışan uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-146">Stop the running app in the command shell.</span></span> <span data-ttu-id="f0d2a-147">Komutuyla uygulamayı yeniden derleyin ve çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-147">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="f0d2a-148">Yeni kodu test etmek için Todo listesine bazı Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-148">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="f0d2a-149">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-149">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f0d2a-150">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-150">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f0d2a-151">Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-151">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="f0d2a-152">Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).</span><span class="sxs-lookup"><span data-stu-id="f0d2a-152">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="f0d2a-153">Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="f0d2a-153">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="f0d2a-154">Komut kabuğundan çalışan uygulamayı durdurun.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-154">Stop the running app in the command shell.</span></span> <span data-ttu-id="f0d2a-155">Komutuyla uygulamayı yeniden derleyin ve çalıştırın `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="f0d2a-155">Rebuild and run the app with the `dotnet run` command.</span></span> <span data-ttu-id="f0d2a-156">Yeni kodu test etmek için Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f0d2a-156">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f0d2a-157">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="f0d2a-157">Next steps</span></span>

<span data-ttu-id="f0d2a-158">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="f0d2a-158">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f0d2a-159">Yapılacaklar listesi :::no-loc(Blazor)::: uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="f0d2a-159">Create a todo list :::no-loc(Blazor)::: app project</span></span>
> * <span data-ttu-id="f0d2a-160">:::no-loc(Razor):::Bileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="f0d2a-160">Modify :::no-loc(Razor)::: components</span></span>
> * <span data-ttu-id="f0d2a-161">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="f0d2a-161">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f0d2a-162">Bir uygulamada yönlendirmeyi kullanma :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="f0d2a-162">Use routing in a :::no-loc(Blazor)::: app</span></span>

<span data-ttu-id="f0d2a-163">ASP.NET Core araçları hakkında bilgi edinin :::no-loc(Blazor)::: :</span><span class="sxs-lookup"><span data-stu-id="f0d2a-163">Learn about tooling for ASP.NET Core :::no-loc(Blazor)::::</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
