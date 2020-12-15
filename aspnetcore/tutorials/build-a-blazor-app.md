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
ms.openlocfilehash: 87626ff30589de82a04c95634fc0dcbcf2eeac18
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507013"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="d167e-103">BlazorYapılacaklar listesi uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="d167e-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="d167e-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="d167e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d167e-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="d167e-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="d167e-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d167e-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d167e-107">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="d167e-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="d167e-108">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="d167e-108">Modify Razor components</span></span>
> * <span data-ttu-id="d167e-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="d167e-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="d167e-110">Bir uygulamada yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="d167e-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="d167e-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="d167e-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d167e-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="d167e-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="d167e-113">Yapılacaklar listesi uygulaması oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="d167e-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="d167e-114">BlazorKomut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :</span><span class="sxs-lookup"><span data-stu-id="d167e-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="d167e-115">Yukarıdaki komut, `TodoList` uygulamayı tutma seçeneğiyle adlı bir klasör oluşturur `-o|--output` .</span><span class="sxs-lookup"><span data-stu-id="d167e-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="d167e-116">`TodoList`Klasör, projenin *kök klasörüdür* .</span><span class="sxs-lookup"><span data-stu-id="d167e-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="d167e-117">`TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d167e-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="d167e-118">`Todo` Razor Aşağıdaki komutu kullanarak uygulamaya yeni bir bileşen ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d167e-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="d167e-119">`-n|--name`Yukarıdaki komutta seçeneği, yeni bileşenin adını belirtir Razor .</span><span class="sxs-lookup"><span data-stu-id="d167e-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="d167e-120">Yeni bileşen, `Pages` seçeneğiyle birlikte proje klasöründe oluşturulur `-o|--output` .</span><span class="sxs-lookup"><span data-stu-id="d167e-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="d167e-121">Razor bileşen dosyası adları, büyük harfle bir ilk harf gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d167e-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="d167e-122">Klasörünü açın `Pages` ve `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .</span><span class="sxs-lookup"><span data-stu-id="d167e-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="d167e-123">Dosya adı olmalıdır `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="d167e-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="d167e-124">`Todo`Bileşeni herhangi bir dosya düzenleyicisinde açın ve `@page` Razor göreli URL 'si ile dosyanın en üstüne bir yönerge ekleyin `/todo` .</span><span class="sxs-lookup"><span data-stu-id="d167e-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="d167e-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="d167e-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo0.razor?highlight=1)]

   <span data-ttu-id="d167e-126">`Pages/Todo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d167e-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="d167e-127">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d167e-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="d167e-128">`NavMenu`Bileşen, uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d167e-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="d167e-129">Düzenler, bir uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="d167e-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="d167e-130">Bileşen, `NavLink` uygulama tarafından bileşen URL 'si yüklendiğinde uygulamanın kullanıcı arabiriminde bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="d167e-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="d167e-131">Bileşenin sırasız listesinde ( `<ul>...</ul>` ) `NavMenu` , bileşen için aşağıdaki liste öğesini ( `<li>...</li>` ) ve `NavLink` bileşenini ekleyin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="d167e-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="d167e-132">`Shared/NavMenu.razor` içinde:</span><span class="sxs-lookup"><span data-stu-id="d167e-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="d167e-133">`Shared/NavMenu.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d167e-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="d167e-134">[`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)Klasörden komut kabuğu 'ndaki komutu yürüterek uygulamayı derleyin ve çalıştırın `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="d167e-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="d167e-135">Uygulama çalıştırıldıktan sonra, **`Todo`** uygulamanın gezinti çubuğundaki bağlantıyı seçerek yeni Todo sayfasını ziyaret edin `/todo` .</span><span class="sxs-lookup"><span data-stu-id="d167e-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="d167e-136">Uygulamadan komut kabuğunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d167e-136">Leave the app running the command shell.</span></span> <span data-ttu-id="d167e-137">Bir dosya her kaydedildiğinde, uygulama otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d167e-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="d167e-138">Tarayıcı, derleme ve yeniden başlatma sırasında uygulamayla bağlantısını geçici olarak kaybeder.</span><span class="sxs-lookup"><span data-stu-id="d167e-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="d167e-139">Bağlantı yeniden oluşturulduğunda Tarayıcıdaki sayfa otomatik olarak yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d167e-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="d167e-140">`TodoItem.cs` `TodoList` Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin (klasör) köküne bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d167e-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="d167e-141">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="d167e-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="d167e-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="d167e-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="d167e-143">`Todo`Bileşene dönün ve aşağıdaki görevleri gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="d167e-143">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="d167e-144">Bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="d167e-144">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="d167e-145">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d167e-145">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d167e-146">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="d167e-146">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="d167e-147">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="d167e-147">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="d167e-148">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d167e-148">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="d167e-149">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="d167e-149">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="d167e-150">`TodoItem.cs`Dosyayı ve güncelleştirilmiş `Pages/Todo.razor` dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d167e-150">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="d167e-151">Komut kabuğunda, dosyalar kaydedildiğinde uygulama otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d167e-151">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="d167e-152">Tarayıcı, uygulamanın bağlantısını geçici olarak kaybeder ve bağlantı yeniden oluşturulduğunda sayfayı yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="d167e-152">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="d167e-153">**`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye eklenmediğinden hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="d167e-153">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="d167e-154">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğmenin metodunu kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="d167e-154">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="d167e-155">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="d167e-155">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="d167e-156">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğun üst kısmına bir dize alanı ekleyin `@code` :</span><span class="sxs-lookup"><span data-stu-id="d167e-156">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   <span data-ttu-id="d167e-157">`<input>`Özniteliği ile bağlanacak metin öğesini değiştirin `newTodo` `@bind` :</span><span class="sxs-lookup"><span data-stu-id="d167e-157">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="d167e-158">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="d167e-158">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d167e-159">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="d167e-159">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="d167e-160">`Pages/ToDo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d167e-160">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="d167e-161">Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d167e-161">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="d167e-162">Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d167e-162">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="d167e-163">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d167e-163">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d167e-164">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="d167e-164">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d167e-165">`@todo.Title` `<input>` İle bağlantılı bir öğeye geçin `todo.Title` `@bind` :</span><span class="sxs-lookup"><span data-stu-id="d167e-165">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=4-7)]

1. <span data-ttu-id="d167e-166">`<h3>`Üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının (yani) sayısını gösterecek şekilde güncelleştirin `IsDone` `false` .</span><span class="sxs-lookup"><span data-stu-id="d167e-166">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="d167e-167">Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="d167e-167">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="d167e-168">`Pages/ToDo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d167e-168">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="d167e-169">Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d167e-169">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="d167e-170">Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d167e-170">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="d167e-171">Bileşeni test etmek için öğe ekleyin, öğeleri düzenleyin ve Todo öğelerini işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="d167e-171">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="d167e-172">İşiniz bittiğinde, komut kabuğu 'nda uygulamayı kapatın.</span><span class="sxs-lookup"><span data-stu-id="d167e-172">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="d167e-173">Birçok komut kabuklarını, <kbd></kbd> + bir uygulamayı durdurmak için CTRL<kbd>c</kbd> klavye komutunu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d167e-173">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d167e-174">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="d167e-174">Next steps</span></span>

<span data-ttu-id="d167e-175">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="d167e-175">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d167e-176">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="d167e-176">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="d167e-177">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="d167e-177">Modify Razor components</span></span>
> * <span data-ttu-id="d167e-178">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="d167e-178">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="d167e-179">Bir uygulamada yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="d167e-179">Use routing in a Blazor app</span></span>

<span data-ttu-id="d167e-180">ASP.NET Core araçları hakkında bilgi edinin Blazor :</span><span class="sxs-lookup"><span data-stu-id="d167e-180">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
