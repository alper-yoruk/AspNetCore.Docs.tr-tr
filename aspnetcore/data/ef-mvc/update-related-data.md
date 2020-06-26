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
ms.openlocfilehash: 59bf94f6818108f09e9af147559fc304f48936bc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401317"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Öğretici: ilgili verileri güncelleştirme-ASP.NET MVC EF Core

Önceki öğreticide ilgili verileri görüntülediyseniz; Bu öğreticide, yabancı anahtar alanlarını ve gezinti özelliklerini güncelleştirerek ilgili verileri güncelleştireceksiniz.

Aşağıdaki çizimlerde, üzerinde çalıştığınız sayfaların bazıları gösterilmektedir.

![Kurs düzenleme sayfası](update-related-data/_static/course-edit.png)

![Eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Kurslar sayfalarını özelleştirme
> * Eğitmenler düzenleme sayfası ekle
> * Düzenleme sayfasına kurslar ekleyin
> * Güncelleştirme silme sayfası
> * Sayfa oluşturmak için Office konumu ve kurslar ekleme

## <a name="prerequisites"></a>Ön koşullar

* [İlgili verileri okuma](read-related-data.md)

## <a name="customize-courses-pages"></a>Kurslar sayfalarını özelleştirme

Yeni bir kurs varlığı oluşturulduğunda, mevcut bir departmanla bir ilişkisi olmalıdır. Bunu kolaylaştırmak için, yapı iskelesi kodu denetleyici yöntemlerini içerir ve departmanı seçmeye yönelik bir açılan liste içeren görünümler oluşturup düzenleyebilir. Açılan liste, `Course.DepartmentID` yabancı anahtar özelliğini ayarlar ve `Department` ilgili departman varlığı ile gezinti özelliğini yüklemek için tüm Entity Framework gereksinimlidir. Scafkatmış kodu kullanacaksınız, ancak hata işleme eklemek ve açılan listeyi sıralamak için biraz değişiklik yapacaksınız.

*CoursesController.cs*' de, dört oluşturma ve düzenleme yöntemini silin ve bunları şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

`Edit`HttpPost yönteminden sonra, açılan liste için departman bilgisini yükleyen yeni bir yöntem oluşturun.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

`PopulateDepartmentsDropDownList`Yöntemi ada göre sıralanmış tüm bölümlerin listesini alır, `SelectList` açılan liste için bir koleksiyon oluşturur ve koleksiyonu içindeki görünümüne geçirir `ViewBag` . Yöntemi, `selectedDepartment` çağıran kodun, açılan liste işlendiğinde seçilecek öğeyi belirtmesini sağlayan isteğe bağlı parametresini kabul eder. Görünüm, "DepartmentID" adını `<select>` etiket yardımcısından geçireceğini ve yardımcı sonra, `ViewBag` `SelectList` "DepartmentID" adlı bir nesneyi bulmak için bu adı bilecektir.

HttpGet `Create` yöntemi, `PopulateDepartmentsDropDownList` Seçilen öğeyi ayarlamadan yöntemi çağırır, çünkü yeni bir kurs için departman henüz kurulmadı:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet `Edit` yöntemi, düzenlenen kursa zaten atanmış olan DEPARTMANıN kimliğine bağlı olarak seçili öğeyi ayarlar:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Hem hem de için HttpPost yöntemleri, `Create` `Edit` bir hatadan sonra sayfayı yeniden görüntülerken seçili öğeyi ayarlayan kodu içerir. Bu, sayfa hata iletisini göstermek için yeniden görüntülendiğinde, seçilen departmanın seçili kalır olduğunu sağlar.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Ekleyemiyorum. Ayrıntı ve silme yöntemlerine AsNoTracking

Kurs ayrıntılarının ve sayfa silmenin performansını iyileştirmek için, `AsNoTracking` `Details` ve HttpGet yöntemlerine çağrı ekleyin `Delete` .

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Kurs görünümlerini değiştirme

*Görünümler/kurslar/oluşturma. cshtml*'de, **Departman** açılan listesine bir "Departman Seç" seçeneği ekleyin, **DepartmentID** etiketini **bölüm**olarak değiştirin ve bir doğrulama iletisi ekleyin.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

*Görünümler/kurslar/Düzenle. cshtml*'de, *Create. cshtml*içinde yaptığınız departman alanı için aynı değişikliği yapın.

Ayrıca, *Görünümler/kurslar/Düzenle. cshtml*'de **başlık** alanından önce bir kurs numarası alanı ekleyin. Kurs numarası birincil anahtar olduğundan, görüntülenir, ancak değiştirilemez.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

`<input type="hidden">`Düzenleme görünümündeki kurs numarası için zaten gizli bir alan () var. `<label>`Kullanıcı **düzenleme** sayfasında **Kaydet** ' i tıklattığında, bir etiket Yardımcısı ekleme, gizli alan gereksinimini ortadan kaldırmaz, çünkü

*Görünümler/kurslar/delete. cshtml*'de, üst kısımdaki bir kurs numarası alanı ekleyin ve bölüm kimliğini bölüm adı olarak değiştirin.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

*Görünümler/kurslar/ayrıntılar. cshtml*'de, *delete. cshtml*için yaptığınız aynı değişikliği yapın.

### <a name="test-the-course-pages"></a>Kurs sayfalarını test etme

Uygulamayı çalıştırın, **Kurslar** sekmesini seçin, **Yeni oluştur**' a tıklayın ve yeni bir kurs için veri girin:

![Kurs sayfa oluştur](update-related-data/_static/course-create.png)

**Oluştur**'a tıklayın. Kurslar Dizin sayfası, listeye eklenen yeni kursla birlikte görüntülenir. Dizin sayfası listesindeki departman adı, ilişkinin doğru şekilde oluşturulduğunu gösteren gezinti özelliğinden gelir.

Kurslar Dizin sayfasında bir kursa **Düzenle** ' ye tıklayın.

![Kurs düzenleme sayfası](update-related-data/_static/course-edit.png)

Sayfadaki verileri değiştirin ve **Kaydet**' e tıklayın. Kurslar Dizin sayfası, güncelleştirilmiş kurs verileriyle birlikte görüntülenir.

## <a name="add-instructors-edit-page"></a>Eğitmenler düzenleme sayfası ekle

Bir eğitmen kaydını düzenlediğinizde, eğitmenin Office atamasını güncelleştirebilmek istersiniz. Eğitmen varlığı, OfficeAssignment varlığıyla bire sıfır veya-arasında bir ilişkiye sahiptir; bu, kodunuzun aşağıdaki durumları işlemesi gerektiği anlamına gelir:

* Kullanıcı Office atamasını temizlediğinde ve başlangıçta bir değere sahipse, OfficeAssignment varlığını silin.

* Kullanıcı bir Office atama değeri girerse ve başlangıçta boşsa, yeni bir OfficeAssignment varlığı oluşturun.

* Kullanıcı bir Office atamasının değerini değiştirirse, var olan bir OfficeAssignment varlığındaki değeri değiştirin.

### <a name="update-the-instructors-controller"></a>Eğitmenler denetleyicisini güncelleştirme

*InstructorsController.cs*Içinde, HttpGet yöntemindeki kodu, `Edit` eğitmen varlığının `OfficeAssignment` gezinti özelliğini ve çağrılarını yükleyen şekilde değiştirin `AsNoTracking` :

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

HttpPost yöntemini, `Edit` Office atama güncelleştirmelerini işlemek için aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Kod şunları yapar:

* `EditPost`İmza artık HttpGet yöntemiyle aynı olduğundan `Edit` ( `ActionName` öznitelik, `/Edit/` URL 'nin hala kullanıldığını belirtir), yöntem adını olarak değiştirir.

* Gezinti özelliği için Eager yüklemesini kullanarak geçerli eğitmen varlığını veritabanından alır `OfficeAssignment` . Bu, HttpGet yönteminde yaptığınız şeydir `Edit` .

* Alınan eğitmen varlığını model Ciltçideki değerlerle güncelleştirir. `TryUpdateModel`Aşırı yükleme, dahil etmek istediğiniz özellikleri beyaz listelemenize olanak sağlar. Bu, [ikinci öğreticide](crud.md)açıklandığı gibi, daha fazla nakletmeyi önler.

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Office konumu boşsa, OfficeAssignment tablosundaki ilgili satırın silinebilmesi için eğitmen. OfficeAssignment özelliğini null olarak ayarlar.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Değişiklikleri veritabanına kaydeder.

### <a name="update-the-instructor-edit-view"></a>Eğitmen düzenleme görünümünü güncelleştirme

*Görünümler/eğitmenler/Edit. cshtml*' de, **Kaydet** düğmesinin sonundaki Office konumunu düzenlemek için yeni bir alan ekleyin:

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Uygulamayı çalıştırın, **eğitmenler** sekmesini seçin ve ardından bir eğitmende **Düzenle** ' ye tıklayın. **Office konumunu** değiştirin ve **Kaydet**' e tıklayın.

![Eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Düzenleme sayfasına kurslar ekleyin

Eğitmenler, istediğiniz sayıda kurs öğretebilir. Artık, aşağıdaki ekran görüntüsünde gösterildiği gibi, bir grup onay kutusu kullanarak kurs atamalarını değiştirme özelliğini ekleyerek eğitmen düzenleme sayfasını geliştirirsiniz:

![Kurslar ile eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

Kurs ve eğitmen varlıkları arasındaki ilişki çoktan çoğa olur. İlişki eklemek ve kaldırmak için, kurs ekleme ve kurs, katılımcı varlık kümesine ekleme ve kaldırma.

Bir eğitmenin hangi kurslara atandığını değiştirmenize olanak sağlayan kullanıcı arabirimi bir grup onay kutusu olur. Veritabanındaki her kurs için bir onay kutusu görüntülenir ve eğitmenin Şu anda atanmış olduğu yer seçilidir. Kullanıcı kurs atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir. Kurs sayısı çok fazlaysa, büyük olasılıkla görünümde verileri göstermek için farklı bir yöntem kullanmak isteyeceksiniz, ancak ilişkiler oluşturmak veya silmek için bir JOIN varlığını işlemek için aynı yöntemi kullanmanız gerekir.

### <a name="update-the-instructors-controller"></a>Eğitmenler denetleyicisini güncelleştirme

Onay kutuları listesinin görünümüne veri sağlamak için bir görünüm modeli sınıfı kullanacaksınız.

*SchoolViewModels* klasöründe *AssignedCourseData.cs* oluşturun ve mevcut kodu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

*InstructorsController.cs*' de, HttpGet `Edit` yöntemini aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Kod, gezinti özelliği için Eager yüklemesi ekler `Courses` ve `PopulateAssignedCourseData` `AssignedCourseData` görüntüleme modeli sınıfını kullanarak onay kutusu dizisi için bilgi sağlamak üzere yeni yöntemi çağırır.

Yöntemindeki kod, `PopulateAssignedCourseData` görünüm modeli sınıfını kullanarak bir kurs listesi yüklemek için tüm kurs varlıklarını okur. Kod her kurs için, kursun, eğitmenin gezinti özelliğinde mevcut olup olmadığını denetler `Courses` . Eğitmenin bir kurs atanıp atanmadığını denetlerken etkili arama oluşturmak için, eğitmene atanan kurslar bir `HashSet` koleksiyona konur. , `Assigned` Eğitmenin atandığı kurslar için özelliği true olarak ayarlanır. Görünüm, hangi onay kutularının seçili olarak gösterileceğini belirlemede bu özelliği kullanır. Son olarak, liste içindeki görünüme geçirilir `ViewData` .

Sonra, Kullanıcı **Kaydet**' i tıklattığında yürütülen kodu ekleyin. `EditPost`Yöntemini aşağıdaki kodla değiştirin ve `Courses` eğitmen varlığının gezinti özelliğini güncelleştiren yeni bir yöntem ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Yöntem imzası artık HttpGet `Edit` yönteminden farklıdır, bu nedenle Yöntem adı ' dan ' a değişir `EditPost` `Edit` .

Görünüm bir kurs varlıkları koleksiyonuna sahip olmadığından, model Bağlayıcısı gezinti özelliğini otomatik olarak güncelleştiremez `CourseAssignments` . Gezinti özelliğini güncelleştirmek için model cildi kullanmak yerine `CourseAssignments` , bunu yeni yöntemde yapmanız gerekir `UpdateInstructorCourses` . Bu nedenle, `CourseAssignments` özelliği model bağlamadan hariç bırakmanız gerekir. Bu, `TryUpdateModel` beyaz liste aşırı yüklemesini kullandığınız ve içerme listesinde olmadığı için çağıran kodda herhangi bir değişiklik yapılmasını gerektirmez `CourseAssignments` .

Hiçbir onay kutusu seçili değilse, içindeki kod `UpdateInstructorCourses` `CourseAssignments` gezinti özelliğini boş bir koleksiyonla başlatır ve döndürür:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Kod daha sonra, veritabanındaki tüm kurslardan geçer ve bu her kursu, görünümde seçili olanlar ile ilgili olarak eğitmenin atandığı her bir kursa karşı denetler. Etkili aramaları kolaylaştırmak için, ikinci iki koleksiyon `HashSet` nesnelerde depolanır.

Kurs onay kutusu seçilmişse ancak kurs, `Instructor.CourseAssignments` Gezinti özelliğinde değilse kurs, Gezinti özelliğindeki koleksiyona eklenir.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Kurs onay kutusu seçili değilse, ancak kurs `Instructor.CourseAssignments` gezinti özelliği ise, kurs, gezinti özelliğinden kaldırılır.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Eğitmen görünümlerini güncelleştirme

*Görünümler/eğitmenler/Edit. cshtml*' de, **Courses** `div` **Office** alanı öğelerinden hemen sonra ve `div` **Kaydet** düğmesine ait öğeden önce aşağıdaki kodu ekleyerek bir dizi onay kutusu içeren bir kurslar alanı ekleyin.

<a id="notepad"></a>
> [!NOTE]
> Kodu Visual Studio 'Ya yapıştırdığınızda, satır sonları kodu kesen bir şekilde değiştirilebilir. Kod yapıştırdıktan sonra farklı görünüyorsa, otomatik biçimlendirmeyi geri almak için CTRL + Z bir kez tuşuna basın. Bu işlem satır sonlarını, burada gördüğünüz gibi görünmeleri için düzeltir. Girintide kusursuz olması gerekmez, ancak `@:</tr><tr>` ,, `@:<td>` `@:</td>` ve `@:</tr>` çizgilerinin her biri gösterildiği gibi tek bir satırda olması gerekir, aksi halde bir çalışma zamanı hatası alırsınız. Yeni kod bloğu seçiliyken, yeni kodu mevcut kodla hizalamak için üç kez Tab tuşuna basın. Bu sorun Visual Studio 2019 ' de düzeltilmiştir.

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Bu kod, üç sütun içeren bir HTML tablosu oluşturur. Her sütunda, bir onay kutusu ve ardından kurs numarası ve başlığından oluşan bir açıklamalı alt yazı bulunur. Onay kutularının hepsi aynı ada ("Selectedkurslar") sahiptir, bu da model cilde bir grup olarak değerlendirilme bildirir. Her onay kutusunun değer özniteliği değerine ayarlanır `CourseID` . Sayfa gönderildiğinde, model Ciltçi `CourseID` yalnızca seçili onay kutularının değerlerinden oluşan bir diziyi denetleyiciye geçirir.

Onay kutuları başlangıçta işlendiğinde, eğitmenin atandığı kurslara yönelik olanlar, işaretlenmiş özniteliklere sahiptir ve bunları seçer (denetlenen görüntüler).

Uygulamayı çalıştırın, **eğitmenler** sekmesini seçin ve ardından **düzenleme** sayfasını görmek Için bir eğitmende **Düzenle** ' ye tıklayın.

![Kurslar ile eğitmen düzenleme sayfası](update-related-data/_static/instructor-edit-courses.png)

Bazı kurs atamalarını değiştirin ve Kaydet ' e tıklayın. Yaptığınız değişiklikler Dizin sayfasında yansıtılır.

> [!NOTE]
> Eğitim kursu verilerini düzenlemek için buradaki yaklaşım, sınırlı sayıda kurs olduğunda iyi bir şekilde gerçekleştirilir. Çok daha büyük olan koleksiyonlar için, farklı bir kullanıcı arabirimi ve farklı bir güncelleştirme yöntemi gerekir.

## <a name="update-delete-page"></a>Güncelleştirme silme sayfası

*InstructorsController.cs*' de, `DeleteConfirmed` yöntemini silin ve yerine aşağıdaki kodu ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Bu kod aşağıdaki değişiklikleri yapar:

* , Gezinti özelliği için ekip yükleme yapar `CourseAssignments` . Bunu eklemeniz gerekir, ilgili varlıklar hakkında bilgi sahibi olmaz `CourseAssignment` ve onları silmez. Bunları okumaktan kaçınmak için, veritabanında art arda silme yapılandırabilirsiniz.

* Silinecek eğitmen herhangi bir departmanların Yöneticisi olarak atanırsa, bu departmanlardan eğitmen atamasını kaldırır.

## <a name="add-office-location-and-courses-to-create-page"></a>Sayfa oluşturmak için Office konumu ve kurslar ekleme

*InstructorsController.cs*Içinde, HttpGet ve HttpPost yöntemlerini silin `Create` ve ardından aşağıdaki kodu kendi yerine ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Bu kod, `Edit` ilk olarak hiçbir kurs seçilmemiş olması dışında, yöntemler için gördüğünüz yönteme benzer. HttpGet `Create` yöntemi, `PopulateAssignedCourseData` ' ın seçili olması, ancak görünümde döngü için boş bir koleksiyon sağlamak üzere değil yöntemini çağırır `foreach` (Aksi halde görünüm kodu bir null başvuru özel durumu oluşturur).

HttpPost `Create` yöntemi, seçili her kursu `CourseAssignments` doğrulama hatalarını kontrol etmeden önce gezinti özelliğine ekler ve yeni eğitmeni veritabanına ekler. Model hataları olduğunda (örneğin, Kullanıcı geçersiz bir tarih anahtarlanır) ve sayfa bir hata iletisiyle yeniden görüntülenirken, yapılan kurs seçimleri otomatik olarak geri yüklenir.

Gezinti özelliğine kurslar ekleyebilmesinin, `CourseAssignments` özelliği boş bir koleksiyon olarak başlatmak için sahip olmanız gerektiğini unutmayın:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Bunu denetleyici kodunda yapmanın bir alternatifi olarak, aşağıdaki örnekte gösterildiği gibi, özellik alıcısının, koleksiyonu otomatik olarak oluşturmak üzere değiştirerek, bunu eğitmen modelinde yapabilirsiniz:

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

`CourseAssignments`Özelliği bu şekilde değiştirirseniz, denetleyicideki açık özellik başlatma kodunu kaldırabilirsiniz.

*Görünümler/eğitmen/oluşturma. cshtml*'de, gönder düğmesinden önce kurslar için bir Office konum metin kutusu ve onay kutuları ekleyin. Düzenleme sayfasında olduğu gibi, [dosyayı yapıştırdığınızda Visual Studio kodu yeniden biçimlendirdiğinden biçimlendirmeyi onarın](#notepad).

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Uygulamayı çalıştırıp bir eğitmen oluşturarak test edin.

## <a name="handling-transactions"></a>Işlemleri işleme

[CRUD öğreticisinde](crud.md)açıklandığı gibi Entity Framework, işlemleri örtük olarak uygular. Daha fazla denetime ihtiyacınız olan senaryolar için--örneğin, işlem içinde Entity Framework dışında yapılan işlemleri eklemek istiyorsanız, bkz. [işlemler](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Özelleştirilmiş Kurslar sayfaları
> * Eklenen eğitmenler düzenleme sayfası
> * Düzenleme sayfasına kurslar eklendi
> * Silme sayfası güncelleştirildi
> * Sayfa oluşturmak için Office konumu ve kurslar eklendi

Eşzamanlılık çakışmalarını nasıl işleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Eşzamanlılık çakışmalarını işleme](concurrency.md)
