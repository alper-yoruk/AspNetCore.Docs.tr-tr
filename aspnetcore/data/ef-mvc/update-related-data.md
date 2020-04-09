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
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Öğretici: Güncelleme ile ilgili verileri - EF Core ile ASP.NET MVC

Önceki öğreticide ilgili verileri görüntülediğiniz; Bu eğitimde, yabancı anahtar alanlarını ve gezinme özelliklerini güncelleyerek ilgili verileri güncelleştireceksiniz.

Aşağıdaki çizimler, çalışacağınız bazı sayfaları gösterir.

![Kurs Edit sayfası](update-related-data/_static/course-edit.png)

![Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Kursları Özelleştir sayfalarını özelleştir
> * Eğitmenler Ekle Sayfayı Edit
> * Ekle sayfasına ders ekle
> * Sayfayı Sil'i Güncelleştir
> * Sayfa Oluştur'a ofis konumu ve kurslar ekleme

## <a name="prerequisites"></a>Ön koşullar

* [İlgili verileri okuma](read-related-data.md)

## <a name="customize-courses-pages"></a>Kursları Özelleştir sayfalarını özelleştir

Yeni bir ders varlığı oluşturulduğunda, varolan bir bölümle ilişkisi olmalıdır. Bunu kolaylaştırmak için, iskelekodu denetleyici yöntemlerini içerir ve bölümü seçmek için açılır liste içeren görünümler oluşturun ve edit. Açılan liste yabancı anahtar `Course.DepartmentID` özelliğini ayarlar ve gezinti özelliğini `Department` uygun Departman varlığıyla yüklemek için Entity Framework'ün ihtiyacı olan tek şey bu. İskele kodu kullanırsınız, ancak hata işleme eklemek ve açılır listeyi sıralamak için biraz değiştirin.

*CoursesController.cs,* dört Oluştur ve Düzenle yöntemini silin ve bunları aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

HttpPost `Edit` yönteminden sonra, açılan liste için departman bilgilerini yükleyen yeni bir yöntem oluşturun.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

Yöntem, `PopulateDepartmentsDropDownList` ada göre sıralanmış tüm bölümlerin `SelectList` listesini alır, açılır liste için bir koleksiyon oluşturur `ViewBag`ve koleksiyonu 'daki görünüme geçirir. Yöntem, arama kodunun açılır liste işlendiğinde seçilecek öğeyi belirtmesine olanak tanıyan isteğe bağlı `selectedDepartment` parametreyi kabul eder. `<select>` Görünüm, etiket yardımcısına "DepartmentID" adını geçirir ve yardımcı daha sonra "DepartmentID" adlı bir `ViewBag` `SelectList` nesnenin nesnesine bakmayı bilir.

Yeni bir `Create` kurs `PopulateDepartmentsDropDownList` için bölüm henüz kurulmadığından, HttpGet yöntemi seçili öğeyi ayarlamadan yöntemi çağırır:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

HttpGet `Edit` yöntemi, seçili öğeyi, düzenlenen kursa atanmış olan bölümün kimliğine göre ayarlar:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Her ikisi `Create` için de `Edit` HttpPost yöntemleri ve ayrıca bir hatadan sonra sayfayı yeniden görüntülediğinde seçili öğeyi ayarlayan kod içerir. Bu, sayfa hata iletisini göstermek için yeniden görüntülendiğinde, seçilen bölümün seçili kalmasını sağlar.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Ekle. Ayrıntılara AsNoTracking ve Silme yöntemleri

Ders Ayrıntıları ve Sil sayfalarının performansını `AsNoTracking` optimize `Details` etmek için, http ve httpget `Delete` yöntemlerine arama ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Ders görünümlerini değiştirme

*Görünümler/Kurslar/Create.cshtml'de,* **Bölüm** açılır listesine "Bölüm Seç" seçeneğini ekleyin, altyazıyı **DepartmentID'den** **Bölüm'e**değiştirin ve doğrulama iletisi ekleyin.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

*Görünümler /Kurslar/Edit.cshtml*olarak, sadece *Create.cshtml*yaptım Bölüm alanı için aynı değişikliği yapmak.

Ayrıca *Görünümler /Kurslar/Edit.cshtml'* de, **Başlık** alanından önce bir ders numarası alanı ekleyin. Kurs numarası birincil anahtar olduğundan, görüntülenir, ancak değiştirilemez.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Edit görünümünde kurs`<input type="hidden">`numarası için zaten gizli bir alan var. `<label>` Etiket yardımcısı eklemek, kullanıcı **Edit** sayfasında **Kaydet'i** tıklattığında kurs numarasının deftere nakledilen verilere eklenmesine neden olmadığından gizli alana olan gereksinimi ortadan kaldırmaz.

*Görünümler/Kurslar/Sil.cshtml'de,* en üste bir ders numarası alanı ekleyin ve bölüm adını bölüm adına değiştirin.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

*Görünümler / Dersler / Details.cshtml*olarak, sadece *Delete.cshtml*için yaptığınız aynı değişikliği yapmak .

### <a name="test-the-course-pages"></a>Ders sayfalarını test edin

Uygulamayı çalıştırın, **Kurslar** sekmesini seçin, **Yeni Oluştur'u**tıklatın ve yeni bir kurs için veri girin:

![Kurs Oluşturma sayfası](update-related-data/_static/course-create.png)

**Oluştur'u**tıklatın. Dersler Endeksi sayfası, listeye eklenen yeni dersle birlikte görüntülenir. Dizin sayfası listesindeki bölüm adı, ilişkinin doğru kurulduğunu gösteren gezinti özelliğinden gelir.

Kurslar Dizini sayfasındaki bir kursu **Edit'e** tıklayın.

![Kurs Edit sayfası](update-related-data/_static/course-edit.png)

Sayfadaki verileri değiştirin ve **Kaydet'i**tıklatın. Dersler Dizini sayfası güncelleştirilmiş ders verileri ile birlikte görüntülenir.

## <a name="add-instructors-edit-page"></a>Eğitmenler Ekle Sayfayı Edit

Bir eğitmen kaydını güncellediğinizde, eğitmenin ofis atamasını güncelleştirmek istersiniz. Eğitmen tüzel kişiliğinin OfficeAssignment varlığıyla bire sıfır veya bir ilişkisi vardır, bu da kodunuzun aşağıdaki durumları ele almak zorunda olduğu anlamına gelir:

* Kullanıcı ofis atamasını temizlerse ve başlangıçta bir değeri varsa, OfficeAssignment varlığını silin.

* Kullanıcı bir ofis atama değeri girer ve başlangıçta boşsa, yeni bir OfficeAssignment tüzel kişiliği oluşturun.

* Kullanıcı bir ofis atamasının değerini değiştirirse, varolan bir OfficeAssignment varlığındaki değeri değiştirin.

### <a name="update-the-instructors-controller"></a>Eğitmenler denetleyicisini güncelleştirin

*InstructorsController.cs,* HttpGet `Edit` yöntemindeki kodu, Eğitmen varlığın `OfficeAssignment` gezinti özelliğini yükler `AsNoTracking`ve aşağıdakileri çağırır:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

Ofis atama `Edit` güncelleştirmelerini işlemek için HttpPost yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

Kod aşağıdakileri yapar:

* İmza `EditPost` artık HttpGet `Edit` yöntemiyle aynı olduğundan yöntem adını değiştirir `ActionName` (öznitelik URL'nin `/Edit/` hala kullanıldığını belirtir).

* `OfficeAssignment` Gezinti özelliği için istekli yüklemeyi kullanarak geçerli Instructor varlığını veritabanından alır. Bu, HttpGet `Edit` yönteminde yaptığınızla aynıdır.

* Alınan Instructor varlığı model bağlayıcısından gelen değerlerle güncelleştirir. Aşırı `TryUpdateModel` yükleme, eklemek istediğiniz özellikleri beyaz listeye almanızı sağlar. Bu, [ikinci öğreticide](crud.md)açıklandığı gibi aşırı deftere nakil yi önler.

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* Ofis konumu boşsa, OfficeAtama tablosundaki ilgili satırın silinecek şekilde Instructor.OfficeAtama özelliğini geçersiz kılacak şekilde ayarlar.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* Değişiklikleri veritabanına kaydeder.

### <a name="update-the-instructor-edit-view"></a>Eğitmen Edit görünümünü güncelleştir

*Görünümler/Eğitmenler/Edit.cshtml'de,* **Kaydet** düğmesinden önce sonunda, ofis konumunu düzenlemek için yeni bir alan ekleyin:

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Uygulamayı çalıştırın, **Eğitmenler** sekmesini seçin ve ardından bir eğitmenin üzerine **Edit'i** tıklatın. Office **Konumunu** değiştirin ve **Kaydet'i**tıklatın.

![Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Ekle sayfasına ders ekle

Eğitmenler herhangi bir sayıda ders verebilirler. Şimdi, aşağıdaki ekran görüntüsünde gösterildiği gibi, bir kutu grubu kullanarak ders atamalarını değiştirme özelliğini ekleyerek Eğitmen Düzenleme sayfasını geliştireceksiniz:

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

Kurs ve Eğitmen varlıkları arasındaki ilişki çok-çok. İlişkiler eklemek ve kaldırmak için, Ders Atamaları'na ve programdan gelen varlıkları ekler ve kaldırırsınız.

Bir eğitmenin atandığı dersleri değiştirmenizi sağlayan uI, bir onay kutusu grubudur. Veritabanındaki her ders için bir onay kutusu görüntülenir ve eğitmenin şu anda atandığı onay kutusu seçilir. Kullanıcı, ders atamalarını değiştirmek için onay kutularını seçebilir veya temizleyebilir. Kurs sayısı çok daha fazla olsaydı, büyük olasılıkla görünümdeki verileri sunmak için farklı bir yöntem kullanmak isteyebilirsiniz, ancak ilişkileri oluşturmak veya silmek için birleştirilmiş varlığı manipüle etme yöntemini kullanırsınız.

### <a name="update-the-instructors-controller"></a>Eğitmenler denetleyicisini güncelleştirin

Onay kutuları listesinin görünümüne veri sağlamak için bir görünüm modeli sınıfı kullanırsınız.

*SchoolViewModels* klasöründe *AssignedCourseData.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

*InstructorsController.cs,* HttpGet `Edit` yöntemini aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

Kod, `Courses` gezinti özelliği için istekli yükleme `PopulateAssignedCourseData` ekler ve `AssignedCourseData` görünüm modeli sınıfını kullanarak onay kutusu dizisi için bilgi sağlamak için yeni yöntemi çağırır.

Yöntemdeki `PopulateAssignedCourseData` kod, görünüm modeli sınıfını kullanan derslerin listesini yüklemek için tüm Ders varlıklarını okur. Her kurs için kod, kursun eğitmenin `Courses` navigasyon özelliğinde bulunup bulunmadığına kontrol eder. Bir kursun öğretim üyesine atanıp atanmadığını kontrol ederken etkin bir araştırma oluşturmak `HashSet` için, öğretim üyesine atanan dersler bir koleksiyona konur. Tesis, `Assigned` eğitmenin atandığı dersler için geçerli dir. Görünüm, seçili olarak hangi onay kutularının görüntülenmesi gerektiğini belirlemek için bu özelliği kullanır. Son olarak, liste ' deki `ViewData`görünüme geçirilir.

Ardından, kullanıcı **Kaydet'i**tıklattığında çalıştırılan kodu ekleyin. `EditPost` Yöntemi aşağıdaki kodla değiştirin ve Eğitmen varlığın `Courses` gezinti özelliğini güncelleyen yeni bir yöntem ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

Yöntem imzası artık HttpGet `Edit` yönteminden farklıdır, bu nedenle `EditPost` yöntem `Edit`adı arkadan 'a değişir.

Görünümde Ders varlıkları koleksiyonu olmadığından, model bağlayıcısı gezinti özelliğini `CourseAssignments` otomatik olarak güncelleştiremez. Gezinti özelliğini güncelleştirmek için `CourseAssignments` model bağlayıcısını kullanmak yerine, `UpdateInstructorCourses` bunu yeni yöntemde yaparsınız. Bu nedenle `CourseAssignments` özelliği model bağlamadan çıkarmanız gerekir. Bu, beyaz liste aşırı yüklemesini `TryUpdateModel` kullandığınız ve `CourseAssignments` dahil listesinde olmadığınız için çağıran kodda herhangi bir değişiklik gerektirmez.

Onay kutusu seçilmediyse, kod `UpdateInstructorCourses` gezinti özelliğini `CourseAssignments` boş bir koleksiyonla baş harfe döndürür ve döndürür:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Kod daha sonra veritabanındaki tüm dersler arasında döngüler ve görünümde seçilen olanlar aleyhine şu anda öğretim atanan olanlar atanır her ders denetler. Verimli aramaları kolaylaştırmak için, son iki koleksiyon `HashSet` nesnelerde depolanır.

Bir kursun onay kutusu seçiliyse ancak kurs `Instructor.CourseAssignments` navigasyon özelliğinde değilse, kurs gezinti özelliğindeki koleksiyona eklenir.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Bir kursun onay kutusu seçilmediyse, ancak kurs navigasyon `Instructor.CourseAssignments` özelliğindeyse, kurs navigasyon özelliğinden kaldırılır.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Eğitmen görünümlerini güncelleştirme

*Görünümler/Eğitmenler/Edit.cshtml'de,* **Office** alanının `div` `div` öğelerinden hemen sonra ve **Kaydet** düğmesinin öğesinden önce aşağıdaki kodu ekleyerek bir dizi onay kutusu içeren bir **Kurs** alanı ekleyin.

<a id="notepad"></a>
> [!NOTE]
> Görsel Studio'da kodu yapıştırdığınızda, satır sonları kodu kıracak şekilde değiştirilebilir. Yapıştırma dan sonra kod farklı görünüyorsa, otomatik biçimlendirmeyi geri almak için ctrl+Z tuşuna bir kez basın. Bu, hat sonlarını düzeltecek, böylece burada gördüğünüz gibi görünürler. Girintisi mükemmel olmak zorunda değildir, ancak `@</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` , ve çizgiler her gösterildiği gibi tek bir satırda olmalıdır veya bir çalışma zamanı hatası alırsınız. Yeni kod bloğu seçilise, yeni kodu varolan kodla hizalamak için Sekme'ye üç kez basın. Bu sorun Visual Studio 2019'da giderilmiştir.

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Bu kod, üç sütunu olan bir HTML tablosu oluşturur. Her sütunda bir onay kutusu ve ardından ders numarası ve başlığından oluşan bir başlık vardır. Onay kutularının tümü, model bağlayıcısına grup olarak davranılması gerektiğini bildiren aynı ada ("selectedCourses") sahiptir. Her onay kutusunun değer `CourseID`özniteliği. Sayfa deftere nakledildiğinde, model bağlayıcısı yalnızca seçili onay `CourseID` kutuları için değerlerden oluşan bir diziyi denetleyiciye geçirir.

Onay kutuları başlangıçta işlendiğinde, eğitmene atanan dersler için olanlar, onları seçen (kontrol edilmiş olarak görüntüler) özniteliklerini işaretlemiş olur.

Uygulamayı çalıştırın, **Eğitmenler** sekmesini seçin ve **Edit** sayfasını görmek için bir eğitmende **Edit'i** tıklatın.

![Kurslarla Eğitmen Edit sayfası](update-related-data/_static/instructor-edit-courses.png)

Bazı ders atamalarını değiştirin ve Kaydet'i tıklatın. Yaptığınız değişiklikler Dizin sayfasına yansıtılır.

> [!NOTE]
> Eğitmen ders verilerini yeniden düzenlemesi için burada alınan yaklaşım, sınırlı sayıda kurs olduğunda iyi çalışır. Çok daha büyük koleksiyonlar için farklı bir u-ui ve farklı bir güncelleştirme yöntemi gerekir.

## <a name="update-delete-page"></a>Sayfayı Sil'i Güncelleştir

*InstructorsController.cs,* `DeleteConfirmed` yöntemi silin ve yerine aşağıdaki kodu ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Bu kod aşağıdaki değişiklikleri yapar:

* `CourseAssignments` Navigasyon özelliği için istekli yükleme yapar. Bunu eklemeniz gerekir, yoksa EF ilgili `CourseAssignment` varlıklar hakkında bilgi sahibi olmaz ve bunları silmez. Burada bunları okumak gerekmemesi için veritabanında basamaklı silme yapılandırmak olabilir.

* Silinecek eğitmen herhangi bir bölümün yöneticisi olarak atanırsa, eğitmen atamasını bu bölümlerden kaldırır.

## <a name="add-office-location-and-courses-to-create-page"></a>Sayfa Oluştur'a ofis konumu ve kurslar ekleme

*InstructorsController.cs,* HttpGet ve HttpPost `Create` yöntemlerini silin ve ardından yerine aşağıdaki kodu ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Bu kod, başlangıçta hiçbir ders `Edit` seçilmemiş olması dışında yöntemler için gördüklerinize benzer. HttpGet `Create` yöntemi, `PopulateAssignedCourseData` seçili kurslar olabileceğinden değil, görünümdeki `foreach` döngü için boş bir koleksiyon sağlamak için (aksi takdirde görünüm kodu geçersiz bir başvuru özel durum oluşturur) yöntemi çağırır.

HttpPost `Create` yöntemi, doğrulama hatalarını `CourseAssignments` kontrol etmeden önce seçilen her kursu gezinti özelliğine ekler ve veritabanına yeni eğitmen ekler. Modellerde hatalar olsa bile kurslar eklenir (örneğin, kullanıcı geçersiz bir tarih anahtarladı) ve sayfa bir hata iletisiyle yeniden görüntülenir, yapılan kurs seçimleri otomatik olarak geri yüklenir.

`CourseAssignments` Navigasyon özelliğine kurs ekleyebilmek için mülkü boş bir koleksiyon olarak başlatmanız gerektiğine dikkat edin:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Denetleyici kodunda bunu yapmanın alternatifi olarak, aşağıdaki örnekte gösterildiği gibi, özellik getter'Ini otomatik olarak oluşturabilecek şekilde değiştirerek, bunu Eğitmen modelinde yapabilirsiniz:

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

`CourseAssignments` Özelliği bu şekilde değiştirirseniz, denetleyicideki açık özellik başlatma kodunu kaldırabilirsiniz.

*Görünümler/Eğitmen/Create.cshtml'de,* Gönder düğmesinden önce bir ofis konumu metin kutusu ekleyin ve kurslar için onay kutuları ekleyin. Edit sayfasında olduğu gibi, [Visual Studio yapıştırdığınızda kodu yeniden biçimlendirirse biçimlendirmeyi düzeltin.](#notepad)

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Uygulamayı çalıştırarak ve bir eğitmen oluşturarak test edin.

## <a name="handling-transactions"></a>İşlemleri N

[CRUD öğreticisinde](crud.md)açıklandığı gibi, Entity Framework işlemleri dolaylı olarak uygular. Daha fazla denetime ihtiyaç duyduğunuz senaryolar için (örneğin, bir harekette Entity Framework dışında yapılan işlemleri eklemek istiyorsanız), [Hareketler'e](/ef/core/saving/transactions)bakın.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Özelleştirilmiş Kurslar sayfaları
> * Eklenen Eğitmenler Edit sayfası
> * Ekle sayfasına dersler eklendi
> * Güncelleştirilmiş Silme sayfası
> * Sayfa Oluşturma'ya ofis konumu ve kurslar eklendi

Eşzamanlılık çakışmaları nasıl ele alınılacağını öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Eşzamanlılık çakışmalarını işleme](concurrency.md)
