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
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="dd339-103">BlazorYapılacaklar listesi uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="dd339-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="dd339-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="dd339-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dd339-105">Bu öğreticide, bir uygulamayı nasıl oluşturabileceğiniz ve değiştireceğiniz gösterilmektedir Blazor .</span><span class="sxs-lookup"><span data-stu-id="dd339-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="dd339-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="dd339-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dd339-107">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="dd339-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="dd339-108">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="dd339-108">Modify Razor components</span></span>
> * <span data-ttu-id="dd339-109">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="dd339-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="dd339-110">Bir uygulamada yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="dd339-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="dd339-111">Bu öğreticinin sonunda, çalışan bir yapılacaklar listesi uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="dd339-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dd339-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="dd339-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="dd339-113">Yapılacaklar listesi uygulaması oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="dd339-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="dd339-114">BlazorKomut kabuğunda adlı yeni bir uygulama oluşturun `TodoList` :</span><span class="sxs-lookup"><span data-stu-id="dd339-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="dd339-115">Yukarıdaki komut, `TodoList` uygulamayı tutma seçeneğiyle adlı bir klasör oluşturur `-o|--output` .</span><span class="sxs-lookup"><span data-stu-id="dd339-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="dd339-116">`TodoList`Klasör, projenin *kök klasörüdür* .</span><span class="sxs-lookup"><span data-stu-id="dd339-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="dd339-117">`TodoList`Aşağıdaki komutla klasörleri klasöre değiştirin:</span><span class="sxs-lookup"><span data-stu-id="dd339-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="dd339-118">`Todo` Razor Aşağıdaki komutu kullanarak uygulamaya yeni bir bileşen ekleyin:</span><span class="sxs-lookup"><span data-stu-id="dd339-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="dd339-119">`-n|--name`Yukarıdaki komutta seçeneği, yeni bileşenin adını belirtir Razor .</span><span class="sxs-lookup"><span data-stu-id="dd339-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="dd339-120">Yeni bileşen, `Pages` seçeneğiyle birlikte proje klasöründe oluşturulur `-o|--output` .</span><span class="sxs-lookup"><span data-stu-id="dd339-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="dd339-121">Razor bileşen dosyası adları, büyük harfle bir ilk harf gerektirir.</span><span class="sxs-lookup"><span data-stu-id="dd339-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="dd339-122">Klasörünü açın `Pages` ve `Todo` bileşen dosyası adının büyük harfle başlatıldığını onaylayın `T` .</span><span class="sxs-lookup"><span data-stu-id="dd339-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="dd339-123">Dosya adı olmalıdır `Todo.razor` .</span><span class="sxs-lookup"><span data-stu-id="dd339-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="dd339-124">`Todo`Bileşeni herhangi bir dosya düzenleyicisinde açın ve `@page` Razor göreli URL 'si ile dosyanın en üstüne bir yönerge ekleyin `/todo` .</span><span class="sxs-lookup"><span data-stu-id="dd339-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="dd339-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="dd339-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   <span data-ttu-id="dd339-126">`Pages/Todo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="dd339-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="dd339-127">`Todo`Bileşeni gezinti çubuğuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dd339-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="dd339-128">`NavMenu`Bileşen, uygulamanın düzeninde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dd339-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="dd339-129">Düzenler, bir uygulamadaki içeriğin çoğaltılmasını önlemenize olanak sağlayan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="dd339-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="dd339-130">Bileşen, `NavLink` uygulama tarafından bileşen URL 'si yüklendiğinde uygulamanın kullanıcı arabiriminde bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="dd339-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="dd339-131">Bileşenin sırasız listesinde ( `<ul>...</ul>` ) `NavMenu` , bileşen için aşağıdaki liste öğesini ( `<li>...</li>` ) ve `NavLink` bileşenini ekleyin `Todo` .</span><span class="sxs-lookup"><span data-stu-id="dd339-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="dd339-132">`Shared/NavMenu.razor` içinde:</span><span class="sxs-lookup"><span data-stu-id="dd339-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="dd339-133">`Shared/NavMenu.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="dd339-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="dd339-134">[`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)Klasörden komut kabuğu 'ndaki komutu yürüterek uygulamayı derleyin ve çalıştırın `TodoList` .</span><span class="sxs-lookup"><span data-stu-id="dd339-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="dd339-135">Uygulama çalıştırıldıktan sonra, **`Todo`** uygulamanın gezinti çubuğundaki bağlantıyı seçerek yeni Todo sayfasını ziyaret edin `/todo` .</span><span class="sxs-lookup"><span data-stu-id="dd339-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="dd339-136">Uygulamadan komut kabuğunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dd339-136">Leave the app running the command shell.</span></span> <span data-ttu-id="dd339-137">Bir dosya her kaydedildiğinde, uygulama otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dd339-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="dd339-138">Tarayıcı, derleme ve yeniden başlatma sırasında uygulamayla bağlantısını geçici olarak kaybeder.</span><span class="sxs-lookup"><span data-stu-id="dd339-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="dd339-139">Bağlantı yeniden oluşturulduğunda Tarayıcıdaki sayfa otomatik olarak yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="dd339-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="dd339-140">`TodoItem.cs` `TodoList` Bir Todo öğesini temsil eden bir sınıfı tutmak için projenin (klasör) köküne bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dd339-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="dd339-141">Sınıfı için aşağıdaki C# kodunu kullanın `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="dd339-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="dd339-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="dd339-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > <span data-ttu-id="dd339-143">Dosyayı ve sınıfı oluşturmak için Visual Studio kullanıyorsanız `ToDoItem.cs` `ToDoItem` aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="dd339-143">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="dd339-144">Sınıf için Visual Studio 'Nun oluşturduğu ad alanını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="dd339-144">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="dd339-145">Yukarıdaki kod bloğundaki **Kopyala** düğmesini kullanın ve Visual Studio 'nun oluşturduğu dosyanın tüm içeriğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="dd339-145">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="dd339-146">`Todo`Bileşene dönün ve aşağıdaki görevleri gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="dd339-146">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="dd339-147">Bloktaki Todo öğeleri için bir alan ekleyin `@code` .</span><span class="sxs-lookup"><span data-stu-id="dd339-147">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="dd339-148">`Todo`Bileşen, Todo listesinin durumunu korumak için bu alanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="dd339-148">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="dd339-149">`foreach`Her Todo öğesini bir liste öğesi () olarak işlemek için sıralanmamış liste işaretlemesi ve bir döngüsü ekleyin `<li>` .</span><span class="sxs-lookup"><span data-stu-id="dd339-149">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="dd339-150">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="dd339-150">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="dd339-151">Uygulama, listeye Todo öğeleri eklemek için Kullanıcı arabirimi öğeleri gerektirir.</span><span class="sxs-lookup"><span data-stu-id="dd339-151">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="dd339-152">Sıralanmamış listenin () altına bir metin girişi ( `<input>` ) ve bir düğme () ekleyin `<button>` `<ul>...</ul>` :</span><span class="sxs-lookup"><span data-stu-id="dd339-152">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. <span data-ttu-id="dd339-153">`TodoItem.cs`Dosyayı ve güncelleştirilmiş `Pages/Todo.razor` dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="dd339-153">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="dd339-154">Komut kabuğunda, dosyalar kaydedildiğinde uygulama otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dd339-154">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="dd339-155">Tarayıcı, uygulamanın bağlantısını geçici olarak kaybeder ve bağlantı yeniden oluşturulduğunda sayfayı yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="dd339-155">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="dd339-156">**`Add todo`** Düğme seçildiğinde, bir olay işleyicisi düğmeye eklenmediğinden hiçbir şey olmaz.</span><span class="sxs-lookup"><span data-stu-id="dd339-156">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="dd339-157">Bileşene bir `AddTodo` Yöntem ekleyin `Todo` ve özniteliği kullanarak düğmenin metodunu kaydedin `@onclick` .</span><span class="sxs-lookup"><span data-stu-id="dd339-157">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="dd339-158">`AddTodo`C# yöntemi, düğme seçildiğinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="dd339-158">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="dd339-159">Yeni Todo öğesinin başlığını almak için, `newTodo` bloğun üst kısmına bir dize alanı ekleyin `@code` :</span><span class="sxs-lookup"><span data-stu-id="dd339-159">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   <span data-ttu-id="dd339-160">`<input>`Özniteliği ile bağlanacak metin öğesini değiştirin `newTodo` `@bind` :</span><span class="sxs-lookup"><span data-stu-id="dd339-160">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="dd339-161">`AddTodo`Belirtilen başlığa sahip öğesini listeye eklemek için yöntemini güncelleştirin `TodoItem` .</span><span class="sxs-lookup"><span data-stu-id="dd339-161">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="dd339-162">Metin girişinin değerini `newTodo` boş bir dizeye ayarlayarak temizleyin:</span><span class="sxs-lookup"><span data-stu-id="dd339-162">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. <span data-ttu-id="dd339-163">`Pages/Todo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="dd339-163">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="dd339-164">Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dd339-164">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="dd339-165">Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="dd339-165">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="dd339-166">Her Todo öğesi için başlık metni düzenlenebilir hale getirilebilir ve bir onay kutusu kullanıcının tamamlanmış öğeleri izlemesine yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="dd339-166">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="dd339-167">Her Todo öğesi için bir onay kutusu girişi ekleyin ve değerini `IsDone` özelliğine bağlayın.</span><span class="sxs-lookup"><span data-stu-id="dd339-167">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="dd339-168">`@todo.Title` `<input>` İle bağlantılı bir öğeye geçin `todo.Title` `@bind` :</span><span class="sxs-lookup"><span data-stu-id="dd339-168">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. <span data-ttu-id="dd339-169">`<h3>`Üst bilgiyi, tamamlanmamış olan Todo öğelerinin sayısının (yani) sayısını gösterecek şekilde güncelleştirin `IsDone` `false` .</span><span class="sxs-lookup"><span data-stu-id="dd339-169">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="dd339-170">Tamamlanan `Todo` bileşen ( `Pages/Todo.razor` ):</span><span class="sxs-lookup"><span data-stu-id="dd339-170">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="dd339-171">`Pages/Todo.razor` dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="dd339-171">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="dd339-172">Uygulama, komut kabuğu 'nda otomatik olarak yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dd339-172">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="dd339-173">Tarayıcı uygulamaya yeniden bağlandığında sayfa tarayıcıya yeniden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="dd339-173">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="dd339-174">Bileşeni test etmek için öğe ekleyin, öğeleri düzenleyin ve Todo öğelerini işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="dd339-174">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="dd339-175">İşiniz bittiğinde, komut kabuğu 'nda uygulamayı kapatın.</span><span class="sxs-lookup"><span data-stu-id="dd339-175">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="dd339-176">Birçok komut kabuklarını, <kbd></kbd> + bir uygulamayı durdurmak için CTRL<kbd>c</kbd> klavye komutunu kabul eder.</span><span class="sxs-lookup"><span data-stu-id="dd339-176">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dd339-177">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="dd339-177">Next steps</span></span>

<span data-ttu-id="dd339-178">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="dd339-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dd339-179">Yapılacaklar listesi Blazor uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="dd339-179">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="dd339-180">RazorBileşenleri Değiştir</span><span class="sxs-lookup"><span data-stu-id="dd339-180">Modify Razor components</span></span>
> * <span data-ttu-id="dd339-181">Bileşenlerde olay işleme ve veri bağlamayı kullanma</span><span class="sxs-lookup"><span data-stu-id="dd339-181">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="dd339-182">Bir uygulamada yönlendirmeyi kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="dd339-182">Use routing in a Blazor app</span></span>

<span data-ttu-id="dd339-183">ASP.NET Core araçları hakkında bilgi edinin Blazor :</span><span class="sxs-lookup"><span data-stu-id="dd339-183">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
