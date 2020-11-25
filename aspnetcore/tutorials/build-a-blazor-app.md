---
title: BlazorYapılacaklar listesi uygulaması oluşturma
author: guardrex
description: Bir Blazor uygulamayı adım adım oluşturun.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870444"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="a1c38-103">BlazorYapılacaklar listesi uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a1c38-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="a1c38-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="a1c38-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a1c38-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="a1c38-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="a1c38-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a1c38-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a1c38-107">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="a1c38-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a1c38-108">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="a1c38-108">Modify Razor components</span></span>
> * <span data-ttu-id="a1c38-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="a1c38-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a1c38-110">Bir uygulamada yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="a1c38-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="a1c38-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="a1c38-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a1c38-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a1c38-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="a1c38-113">Yapılacaklar listesi uygulaması oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="a1c38-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="a1c38-114">BlazorKomut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="a1c38-115">Yukarıdaki komut, uygulamayı tutmak için adlı bir klasör oluşturur `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="a1c38-116">`TodoList`Klasör, projenin *kök klasörüdür* .</span><span class="sxs-lookup"><span data-stu-id="a1c38-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="a1c38-117">`TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:</span><span class="sxs-lookup"><span data-stu-id="a1c38-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="a1c38-118">`Todo` Razor Aşağıdaki komutu kullanarak klasördeki uygulamaya yeni bir bileşen ekleyin `Pages` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="a1c38-119">Razor bileşen dosyası adları, büyük harfle bir ilk harf gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a1c38-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="a1c38-120">Klasörünü açın `Pages` ve `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="a1c38-121">Dosya adı olmalıdır `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="a1c38-122">İçinde `Pages/Todo.razor` bileşen için ilk biçimlendirmeyi belirtin:</span><span class="sxs-lookup"><span data-stu-id="a1c38-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="a1c38-123">`Pages/Todo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="a1c38-124">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a1c38-125">`NavMenu`Bileşen ( `Shared/NavMenu.razor` ) uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a1c38-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="a1c38-126">Düzenler, uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="a1c38-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="a1c38-127">`<NavLink>` `Todo` Aşağıdaki liste öğesi işaretlemesini, dosyadaki var olan liste öğelerinin altına ekleyerek bileşen için bir öğe ekleyin `Shared/NavMenu.razor` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="a1c38-128">`Shared/NavMenu.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="a1c38-129">[`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)Klasörden komut kabuğu 'ndaki komutu yürüterek uygulamayı derleyin ve çalıştırın `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="a1c38-130">`https://localhost:5001/todo`Bileşen için kenar çubuğu Gezinti bağlantısının çalışıp çalışmadığını doğrulamak için, yeni Todo sayfasını ziyaret edin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="a1c38-131">`TodoItem.cs` `TodoList` Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin (klasör) köküne bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="a1c38-132">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="a1c38-133">Bileşene geri dön `Todo` ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a1c38-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="a1c38-134">Bir bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="a1c38-135">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a1c38-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a1c38-136">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="a1c38-137">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a1c38-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a1c38-138">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="a1c38-139">`TodoItem.cs`Dosyayı ve güncelleştirilmiş `Pages/Todo.razor` dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a1c38-140">Komut kabuğunda, dosyalar kaydedildiğinde uygulama otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a1c38-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="a1c38-141">Tarayıcı, uygulamanın bağlantısını geçici olarak kaybeder ve bağlantı yeniden oluşturulduğunda sayfayı yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="a1c38-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="a1c38-142">**`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye eklenmediğinden hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="a1c38-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="a1c38-143">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğme seçimleri için kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="a1c38-144">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="a1c38-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="a1c38-145">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğunun üst kısmına bir dize alanı ekleyin `@code` ve bunu `bind` , öğesindeki özniteliği kullanarak metin girişinin değerine bağlayın `<input>` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a1c38-146">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="a1c38-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a1c38-147">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="a1c38-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="a1c38-148">`Pages/ToDo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a1c38-149">Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a1c38-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a1c38-150">Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="a1c38-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a1c38-151">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="a1c38-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a1c38-152">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="a1c38-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a1c38-153">Öğesine göre `@todo.Title` bir `<input>` öğeye geç `@todo.Title` :</span><span class="sxs-lookup"><span data-stu-id="a1c38-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="a1c38-154">Bu değerlerin bağlandığını doğrulamak için `<h3>` üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının sayısını gösterecek şekilde güncelleştirin ( `IsDone` yani `false` ).</span><span class="sxs-lookup"><span data-stu-id="a1c38-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a1c38-155">Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a1c38-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="a1c38-156">`Pages/ToDo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a1c38-157">Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a1c38-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a1c38-158">Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="a1c38-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a1c38-159">Yeni kodu test etmek için Todo öğeleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a1c38-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="a1c38-160">İşiniz bittiğinde, komut kabuğu 'nda uygulamayı kapatın.</span><span class="sxs-lookup"><span data-stu-id="a1c38-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="a1c38-161">Birçok komut kabuklarını, <kbd>Ctrl</kbd> + bir uygulamayı durdurmak için CTRL<kbd>c</kbd> klavye komutunu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a1c38-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a1c38-162">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="a1c38-162">Next steps</span></span>

<span data-ttu-id="a1c38-163">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="a1c38-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a1c38-164">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="a1c38-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a1c38-165">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="a1c38-165">Modify Razor components</span></span>
> * <span data-ttu-id="a1c38-166">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="a1c38-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a1c38-167">Bir uygulamada yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="a1c38-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="a1c38-168">ASP.NET Core araçları hakkında bilgi edinin Blazor :</span><span class="sxs-lookup"><span data-stu-id="a1c38-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
