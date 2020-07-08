---
title: 'Öğretici: ilgili verileri güncelleştirme-ASP.NET MVC EF Core'
description: Bu öğreticide, yabancı anahtar alanlarını ve gezinti özelliklerini güncelleştirerek ilgili verileri güncelleştireceksiniz.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 066bebf95a941fca5e7cc175c4c0d6d56abc9cb5
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060065"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="5707a-103">Öğretici: ilgili verileri güncelleştirme-ASP.NET MVC EF Core</span><span class="sxs-lookup"><span data-stu-id="5707a-103">Tutorial: Update related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="5707a-104">Önceki öğreticide ilgili verileri görüntülediyseniz; Bu öğreticide, yabancı anahtar alanlarını ve gezinti özelliklerini güncelleştirerek ilgili verileri güncelleştireceksiniz.</span><span class="sxs-lookup"><span data-stu-id="5707a-104">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="5707a-105">Aşağıdaki çizimlerde, üzerinde çalıştığınız sayfaların bazıları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="5707a-105">The following illustrations show some of the pages that you'll work with.</span></span>

![Kurs düzenleme sayfası](update-related-data/_static/course-edit.png)

![Eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="5707a-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="5707a-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5707a-109">Kurslar sayfalarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-109">Customize Courses pages</span></span>
> * <span data-ttu-id="5707a-110">Eğitmenler düzenleme sayfası ekle</span><span class="sxs-lookup"><span data-stu-id="5707a-110">Add Instructors Edit page</span></span>
> * <span data-ttu-id="5707a-111">Düzenleme sayfasına kurslar ekleyin</span><span class="sxs-lookup"><span data-stu-id="5707a-111">Add courses to Edit page</span></span>
> * <span data-ttu-id="5707a-112">Güncelleştirme silme sayfası</span><span class="sxs-lookup"><span data-stu-id="5707a-112">Update Delete page</span></span>
> * <span data-ttu-id="5707a-113">Sayfa oluşturmak için Office konumu ve kurslar ekleme</span><span class="sxs-lookup"><span data-stu-id="5707a-113">Add office location and courses to Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5707a-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="5707a-114">Prerequisites</span></span>

* [<span data-ttu-id="5707a-115">İlgili verileri okuma</span><span class="sxs-lookup"><span data-stu-id="5707a-115">Read related data</span></span>](read-related-data.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="5707a-116">Kurslar sayfalarını özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-116">Customize Courses pages</span></span>

<span data-ttu-id="5707a-117">Yeni bir kurs varlığı oluşturulduğunda, mevcut bir departmanla bir ilişkisi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5707a-117">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="5707a-118">Bunu kolaylaştırmak için, yapı iskelesi kodu denetleyici yöntemlerini içerir ve departmanı seçmeye yönelik bir açılan liste içeren görünümler oluşturup düzenleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5707a-118">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="5707a-119">Açılan liste, `Course.DepartmentID` yabancı anahtar özelliğini ayarlar ve `Department` ilgili departman varlığı ile gezinti özelliğini yüklemek için tüm Entity Framework gereksinimlidir.</span><span class="sxs-lookup"><span data-stu-id="5707a-119">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="5707a-120">Scafkatmış kodu kullanacaksınız, ancak hata işleme eklemek ve açılan listeyi sıralamak için biraz değişiklik yapacaksınız.</span><span class="sxs-lookup"><span data-stu-id="5707a-120">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="5707a-121">*CoursesController.cs*' de, dört oluşturma ve düzenleme yöntemini silin ve bunları şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5707a-121">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="5707a-122">`Edit`HttpPost yönteminden sonra, açılan liste için departman bilgisini yükleyen yeni bir yöntem oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5707a-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="5707a-123">`PopulateDepartmentsDropDownList`Yöntemi ada göre sıralanmış tüm bölümlerin listesini alır, `SelectList` açılan liste için bir koleksiyon oluşturur ve koleksiyonu içindeki görünümüne geçirir `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="5707a-123">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="5707a-124">Yöntemi, `selectedDepartment` çağıran kodun, açılan liste işlendiğinde seçilecek öğeyi belirtmesini sağlayan isteğe bağlı parametresini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5707a-124">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="5707a-125">Görünüm, "DepartmentID" adını `<select>` etiket yardımcısından geçireceğini ve yardımcı sonra, `ViewBag` `SelectList` "DepartmentID" adlı bir nesneyi bulmak için bu adı bilecektir.</span><span class="sxs-lookup"><span data-stu-id="5707a-125">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="5707a-126">HttpGet `Create` yöntemi, `PopulateDepartmentsDropDownList` Seçilen öğeyi ayarlamadan yöntemi çağırır, çünkü yeni bir kurs için departman henüz kurulmadı:</span><span class="sxs-lookup"><span data-stu-id="5707a-126">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="5707a-127">HttpGet `Edit` yöntemi, düzenlenen kursa zaten atanmış olan DEPARTMANıN kimliğine bağlı olarak seçili öğeyi ayarlar:</span><span class="sxs-lookup"><span data-stu-id="5707a-127">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="5707a-128">Hem hem de için HttpPost yöntemleri, `Create` `Edit` bir hatadan sonra sayfayı yeniden görüntülerken seçili öğeyi ayarlayan kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="5707a-128">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="5707a-129">Bu, sayfa hata iletisini göstermek için yeniden görüntülendiğinde, seçilen departmanın seçili kalır olduğunu sağlar.</span><span class="sxs-lookup"><span data-stu-id="5707a-129">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="5707a-130">Ekleyemiyorum. Ayrıntı ve silme yöntemlerine AsNoTracking</span><span class="sxs-lookup"><span data-stu-id="5707a-130">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="5707a-131">Kurs ayrıntılarının ve sayfa silmenin performansını iyileştirmek için, `AsNoTracking` `Details` ve HttpGet yöntemlerine çağrı ekleyin `Delete` .</span><span class="sxs-lookup"><span data-stu-id="5707a-131">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="5707a-132">Kurs görünümlerini değiştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-132">Modify the Course views</span></span>

<span data-ttu-id="5707a-133">*Görünümler/kurslar/oluşturma. cshtml*'de, **Departman** açılan listesine bir "Departman Seç" seçeneği ekleyin, **DepartmentID** etiketini **bölüm**olarak değiştirin ve bir doğrulama iletisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-133">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="5707a-134">*Görünümler/kurslar/Düzenle. cshtml*'de, *Create. cshtml*içinde yaptığınız departman alanı için aynı değişikliği yapın.</span><span class="sxs-lookup"><span data-stu-id="5707a-134">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="5707a-135">Ayrıca, *Görünümler/kurslar/Düzenle. cshtml*'de **başlık** alanından önce bir kurs numarası alanı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-135">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="5707a-136">Kurs numarası birincil anahtar olduğundan, görüntülenir, ancak değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="5707a-136">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="5707a-137">`<input type="hidden">`Düzenleme görünümündeki kurs numarası için zaten gizli bir alan () var.</span><span class="sxs-lookup"><span data-stu-id="5707a-137">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="5707a-138">`<label>`Kullanıcı **düzenleme** sayfasında **Kaydet** ' i tıklattığında, bir etiket Yardımcısı ekleme, gizli alan gereksinimini ortadan kaldırmaz, çünkü</span><span class="sxs-lookup"><span data-stu-id="5707a-138">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="5707a-139">*Görünümler/kurslar/delete. cshtml*'de, üst kısımdaki bir kurs numarası alanı ekleyin ve bölüm kimliğini bölüm adı olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5707a-139">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="5707a-140">*Görünümler/kurslar/ayrıntılar. cshtml*'de, *delete. cshtml*için yaptığınız aynı değişikliği yapın.</span><span class="sxs-lookup"><span data-stu-id="5707a-140">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="5707a-141">Kurs sayfalarını test etme</span><span class="sxs-lookup"><span data-stu-id="5707a-141">Test the Course pages</span></span>

<span data-ttu-id="5707a-142">Uygulamayı çalıştırın, **Kurslar** sekmesini seçin, **Yeni oluştur**' a tıklayın ve yeni bir kurs için veri girin:</span><span class="sxs-lookup"><span data-stu-id="5707a-142">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Kurs sayfa oluştur](update-related-data/_static/course-create.png)

<span data-ttu-id="5707a-144">**Oluştur**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-144">Click **Create**.</span></span> <span data-ttu-id="5707a-145">Kurslar Dizin sayfası, listeye eklenen yeni kursla birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5707a-145">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="5707a-146">Dizin sayfası listesindeki departman adı, ilişkinin doğru şekilde oluşturulduğunu gösteren gezinti özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="5707a-146">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="5707a-147">Kurslar Dizin sayfasında bir kursa **Düzenle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-147">Click **Edit** on a course in the Courses Index page.</span></span>

![Kurs düzenleme sayfası](update-related-data/_static/course-edit.png)

<span data-ttu-id="5707a-149">Sayfadaki verileri değiştirin ve **Kaydet**' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-149">Change data on the page and click **Save**.</span></span> <span data-ttu-id="5707a-150">Kurslar Dizin sayfası, güncelleştirilmiş kurs verileriyle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="5707a-150">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-instructors-edit-page"></a><span data-ttu-id="5707a-151">Eğitmenler düzenleme sayfası ekle</span><span class="sxs-lookup"><span data-stu-id="5707a-151">Add Instructors Edit page</span></span>

<span data-ttu-id="5707a-152">Bir eğitmen kaydını düzenlediğinizde, eğitmenin Office atamasını güncelleştirebilmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="5707a-152">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="5707a-153">Eğitmen varlığı, OfficeAssignment varlığıyla bire sıfır veya-arasında bir ilişkiye sahiptir; bu, kodunuzun aşağıdaki durumları işlemesi gerektiği anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="5707a-153">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="5707a-154">Kullanıcı Office atamasını temizlediğinde ve başlangıçta bir değere sahipse, OfficeAssignment varlığını silin.</span><span class="sxs-lookup"><span data-stu-id="5707a-154">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="5707a-155">Kullanıcı bir Office atama değeri girerse ve başlangıçta boşsa, yeni bir OfficeAssignment varlığı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5707a-155">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="5707a-156">Kullanıcı bir Office atamasının değerini değiştirirse, var olan bir OfficeAssignment varlığındaki değeri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5707a-156">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="5707a-157">Eğitmenler denetleyicisini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-157">Update the Instructors controller</span></span>

<span data-ttu-id="5707a-158">*InstructorsController.cs*Içinde, HttpGet yöntemindeki kodu, `Edit` eğitmen varlığının `OfficeAssignment` gezinti özelliğini ve çağrılarını yükleyen şekilde değiştirin `AsNoTracking` :</span><span class="sxs-lookup"><span data-stu-id="5707a-158">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

<span data-ttu-id="5707a-159">HttpPost yöntemini, `Edit` Office atama güncelleştirmelerini işlemek için aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5707a-159">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="5707a-160">Kod şunları yapar:</span><span class="sxs-lookup"><span data-stu-id="5707a-160">The code does the following:</span></span>

* <span data-ttu-id="5707a-161">`EditPost`İmza artık HttpGet yöntemiyle aynı olduğundan `Edit` ( `ActionName` öznitelik, `/Edit/` URL 'nin hala kullanıldığını belirtir), yöntem adını olarak değiştirir.</span><span class="sxs-lookup"><span data-stu-id="5707a-161">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

* <span data-ttu-id="5707a-162">Gezinti özelliği için Eager yüklemesini kullanarak geçerli eğitmen varlığını veritabanından alır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="5707a-162">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="5707a-163">Bu, HttpGet yönteminde yaptığınız şeydir `Edit` .</span><span class="sxs-lookup"><span data-stu-id="5707a-163">This is the same as what you did in the HttpGet `Edit` method.</span></span>

* <span data-ttu-id="5707a-164">Alınan eğitmen varlığını model Ciltçideki değerlerle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="5707a-164">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="5707a-165">`TryUpdateModel`Aşırı yükleme, dahil etmek istediğiniz özellikleri bildirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="5707a-165">The `TryUpdateModel` overload enables you to declare the properties you want to include.</span></span> <span data-ttu-id="5707a-166">Bu, [ikinci öğreticide](crud.md)açıklandığı gibi, daha fazla nakletmeyi önler.</span><span class="sxs-lookup"><span data-stu-id="5707a-166">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* <span data-ttu-id="5707a-167">Office konumu boşsa, OfficeAssignment tablosundaki ilgili satırın silinebilmesi için eğitmen. OfficeAssignment özelliğini null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5707a-167">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* <span data-ttu-id="5707a-168">Değişiklikleri veritabanına kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5707a-168">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="5707a-169">Eğitmen düzenleme görünümünü güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-169">Update the Instructor Edit view</span></span>

<span data-ttu-id="5707a-170">*Görünümler/eğitmenler/Edit. cshtml*' de, **Kaydet** düğmesinin sonundaki Office konumunu düzenlemek için yeni bir alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5707a-170">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="5707a-171">Uygulamayı çalıştırın, **eğitmenler** sekmesini seçin ve ardından bir eğitmende **Düzenle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-171">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="5707a-172">**Office konumunu** değiştirin ve **Kaydet**' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-172">Change the **Office Location** and click **Save**.</span></span>

![Eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a><span data-ttu-id="5707a-174">Düzenleme sayfasına kurslar ekleyin</span><span class="sxs-lookup"><span data-stu-id="5707a-174">Add courses to Edit page</span></span>

<span data-ttu-id="5707a-175">Eğitmenler, istediğiniz sayıda kurs öğretebilir.</span><span class="sxs-lookup"><span data-stu-id="5707a-175">Instructors may teach any number of courses.</span></span> <span data-ttu-id="5707a-176">Artık, aşağıdaki ekran görüntüsünde gösterildiği gibi, bir grup onay kutusu kullanarak kurs atamalarını değiştirme özelliğini ekleyerek eğitmen düzenleme sayfasını geliştirirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5707a-176">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Kurslar ile eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="5707a-178">Kurs ve eğitmen varlıkları arasındaki ilişki çoktan çoğa olur.</span><span class="sxs-lookup"><span data-stu-id="5707a-178">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="5707a-179">İlişki eklemek ve kaldırmak için, kurs ekleme ve kurs, katılımcı varlık kümesine ekleme ve kaldırma.</span><span class="sxs-lookup"><span data-stu-id="5707a-179">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="5707a-180">Bir eğitmenin hangi kurslara atandığını değiştirmenize olanak sağlayan kullanıcı arabirimi bir grup onay kutusu olur.</span><span class="sxs-lookup"><span data-stu-id="5707a-180">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="5707a-181">Veritabanındaki her kurs için bir onay kutusu görüntülenir ve eğitmenin Şu anda atanmış olduğu yer seçilidir.</span><span class="sxs-lookup"><span data-stu-id="5707a-181">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="5707a-182">Kullanıcı kurs atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5707a-182">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="5707a-183">Kurs sayısı çok fazlaysa, büyük olasılıkla görünümde verileri göstermek için farklı bir yöntem kullanmak isteyeceksiniz, ancak ilişkiler oluşturmak veya silmek için bir JOIN varlığını işlemek için aynı yöntemi kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5707a-183">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="5707a-184">Eğitmenler denetleyicisini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-184">Update the Instructors controller</span></span>

<span data-ttu-id="5707a-185">Onay kutuları listesinin görünümüne veri sağlamak için bir görünüm modeli sınıfı kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="5707a-185">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="5707a-186">*SchoolViewModels* klasöründe *AssignedCourseData.cs* oluşturun ve mevcut kodu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="5707a-186">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="5707a-187">*InstructorsController.cs*' de, HttpGet `Edit` yöntemini aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5707a-187">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="5707a-188">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="5707a-188">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="5707a-189">Kod, gezinti özelliği için Eager yüklemesi ekler `Courses` ve `PopulateAssignedCourseData` `AssignedCourseData` görüntüleme modeli sınıfını kullanarak onay kutusu dizisi için bilgi sağlamak üzere yeni yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="5707a-189">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="5707a-190">Yöntemindeki kod, `PopulateAssignedCourseData` görünüm modeli sınıfını kullanarak bir kurs listesi yüklemek için tüm kurs varlıklarını okur.</span><span class="sxs-lookup"><span data-stu-id="5707a-190">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="5707a-191">Kod her kurs için, kursun, eğitmenin gezinti özelliğinde mevcut olup olmadığını denetler `Courses` .</span><span class="sxs-lookup"><span data-stu-id="5707a-191">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="5707a-192">Eğitmenin bir kurs atanıp atanmadığını denetlerken etkili arama oluşturmak için, eğitmene atanan kurslar bir `HashSet` koleksiyona konur.</span><span class="sxs-lookup"><span data-stu-id="5707a-192">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="5707a-193">, `Assigned` Eğitmenin atandığı kurslar için özelliği true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5707a-193">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="5707a-194">Görünüm, hangi onay kutularının seçili olarak gösterileceğini belirlemede bu özelliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="5707a-194">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="5707a-195">Son olarak, liste içindeki görünüme geçirilir `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="5707a-195">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="5707a-196">Sonra, Kullanıcı **Kaydet**' i tıklattığında yürütülen kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-196">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="5707a-197">`EditPost`Yöntemini aşağıdaki kodla değiştirin ve `Courses` eğitmen varlığının gezinti özelliğini güncelleştiren yeni bir yöntem ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-197">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="5707a-198">Yöntem imzası artık HttpGet `Edit` yönteminden farklıdır, bu nedenle Yöntem adı ' dan ' a değişir `EditPost` `Edit` .</span><span class="sxs-lookup"><span data-stu-id="5707a-198">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="5707a-199">Görünüm bir kurs varlıkları koleksiyonuna sahip olmadığından, model Bağlayıcısı gezinti özelliğini otomatik olarak güncelleştiremez `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="5707a-199">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="5707a-200">Gezinti özelliğini güncelleştirmek için model cildi kullanmak yerine `CourseAssignments` , bunu yeni yöntemde yapmanız gerekir `UpdateInstructorCourses` .</span><span class="sxs-lookup"><span data-stu-id="5707a-200">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="5707a-201">Bu nedenle, `CourseAssignments` özelliği model bağlamadan hariç bırakmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5707a-201">Therefore, you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="5707a-202">Bu, `TryUpdateModel` açık onay gerektiren ve ekleme listesinde olmayan aşırı yüklemeyi kullandığınız için çağıran kodda herhangi bir değişiklik yapılmasını gerektirmez `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="5707a-202">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload that requires explicit approval and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="5707a-203">Hiçbir onay kutusu seçili değilse, içindeki kod `UpdateInstructorCourses` `CourseAssignments` gezinti özelliğini boş bir koleksiyonla başlatır ve döndürür:</span><span class="sxs-lookup"><span data-stu-id="5707a-203">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="5707a-204">Kod daha sonra, veritabanındaki tüm kurslardan geçer ve bu her kursu, görünümde seçili olanlar ile ilgili olarak eğitmenin atandığı her bir kursa karşı denetler.</span><span class="sxs-lookup"><span data-stu-id="5707a-204">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="5707a-205">Etkili aramaları kolaylaştırmak için, ikinci iki koleksiyon `HashSet` nesnelerde depolanır.</span><span class="sxs-lookup"><span data-stu-id="5707a-205">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="5707a-206">Kurs onay kutusu seçilmişse ancak kurs, `Instructor.CourseAssignments` Gezinti özelliğinde değilse kurs, Gezinti özelliğindeki koleksiyona eklenir.</span><span class="sxs-lookup"><span data-stu-id="5707a-206">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="5707a-207">Kurs onay kutusu seçili değilse, ancak kurs `Instructor.CourseAssignments` gezinti özelliği ise, kurs, gezinti özelliğinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="5707a-207">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="5707a-208">Eğitmen görünümlerini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="5707a-208">Update the Instructor views</span></span>

<span data-ttu-id="5707a-209">*Görünümler/eğitmenler/Edit. cshtml*' de, **Courses** `div` **Office** alanı öğelerinden hemen sonra ve `div` **Kaydet** düğmesine ait öğeden önce aşağıdaki kodu ekleyerek bir dizi onay kutusu içeren bir kurslar alanı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-209">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="5707a-210">Kodu Visual Studio 'Ya yapıştırdığınızda, satır sonları kodu kesen bir şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5707a-210">When you paste the code in Visual Studio, line breaks might be changed in a way that breaks the code.</span></span> <span data-ttu-id="5707a-211">Kod yapıştırdıktan sonra farklı görünüyorsa, otomatik biçimlendirmeyi geri almak için CTRL + Z bir kez tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="5707a-211">If the code looks different after pasting, press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="5707a-212">Bu işlem satır sonlarını, burada gördüğünüz gibi görünmeleri için düzeltir.</span><span class="sxs-lookup"><span data-stu-id="5707a-212">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="5707a-213">Girintide kusursuz olması gerekmez, ancak `@:</tr><tr>` ,, `@:<td>` `@:</td>` ve `@:</tr>` çizgilerinin her biri gösterildiği gibi tek bir satırda olması gerekir, aksi halde bir çalışma zamanı hatası alırsınız.</span><span class="sxs-lookup"><span data-stu-id="5707a-213">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="5707a-214">Yeni kod bloğu seçiliyken, yeni kodu mevcut kodla hizalamak için üç kez Tab tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="5707a-214">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="5707a-215">Bu sorun Visual Studio 2019 ' de düzeltilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5707a-215">This problem is fixed in Visual Studio 2019.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="5707a-216">Bu kod, üç sütun içeren bir HTML tablosu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5707a-216">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="5707a-217">Her sütunda, bir onay kutusu ve ardından kurs numarası ve başlığından oluşan bir açıklamalı alt yazı bulunur.</span><span class="sxs-lookup"><span data-stu-id="5707a-217">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="5707a-218">Onay kutularının hepsi aynı ada ("Selectedkurslar") sahiptir, bu da model cilde bir grup olarak değerlendirilme bildirir.</span><span class="sxs-lookup"><span data-stu-id="5707a-218">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="5707a-219">Her onay kutusunun değer özniteliği değerine ayarlanır `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="5707a-219">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="5707a-220">Sayfa gönderildiğinde, model Ciltçi `CourseID` yalnızca seçili onay kutularının değerlerinden oluşan bir diziyi denetleyiciye geçirir.</span><span class="sxs-lookup"><span data-stu-id="5707a-220">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="5707a-221">Onay kutuları başlangıçta işlendiğinde, eğitmenin atandığı kurslara yönelik olanlar, işaretlenmiş özniteliklere sahiptir ve bunları seçer (denetlenen görüntüler).</span><span class="sxs-lookup"><span data-stu-id="5707a-221">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="5707a-222">Uygulamayı çalıştırın, **eğitmenler** sekmesini seçin ve ardından **düzenleme** sayfasını görmek Için bir eğitmende **Düzenle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-222">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Kurslar ile eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="5707a-224">Bazı kurs atamalarını değiştirin ve Kaydet ' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5707a-224">Change some course assignments and click Save.</span></span> <span data-ttu-id="5707a-225">Yaptığınız değişiklikler Dizin sayfasında yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="5707a-225">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="5707a-226">Eğitim kursu verilerini düzenlemek için buradaki yaklaşım, sınırlı sayıda kurs olduğunda iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5707a-226">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="5707a-227">Çok daha büyük olan koleksiyonlar için, farklı bir kullanıcı arabirimi ve farklı bir güncelleştirme yöntemi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5707a-227">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-delete-page"></a><span data-ttu-id="5707a-228">Güncelleştirme silme sayfası</span><span class="sxs-lookup"><span data-stu-id="5707a-228">Update Delete page</span></span>

<span data-ttu-id="5707a-229">*InstructorsController.cs*' de, `DeleteConfirmed` yöntemini silin ve yerine aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-229">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="5707a-230">Bu kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="5707a-230">This code makes the following changes:</span></span>

* <span data-ttu-id="5707a-231">, Gezinti özelliği için ekip yükleme yapar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="5707a-231">Does eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="5707a-232">Bunu eklemeniz gerekir, ilgili varlıklar hakkında bilgi sahibi olmaz `CourseAssignment` ve onları silmez.</span><span class="sxs-lookup"><span data-stu-id="5707a-232">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span> <span data-ttu-id="5707a-233">Bunları okumaktan kaçınmak için, veritabanında art arda silme yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5707a-233">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="5707a-234">Silinecek eğitmen herhangi bir departmanların Yöneticisi olarak atanırsa, bu departmanlardan eğitmen atamasını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="5707a-234">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-create-page"></a><span data-ttu-id="5707a-235">Sayfa oluşturmak için Office konumu ve kurslar ekleme</span><span class="sxs-lookup"><span data-stu-id="5707a-235">Add office location and courses to Create page</span></span>

<span data-ttu-id="5707a-236">*InstructorsController.cs*Içinde, HttpGet ve HttpPost yöntemlerini silin `Create` ve ardından aşağıdaki kodu kendi yerine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5707a-236">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="5707a-237">Bu kod, `Edit` ilk olarak hiçbir kurs seçilmemiş olması dışında, yöntemler için gördüğünüz yönteme benzer.</span><span class="sxs-lookup"><span data-stu-id="5707a-237">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="5707a-238">HttpGet `Create` yöntemi, `PopulateAssignedCourseData` ' ın seçili olması, ancak görünümde döngü için boş bir koleksiyon sağlamak üzere değil yöntemini çağırır `foreach` (Aksi halde görünüm kodu bir null başvuru özel durumu oluşturur).</span><span class="sxs-lookup"><span data-stu-id="5707a-238">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="5707a-239">HttpPost `Create` yöntemi, seçili her kursu `CourseAssignments` doğrulama hatalarını kontrol etmeden önce gezinti özelliğine ekler ve yeni eğitmeni veritabanına ekler.</span><span class="sxs-lookup"><span data-stu-id="5707a-239">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="5707a-240">Model hataları olduğunda (örneğin, Kullanıcı geçersiz bir tarih anahtarlanır) ve sayfa bir hata iletisiyle yeniden görüntülenirken, yapılan kurs seçimleri otomatik olarak geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="5707a-240">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="5707a-241">Gezinti özelliğine kurslar ekleyebilmesinin, `CourseAssignments` özelliği boş bir koleksiyon olarak başlatmak için sahip olmanız gerektiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="5707a-241">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="5707a-242">Bunu denetleyici kodunda yapmanın bir alternatifi olarak, aşağıdaki örnekte gösterildiği gibi, özellik alıcısının, koleksiyonu otomatik olarak oluşturmak üzere değiştirerek, bunu eğitmen modelinde yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5707a-242">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="5707a-243">`CourseAssignments`Özelliği bu şekilde değiştirirseniz, denetleyicideki açık özellik başlatma kodunu kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5707a-243">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="5707a-244">*Görünümler/eğitmen/oluşturma. cshtml*'de, gönder düğmesinden önce kurslar için bir Office konum metin kutusu ve onay kutuları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-244">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="5707a-245">Düzenleme sayfasında olduğu gibi, [dosyayı yapıştırdığınızda Visual Studio kodu yeniden biçimlendirdiğinden biçimlendirmeyi onarın](#notepad).</span><span class="sxs-lookup"><span data-stu-id="5707a-245">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="5707a-246">Uygulamayı çalıştırıp bir eğitmen oluşturarak test edin.</span><span class="sxs-lookup"><span data-stu-id="5707a-246">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="5707a-247">Işlemleri işleme</span><span class="sxs-lookup"><span data-stu-id="5707a-247">Handling Transactions</span></span>

<span data-ttu-id="5707a-248">[CRUD öğreticisinde](crud.md)açıklandığı gibi Entity Framework, işlemleri örtük olarak uygular.</span><span class="sxs-lookup"><span data-stu-id="5707a-248">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="5707a-249">Daha fazla denetime ihtiyacınız olan senaryolar için--örneğin, işlem içinde Entity Framework dışında yapılan işlemleri eklemek istiyorsanız, bkz. [işlemler](/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="5707a-249">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="5707a-250">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="5707a-250">Get the code</span></span>

[<span data-ttu-id="5707a-251">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-251">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="5707a-252">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="5707a-252">Next steps</span></span>

<span data-ttu-id="5707a-253">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="5707a-253">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5707a-254">Özelleştirilmiş Kurslar sayfaları</span><span class="sxs-lookup"><span data-stu-id="5707a-254">Customized Courses pages</span></span>
> * <span data-ttu-id="5707a-255">Eklenen eğitmenler düzenleme sayfası</span><span class="sxs-lookup"><span data-stu-id="5707a-255">Added Instructors Edit page</span></span>
> * <span data-ttu-id="5707a-256">Düzenleme sayfasına kurslar eklendi</span><span class="sxs-lookup"><span data-stu-id="5707a-256">Added courses to Edit page</span></span>
> * <span data-ttu-id="5707a-257">Silme sayfası güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="5707a-257">Updated Delete page</span></span>
> * <span data-ttu-id="5707a-258">Sayfa oluşturmak için Office konumu ve kurslar eklendi</span><span class="sxs-lookup"><span data-stu-id="5707a-258">Added office location and courses to Create page</span></span>

<span data-ttu-id="5707a-259">Eşzamanlılık çakışmalarını nasıl işleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="5707a-259">Advance to the next tutorial to learn how to handle concurrency conflicts.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5707a-260">Eşzamanlılık çakışmalarını işleme</span><span class="sxs-lookup"><span data-stu-id="5707a-260">Handle concurrency conflicts</span></span>](concurrency.md)
