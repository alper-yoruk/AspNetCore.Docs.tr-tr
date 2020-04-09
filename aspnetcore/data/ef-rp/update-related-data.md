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
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>ASP.NET Core'da EF Core'lu Jilet Sayfaları - İlgili Verileri Güncelle - 7 /8

Yazar: [Tom Dykstra](https://github.com/tdykstra)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, ilgili verilerin nasıl güncelleştirilebildiğini gösterir. Aşağıdaki çizimler, tamamlanan bazı sayfaları gösterir.

![Ders Edit](update-related-data/_static/course-edit30.png)
![sayfası Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses30.png)

## <a name="update-the-course-create-and-edit-pages"></a>Ders Oluştur ve Sayfalarını Güncelleştir

Ders Oluştur ve Edit sayfaları için iskele kodu, Bölüm Kimliğini (tamsayı) gösteren bir Bölüm açılır listesine sahiptir. Açılan sayfabölüm adını göstermelidir, bu nedenle bu sayfaların her ikisinin de bölüm adlarının bir listesi gerekir. Bu listeyi sağlamak için, Oluştur ve Edit sayfaları için bir taban sınıf kullanın.

### <a name="create-a-base-class-for-course-create-and-edit"></a>Ders Oluştur ve Edin için taban sınıf oluşturma

Aşağıdaki kodile bir *Sayfa/Kurs/DepartmentNamePageModel.cs* dosyası oluşturun:

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

Önceki kod, bölüm adları listesini içerecek bir [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) oluşturur. `selectedDepartment` Belirtilirse, bu bölüm `SelectList`seçilir.

Oluştur ve Düzenleme sayfası modeli sınıfları. `DepartmentNamePageModel`

### <a name="update-the-course-create-page-model"></a>Ders Oluştur sayfa modelini güncelleştirin

Bir Ders Bir Bölüme atanır. Oluştur ve Edit sayfaları için taban `SelectList` sınıf bölümü seçmek için bir sağlar. Yabancı anahtar (FK) `SelectList` özelliğini kümeleyen `Course.DepartmentID` açılır liste. EF Core, `Course.DepartmentID` navigasyon özelliğini `Department` yüklemek için FK'yı kullanır.

![Kurs oluşturma](update-related-data/_static/ddl30.png)

*Sayfaları/Dersleri/Create.cshtml.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Yukarıdaki kod:

* `DepartmentNamePageModel`Türetilmiştir.
* Aşırı `TryUpdateModelAsync` [göndermeyi](xref:data/ef-rp/crud#overposting)önlemek için kullanır.
* `ViewData["DepartmentID"]`Kaldırır. `DepartmentNameSL`taban sınıftan güçlü bir şekilde yazılan bir modeldir ve Razor sayfası tarafından kullanılacaktır. Güçlü dakti-si yazılan modeller zayıf yazılanmodellere göre tercih edilir. Daha fazla bilgi için zayıf [yazılan verilere (ViewData ve ViewBag)](xref:mvc/views/overview#VD_VB)bakın.

### <a name="update-the-course-create-razor-page"></a>Rota Oluştur Jilet sayfasını güncelleştirin

*Sayfaları/Kursları/Create.cshtml'i* aşağıdaki kodla güncelleyin:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

Önceki kod aşağıdaki değişiklikleri yapar:

* **Başlık BölümKimliği'nden** **Departmana**dönüşür.
* (taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.
* "Bölüm Seç" seçeneğini ekler. Bu değişiklik, ilk bölüm yerine henüz hiçbir bölüm seçilmediğinde açılan bölümde "Bölüm Seç"i işler.
* Bölüm seçilmediğinde bir doğrulama iletisi ekler.

Jilet Sayfası [Seç Etiket Yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper)kullanır:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Oluştur sayfasını test edin. Oluştur sayfası, departman kimliği yerine bölüm adını görüntüler.

### <a name="update-the-course-edit-page-model"></a>Ders Düzenleme sayfası modelini güncelleştirin

*Sayfaları/Dersleri/Edit.cshtml.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

Değişiklikler, Sayfa Oluştur modelinde yapılanlara benzer. Önceki kodda, `PopulateDepartmentsDropDownList` açılan listedeki bölümü seçen bölüm kimliği geçer.

### <a name="update-the-course-edit-razor-page"></a>Kursu Eritme Jiletsayfasını Güncelleştir

*Sayfaları/Kursları/Edit.cshtml'i* aşağıdaki kodla güncelleyin:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Önceki kod aşağıdaki değişiklikleri yapar:

* Kurs kimliğini görüntüler. Genellikle bir varlığın Birincil Anahtarı (PK) görüntülenmez. PK'lar genellikle kullanıcılar için anlamsızdır. Bu durumda, PK kurs numarasıdır.
* Bölüm Bölümü'nün bölüm den **BölümKimliği'ne** **Department**açılan alt yazısını değiştirir.
* (taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.

Sayfa, kurs numarası`<input type="hidden">`için gizli bir alan ( ) içerir. Bir `asp-for="Course.CourseID"` `<label>` etiket yardımcısı eklemek, gizli alana olan gereksinimi ortadan kaldırmaz. `<input type="hidden">`kullanıcı **Kaydet'i**tıklattığında, gönderilen verilere eklenecek kurs numarasının olması gerekmektedir.

## <a name="update-the-course-details-and-delete-pages"></a>Ders Ayrıntılarını Güncelleştir in ve Sayfalarını Sil

[AsNoTracking,](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) izleme gerekli olmadığında performansı artırabilir.

### <a name="update-the-course-page-models"></a>Kurs sayfası modellerini güncelleştirin

*Güncelleme Sayfaları /Kurslar/Delete.cshtml.cs* aşağıdaki kodu `AsNoTracking`eklemek için:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

*Sayfalar/Kurslar/Ayrıntılar.cshtml.cs* dosyasında da aynı değişikliği yapın:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a>Course Razor sayfalarını güncelleştirin

*Sayfaları/Dersleri/Sil.cshtml'i* aşağıdaki kodla güncelleyin:

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

Ayrıntılar sayfasında da aynı değişiklikleri yapın.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a>Ders sayfalarını test edin

Sayfaları oluşturma, düzenle, ayrıntıları ve silmeyi test edin.

## <a name="update-the-instructor-create-and-edit-pages"></a>Eğitmen Oluşturma ve Sayfalarını Edin

Eğitmenler herhangi bir sayıda ders verebilirler. Aşağıdaki resimde, eğitmen Düzenle sayfasını bir dizi ders onay kutusu ile gösterilmektedir.

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses30.png)

Onay kutuları, bir eğitmenin atandığı derslerde değişiklik yapılmasını sağlar. Veritabanındaki her ders için bir onay kutusu görüntülenir. Eğitmenin atandığı dersler seçilir. Kullanıcı, ders atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir. Kurs sayısı çok daha fazla olsaydı, farklı bir u-ui daha iyi çalışabilir. Ama burada gösterilen çok-çok ilişkisi yönetme yöntemi değişmez. İlişkiler oluşturmak veya silmek için bir birleştirme varlığını manipüle eleştirirsiniz.

### <a name="create-a-class-for-assigned-courses-data"></a>Atanan ders verileri için sınıf oluşturma

Aşağıdaki kodla *SchoolViewModels/AssignedCourseData.cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

Sınıf, `AssignedCourseData` bir eğitmene atanan derslerin onay kutularını oluşturmak için veriler içerir.

### <a name="create-an-instructor-page-model-base-class"></a>Eğitmen sayfası modeli taban sınıfı oluşturma

*Sayfaları/Eğitmenleri/EğitmenKurslarıSayfaModel.cs* taban sınıfını oluşturun:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

Sayfa `InstructorCoursesPageModel` yı oluştur ve oluştur modelleri için kullanacağınız taban sınıfdır. `PopulateAssignedCourseData`doldurmak `Course` için tüm varlıkları `AssignedCourseDataList`okur. Her kurs için, kod `CourseID`, başlık ve olup olmadığını eğitmen derse atanmış olarak ayarlar. [HashSet](/dotnet/api/system.collections.generic.hashset-1) verimli aramalar için kullanılır.

Razor sayfasında Ders varlıkları koleksiyonu olmadığından, model bağlayıcısı gezinme özelliğini `CourseAssignments` otomatik olarak güncelleştiremez. Gezinti özelliğini güncelleştirmek için `CourseAssignments` model bağlayıcısını kullanmak yerine, `UpdateInstructorCourses` bunu yeni yöntemde yaparsınız. Bu nedenle `CourseAssignments` özelliği model bağlamadan çıkarmanız gerekir. Bu, beyaz liste aşırı yüklemesini `TryUpdateModel` kullandığınız ve `CourseAssignments` dahil listesinde olmadığınız için çağıran kodda herhangi bir değişiklik gerektirmez.

Onay kutusu seçilmediyse, kod `UpdateInstructorCourses` gezinti özelliğini `CourseAssignments` boş bir koleksiyonla baş harfe döndürür ve döndürür:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

Kod daha sonra veritabanındaki tüm dersler arasında döngüler ve her ders şu anda öğretim üyesi ne sayfada seçilmiş olanlar karşı atanan karşı kontrol eder. Verimli aramaları kolaylaştırmak için, son iki koleksiyon `HashSet` nesnelerde depolanır.

Bir kursun onay kutusu seçiliyse ancak kurs `Instructor.CourseAssignments` navigasyon özelliğinde değilse, kurs gezinti özelliğindeki koleksiyona eklenir.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

Bir kursun onay kutusu seçilmediyse, ancak kurs navigasyon `Instructor.CourseAssignments` özelliğindeyse, kurs navigasyon özelliğinden kaldırılır.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a>Ofis konumunu işleme

Edit sayfasının ele almak zorunda olduğu bir diğer ilişki de, Eğitmen varlığın `OfficeAssignment` varlıkla sahip olduğu birden sıfıra veya bir ilişkisidir. Eğitmen edit kodu aşağıdaki senaryoları işlemelidir: 

* Kullanıcı ofis atamasını temizlerse, `OfficeAssignment` varlığı silin.
* Kullanıcı bir ofis ataması girerse ve boşsa, yeni `OfficeAssignment` bir varlık oluşturun.
* Kullanıcı ofis atamasını değiştirirse, varlığı güncelleştirin. `OfficeAssignment`

### <a name="update-the-instructor-edit-page-model"></a>Eğitmen Düzenleme sayfası modelini güncelleştirin

*Sayfaları/Eğitmenleri/Edit.cshtml.cs'yi* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

Yukarıdaki kod:

* , , `Instructor` `CourseAssignment`ve `CourseAssignment.Course` gezinme özellikleri için istekli yükleme kullanarak veritabanından geçerli varlık alır. `OfficeAssignment`
* Alınan `Instructor` varlığı model bağlayıcısından gelen değerlerle güncelleştirir. `TryUpdateModel`aşırı [göndermeyi](xref:data/ef-rp/crud#overposting)önler.
* Ofis konumu boşsa, null `Instructor.OfficeAssignment` ayarlar. Null `Instructor.OfficeAssignment` olduğunda, tablodaki `OfficeAssignment` ilgili satır silinir.
* Görünüm modeli sınıfını kullanarak onay kutuları için bilgi sağlamak için çağırır. `PopulateAssignedCourseData` `OnGetAsync` `AssignedCourseData`
* Onay kutularından gelen bilgileri düzenlenen Eğitmen varlığına uygulamak için çağrılar. `UpdateInstructorCourses` `OnPostAsync`
* Aramalar `PopulateAssignedCourseData` `UpdateInstructorCourses` ve `OnPostAsync` `TryUpdateModel` başarısız olursa. Bu yöntem, bir hata iletisi ile yeniden görüntülendiğinde sayfada girilen atanan kurs verilerini geri yükleme çağrıları.

### <a name="update-the-instructor-edit-razor-page"></a>Eğitmen Razor sayfasını güncelleştirin

*Sayfaları/Eğitmenleri/Edit.cshtml'i* aşağıdaki kodla güncelleyin:

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

Önceki kod, üç sütunu olan bir HTML tablosu oluşturur. Her sütunda bir onay kutusu ve ders numarasını ve başlığını içeren bir başlık vardır. Onay kutularının tümü aynı ada ("selectedCourses") sahiptir. Aynı adı kullanarak bir grup olarak tedavi etmek için model bağlayıcı bildirir. Her onay kutusunun değer özniteliği `CourseID`. Sayfa deftere nakledildiğinde, model bağlayıcısı yalnızca seçili onay kutularıiçin `CourseID` değerlerden oluşan bir dizigeçer.

Onay kutuları ilk işlendiğinde, eğitmene atanan dersler seçilir.

Not: Eğitmen ders verilerini yeniden düzenlemesi için burada alınan yaklaşım, sınırlı sayıda kurs olduğunda iyi çalışır. Çok daha büyük koleksiyonlar için, farklı bir ui ve farklı bir güncelleştirme yöntemi daha kullanılabilir ve verimli olacaktır.

Uygulamayı çalıştırın ve güncelleştirilmiş Eğitmenler Ii tuşu sayfasını test edin. Bazı ders atamalarını değiştirin. Değişiklikler Dizin sayfasına yansıtılır.

### <a name="update-the-instructor-create-page"></a>Eğitmen Oluştur sayfasını güncelleştir

Eğitmen Oluştur sayfa modelini ve Razor sayfasını Düzenleme sayfasına benzer kodlarla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

Eğitmen Oluştur sayfasını test edin.

## <a name="update-the-instructor-delete-page"></a>Eğitmen Silme sayfasını güncelleştirin

*Sayfaları/Eğitmenleri/Sil.cshtml.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

Önceki kod aşağıdaki değişiklikleri yapar:

* Gezinti özelliği için `CourseAssignments` istekli yükleme kullanır. `CourseAssignments`eklenmesi gerekir veya eğitmen silindiğinde silinmezler. Bunları okumaya gerek kalmamak için, veritabanında basamaklı silme yapılandırma.

* Silinecek eğitmen herhangi bir bölümün yöneticisi olarak atanırsa, eğitmen atamasını bu bölümlerden kaldırır.

Uygulamayı çalıştırın ve Sil sayfasını test edin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/read-related-data)
> [Sonraki öğretici](xref:data/ef-rp/concurrency)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğretici, ilgili verilerin güncelleştirilmesini gösterir. Çözemediğiniz sorunlarla karşılaştıysanız, [tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Talimatları indirin.](xref:index#how-to-download-a-sample)

Aşağıdaki çizimler, tamamlanan bazı sayfaları gösterir.

![Ders Edit](update-related-data/_static/course-edit.png)
![sayfası Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

Oluştur ve Yap ders sayfalarını inceleyin ve test edin. Yeni bir rota oluşturun. Bölüm, adı değil, birincil anahtarı (bir sonda) tarafından seçilir. Yeni rotayı yeniden edin. Testi tamamladığınızda, yeni kursu silin.

## <a name="create-a-base-class-to-share-common-code"></a>Ortak kodu paylaşmak için taban sınıf oluşturma

Dersler/Oluşturma ve Kurs/Edit sayfaları her bölüm adlarının bir listesini gerekir. Oluştur ve Düzenleme sayfaları için *Sayfalar/Kurslar/DepartmentNamePageModel.cshtml.cs* taban sınıfını oluşturun:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Önceki kod, bölüm adları listesini içerecek bir [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) oluşturur. `selectedDepartment` Belirtilirse, bu bölüm `SelectList`seçilir.

Oluştur ve Düzenleme sayfası modeli sınıfları. `DepartmentNamePageModel`

## <a name="customize-the-courses-pages"></a>Ders Sayfalarını Özelleştir

Yeni bir ders varlığı oluşturulduğunda, varolan bir bölümle ilişkisi olmalıdır. Kurs oluştururken bölüm eklemek için, Oluştur ve Edit taban sınıfı bölümü seçmek için bir açılır liste içerir. Açılan liste yabancı anahtarı `Course.DepartmentID` (FK) özelliğini ayarlar. EF Core, `Course.DepartmentID` navigasyon özelliğini `Department` yüklemek için FK'yı kullanır.

![Kurs oluşturma](update-related-data/_static/ddl.png)

Sayfa oluştur modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Yukarıdaki kod:

* `DepartmentNamePageModel`Türetilmiştir.
* Aşırı `TryUpdateModelAsync` [göndermeyi](xref:data/ef-rp/crud#overposting)önlemek için kullanır.
* (taban `ViewData["DepartmentID"]` `DepartmentNameSL` sınıftan) ile değiştirir.

`ViewData["DepartmentID"]`güçlü bir şekilde yazılanile `DepartmentNameSL`değiştirilir. Güçlü dakti-si yazılan modeller zayıf yazılanmodellere göre tercih edilir. Daha fazla bilgi için zayıf [yazılan verilere (ViewData ve ViewBag)](xref:mvc/views/overview#VD_VB)bakın.

### <a name="update-the-courses-create-page"></a>Kurslar Oluşturma sayfasını güncelleştirin

*Sayfaları/Kursları/Create.cshtml'i* aşağıdaki kodla güncelleyin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Önceki biçimlendirme aşağıdaki değişiklikleri yapar:

* **Başlık BölümKimliği'nden** **Departmana**dönüşür.
* (taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.
* "Bölüm Seç" seçeneğini ekler. Bu değişiklik, birinci bölüm yerine "Bölüm Seç"i işler.
* Bölüm seçilmediğinde bir doğrulama iletisi ekler.

Jilet Sayfası [Seç Etiket Yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper)kullanır:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Oluştur sayfasını test edin. Oluştur sayfası, departman kimliği yerine bölüm adını görüntüler.

### <a name="update-the-courses-edit-page"></a>Dersleri Edit sayfasını güncelleyin.

*Sayfalar/Kurslar/Edit.cshtml.cs'deki* kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Değişiklikler, Sayfa Oluştur modelinde yapılanlara benzer. Önceki kodda, `PopulateDepartmentsDropDownList` açılan listede belirtilen bölümü seçen departman kimliği geçer.

*Sayfaları/Dersleri/Edit.cshtml'i* aşağıdaki biçimlendirmeyle güncelleyin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Önceki biçimlendirme aşağıdaki değişiklikleri yapar:

* Kurs kimliğini görüntüler. Genellikle bir varlığın Birincil Anahtarı (PK) görüntülenmez. PK'lar genellikle kullanıcılar için anlamsızdır. Bu durumda, PK kurs numarasıdır.
* **Başlık BölümKimliği'nden** **Departmana**dönüşür.
* (taban `"ViewBag.DepartmentID"` `DepartmentNameSL` sınıftan) ile değiştirir.

Sayfa, kurs numarası`<input type="hidden">`için gizli bir alan ( ) içerir. Bir `asp-for="Course.CourseID"` `<label>` etiket yardımcısı eklemek, gizli alana olan gereksinimi ortadan kaldırmaz. `<input type="hidden">`kullanıcı **Kaydet'i**tıklattığında, gönderilen verilere eklenecek kurs numarasının olması gerekmektedir.

Güncelleştirilmiş kodu test edin. Bir kurs oluşturun, düzenleyin ve silin.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Ayrıntılara AsNoTracking ekleyin ve sayfa modellerini sil

[AsNoTracking,](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) izleme gerekli olmadığında performansı artırabilir. Sil `AsNoTracking` ve Ayrıntılar sayfa modeline ekleyin. Aşağıdaki kod güncelleştirilmiş Delete sayfa modelini gösterir:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

`OnGetAsync` *Sayfalar/Kurslar/Ayrıntılar.cshtml.cs* dosyasındaki yöntemi güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Sil ve Ayrıntılar sayfalarını değiştirme

Razor'u sil sayfasını aşağıdaki biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Ayrıntılar sayfasında da aynı değişiklikleri yapın.

### <a name="test-the-course-pages"></a>Ders sayfalarını test edin

Test oluştur, düzenle, ayrıntıları ve silme.

## <a name="update-the-instructor-pages"></a>Eğitmen sayfalarını güncelleştirin

Aşağıdaki bölümler eğitmen sayfalarını günceller.

### <a name="add-office-location"></a>Ofis konumu ekleme

Bir eğitmen kaydını düzenlerken, eğitmenin ofis atamasını güncelleştirmek isteyebilirsiniz. Varlığın `Instructor` `OfficeAssignment` varlıkla bire sıfır veya bir ilişkisi vardır. Eğitmen kodu işlemelidir:

* Kullanıcı ofis atamasını temizlerse, `OfficeAssignment` varlığı silin.
* Kullanıcı bir ofis ataması girerse ve boşsa, yeni `OfficeAssignment` bir varlık oluşturun.
* Kullanıcı ofis atamasını değiştirirse, varlığı güncelleştirin. `OfficeAssignment`

Eğitmenlersayfa modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Yukarıdaki kod:

* Gezinti özelliği `Instructor` için istekli yüklemeyi kullanarak geçerli varlığı veritabanından alır. `OfficeAssignment`
* Alınan `Instructor` varlığı model bağlayıcısından gelen değerlerle güncelleştirir. `TryUpdateModel`aşırı [göndermeyi](xref:data/ef-rp/crud#overposting)önler.
* Ofis konumu boşsa, null `Instructor.OfficeAssignment` ayarlar. Null `Instructor.OfficeAssignment` olduğunda, tablodaki `OfficeAssignment` ilgili satır silinir.

### <a name="update-the-instructor-edit-page"></a>Eğitmen Edit sayfasını güncelleştir

*Sayfaları/Eğitmenleri/Edit.cshtml'i* ofis konumuyla güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Eğitmenlerin ofis konumunu değiştirebileceğinizi doğrulayın.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Eğitmen Edit sayfasına Ders atamaları ekleme

Eğitmenler herhangi bir sayıda ders verebilirler. Bu bölümde, ders atamalarını değiştirme yeteneğini eklersiniz. Aşağıdaki resimde güncelleştirilmiş eğitmen Edit sayfası gösterilmektedir:

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

`Course`ve `Instructor` çok-çok ilişkisi vardır. İlişkiler eklemek ve kaldırmak için, varlıkları birleştirme `CourseAssignments` varlık kümesinden ekler ve kaldırırsınız.

Onay kutuları, bir eğitmenin atandığı derslerde değişiklik yapılmasını sağlar. Veritabanındaki her ders için bir onay kutusu görüntülenir. Eğitmenin atandığı dersler kontrol edilir. Kullanıcı, ders atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir. Kurs sayısı çok daha fazla olsaydı:

* Kursları görüntülemek için muhtemelen farklı bir kullanıcı arabirimi kullanırsınız.
* İlişkiler oluşturmak veya silmek için birleştirme varlığını manipüle etme yöntemi değişmez.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Eğitmen sayfalarını oluştur ve edit'i desteklemek için sınıflar ekleme

Aşağıdaki kodla *SchoolViewModels/AssignedCourseData.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Sınıf, `AssignedCourseData` bir eğitmen tarafından atanan dersler için onay kutularını oluşturmak için veriler içerir.

*Sayfaları/Eğitmenleri/EğitmenKurslarıSayfaModel.cshtml.cs* taban sınıfını oluşturun:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

Sayfa `InstructorCoursesPageModel` yı oluştur ve oluştur modelleri için kullanacağınız taban sınıfdır. `PopulateAssignedCourseData`doldurmak `Course` için tüm varlıkları `AssignedCourseDataList`okur. Her kurs için, kod `CourseID`, başlık ve olup olmadığını eğitmen derse atanmış olarak ayarlar. [Bir HashSet](/dotnet/api/system.collections.generic.hashset-1) verimli aramalar oluşturmak için kullanılır.

### <a name="instructors-edit-page-model"></a>Eğitmenler Sayfa modelini düzenleme

Eğitmen Düzenleme sayfa modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Önceki kod ofis atama değişiklikleri işler.

Eğitmen Razor View güncelleyin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Visual Studio'da kodu yapıştırdığınızda, satır sonları kodu kırılayacak şekilde değiştirilir. Otomatik biçimlendirmeyi geri almak için ctrl+Z tuşuna bir kez basın. Ctrl+Z, satır sonları burada gördüğünüz gibi görünecek şekilde düzeltir. Girintinin mükemmel olması gerekmez, ancak `@:</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` ve çizgilerin her biri gösterildiği gibi tek bir satırda olmalıdır. Yeni kod bloğu seçilise, yeni kodu varolan kodla hizalamak için Sekme'ye üç kez basın. Bu bağlantıyla bu hatanın durumunu oyleyin veya gözden [geçirin.](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)

Önceki kod, üç sütunu olan bir HTML tablosu oluşturur. Her sütunda bir onay kutusu ve ders numarası ve başlığı içeren bir başlık vardır. Onay kutularının tümü aynı ada ("selectedCourses") sahiptir. Aynı adı kullanarak bir grup olarak tedavi etmek için model bağlayıcı bildirir. Her onay kutusunun değer özniteliği `CourseID`. Sayfa deftere nakledildiğinde, model bağlayıcısı yalnızca seçili onay kutularıiçin `CourseID` değerlerden oluşan bir dizigeçer.

Onay kutuları başlangıçta işlendiğinde, eğitmene atanan dersler öznitelikleri denetlemektedir.

Uygulamayı çalıştırın ve güncelleştirilmiş eğitmenlerin Edit sayfasını test edin. Bazı ders atamalarını değiştirin. Değişiklikler Dizin sayfasına yansıtılır.

Not: Eğitmen ders verilerini yeniden düzenlemesi için burada alınan yaklaşım, sınırlı sayıda kurs olduğunda iyi çalışır. Çok daha büyük koleksiyonlar için, farklı bir ui ve farklı bir güncelleştirme yöntemi daha kullanılabilir ve verimli olacaktır.

### <a name="update-the-instructors-create-page"></a>Eğitmenleri güncelleştirsayfa oluşturma

Eğitmen Oluştur sayfa modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Önceki kod *Sayfalar/Eğitmenler/Edit.cshtml.cs* koduna benzer.

Eğitmen Jilet Oluştur sayfasını aşağıdaki biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Eğitmen Oluştur sayfasını test edin.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

Sayfasil modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Önceki kod aşağıdaki değişiklikleri yapar:

* Gezinti özelliği için `CourseAssignments` istekli yükleme kullanır. `CourseAssignments`eklenmesi gerekir veya eğitmen silindiğinde silinmezler. Bunları okumaya gerek kalmamak için, veritabanında basamaklı silme yapılandırma.

* Silinecek eğitmen herhangi bir bölümün yöneticisi olarak atanırsa, eğitmen atamasını bu bölümlerden kaldırır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğretici Nin YouTube sürümü (Bölüm 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Bu öğretici YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/read-related-data)
> [Sonraki](xref:data/ef-rp/concurrency)

::: moniker-end
