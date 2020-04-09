---
title: ASP.NET Core'da EF Core'lu Jilet Sayfaları - İlgili Verileri Güncelle - 7 /8
author: rick-anderson
description: Bu eğitimde, yabancı anahtar alanlarını ve gezinme özelliklerini güncelleştirerek ilgili verileri güncelleştirebilirsiniz.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656424"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="ac0b7-103">ASP.NET Core'da EF Core'lu Jilet Sayfaları - İlgili Verileri Güncelle - 7 /8</span><span class="sxs-lookup"><span data-stu-id="ac0b7-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="ac0b7-104">Yazar: [Tom Dykstra](https://github.com/tdykstra)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ac0b7-105">Bu öğretici, ilgili verilerin nasıl güncelleştirilebildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="ac0b7-106">Aşağıdaki çizimler, tamamlanan bazı sayfaları gösterir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="ac0b7-107">![Ders Edit](update-related-data/_static/course-edit30.png)
![sayfası Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="ac0b7-108">Ders Oluştur ve Sayfalarını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ac0b7-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="ac0b7-109">Ders Oluştur ve Edit sayfaları için iskele kodu, Bölüm Kimliğini (tamsayı) gösteren bir Bölüm açılır listesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="ac0b7-110">Açılan sayfabölüm adını göstermelidir, bu nedenle bu sayfaların her ikisinin de bölüm adlarının bir listesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="ac0b7-111">Bu listeyi sağlamak için, Oluştur ve Edit sayfaları için bir taban sınıf kullanın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="ac0b7-112">Ders Oluştur ve Edin için taban sınıf oluşturma</span><span class="sxs-lookup"><span data-stu-id="ac0b7-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="ac0b7-113">Aşağıdaki kodile bir *Sayfa/Kurs/DepartmentNamePageModel.cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="ac0b7-114">Önceki kod, bölüm adları listesini içerecek bir [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="ac0b7-115">`selectedDepartment` Belirtilirse, bu bölüm `SelectList`seçilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="ac0b7-116">Oluştur ve Düzenleme sayfası modeli sınıfları. `DepartmentNamePageModel`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="ac0b7-117">Ders Oluştur sayfa modelini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-117">Update the Course Create page model</span></span>

<span data-ttu-id="ac0b7-118">Bir Ders Bir Bölüme atanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="ac0b7-119">Oluştur ve Edit sayfaları için taban `SelectList` sınıf bölümü seçmek için bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="ac0b7-120">Yabancı anahtar (FK) `SelectList` özelliğini kümeleyen `Course.DepartmentID` açılır liste.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="ac0b7-121">EF Core, `Course.DepartmentID` navigasyon özelliğini `Department` yüklemek için FK'yı kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Kurs oluşturma](update-related-data/_static/ddl30.png)

<span data-ttu-id="ac0b7-123">*Sayfaları/Dersleri/Create.cshtml.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ac0b7-124">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-124">The preceding code:</span></span>

* <span data-ttu-id="ac0b7-125">`DepartmentNamePageModel`Türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="ac0b7-126">Aşırı `TryUpdateModelAsync` [göndermeyi](xref:data/ef-rp/crud#overposting)önlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac0b7-127">`ViewData["DepartmentID"]`Kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="ac0b7-128">`DepartmentNameSL`taban sınıftan güçlü bir şekilde yazılan bir modeldir ve Razor sayfası tarafından kullanılacaktır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="ac0b7-129">Güçlü dakti-si yazılan modeller zayıf yazılanmodellere göre tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="ac0b7-130">Daha fazla bilgi için zayıf [yazılan verilere (ViewData ve ViewBag)](xref:mvc/views/overview#VD_VB)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="ac0b7-131">Rota Oluştur Jilet sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="ac0b7-132">*Sayfaları/Kursları/Create.cshtml'i* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="ac0b7-133">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac0b7-134">**Başlık BölümKimliği'nden** **Departmana**dönüşür.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac0b7-135">(taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="ac0b7-136">"Bölüm Seç" seçeneğini ekler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="ac0b7-137">Bu değişiklik, ilk bölüm yerine henüz hiçbir bölüm seçilmediğinde açılan bölümde "Bölüm Seç"i işler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="ac0b7-138">Bölüm seçilmediğinde bir doğrulama iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="ac0b7-139">Jilet Sayfası [Seç Etiket Yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper)kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="ac0b7-140">Oluştur sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-140">Test the Create page.</span></span> <span data-ttu-id="ac0b7-141">Oluştur sayfası, departman kimliği yerine bölüm adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="ac0b7-142">Ders Düzenleme sayfası modelini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-142">Update the Course Edit page model</span></span>

<span data-ttu-id="ac0b7-143">*Sayfaları/Dersleri/Edit.cshtml.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="ac0b7-144">Değişiklikler, Sayfa Oluştur modelinde yapılanlara benzer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="ac0b7-145">Önceki kodda, `PopulateDepartmentsDropDownList` açılan listedeki bölümü seçen bölüm kimliği geçer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="ac0b7-146">Kursu Eritme Jiletsayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ac0b7-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="ac0b7-147">*Sayfaları/Kursları/Edit.cshtml'i* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="ac0b7-148">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac0b7-149">Kurs kimliğini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-149">Displays the course ID.</span></span> <span data-ttu-id="ac0b7-150">Genellikle bir varlığın Birincil Anahtarı (PK) görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="ac0b7-151">PK'lar genellikle kullanıcılar için anlamsızdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="ac0b7-152">Bu durumda, PK kurs numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="ac0b7-153">Bölüm Bölümü'nün bölüm den **BölümKimliği'ne** **Department**açılan alt yazısını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac0b7-154">(taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ac0b7-155">Sayfa, kurs numarası`<input type="hidden">`için gizli bir alan ( ) içerir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="ac0b7-156">Bir `asp-for="Course.CourseID"` `<label>` etiket yardımcısı eklemek, gizli alana olan gereksinimi ortadan kaldırmaz.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="ac0b7-157">`<input type="hidden">`kullanıcı **Kaydet'i**tıklattığında, gönderilen verilere eklenecek kurs numarasının olması gerekmektedir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="ac0b7-158">Ders Ayrıntılarını Güncelleştir in ve Sayfalarını Sil</span><span class="sxs-lookup"><span data-stu-id="ac0b7-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="ac0b7-159">[AsNoTracking,](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) izleme gerekli olmadığında performansı artırabilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="ac0b7-160">Kurs sayfası modellerini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-160">Update the Course page models</span></span>

<span data-ttu-id="ac0b7-161">*Güncelleme Sayfaları /Kurslar/Delete.cshtml.cs* aşağıdaki kodu `AsNoTracking`eklemek için:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="ac0b7-162">*Sayfalar/Kurslar/Ayrıntılar.cshtml.cs* dosyasında da aynı değişikliği yapın:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="ac0b7-163">Course Razor sayfalarını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-163">Update the Course Razor pages</span></span>

<span data-ttu-id="ac0b7-164">*Sayfaları/Dersleri/Sil.cshtml'i* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="ac0b7-165">Ayrıntılar sayfasında da aynı değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="ac0b7-166">Ders sayfalarını test edin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-166">Test the Course pages</span></span>

<span data-ttu-id="ac0b7-167">Sayfaları oluşturma, düzenle, ayrıntıları ve silmeyi test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="ac0b7-168">Eğitmen Oluşturma ve Sayfalarını Edin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="ac0b7-169">Eğitmenler herhangi bir sayıda ders verebilirler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="ac0b7-170">Aşağıdaki resimde, eğitmen Düzenle sayfasını bir dizi ders onay kutusu ile gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="ac0b7-172">Onay kutuları, bir eğitmenin atandığı derslerde değişiklik yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="ac0b7-173">Veritabanındaki her ders için bir onay kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="ac0b7-174">Eğitmenin atandığı dersler seçilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="ac0b7-175">Kullanıcı, ders atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="ac0b7-176">Kurs sayısı çok daha fazla olsaydı, farklı bir u-ui daha iyi çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="ac0b7-177">Ama burada gösterilen çok-çok ilişkisi yönetme yöntemi değişmez.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="ac0b7-178">İlişkiler oluşturmak veya silmek için bir birleştirme varlığını manipüle eleştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="ac0b7-179">Atanan ders verileri için sınıf oluşturma</span><span class="sxs-lookup"><span data-stu-id="ac0b7-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="ac0b7-180">Aşağıdaki kodla *SchoolViewModels/AssignedCourseData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="ac0b7-181">Sınıf, `AssignedCourseData` bir eğitmene atanan derslerin onay kutularını oluşturmak için veriler içerir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="ac0b7-182">Eğitmen sayfası modeli taban sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="ac0b7-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="ac0b7-183">*Sayfaları/Eğitmenleri/EğitmenKurslarıSayfaModel.cs* taban sınıfını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="ac0b7-184">Sayfa `InstructorCoursesPageModel` yı oluştur ve oluştur modelleri için kullanacağınız taban sınıfdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="ac0b7-185">`PopulateAssignedCourseData`doldurmak `Course` için tüm varlıkları `AssignedCourseDataList`okur.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="ac0b7-186">Her kurs için, kod `CourseID`, başlık ve olup olmadığını eğitmen derse atanmış olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="ac0b7-187">[HashSet](/dotnet/api/system.collections.generic.hashset-1) verimli aramalar için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="ac0b7-188">Razor sayfasında Ders varlıkları koleksiyonu olmadığından, model bağlayıcısı gezinme özelliğini `CourseAssignments` otomatik olarak güncelleştiremez.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ac0b7-189">Gezinti özelliğini güncelleştirmek için `CourseAssignments` model bağlayıcısını kullanmak yerine, `UpdateInstructorCourses` bunu yeni yöntemde yaparsınız.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="ac0b7-190">Bu nedenle `CourseAssignments` özelliği model bağlamadan çıkarmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="ac0b7-191">Bu, beyaz liste aşırı yüklemesini `TryUpdateModel` kullandığınız ve `CourseAssignments` dahil listesinde olmadığınız için çağıran kodda herhangi bir değişiklik gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="ac0b7-192">Onay kutusu seçilmediyse, kod `UpdateInstructorCourses` gezinti özelliğini `CourseAssignments` boş bir koleksiyonla baş harfe döndürür ve döndürür:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="ac0b7-193">Kod daha sonra veritabanındaki tüm dersler arasında döngüler ve her ders şu anda öğretim üyesi ne sayfada seçilmiş olanlar karşı atanan karşı kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="ac0b7-194">Verimli aramaları kolaylaştırmak için, son iki koleksiyon `HashSet` nesnelerde depolanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="ac0b7-195">Bir kursun onay kutusu seçiliyse ancak kurs `Instructor.CourseAssignments` navigasyon özelliğinde değilse, kurs gezinti özelliğindeki koleksiyona eklenir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="ac0b7-196">Bir kursun onay kutusu seçilmediyse, ancak kurs navigasyon `Instructor.CourseAssignments` özelliğindeyse, kurs navigasyon özelliğinden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="ac0b7-197">Ofis konumunu işleme</span><span class="sxs-lookup"><span data-stu-id="ac0b7-197">Handle office location</span></span>

<span data-ttu-id="ac0b7-198">Edit sayfasının ele almak zorunda olduğu bir diğer ilişki de, Eğitmen varlığın `OfficeAssignment` varlıkla sahip olduğu birden sıfıra veya bir ilişkisidir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="ac0b7-199">Eğitmen edit kodu aşağıdaki senaryoları işlemelidir:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="ac0b7-200">Kullanıcı ofis atamasını temizlerse, `OfficeAssignment` varlığı silin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac0b7-201">Kullanıcı bir ofis ataması girerse ve boşsa, yeni `OfficeAssignment` bir varlık oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac0b7-202">Kullanıcı ofis atamasını değiştirirse, varlığı güncelleştirin. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="ac0b7-203">Eğitmen Düzenleme sayfası modelini güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="ac0b7-204">*Sayfaları/Eğitmenleri/Edit.cshtml.cs'yi* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="ac0b7-205">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-205">The preceding code:</span></span>

* <span data-ttu-id="ac0b7-206">, , `Instructor` `CourseAssignment`ve `CourseAssignment.Course` gezinme özellikleri için istekli yükleme kullanarak veritabanından geçerli varlık alır. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="ac0b7-207">Alınan `Instructor` varlığı model bağlayıcısından gelen değerlerle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="ac0b7-208">`TryUpdateModel`aşırı [göndermeyi](xref:data/ef-rp/crud#overposting)önler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac0b7-209">Ofis konumu boşsa, null `Instructor.OfficeAssignment` ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="ac0b7-210">Null `Instructor.OfficeAssignment` olduğunda, tablodaki `OfficeAssignment` ilgili satır silinir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="ac0b7-211">Görünüm modeli sınıfını kullanarak onay kutuları için bilgi sağlamak için çağırır. `PopulateAssignedCourseData` `OnGetAsync` `AssignedCourseData`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="ac0b7-212">Onay kutularından gelen bilgileri düzenlenen Eğitmen varlığına uygulamak için çağrılar. `UpdateInstructorCourses` `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="ac0b7-213">Aramalar `PopulateAssignedCourseData` `UpdateInstructorCourses` ve `OnPostAsync` `TryUpdateModel` başarısız olursa.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="ac0b7-214">Bu yöntem, bir hata iletisi ile yeniden görüntülendiğinde sayfada girilen atanan kurs verilerini geri yükleme çağrıları.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="ac0b7-215">Eğitmen Razor sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="ac0b7-216">*Sayfaları/Eğitmenleri/Edit.cshtml'i* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="ac0b7-217">Önceki kod, üç sütunu olan bir HTML tablosu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="ac0b7-218">Her sütunda bir onay kutusu ve ders numarasını ve başlığını içeren bir başlık vardır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="ac0b7-219">Onay kutularının tümü aynı ada ("selectedCourses") sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="ac0b7-220">Aynı adı kullanarak bir grup olarak tedavi etmek için model bağlayıcı bildirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="ac0b7-221">Her onay kutusunun değer özniteliği `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="ac0b7-222">Sayfa deftere nakledildiğinde, model bağlayıcısı yalnızca seçili onay kutularıiçin `CourseID` değerlerden oluşan bir dizigeçer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="ac0b7-223">Onay kutuları ilk işlendiğinde, eğitmene atanan dersler seçilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="ac0b7-224">Not: Eğitmen ders verilerini yeniden düzenlemesi için burada alınan yaklaşım, sınırlı sayıda kurs olduğunda iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="ac0b7-225">Çok daha büyük koleksiyonlar için, farklı bir ui ve farklı bir güncelleştirme yöntemi daha kullanılabilir ve verimli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="ac0b7-226">Uygulamayı çalıştırın ve güncelleştirilmiş Eğitmenler Ii tuşu sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="ac0b7-227">Bazı ders atamalarını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-227">Change some course assignments.</span></span> <span data-ttu-id="ac0b7-228">Değişiklikler Dizin sayfasına yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="ac0b7-229">Eğitmen Oluştur sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ac0b7-229">Update the Instructor Create page</span></span>

<span data-ttu-id="ac0b7-230">Eğitmen Oluştur sayfa modelini ve Razor sayfasını Düzenleme sayfasına benzer kodlarla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="ac0b7-231">Eğitmen Oluştur sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="ac0b7-232">Eğitmen Silme sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="ac0b7-233">*Sayfaları/Eğitmenleri/Sil.cshtml.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="ac0b7-234">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac0b7-235">Gezinti özelliği için `CourseAssignments` istekli yükleme kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ac0b7-236">`CourseAssignments`eklenmesi gerekir veya eğitmen silindiğinde silinmezler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="ac0b7-237">Bunları okumaya gerek kalmamak için, veritabanında basamaklı silme yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="ac0b7-238">Silinecek eğitmen herhangi bir bölümün yöneticisi olarak atanırsa, eğitmen atamasını bu bölümlerden kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="ac0b7-239">Uygulamayı çalıştırın ve Sil sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac0b7-240">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="ac0b7-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ac0b7-241">[Önceki öğretici](xref:data/ef-rp/read-related-data)
> [Sonraki öğretici](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac0b7-242">Bu öğretici, ilgili verilerin güncelleştirilmesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="ac0b7-243">Çözemediğiniz sorunlarla karşılaştıysanız, [tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="ac0b7-244">[Talimatları indirin.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ac0b7-245">Aşağıdaki çizimler, tamamlanan bazı sayfaları gösterir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="ac0b7-246">![Ders Edit](update-related-data/_static/course-edit.png)
![sayfası Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="ac0b7-247">Oluştur ve Yap ders sayfalarını inceleyin ve test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="ac0b7-248">Yeni bir rota oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-248">Create a new course.</span></span> <span data-ttu-id="ac0b7-249">Bölüm, adı değil, birincil anahtarı (bir sonda) tarafından seçilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="ac0b7-250">Yeni rotayı yeniden edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-250">Edit the new course.</span></span> <span data-ttu-id="ac0b7-251">Testi tamamladığınızda, yeni kursu silin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="ac0b7-252">Ortak kodu paylaşmak için taban sınıf oluşturma</span><span class="sxs-lookup"><span data-stu-id="ac0b7-252">Create a base class to share common code</span></span>

<span data-ttu-id="ac0b7-253">Dersler/Oluşturma ve Kurs/Edit sayfaları her bölüm adlarının bir listesini gerekir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="ac0b7-254">Oluştur ve Düzenleme sayfaları için *Sayfalar/Kurslar/DepartmentNamePageModel.cshtml.cs* taban sınıfını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="ac0b7-255">Önceki kod, bölüm adları listesini içerecek bir [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="ac0b7-256">`selectedDepartment` Belirtilirse, bu bölüm `SelectList`seçilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="ac0b7-257">Oluştur ve Düzenleme sayfası modeli sınıfları. `DepartmentNamePageModel`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="ac0b7-258">Ders Sayfalarını Özelleştir</span><span class="sxs-lookup"><span data-stu-id="ac0b7-258">Customize the Courses Pages</span></span>

<span data-ttu-id="ac0b7-259">Yeni bir ders varlığı oluşturulduğunda, varolan bir bölümle ilişkisi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="ac0b7-260">Kurs oluştururken bölüm eklemek için, Oluştur ve Edit taban sınıfı bölümü seçmek için bir açılır liste içerir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="ac0b7-261">Açılan liste yabancı anahtarı `Course.DepartmentID` (FK) özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="ac0b7-262">EF Core, `Course.DepartmentID` navigasyon özelliğini `Department` yüklemek için FK'yı kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Kurs oluşturma](update-related-data/_static/ddl.png)

<span data-ttu-id="ac0b7-264">Sayfa oluştur modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="ac0b7-265">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-265">The preceding code:</span></span>

* <span data-ttu-id="ac0b7-266">`DepartmentNamePageModel`Türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="ac0b7-267">Aşırı `TryUpdateModelAsync` [göndermeyi](xref:data/ef-rp/crud#overposting)önlemek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac0b7-268">(taban `ViewData["DepartmentID"]` `DepartmentNameSL` sınıftan) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ac0b7-269">`ViewData["DepartmentID"]`güçlü bir şekilde yazılanile `DepartmentNameSL`değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="ac0b7-270">Güçlü dakti-si yazılan modeller zayıf yazılanmodellere göre tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="ac0b7-271">Daha fazla bilgi için zayıf [yazılan verilere (ViewData ve ViewBag)](xref:mvc/views/overview#VD_VB)bakın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="ac0b7-272">Kurslar Oluşturma sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-272">Update the Courses Create page</span></span>

<span data-ttu-id="ac0b7-273">*Sayfaları/Kursları/Create.cshtml'i* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="ac0b7-274">Önceki biçimlendirme aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ac0b7-275">**Başlık BölümKimliği'nden** **Departmana**dönüşür.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac0b7-276">(taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="ac0b7-277">"Bölüm Seç" seçeneğini ekler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="ac0b7-278">Bu değişiklik, birinci bölüm yerine "Bölüm Seç"i işler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="ac0b7-279">Bölüm seçilmediğinde bir doğrulama iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="ac0b7-280">Jilet Sayfası [Seç Etiket Yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper)kullanır:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="ac0b7-281">Oluştur sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-281">Test the Create page.</span></span> <span data-ttu-id="ac0b7-282">Oluştur sayfası, departman kimliği yerine bölüm adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="ac0b7-283">Dersleri Edit sayfasını güncelleyin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="ac0b7-284">*Sayfalar/Kurslar/Edit.cshtml.cs'deki* kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="ac0b7-285">Değişiklikler, Sayfa Oluştur modelinde yapılanlara benzer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="ac0b7-286">Önceki kodda, `PopulateDepartmentsDropDownList` açılan listede belirtilen bölümü seçen departman kimliği geçer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="ac0b7-287">*Sayfaları/Dersleri/Edit.cshtml'i* aşağıdaki biçimlendirmeyle güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="ac0b7-288">Önceki biçimlendirme aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ac0b7-289">Kurs kimliğini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-289">Displays the course ID.</span></span> <span data-ttu-id="ac0b7-290">Genellikle bir varlığın Birincil Anahtarı (PK) görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="ac0b7-291">PK'lar genellikle kullanıcılar için anlamsızdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="ac0b7-292">Bu durumda, PK kurs numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="ac0b7-293">**Başlık BölümKimliği'nden** **Departmana**dönüşür.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="ac0b7-294">(taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="ac0b7-295">Sayfa, kurs numarası`<input type="hidden">`için gizli bir alan ( ) içerir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="ac0b7-296">Bir `asp-for="Course.CourseID"` `<label>` etiket yardımcısı eklemek, gizli alana olan gereksinimi ortadan kaldırmaz.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="ac0b7-297">`<input type="hidden">`kullanıcı **Kaydet'i**tıklattığında, gönderilen verilere eklenecek kurs numarasının olması gerekmektedir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="ac0b7-298">Güncelleştirilmiş kodu test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-298">Test the updated code.</span></span> <span data-ttu-id="ac0b7-299">Bir kurs oluşturun, düzenleyin ve silin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="ac0b7-300">Ayrıntılara AsNoTracking ekleyin ve sayfa modellerini sil</span><span class="sxs-lookup"><span data-stu-id="ac0b7-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="ac0b7-301">[AsNoTracking,](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) izleme gerekli olmadığında performansı artırabilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="ac0b7-302">Sil `AsNoTracking` ve Ayrıntılar sayfa modeline ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="ac0b7-303">Aşağıdaki kod güncelleştirilmiş Delete sayfa modelini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="ac0b7-304">`OnGetAsync` *Sayfalar/Kurslar/Ayrıntılar.cshtml.cs* dosyasındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="ac0b7-305">Sil ve Ayrıntılar sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="ac0b7-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="ac0b7-306">Razor'u sil sayfasını aşağıdaki biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="ac0b7-307">Ayrıntılar sayfasında da aynı değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="ac0b7-308">Ders sayfalarını test edin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-308">Test the Course pages</span></span>

<span data-ttu-id="ac0b7-309">Test oluştur, düzenle, ayrıntıları ve silme.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="ac0b7-310">Eğitmen sayfalarını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ac0b7-310">Update the instructor pages</span></span>

<span data-ttu-id="ac0b7-311">Aşağıdaki bölümler eğitmen sayfalarını günceller.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="ac0b7-312">Ofis konumu ekleme</span><span class="sxs-lookup"><span data-stu-id="ac0b7-312">Add office location</span></span>

<span data-ttu-id="ac0b7-313">Bir eğitmen kaydını düzenlerken, eğitmenin ofis atamasını güncelleştirmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="ac0b7-314">Varlığın `Instructor` `OfficeAssignment` varlıkla bire sıfır veya bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="ac0b7-315">Eğitmen kodu işlemelidir:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-315">The instructor code must handle:</span></span>

* <span data-ttu-id="ac0b7-316">Kullanıcı ofis atamasını temizlerse, `OfficeAssignment` varlığı silin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac0b7-317">Kullanıcı bir ofis ataması girerse ve boşsa, yeni `OfficeAssignment` bir varlık oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="ac0b7-318">Kullanıcı ofis atamasını değiştirirse, varlığı güncelleştirin. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="ac0b7-319">Eğitmenlersayfa modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="ac0b7-320">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-320">The preceding code:</span></span>

* <span data-ttu-id="ac0b7-321">Gezinti özelliği `Instructor` için istekli yüklemeyi kullanarak geçerli varlığı veritabanından alır. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="ac0b7-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="ac0b7-322">Alınan `Instructor` varlığı model bağlayıcısından gelen değerlerle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="ac0b7-323">`TryUpdateModel`aşırı [göndermeyi](xref:data/ef-rp/crud#overposting)önler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="ac0b7-324">Ofis konumu boşsa, null `Instructor.OfficeAssignment` ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="ac0b7-325">Null `Instructor.OfficeAssignment` olduğunda, tablodaki `OfficeAssignment` ilgili satır silinir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="ac0b7-326">Eğitmen Edit sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ac0b7-326">Update the instructor Edit page</span></span>

<span data-ttu-id="ac0b7-327">*Sayfaları/Eğitmenleri/Edit.cshtml'i* ofis konumuyla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="ac0b7-328">Eğitmenlerin ofis konumunu değiştirebileceğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="ac0b7-329">Eğitmen Edit sayfasına Ders atamaları ekleme</span><span class="sxs-lookup"><span data-stu-id="ac0b7-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="ac0b7-330">Eğitmenler herhangi bir sayıda ders verebilirler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="ac0b7-331">Bu bölümde, ders atamalarını değiştirme yeteneğini eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="ac0b7-332">Aşağıdaki resimde güncelleştirilmiş eğitmen Edit sayfası gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-332">The following image shows the updated instructor Edit page:</span></span>

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="ac0b7-334">`Course`ve `Instructor` çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="ac0b7-335">İlişkiler eklemek ve kaldırmak için, varlıkları birleştirme `CourseAssignments` varlık kümesinden ekler ve kaldırırsınız.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="ac0b7-336">Onay kutuları, bir eğitmenin atandığı derslerde değişiklik yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="ac0b7-337">Veritabanındaki her ders için bir onay kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="ac0b7-338">Eğitmenin atandığı dersler kontrol edilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="ac0b7-339">Kullanıcı, ders atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="ac0b7-340">Kurs sayısı çok daha fazla olsaydı:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="ac0b7-341">Kursları görüntülemek için muhtemelen farklı bir kullanıcı arabirimi kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="ac0b7-342">İlişkiler oluşturmak veya silmek için birleştirme varlığını manipüle etme yöntemi değişmez.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="ac0b7-343">Eğitmen sayfalarını oluştur ve edit'i desteklemek için sınıflar ekleme</span><span class="sxs-lookup"><span data-stu-id="ac0b7-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="ac0b7-344">Aşağıdaki kodla *SchoolViewModels/AssignedCourseData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="ac0b7-345">Sınıf, `AssignedCourseData` bir eğitmen tarafından atanan dersler için onay kutularını oluşturmak için veriler içerir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="ac0b7-346">*Sayfaları/Eğitmenleri/EğitmenKurslarıSayfaModel.cshtml.cs* taban sınıfını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="ac0b7-347">Sayfa `InstructorCoursesPageModel` yı oluştur ve oluştur modelleri için kullanacağınız taban sınıfdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="ac0b7-348">`PopulateAssignedCourseData`doldurmak `Course` için tüm varlıkları `AssignedCourseDataList`okur.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="ac0b7-349">Her kurs için, kod `CourseID`, başlık ve olup olmadığını eğitmen derse atanmış olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="ac0b7-350">[Bir HashSet](/dotnet/api/system.collections.generic.hashset-1) verimli aramalar oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="ac0b7-351">Eğitmenler Sayfa modelini düzenleme</span><span class="sxs-lookup"><span data-stu-id="ac0b7-351">Instructors Edit page model</span></span>

<span data-ttu-id="ac0b7-352">Eğitmen Düzenleme sayfa modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="ac0b7-353">Önceki kod ofis atama değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="ac0b7-354">Eğitmen Razor View güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="ac0b7-355">Visual Studio'da kodu yapıştırdığınızda, satır sonları kodu kırılayacak şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="ac0b7-356">Otomatik biçimlendirmeyi geri almak için ctrl+Z tuşuna bir kez basın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="ac0b7-357">Ctrl+Z, satır sonları burada gördüğünüz gibi görünecek şekilde düzeltir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="ac0b7-358">Girintinin mükemmel olması gerekmez, ancak `@:</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` ve çizgilerin her biri gösterildiği gibi tek bir satırda olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="ac0b7-359">Yeni kod bloğu seçilise, yeni kodu varolan kodla hizalamak için Sekme'ye üç kez basın.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="ac0b7-360">Bu bağlantıyla bu hatanın durumunu oyleyin veya gözden [geçirin.](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="ac0b7-361">Önceki kod, üç sütunu olan bir HTML tablosu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="ac0b7-362">Her sütunda bir onay kutusu ve ders numarası ve başlığı içeren bir başlık vardır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="ac0b7-363">Onay kutularının tümü aynı ada ("selectedCourses") sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="ac0b7-364">Aynı adı kullanarak bir grup olarak tedavi etmek için model bağlayıcı bildirir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="ac0b7-365">Her onay kutusunun değer özniteliği `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="ac0b7-366">Sayfa deftere nakledildiğinde, model bağlayıcısı yalnızca seçili onay kutularıiçin `CourseID` değerlerden oluşan bir dizigeçer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="ac0b7-367">Onay kutuları başlangıçta işlendiğinde, eğitmene atanan dersler öznitelikleri denetlemektedir.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="ac0b7-368">Uygulamayı çalıştırın ve güncelleştirilmiş eğitmenlerin Edit sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="ac0b7-369">Bazı ders atamalarını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-369">Change some course assignments.</span></span> <span data-ttu-id="ac0b7-370">Değişiklikler Dizin sayfasına yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="ac0b7-371">Not: Eğitmen ders verilerini yeniden düzenlemesi için burada alınan yaklaşım, sınırlı sayıda kurs olduğunda iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="ac0b7-372">Çok daha büyük koleksiyonlar için, farklı bir ui ve farklı bir güncelleştirme yöntemi daha kullanılabilir ve verimli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="ac0b7-373">Eğitmenleri güncelleştirsayfa oluşturma</span><span class="sxs-lookup"><span data-stu-id="ac0b7-373">Update the instructors Create page</span></span>

<span data-ttu-id="ac0b7-374">Eğitmen Oluştur sayfa modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="ac0b7-375">Önceki kod *Sayfalar/Eğitmenler/Edit.cshtml.cs* koduna benzer.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="ac0b7-376">Eğitmen Jilet Oluştur sayfasını aşağıdaki biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="ac0b7-377">Eğitmen Oluştur sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="ac0b7-378">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="ac0b7-378">Update the Delete page</span></span>

<span data-ttu-id="ac0b7-379">Sayfasil modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="ac0b7-380">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ac0b7-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ac0b7-381">Gezinti özelliği için `CourseAssignments` istekli yükleme kullanır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="ac0b7-382">`CourseAssignments`eklenmesi gerekir veya eğitmen silindiğinde silinmezler.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="ac0b7-383">Bunları okumaya gerek kalmamak için, veritabanında basamaklı silme yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="ac0b7-384">Silinecek eğitmen herhangi bir bölümün yöneticisi olarak atanırsa, eğitmen atamasını bu bölümlerden kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ac0b7-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac0b7-385">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ac0b7-385">Additional resources</span></span>

* [<span data-ttu-id="ac0b7-386">Bu öğretici Nin YouTube sürümü (Bölüm 1)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="ac0b7-387">Bu öğretici YouTube sürümü (Bölüm 2)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="ac0b7-388">[Önceki](xref:data/ef-rp/read-related-data)
> [Sonraki](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="ac0b7-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
