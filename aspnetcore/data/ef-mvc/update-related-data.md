---
title: 'Öğretici: Güncelleme ile ilgili verileri - EF Core ile ASP.NET MVC'
description: Bu eğitimde, yabancı anahtar alanlarını ve gezinme özelliklerini güncelleştirerek ilgili verileri güncelleştireceksiniz.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 83d662659fb4bc7a2867be563e4e36927d2adafe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657145"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="23521-103">Öğretici: Güncelleme ile ilgili verileri - EF Core ile ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="23521-103">Tutorial: Update related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="23521-104">Önceki öğreticide ilgili verileri görüntülediğiniz; Bu eğitimde, yabancı anahtar alanlarını ve gezinme özelliklerini güncelleyerek ilgili verileri güncelleştireceksiniz.</span><span class="sxs-lookup"><span data-stu-id="23521-104">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="23521-105">Aşağıdaki çizimler, çalışacağınız bazı sayfaları gösterir.</span><span class="sxs-lookup"><span data-stu-id="23521-105">The following illustrations show some of the pages that you'll work with.</span></span>

![Kurs Edit sayfası](update-related-data/_static/course-edit.png)

![Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="23521-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="23521-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23521-109">Kursları Özelleştir sayfalarını özelleştir</span><span class="sxs-lookup"><span data-stu-id="23521-109">Customize Courses pages</span></span>
> * <span data-ttu-id="23521-110">Eğitmenler Ekle Sayfayı Edit</span><span class="sxs-lookup"><span data-stu-id="23521-110">Add Instructors Edit page</span></span>
> * <span data-ttu-id="23521-111">Ekle sayfasına ders ekle</span><span class="sxs-lookup"><span data-stu-id="23521-111">Add courses to Edit page</span></span>
> * <span data-ttu-id="23521-112">Sayfayı Sil'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="23521-112">Update Delete page</span></span>
> * <span data-ttu-id="23521-113">Sayfa Oluştur'a ofis konumu ve kurslar ekleme</span><span class="sxs-lookup"><span data-stu-id="23521-113">Add office location and courses to Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="23521-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="23521-114">Prerequisites</span></span>

* [<span data-ttu-id="23521-115">İlgili verileri okuma</span><span class="sxs-lookup"><span data-stu-id="23521-115">Read related data</span></span>](read-related-data.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="23521-116">Kursları Özelleştir sayfalarını özelleştir</span><span class="sxs-lookup"><span data-stu-id="23521-116">Customize Courses pages</span></span>

<span data-ttu-id="23521-117">Yeni bir ders varlığı oluşturulduğunda, varolan bir bölümle ilişkisi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="23521-117">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="23521-118">Bunu kolaylaştırmak için, iskelekodu denetleyici yöntemlerini içerir ve bölümü seçmek için açılır liste içeren görünümler oluşturun ve edit.</span><span class="sxs-lookup"><span data-stu-id="23521-118">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="23521-119">Açılan liste yabancı anahtar `Course.DepartmentID` özelliğini ayarlar ve gezinti özelliğini `Department` uygun Departman varlığıyla yüklemek için Entity Framework'ün ihtiyacı olan tek şey bu.</span><span class="sxs-lookup"><span data-stu-id="23521-119">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="23521-120">İskele kodu kullanırsınız, ancak hata işleme eklemek ve açılır listeyi sıralamak için biraz değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23521-120">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="23521-121">*CoursesController.cs,* dört Oluştur ve Düzenle yöntemini silin ve bunları aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="23521-121">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="23521-122">HttpPost `Edit` yönteminden sonra, açılan liste için departman bilgilerini yükleyen yeni bir yöntem oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23521-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="23521-123">Yöntem, `PopulateDepartmentsDropDownList` ada göre sıralanmış tüm bölümlerin `SelectList` listesini alır, açılır liste için bir koleksiyon oluşturur `ViewBag`ve koleksiyonu 'daki görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="23521-123">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="23521-124">Yöntem, arama kodunun açılır liste işlendiğinde seçilecek öğeyi belirtmesine olanak tanıyan isteğe bağlı `selectedDepartment` parametreyi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="23521-124">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="23521-125">`<select>` Görünüm, etiket yardımcısına "DepartmentID" adını geçirir ve yardımcı daha sonra "DepartmentID" adlı bir `ViewBag` `SelectList` nesnenin nesnesine bakmayı bilir.</span><span class="sxs-lookup"><span data-stu-id="23521-125">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="23521-126">Yeni bir `Create` kurs `PopulateDepartmentsDropDownList` için bölüm henüz kurulmadığından, HttpGet yöntemi seçili öğeyi ayarlamadan yöntemi çağırır:</span><span class="sxs-lookup"><span data-stu-id="23521-126">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="23521-127">HttpGet `Edit` yöntemi, seçili öğeyi, düzenlenen kursa atanmış olan bölümün kimliğine göre ayarlar:</span><span class="sxs-lookup"><span data-stu-id="23521-127">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="23521-128">Her ikisi `Create` için de `Edit` HttpPost yöntemleri ve ayrıca bir hatadan sonra sayfayı yeniden görüntülediğinde seçili öğeyi ayarlayan kod içerir.</span><span class="sxs-lookup"><span data-stu-id="23521-128">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="23521-129">Bu, sayfa hata iletisini göstermek için yeniden görüntülendiğinde, seçilen bölümün seçili kalmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="23521-129">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="23521-130">Ekle. Ayrıntılara AsNoTracking ve Silme yöntemleri</span><span class="sxs-lookup"><span data-stu-id="23521-130">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="23521-131">Ders Ayrıntıları ve Sil sayfalarının performansını `AsNoTracking` optimize `Details` etmek için, http ve httpget `Delete` yöntemlerine arama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-131">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="23521-132">Ders görünümlerini değiştirme</span><span class="sxs-lookup"><span data-stu-id="23521-132">Modify the Course views</span></span>

<span data-ttu-id="23521-133">*Görünümler/Kurslar/Create.cshtml'de,* **Bölüm** açılır listesine "Bölüm Seç" seçeneğini ekleyin, altyazıyı **DepartmentID'den** **Bölüm'e**değiştirin ve doğrulama iletisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-133">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="23521-134">*Görünümler /Kurslar/Edit.cshtml*olarak, sadece *Create.cshtml*yaptım Bölüm alanı için aynı değişikliği yapmak.</span><span class="sxs-lookup"><span data-stu-id="23521-134">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="23521-135">Ayrıca *Görünümler /Kurslar/Edit.cshtml'* de, **Başlık** alanından önce bir ders numarası alanı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-135">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="23521-136">Kurs numarası birincil anahtar olduğundan, görüntülenir, ancak değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="23521-136">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="23521-137">Edit görünümünde kurs`<input type="hidden">`numarası için zaten gizli bir alan var.</span><span class="sxs-lookup"><span data-stu-id="23521-137">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="23521-138">`<label>` Etiket yardımcısı eklemek, kullanıcı **Edit** sayfasında **Kaydet'i** tıklattığında kurs numarasının deftere nakledilen verilere eklenmesine neden olmadığından gizli alana olan gereksinimi ortadan kaldırmaz.</span><span class="sxs-lookup"><span data-stu-id="23521-138">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="23521-139">*Görünümler/Kurslar/Sil.cshtml'de,* en üste bir ders numarası alanı ekleyin ve bölüm adını bölüm adına değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23521-139">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="23521-140">*Görünümler / Dersler / Details.cshtml*olarak, sadece *Delete.cshtml*için yaptığınız aynı değişikliği yapmak .</span><span class="sxs-lookup"><span data-stu-id="23521-140">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="23521-141">Ders sayfalarını test edin</span><span class="sxs-lookup"><span data-stu-id="23521-141">Test the Course pages</span></span>

<span data-ttu-id="23521-142">Uygulamayı çalıştırın, **Kurslar** sekmesini seçin, **Yeni Oluştur'u**tıklatın ve yeni bir kurs için veri girin:</span><span class="sxs-lookup"><span data-stu-id="23521-142">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Kurs Oluşturma sayfası](update-related-data/_static/course-create.png)

<span data-ttu-id="23521-144">**Oluştur'u**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="23521-144">Click **Create**.</span></span> <span data-ttu-id="23521-145">Dersler Endeksi sayfası, listeye eklenen yeni dersle birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="23521-145">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="23521-146">Dizin sayfası listesindeki bölüm adı, ilişkinin doğru kurulduğunu gösteren gezinti özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="23521-146">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="23521-147">Kurslar Dizini sayfasındaki bir kursu **Edit'e** tıklayın.</span><span class="sxs-lookup"><span data-stu-id="23521-147">Click **Edit** on a course in the Courses Index page.</span></span>

![Kurs Edit sayfası](update-related-data/_static/course-edit.png)

<span data-ttu-id="23521-149">Sayfadaki verileri değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="23521-149">Change data on the page and click **Save**.</span></span> <span data-ttu-id="23521-150">Dersler Dizini sayfası güncelleştirilmiş ders verileri ile birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="23521-150">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-instructors-edit-page"></a><span data-ttu-id="23521-151">Eğitmenler Ekle Sayfayı Edit</span><span class="sxs-lookup"><span data-stu-id="23521-151">Add Instructors Edit page</span></span>

<span data-ttu-id="23521-152">Bir eğitmen kaydını güncellediğinizde, eğitmenin ofis atamasını güncelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="23521-152">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="23521-153">Eğitmen tüzel kişiliğinin OfficeAssignment varlığıyla bire sıfır veya bir ilişkisi vardır, bu da kodunuzun aşağıdaki durumları ele almak zorunda olduğu anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="23521-153">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="23521-154">Kullanıcı ofis atamasını temizlerse ve başlangıçta bir değeri varsa, OfficeAssignment varlığını silin.</span><span class="sxs-lookup"><span data-stu-id="23521-154">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="23521-155">Kullanıcı bir ofis atama değeri girer ve başlangıçta boşsa, yeni bir OfficeAssignment tüzel kişiliği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23521-155">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="23521-156">Kullanıcı bir ofis atamasının değerini değiştirirse, varolan bir OfficeAssignment varlığındaki değeri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23521-156">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="23521-157">Eğitmenler denetleyicisini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="23521-157">Update the Instructors controller</span></span>

<span data-ttu-id="23521-158">*InstructorsController.cs,* HttpGet `Edit` yöntemindeki kodu, Eğitmen varlığın `OfficeAssignment` gezinti özelliğini yükler `AsNoTracking`ve aşağıdakileri çağırır:</span><span class="sxs-lookup"><span data-stu-id="23521-158">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

<span data-ttu-id="23521-159">Ofis atama `Edit` güncelleştirmelerini işlemek için HttpPost yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="23521-159">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="23521-160">Kod aşağıdakileri yapar:</span><span class="sxs-lookup"><span data-stu-id="23521-160">The code does the following:</span></span>

* <span data-ttu-id="23521-161">İmza `EditPost` artık HttpGet `Edit` yöntemiyle aynı olduğundan yöntem adını değiştirir `ActionName` (öznitelik URL'nin `/Edit/` hala kullanıldığını belirtir).</span><span class="sxs-lookup"><span data-stu-id="23521-161">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

* <span data-ttu-id="23521-162">`OfficeAssignment` Gezinti özelliği için istekli yüklemeyi kullanarak geçerli Instructor varlığını veritabanından alır.</span><span class="sxs-lookup"><span data-stu-id="23521-162">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="23521-163">Bu, HttpGet `Edit` yönteminde yaptığınızla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="23521-163">This is the same as what you did in the HttpGet `Edit` method.</span></span>

* <span data-ttu-id="23521-164">Alınan Instructor varlığı model bağlayıcısından gelen değerlerle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="23521-164">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="23521-165">Aşırı `TryUpdateModel` yükleme, eklemek istediğiniz özellikleri beyaz listeye almanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="23521-165">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="23521-166">Bu, [ikinci öğreticide](crud.md)açıklandığı gibi aşırı deftere nakil yi önler.</span><span class="sxs-lookup"><span data-stu-id="23521-166">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* <span data-ttu-id="23521-167">Ofis konumu boşsa, OfficeAtama tablosundaki ilgili satırın silinecek şekilde Instructor.OfficeAtama özelliğini geçersiz kılacak şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="23521-167">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* <span data-ttu-id="23521-168">Değişiklikleri veritabanına kaydeder.</span><span class="sxs-lookup"><span data-stu-id="23521-168">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="23521-169">Eğitmen Edit görünümünü güncelleştir</span><span class="sxs-lookup"><span data-stu-id="23521-169">Update the Instructor Edit view</span></span>

<span data-ttu-id="23521-170">*Görünümler/Eğitmenler/Edit.cshtml'de,* **Kaydet** düğmesinden önce sonunda, ofis konumunu düzenlemek için yeni bir alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="23521-170">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="23521-171">Uygulamayı çalıştırın, **Eğitmenler** sekmesini seçin ve ardından bir eğitmenin üzerine **Edit'i** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="23521-171">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="23521-172">Office **Konumunu** değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="23521-172">Change the **Office Location** and click **Save**.</span></span>

![Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a><span data-ttu-id="23521-174">Ekle sayfasına ders ekle</span><span class="sxs-lookup"><span data-stu-id="23521-174">Add courses to Edit page</span></span>

<span data-ttu-id="23521-175">Eğitmenler herhangi bir sayıda ders verebilirler.</span><span class="sxs-lookup"><span data-stu-id="23521-175">Instructors may teach any number of courses.</span></span> <span data-ttu-id="23521-176">Şimdi, aşağıdaki ekran görüntüsünde gösterildiği gibi, bir kutu grubu kullanarak ders atamalarını değiştirme özelliğini ekleyerek Eğitmen Düzenleme sayfasını geliştireceksiniz:</span><span class="sxs-lookup"><span data-stu-id="23521-176">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="23521-178">Kurs ve Eğitmen varlıkları arasındaki ilişki çok-çok.</span><span class="sxs-lookup"><span data-stu-id="23521-178">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="23521-179">İlişkiler eklemek ve kaldırmak için, Ders Atamaları'na ve programdan gelen varlıkları ekler ve kaldırırsınız.</span><span class="sxs-lookup"><span data-stu-id="23521-179">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="23521-180">Bir eğitmenin atandığı dersleri değiştirmenizi sağlayan uI, bir onay kutusu grubudur.</span><span class="sxs-lookup"><span data-stu-id="23521-180">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="23521-181">Veritabanındaki her ders için bir onay kutusu görüntülenir ve eğitmenin şu anda atandığı onay kutusu seçilir.</span><span class="sxs-lookup"><span data-stu-id="23521-181">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="23521-182">Kullanıcı, ders atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir.</span><span class="sxs-lookup"><span data-stu-id="23521-182">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="23521-183">Kurs sayısı çok daha fazla olsaydı, büyük olasılıkla görünümdeki verileri sunmak için farklı bir yöntem kullanmak isteyebilirsiniz, ancak ilişkileri oluşturmak veya silmek için birleştirilmiş varlığı manipüle etme yöntemini kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="23521-183">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="23521-184">Eğitmenler denetleyicisini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="23521-184">Update the Instructors controller</span></span>

<span data-ttu-id="23521-185">Onay kutuları listesinin görünümüne veri sağlamak için bir görünüm modeli sınıfı kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="23521-185">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="23521-186">*SchoolViewModels* klasöründe *AssignedCourseData.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="23521-186">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="23521-187">*InstructorsController.cs,* HttpGet `Edit` yöntemini aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="23521-187">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="23521-188">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="23521-188">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="23521-189">Kod, `Courses` gezinti özelliği için istekli yükleme `PopulateAssignedCourseData` ekler ve `AssignedCourseData` görünüm modeli sınıfını kullanarak onay kutusu dizisi için bilgi sağlamak için yeni yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="23521-189">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="23521-190">Yöntemdeki `PopulateAssignedCourseData` kod, görünüm modeli sınıfını kullanan derslerin listesini yüklemek için tüm Ders varlıklarını okur.</span><span class="sxs-lookup"><span data-stu-id="23521-190">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="23521-191">Her kurs için kod, kursun eğitmenin `Courses` navigasyon özelliğinde bulunup bulunmadığına kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="23521-191">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="23521-192">Bir kursun öğretim üyesine atanıp atanmadığını kontrol ederken etkin bir araştırma oluşturmak `HashSet` için, öğretim üyesine atanan dersler bir koleksiyona konur.</span><span class="sxs-lookup"><span data-stu-id="23521-192">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="23521-193">Tesis, `Assigned` eğitmenin atandığı dersler için geçerli dir.</span><span class="sxs-lookup"><span data-stu-id="23521-193">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="23521-194">Görünüm, seçili olarak hangi onay kutularının görüntülenmesi gerektiğini belirlemek için bu özelliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="23521-194">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="23521-195">Son olarak, liste ' deki `ViewData`görünüme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="23521-195">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="23521-196">Ardından, kullanıcı **Kaydet'i**tıklattığında çalıştırılan kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-196">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="23521-197">`EditPost` Yöntemi aşağıdaki kodla değiştirin ve Eğitmen varlığın `Courses` gezinti özelliğini güncelleyen yeni bir yöntem ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-197">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="23521-198">Yöntem imzası artık HttpGet `Edit` yönteminden farklıdır, bu nedenle `EditPost` yöntem `Edit`adı arkadan 'a değişir.</span><span class="sxs-lookup"><span data-stu-id="23521-198">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="23521-199">Görünümde Ders varlıkları koleksiyonu olmadığından, model bağlayıcısı gezinti özelliğini `CourseAssignments` otomatik olarak güncelleştiremez.</span><span class="sxs-lookup"><span data-stu-id="23521-199">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="23521-200">Gezinti özelliğini güncelleştirmek için `CourseAssignments` model bağlayıcısını kullanmak yerine, `UpdateInstructorCourses` bunu yeni yöntemde yaparsınız.</span><span class="sxs-lookup"><span data-stu-id="23521-200">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="23521-201">Bu nedenle `CourseAssignments` özelliği model bağlamadan çıkarmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="23521-201">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="23521-202">Bu, beyaz liste aşırı yüklemesini `TryUpdateModel` kullandığınız ve `CourseAssignments` dahil listesinde olmadığınız için çağıran kodda herhangi bir değişiklik gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="23521-202">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="23521-203">Onay kutusu seçilmediyse, kod `UpdateInstructorCourses` gezinti özelliğini `CourseAssignments` boş bir koleksiyonla baş harfe döndürür ve döndürür:</span><span class="sxs-lookup"><span data-stu-id="23521-203">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="23521-204">Kod daha sonra veritabanındaki tüm dersler arasında döngüler ve görünümde seçilen olanlar aleyhine şu anda öğretim atanan olanlar atanır her ders denetler.</span><span class="sxs-lookup"><span data-stu-id="23521-204">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="23521-205">Verimli aramaları kolaylaştırmak için, son iki koleksiyon `HashSet` nesnelerde depolanır.</span><span class="sxs-lookup"><span data-stu-id="23521-205">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="23521-206">Bir kursun onay kutusu seçiliyse ancak kurs `Instructor.CourseAssignments` navigasyon özelliğinde değilse, kurs gezinti özelliğindeki koleksiyona eklenir.</span><span class="sxs-lookup"><span data-stu-id="23521-206">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="23521-207">Bir kursun onay kutusu seçilmediyse, ancak kurs navigasyon `Instructor.CourseAssignments` özelliğindeyse, kurs navigasyon özelliğinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="23521-207">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="23521-208">Eğitmen görünümlerini güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="23521-208">Update the Instructor views</span></span>

<span data-ttu-id="23521-209">*Görünümler/Eğitmenler/Edit.cshtml'de,* **Office** alanının `div` `div` öğelerinden hemen sonra ve **Kaydet** düğmesinin öğesinden önce aşağıdaki kodu ekleyerek bir dizi onay kutusu içeren bir **Kurs** alanı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-209">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="23521-210">Görsel Studio'da kodu yapıştırdığınızda, satır sonları kodu kıracak şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="23521-210">When you paste the code in Visual Studio, line breaks might be changed in a way that breaks the code.</span></span> <span data-ttu-id="23521-211">Yapıştırma dan sonra kod farklı görünüyorsa, otomatik biçimlendirmeyi geri almak için ctrl+Z tuşuna bir kez basın.</span><span class="sxs-lookup"><span data-stu-id="23521-211">If the code looks different after pasting, press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="23521-212">Bu, hat sonlarını düzeltecek, böylece burada gördüğünüz gibi görünürler.</span><span class="sxs-lookup"><span data-stu-id="23521-212">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="23521-213">Girintisi mükemmel olmak zorunda değildir, ancak `@</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` , ve çizgiler her gösterildiği gibi tek bir satırda olmalıdır veya bir çalışma zamanı hatası alırsınız.</span><span class="sxs-lookup"><span data-stu-id="23521-213">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="23521-214">Yeni kod bloğu seçilise, yeni kodu varolan kodla hizalamak için Sekme'ye üç kez basın.</span><span class="sxs-lookup"><span data-stu-id="23521-214">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="23521-215">Bu sorun Visual Studio 2019'da giderilmiştir.</span><span class="sxs-lookup"><span data-stu-id="23521-215">This problem is fixed in Visual Studio 2019.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="23521-216">Bu kod, üç sütunu olan bir HTML tablosu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="23521-216">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="23521-217">Her sütunda bir onay kutusu ve ardından ders numarası ve başlığından oluşan bir başlık vardır.</span><span class="sxs-lookup"><span data-stu-id="23521-217">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="23521-218">Onay kutularının tümü, model bağlayıcısına grup olarak davranılması gerektiğini bildiren aynı ada ("selectedCourses") sahiptir.</span><span class="sxs-lookup"><span data-stu-id="23521-218">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="23521-219">Her onay kutusunun değer `CourseID`özniteliği.</span><span class="sxs-lookup"><span data-stu-id="23521-219">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="23521-220">Sayfa deftere nakledildiğinde, model bağlayıcısı yalnızca seçili onay `CourseID` kutuları için değerlerden oluşan bir diziyi denetleyiciye geçirir.</span><span class="sxs-lookup"><span data-stu-id="23521-220">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="23521-221">Onay kutuları başlangıçta işlendiğinde, eğitmene atanan dersler için olanlar, onları seçen (kontrol edilmiş olarak görüntüler) özniteliklerini işaretlemiş olur.</span><span class="sxs-lookup"><span data-stu-id="23521-221">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="23521-222">Uygulamayı çalıştırın, **Eğitmenler** sekmesini seçin ve **Edit** sayfasını görmek için bir eğitmende **Edit'i** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="23521-222">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="23521-224">Bazı ders atamalarını değiştirin ve Kaydet'i tıklatın.</span><span class="sxs-lookup"><span data-stu-id="23521-224">Change some course assignments and click Save.</span></span> <span data-ttu-id="23521-225">Yaptığınız değişiklikler Dizin sayfasına yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="23521-225">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="23521-226">Eğitmen ders verilerini yeniden düzenlemesi için burada alınan yaklaşım, sınırlı sayıda kurs olduğunda iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="23521-226">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="23521-227">Çok daha büyük koleksiyonlar için farklı bir u-ui ve farklı bir güncelleştirme yöntemi gerekir.</span><span class="sxs-lookup"><span data-stu-id="23521-227">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-delete-page"></a><span data-ttu-id="23521-228">Sayfayı Sil'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="23521-228">Update Delete page</span></span>

<span data-ttu-id="23521-229">*InstructorsController.cs,* `DeleteConfirmed` yöntemi silin ve yerine aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-229">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="23521-230">Bu kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="23521-230">This code makes the following changes:</span></span>

* <span data-ttu-id="23521-231">`CourseAssignments` Navigasyon özelliği için istekli yükleme yapar.</span><span class="sxs-lookup"><span data-stu-id="23521-231">Does eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="23521-232">Bunu eklemeniz gerekir, yoksa EF ilgili `CourseAssignment` varlıklar hakkında bilgi sahibi olmaz ve bunları silmez.</span><span class="sxs-lookup"><span data-stu-id="23521-232">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span> <span data-ttu-id="23521-233">Burada bunları okumak gerekmemesi için veritabanında basamaklı silme yapılandırmak olabilir.</span><span class="sxs-lookup"><span data-stu-id="23521-233">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="23521-234">Silinecek eğitmen herhangi bir bölümün yöneticisi olarak atanırsa, eğitmen atamasını bu bölümlerden kaldırır.</span><span class="sxs-lookup"><span data-stu-id="23521-234">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-create-page"></a><span data-ttu-id="23521-235">Sayfa Oluştur'a ofis konumu ve kurslar ekleme</span><span class="sxs-lookup"><span data-stu-id="23521-235">Add office location and courses to Create page</span></span>

<span data-ttu-id="23521-236">*InstructorsController.cs,* HttpGet ve HttpPost `Create` yöntemlerini silin ve ardından yerine aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="23521-236">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="23521-237">Bu kod, başlangıçta hiçbir ders `Edit` seçilmemiş olması dışında yöntemler için gördüklerinize benzer.</span><span class="sxs-lookup"><span data-stu-id="23521-237">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="23521-238">HttpGet `Create` yöntemi, `PopulateAssignedCourseData` seçili kurslar olabileceğinden değil, görünümdeki `foreach` döngü için boş bir koleksiyon sağlamak için (aksi takdirde görünüm kodu geçersiz bir başvuru özel durum oluşturur) yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="23521-238">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="23521-239">HttpPost `Create` yöntemi, doğrulama hatalarını `CourseAssignments` kontrol etmeden önce seçilen her kursu gezinti özelliğine ekler ve veritabanına yeni eğitmen ekler.</span><span class="sxs-lookup"><span data-stu-id="23521-239">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="23521-240">Modellerde hatalar olsa bile kurslar eklenir (örneğin, kullanıcı geçersiz bir tarih anahtarladı) ve sayfa bir hata iletisiyle yeniden görüntülenir, yapılan kurs seçimleri otomatik olarak geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23521-240">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="23521-241">`CourseAssignments` Navigasyon özelliğine kurs ekleyebilmek için mülkü boş bir koleksiyon olarak başlatmanız gerektiğine dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="23521-241">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="23521-242">Denetleyici kodunda bunu yapmanın alternatifi olarak, aşağıdaki örnekte gösterildiği gibi, özellik getter'Ini otomatik olarak oluşturabilecek şekilde değiştirerek, bunu Eğitmen modelinde yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="23521-242">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

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

<span data-ttu-id="23521-243">`CourseAssignments` Özelliği bu şekilde değiştirirseniz, denetleyicideki açık özellik başlatma kodunu kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="23521-243">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="23521-244">*Görünümler/Eğitmen/Create.cshtml'de,* Gönder düğmesinden önce bir ofis konumu metin kutusu ekleyin ve kurslar için onay kutuları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-244">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="23521-245">Edit sayfasında olduğu gibi, [Visual Studio yapıştırdığınızda kodu yeniden biçimlendirirse biçimlendirmeyi düzeltin.](#notepad)</span><span class="sxs-lookup"><span data-stu-id="23521-245">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="23521-246">Uygulamayı çalıştırarak ve bir eğitmen oluşturarak test edin.</span><span class="sxs-lookup"><span data-stu-id="23521-246">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="23521-247">İşlemleri N</span><span class="sxs-lookup"><span data-stu-id="23521-247">Handling Transactions</span></span>

<span data-ttu-id="23521-248">[CRUD öğreticisinde](crud.md)açıklandığı gibi, Entity Framework işlemleri dolaylı olarak uygular.</span><span class="sxs-lookup"><span data-stu-id="23521-248">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="23521-249">Daha fazla denetime ihtiyaç duyduğunuz senaryolar için (örneğin, bir harekette Entity Framework dışında yapılan işlemleri eklemek istiyorsanız), [Hareketler'e](/ef/core/saving/transactions)bakın.</span><span class="sxs-lookup"><span data-stu-id="23521-249">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="23521-250">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="23521-250">Get the code</span></span>

[<span data-ttu-id="23521-251">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-251">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="23521-252">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="23521-252">Next steps</span></span>

<span data-ttu-id="23521-253">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="23521-253">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="23521-254">Özelleştirilmiş Kurslar sayfaları</span><span class="sxs-lookup"><span data-stu-id="23521-254">Customized Courses pages</span></span>
> * <span data-ttu-id="23521-255">Eklenen Eğitmenler Edit sayfası</span><span class="sxs-lookup"><span data-stu-id="23521-255">Added Instructors Edit page</span></span>
> * <span data-ttu-id="23521-256">Ekle sayfasına dersler eklendi</span><span class="sxs-lookup"><span data-stu-id="23521-256">Added courses to Edit page</span></span>
> * <span data-ttu-id="23521-257">Güncelleştirilmiş Silme sayfası</span><span class="sxs-lookup"><span data-stu-id="23521-257">Updated Delete page</span></span>
> * <span data-ttu-id="23521-258">Sayfa Oluşturma'ya ofis konumu ve kurslar eklendi</span><span class="sxs-lookup"><span data-stu-id="23521-258">Added office location and courses to Create page</span></span>

<span data-ttu-id="23521-259">Eşzamanlılık çakışmaları nasıl ele alınılacağını öğrenmek için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="23521-259">Advance to the next tutorial to learn how to handle concurrency conflicts.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="23521-260">Eşzamanlılık çakışmalarını işleme</span><span class="sxs-lookup"><span data-stu-id="23521-260">Handle concurrency conflicts</span></span>](concurrency.md)
