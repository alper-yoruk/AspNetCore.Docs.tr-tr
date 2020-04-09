---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - İlgili Verileri Oku - 8'de 6
author: rick-anderson
description: Bu öğreticide, ilgili verileri , yani Varlık Çerçevesi'nin gezinti özelliklerine yüklediğiniz verileri okuyup görüntülersiniz.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: d244ce1527486466bcbc6557ec35869aa206bc4f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656578"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a>ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - İlgili Verileri Oku - 8'de 6

Yazar: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), ve Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, ilgili verilerin nasıl okunup görüntülenebildiğini gösterir. İlgili veriler, EF Core'un gezinme özelliklerine yüklediğini gösteren verilerdir.

Aşağıdaki çizimler bu öğretici için tamamlanmış sayfaları gösterir:

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index30.png)

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Istekli, açık ve tembel yükleme

EF Core'un ilgili verileri bir varlığın gezinti özelliklerine yükleyebildiği birkaç yöntem vardır:

* [Istekli yükleme](/ef/core/querying/related-data#eager-loading). Bir varlık türü için yapılan bir sorgu, ilgili varlıkları da yüklerken, istekli yüklemedir. Bir varlık okunduğunda, ilgili verileri alınır. Bu genellikle, gereken tüm verileri alan tek bir birleştirme sorgusuyla sonuçlanır. EF Core, bazı istekli yükleme türleri için birden çok sorgu yayımlar. Birden çok sorgu vermek, dev tek bir sorgudan daha verimli olabilir. Istekli yükleme `Include` ve `ThenInclude` yöntemleri ile belirtilir.

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  Bir koleksiyon gezintisi dahil edildiğinde istekli yükleme birden çok sorgu gönderir:

  * Ana sorgu için bir sorgu 
  * Yük ağacındaki her koleksiyon "kenar" için bir sorgu.

* Ayrı sorgular `Load`: Veriler ayrı sorgularda alınabilir ve EF Core gezinti özelliklerini "düzeltir". "Düzeltmeler" EF Core'un gezinme özelliklerini otomatik olarak doldurdığı anlamına gelir. Ayrı sorgular, `Load` istekli yüklemeden çok açık yüklemeye benzer.

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  Not: EF Core, daha önce bağlam örneğine yüklenen diğer varlıklara gezinme özelliklerini otomatik olarak düzeltir. Bir gezinti özelliğine ait veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse, özellik yine de doldurulabilir.

* [Açık yükleme](/ef/core/querying/related-data#explicit-loading). Varlık ilk okunduğunda, ilgili veriler alınmaz. Gerektiğinde ilgili verileri almak için kod yazılmalıdır. Ayrı sorgularla açık yükleme, veritabanına gönderilen birden çok sorguyla sonuçlanır. Açık yükleme yle, kod yüklenecek gezinti özelliklerini belirtir. Açık `Load` yükleme yapmak için yöntemi kullanın. Örneğin:

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* [Tembel yükleme](/ef/core/querying/related-data#lazy-loading). [Pİ Core'a sürüm 2.1'de tembel yükleme eklendi.](/ef/core/querying/related-data#lazy-loading) Varlık ilk okunduğunda, ilgili veriler alınmaz. Bir gezinti özelliğine ilk erişici olduğunda, bu gezinti özelliği için gereken veriler otomatik olarak alınır. Bir gezinti özelliğine ilk kez her erişici olduğunda veritabanına bir sorgu gönderilir.

## <a name="create-course-pages"></a>Kurs sayfaları oluşturma

Varlık, `Course` ilgili `Department` varlığı içeren bir gezinti özelliği içerir.

![Kurs.Bölüm](read-related-data/_static/dep-crs.png)

Bir kurs için atanan bölümün adını görüntülemek için:

* İlgili `Department` varlığı gezinti `Course.Department` özelliğine yükleyin.
* Adı varlığın `Department` `Name` mülkünden alın.

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>İskele Kursu sayfaları

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aşağıdaki istisnalar dışında [İskele Öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları izleyin:

  * *Sayfalar/Kurslar* klasörü oluşturun.
  * Model `Course` sınıfı için kullanın.
  * Yeni bir bağlam oluşturmak yerine varolan bağlam sınıfını kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Sayfalar/Kurslar* klasörü oluşturun.

* Kurs sayfalarını iskeleye getirmek için aşağıdaki komutu çalıştırın.

  **Windows'da:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Linux veya macOS'ta:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* *Sayfaları/Dersleri/Index.cshtml.cs'yi* açın `OnGetAsync` ve yöntemi inceleyin. İskele motoru `Department` navigasyon özelliği için istekli yükleme belirtti. Yöntem, `Include` istekli yükleme yi belirtir.

* Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin. Bölüm sütunu `DepartmentID`, hangi yararlı değildir görüntüler.

### <a name="display-the-department-name"></a>Bölüm adını görüntüleme

Sayfaları/Dersleri/Index.cshtml.cs'yi aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Önceki kod `Course` özelliği değiştirir `Courses` ve `AsNoTracking`ekler. `AsNoTracking`döndürülen varlıklar izlenmediği için performansı artırır. Varlıkların izlenmeleri gerekmez, çünkü geçerli bağlamda güncelleştirilmezler.

*Sayfaları/Kursları/Index.cshtml'i* aşağıdaki kodla güncelleyin.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

İskele kodunda aşağıdaki değişiklikler yapılmıştır:

* `Course` Özellik adını ' `Courses`da değiştirildi.
* Özellik değerini gösteren bir Sayı sütunu eklendi. **Number** `CourseID` Varsayılan olarak, birincil anahtarlar genellikle son kullanıcılar için anlamsız olduğundan İskele yekpişmez. Ancak, bu durumda birincil anahtar anlamlıdır.
* Bölüm adını görüntülemek için **Bölüm** sütunu değiştirildi. Kod, gezinti `Name` özelliğine `Department` yüklenen varlığın özelliğini `Department` görüntüler:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uygulamayı çalıştırın ve bölüm adlarının yer alan listesini görmek için **Dersler** sekmesini seçin.

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>İlgili verileri Select ile yükleme

Yöntem, `OnGetAsync` ilgili verileri `Include` yöntemle yükler. Yöntem, `Select` yalnızca gerekli ilgili verileri yükleyen bir alternatiftir. Tek öğeler için, `Department.Name` bir SQL INNER JOIN kullanır gibi. Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak koleksiyonlar üzerindeki `Include` işleç de kullanır.

Aşağıdaki kod `Select` yöntemle ilgili verileri yükler:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Ne `CourseViewModel`var:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Tam bir örnek için [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) bakın.

## <a name="create-instructor-pages"></a>Eğitmen sayfaları oluşturma

Bu bölüm, Eğitmen sayfalarını iskeleler ve ilgili Dersler ve Kayıtlar'ı Eğitmenler Endeksi sayfasına ekler.

<a name="IP"></a>
![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index30.png)

Bu sayfa, ilgili verileri aşağıdaki şekillerde okur ve görüntüler:

* Eğitmenlerlistesi `OfficeAssignment` varlıktan (Önceki resimdeki Office) ilgili verileri görüntüler. `OfficeAssignment` Varlıklar `Instructor` birden sıfıra veya bir ilişkisindedir. Araçlar için `OfficeAssignment` istekli yükleme kullanılır. İlgili verilerin görüntülenmesi gerektiğinde istekli yükleme genellikle daha verimlidir. Bu durumda, eğitmenler için ofis atamaları görüntülenir.
* Kullanıcı bir eğitmen seçtiğinde, `Course` ilgili varlıklar görüntülenir. Ve `Instructor` `Course` varlıklar çok-çok ilişkisi vardır. Istekli yükleme `Course` varlıklar ve ilgili `Department` varlıklar için kullanılır. Bu durumda, yalnızca seçilen eğitmen için kurslar gerektiğinden, ayrı sorgular daha verimli olabilir. Bu örnek, gezinti özellikleri olan varlıklarda gezinme özellikleri için istekli yüklemenin nasıl kullanılacağını gösterir.
* Kullanıcı bir kurs seçtiğinde, `Enrollments` varlıktan ilgili veriler görüntülenir. Bir önceki resimde öğrenci adı ve notu görüntülenir. Ve `Course` `Enrollment` varlıklar bir-çok ilişkisi vardır.

### <a name="create-a-view-model"></a>Görünüm modeli oluşturma

Eğitmenler sayfası üç farklı tablodan verileri gösterir. Üç tabloyu temsil eden üç özelliği içeren bir görünüm modeli gereklidir.

Aşağıdaki kodile *SchoolViewModels/InstructorIndexData.cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>İskele Eğitmeni sayfaları

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aşağıdaki istisnalar dışında [öğrenci sayfalarını İskele'deki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları izleyin:

  * *Sayfalar/Eğitmenler* klasörü oluşturun.
  * Model `Instructor` sınıfı için kullanın.
  * Yeni bir bağlam oluşturmak yerine varolan bağlam sınıfını kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Sayfalar/Eğitmenler* klasörü oluşturun.

* Eğitmen sayfalarını iskeleye getirmek için aşağıdaki komutu çalıştırın.

  **Windows'da:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Linux veya macOS'ta:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

İskeleli sayfayı güncellemeden önce nasıl göründüğünü görmek için uygulamayı çalıştırın ve Eğitmenler sayfasına gidin.

*Sayfaları/Eğitmenleri/Index.cshtml.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

Yöntem, `OnGetAsync` seçili eğitmenin kimliği için isteğe bağlı rota verilerini kabul eder.

*Sayfalar/Eğitmenler/Index.cshtml.cs* dosyasındaki sorguyu inceleyin:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Kod, aşağıdaki gezinme özellikleri için istekli yüklemeyi belirtir:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Tekrarı `Include` ve `ThenInclude` yöntemleri dikkat `CourseAssignments` `Course`edin ve . Bu yineleme, varlığın `Course` iki gezinti özelliği için istekli yükleme yi belirtmek için gereklidir.

Bir eğitmen seçildiğinde aşağıdaki kod`id != null`çalıştırılır ( ).

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Seçilen eğitmen görünüm modelindeki eğitmenler listesinden alınır. Görünüm modelinin `Courses` özelliği, o `Course` eğitmenin `CourseAssignments` gezinti özelliğinden gelen varlıklarla yüklenir.

Yöntem `Where` bir koleksiyon döndürür. Ancak bu durumda, filtre tek bir varlık seçer. bu `Single` nedenle yöntem, koleksiyonu tek `Instructor` bir varlığa dönüştürmek için çağrılır. `CourseAssignments` Varlık, `Instructor` özelliğe erişim sağlar. `CourseAssignments`ilgili `Course` varlıklara erişim sağlar.

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

Yöntem, `Single` koleksiyonda yalnızca bir öğe olduğunda bir koleksiyonda kullanılır. Yöntem, `Single` koleksiyon boşsa veya birden fazla öğe varsa bir özel durum oluşturur. Alternatif, `SingleOrDefault`koleksiyon boşsa varsayılan değeri (bu durumda null) döndüren dir.

Bir kurs seçildiğinde aşağıdaki kod `Enrollments` görünüm modelinin özelliğini doldurur:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Eğitmenlerin Dizin sayfasını güncelleştirin

*Sayfaları/Eğitmenleri/Index.cshtml'i* aşağıdaki kodla güncelleştirin.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Önceki kod aşağıdaki değişiklikleri yapar:

* Yönergeyi `page` `@page` `@page "{id:int?}"`' den . `"{id:int?}"`bir rota şablonudur. Rota şablonu, verileri yönlendirmek için URL'deki sonda sorgu dizelerini değiştirir. Örneğin, yalnızca yönergesi `@page` olan bir eğitmen için **Seç** bağlantısını tıklattığınızda aşağıdaki gibi bir URL üretir:

  `https://localhost:5001/Instructors?id=2`

  Sayfa yönergesi url olduğunda: `@page "{id:int?}"`

  `https://localhost:5001/Instructors/2`

* Yalnızca null `item.OfficeAssignment` değilse `item.OfficeAssignment.Location` görüntüleyen bir **Office** sütunu ekler. Bu bire sıfır veya bir ilişkisi olduğundan, ilgili bir OfficeAssignment varlığı olmayabilir.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Her eğitmen tarafından öğretilen dersleri görüntüleyen bir **Kurslar** sütunu ekler. Bu jilet sözdizimi hakkında daha fazla şey için [Açık satır geçişine](xref:mvc/views/razor#explicit-line-transition) bakın.

* Seçilen eğitmen ve `class="success"` kursun `tr` öğesine dinamik olarak ekleyen kod ekler. Bu, Bootstrap sınıfını kullanarak seçili satır için bir arka plan rengi ayarlar.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Seç**etiketli yeni bir köprü ekler. Bu bağlantı, seçilen eğitmenin kimliğini `Index` yönteme gönderir ve bir arka plan rengi ayarlar.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Seçilen Eğitmen için bir kurs tablosu ekler.

* Seçilen kurs için öğrenci kayıtları tablosu ekler.

Uygulamayı çalıştırın ve **Eğitmenler** sekmesini seçin. Sayfa, ilgili `Location` `OfficeAssignment` varlığın (ofis) görüntüleniyor. Null `OfficeAssignment` ise, boş bir tablo hücresi görüntülenir.

Eğitmen için **Seç** bağlantısına tıklayın. Satır stili değişiklikleri ve bu eğitmene atanan dersler görüntülenir.

Kayıtlı öğrencilerin listesini ve notlarını görmek için bir kurs seçin.

![Eğitmenler Dizin sayfa öğretim görevlisi ve seçilen ders](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Tek Kullanma

Yöntem, `Single` `Where` yöntemi ayrı `Where` ayrı çağırmak yerine koşulda geçebilir:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Bir `Single` Nerede koşulu kişisel tercih meselesi ile kullanımı. Bu `Where` yöntemi kullanarak üzerinde hiçbir fayda sağlar.

## <a name="explicit-loading"></a>Açık yükleme

Geçerli kod için `Enrollments` istekli yükleme `Students`belirtir ve:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Kullanıcıların bir kurstaki kayıtları nadiren görmek istediklerini varsayalım. Bu durumda, en iyi duruma getirilmesi yalnızca istenirse kayıt verilerini yüklemek olacaktır. Bu bölümde, `OnGetAsync` açık yükleme `Enrollments` ve kullanmak `Students`için güncellenir .

*Sayfaları/Eğitmenleri/Index.cshtml.cs'yi* aşağıdaki kodla güncelleyin.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Önceki kod, Kayıt ve öğrenci verileri için *ThenInclude* yöntemini düşürür. Bir kurs seçilirse, açık yükleme kodu alır:

* `Enrollment` Seçilen kursun varlıkları.
* Her `Student` biri `Enrollment`için varlıklar .

Önceki kodun dışarı yorum `.AsNoTracking()`yaptığına dikkat edin. Gezinti özellikleri yalnızca izlenen varlıklar için açıkça yüklenebilir.

Uygulamayı test etme. Bir kullanıcının bakış açısından, uygulama önceki sürümle aynı şekilde çalışır.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğretici, ilgili verilerin nasıl güncelleştirilebildiğini gösterir.

>[!div class="step-by-step"]
>[Önceki öğretici](xref:data/ef-rp/complex-data-model)
>[Sonraki öğretici](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, ilgili veriler okunur ve görüntülenir. İlgili veriler, EF Core'un gezinme özelliklerine yüklediğini gösteren verilerdir.

Çözemediğiniz sorunlarla karşılaştıysanız, [tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Talimatları indirin.](xref:index#how-to-download-a-sample)

Aşağıdaki çizimler bu öğretici için tamamlanmış sayfaları gösterir:

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>İlgili verilerin istekli, açık ve tembel yüklenmesi

EF Core'un ilgili verileri bir varlığın gezinti özelliklerine yükleyebildiği birkaç yöntem vardır:

* [Istekli yükleme](/ef/core/querying/related-data#eager-loading). Bir varlık türü için yapılan bir sorgu, ilgili varlıkları da yüklerken, istekli yüklemedir. Varlık okunduğunda, ilgili verileri alınır. Bu genellikle, gereken tüm verileri alan tek bir birleştirme sorgusuyla sonuçlanır. EF Core, bazı istekli yükleme türleri için birden çok sorgu yayımlar. Birden çok sorgu verme, tek bir sorgunun bulunduğu EF6'daki bazı sorgularda olduğundan daha verimli olabilir. Istekli yükleme `Include` ve `ThenInclude` yöntemleri ile belirtilir.

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  Bir koleksiyon gezintisi dahil edildiğinde istekli yükleme birden çok sorgu gönderir:

  * Ana sorgu için bir sorgu 
  * Yük ağacındaki her koleksiyon "kenar" için bir sorgu.

* Ayrı sorgular `Load`: Veriler ayrı sorgularda alınabilir ve EF Core gezinti özelliklerini "düzeltir". "düzeltmeler", EF Core'un gezinme özelliklerini otomatik olarak doldurdığı anlamına gelir. Ayrı sorgular, `Load` istekli yüklemeden çok açık yüklemeye benzer.

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  Not: EF Core, daha önce bağlam örneğine yüklenen diğer varlıklara gezinme özelliklerini otomatik olarak düzeltir. Bir gezinti özelliğine ait veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse, özellik yine de doldurulabilir.

* [Açık yükleme](/ef/core/querying/related-data#explicit-loading). Varlık ilk okunduğunda, ilgili veriler alınmaz. Gerektiğinde ilgili verileri almak için kod yazılmalıdır. Ayrı sorgularla açık yükleme, DB'ye gönderilen birden çok sorguyla sonuçlanır. Açık yükleme yle, kod yüklenecek gezinti özelliklerini belirtir. Açık `Load` yükleme yapmak için yöntemi kullanın. Örneğin:

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* [Tembel yükleme](/ef/core/querying/related-data#lazy-loading). [Pİ Core'a sürüm 2.1'de tembel yükleme eklendi.](/ef/core/querying/related-data#lazy-loading) Varlık ilk okunduğunda, ilgili veriler alınmaz. Bir gezinti özelliğine ilk erişici olduğunda, bu gezinti özelliği için gereken veriler otomatik olarak alınır. Bir gezinti özelliğine ilk kez her erişir'de DB'ye bir sorgu gönderilir.

* Operatör `Select` yalnızca gerekli ilgili verileri yükler.

## <a name="create-a-course-page-that-displays-department-name"></a>Bölüm adını görüntüleyen bir Kurs sayfası oluşturma

Kurs varlığı, `Department` varlığı içeren bir gezinti özelliği içerir. Kuruluş, `Department` kursun atandığı bölümü içerir.

Atanan bölümün adını bir ders listesinde görüntülemek için:

* Varlığın `Name` mülkiyetini `Department` alın.
* Varlık `Department` `Course.Department` navigasyon özelliğinden gelir.

![Kurs.Bölüm](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>İskele Kursu modeli

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[İskele'deki](xref:data/ef-rp/intro#scaffold-the-student-model) talimatları uygulayın öğrenci modeli `Course` ve model sınıfı için kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Önceki komut `Course` modeli iskeleler. Projeyi Visual Studio'da açın.

*Sayfaları/Dersleri/Index.cshtml.cs'yi* açın `OnGetAsync` ve yöntemi inceleyin. İskele motoru `Department` navigasyon özelliği için istekli yükleme belirtti. Yöntem, `Include` istekli yükleme yi belirtir.

Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin. Bölüm sütunu `DepartmentID`, hangi yararlı değildir görüntüler.

Yöntemi `OnGetAsync` aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Önceki kod ekler. `AsNoTracking` `AsNoTracking`döndürülen varlıklar izlenmediği için performansı artırır. Varlıklar, geçerli bağlamda güncelleştirilmeyecekleri için izlenmez.

*Sayfaları/Dersleri/Index.cshtml'i* aşağıdaki vurgulanan biçimlendirmeyle güncelleyin:

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

İskele kodunda aşağıdaki değişiklikler yapılmıştır:

* Başlığı Index'ten Derslere değiştirdi.
* Özellik değerini gösteren bir Sayı sütunu eklendi. **Number** `CourseID` Varsayılan olarak, birincil anahtarlar genellikle son kullanıcılar için anlamsız olduğundan İskele yekpişmez. Ancak, bu durumda birincil anahtar anlamlıdır.
* Bölüm adını görüntülemek için **Bölüm** sütunu değiştirildi. Kod, gezinti `Name` özelliğine `Department` yüklenen varlığın özelliğini `Department` görüntüler:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uygulamayı çalıştırın ve bölüm adlarının yer alan listesini görmek için **Dersler** sekmesini seçin.

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>İlgili verileri Select ile yükleme

Yöntem, `OnGetAsync` ilgili verileri `Include` yöntemle yükler:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Operatör `Select` yalnızca gerekli ilgili verileri yükler. Tek öğeler için, `Department.Name` bir SQL INNER JOIN kullanır gibi. Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak koleksiyonlar üzerindeki `Include` işleç de kullanır.

Aşağıdaki kod `Select` yöntemle ilgili verileri yükler:

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

Ne `CourseViewModel`var:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Tam bir örnek için [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) bakın.

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Kursları ve Kayıtları gösteren bir Eğitmen sayfası oluşturma

Bu bölümde Eğitmenler sayfası oluşturulmuştur.

<a name="IP"></a>
![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

Bu sayfa, ilgili verileri aşağıdaki şekillerde okur ve görüntüler:

* Eğitmenlerlistesi `OfficeAssignment` varlıktan (Önceki resimdeki Office) ilgili verileri görüntüler. `OfficeAssignment` Varlıklar `Instructor` birden sıfıra veya bir ilişkisindedir. Araçlar için `OfficeAssignment` istekli yükleme kullanılır. İlgili verilerin görüntülenmesi gerektiğinde istekli yükleme genellikle daha verimlidir. Bu durumda, eğitmenler için ofis atamaları görüntülenir.
* Kullanıcı bir eğitmen (önceki resimdeki Harui) seçtiğinde, ilgili `Course` varlıklar görüntülenir. Ve `Instructor` `Course` varlıklar çok-çok ilişkisi vardır. Istekli yükleme `Course` varlıklar ve ilgili `Department` varlıklar için kullanılır. Bu durumda, yalnızca seçilen eğitmen için kurslar gerektiğinden, ayrı sorgular daha verimli olabilir. Bu örnek, gezinti özellikleri olan varlıklarda gezinme özellikleri için istekli yüklemenin nasıl kullanılacağını gösterir.
* Kullanıcı bir ders seçtiğinde (önceki resimdeki kimya), `Enrollments` varlıktan ilgili veriler görüntülenir. Bir önceki resimde öğrenci adı ve notu görüntülenir. Ve `Course` `Enrollment` varlıklar bir-çok ilişkisi vardır.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Eğitmen Dizini görünümü için bir görünüm modeli oluşturma

Eğitmenler sayfası üç farklı tablodan verileri gösterir. Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.

*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>İskele Eğitmen modeli

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[İskele'deki](xref:data/ef-rp/intro#scaffold-the-student-model) talimatları uygulayın öğrenci modeli `Instructor` ve model sınıfı için kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Önceki komut `Instructor` modeli iskeleler. 
Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.

*Sayfaları/Eğitmenleri/Index.cshtml.cs'yi* aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

Yöntem, `OnGetAsync` seçili eğitmenin kimliği için isteğe bağlı rota verilerini kabul eder.

*Sayfalar/Eğitmenler/Index.cshtml.cs* dosyasındaki sorguyu inceleyin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Sorgunun iki tane si vardır:

* `OfficeAssignment`: Eğitmenler [görünümünde](#IP)görüntülenir.
* `CourseAssignments`: Bu da öğretilen dersleri getirir.

### <a name="update-the-instructors-index-page"></a>Eğitmenlerin Dizin sayfasını güncelleştirin

*Sayfaları/Eğitmenleri/Index.cshtml'i* aşağıdaki biçimlendirmeyle güncelleştirin:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Önceki biçimlendirme aşağıdaki değişiklikleri yapar:

* Yönergeyi `page` `@page` `@page "{id:int?}"`' den . `"{id:int?}"`bir rota şablonudur. Rota şablonu, verileri yönlendirmek için URL'deki sonda sorgu dizelerini değiştirir. Örneğin, yalnızca yönergesi `@page` olan bir eğitmen için **Seç** bağlantısını tıklattığınızda aşağıdaki gibi bir URL üretir:

  `http://localhost:1234/Instructors?id=2`

  Sayfa yönergesi, `@page "{id:int?}"`önceki URL'dir:

  `http://localhost:1234/Instructors/2`

* Sayfa başlığı **Eğitmenler**olduğunu.
* Yalnızca null `item.OfficeAssignment` değilse `item.OfficeAssignment.Location` görüntüleyen bir **Office** sütunu eklendi. Bu bire sıfır veya bir ilişkisi olduğundan, ilgili bir OfficeAssignment varlığı olmayabilir.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Her eğitmen tarafından öğretilen dersleri görüntüleyen bir **Kurslar** sütunu eklendi. Bu jilet sözdizimi hakkında daha fazla şey için [Açık satır geçişine](xref:mvc/views/razor#explicit-line-transition) bakın.

* Seçilen eğitmenin `class="success"` `tr` öğesine dinamik olarak eklenen kod eklendi. Bu, Bootstrap sınıfını kullanarak seçili satır için bir arka plan rengi ayarlar.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Seç**etiketli yeni bir köprü eklendi. Bu bağlantı, seçilen eğitmenin kimliğini `Index` yönteme gönderir ve bir arka plan rengi ayarlar.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uygulamayı çalıştırın ve **Eğitmenler** sekmesini seçin. Sayfa, ilgili `Location` `OfficeAssignment` varlığın (ofis) görüntüleniyor. OfficeAssignment' null ise, boş bir tablo hücresi görüntülenir.

**Seç** bağlantısına tıklayın. Satır stili değişir.

### <a name="add-courses-taught-by-selected-instructor"></a>Seçilen eğitmen tarafından verilen dersleri ekleme

`OnGetAsync` *Sayfalar/Eğitmenler/Index.cs.cs'deki* yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Ekle`public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Güncelleştirilmiş sorguyu inceleyin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Önceki sorgu `Department` varlıkları ekler.

Bir eğitmen seçildiğinde aşağıdaki kod`id != null`çalıştırılır ( ). Seçilen eğitmen görünüm modelindeki eğitmenler listesinden alınır. Görünüm modelinin `Courses` özelliği, o `Course` eğitmenin `CourseAssignments` gezinti özelliğinden gelen varlıklarla yüklenir.

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

Yöntem `Where` bir koleksiyon döndürür. Önceki `Where` yöntemde, yalnızca tek `Instructor` bir varlık döndürülür. Yöntem, `Single` koleksiyonu tek `Instructor` bir varlığa dönüştürür. `CourseAssignments` Varlık, `Instructor` özelliğe erişim sağlar. `CourseAssignments`ilgili `Course` varlıklara erişim sağlar.

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

Yöntem, `Single` koleksiyonda yalnızca bir öğe olduğunda bir koleksiyonda kullanılır. Yöntem, `Single` koleksiyon boşsa veya birden fazla öğe varsa bir özel durum oluşturur. Alternatif, `SingleOrDefault`koleksiyon boşsa varsayılan değeri (bu durumda null) döndüren dir. Boş `SingleOrDefault` bir koleksiyonda kullanma:

* Bir özel durumla sonuçlanır (null reference'da bir `Courses` özellik bulmaya çalışmaktan).
* Özel durum iletisi sorunun nedenini daha az açıkça gösterir.

Bir kurs seçildiğinde aşağıdaki kod `Enrollments` görünüm modelinin özelliğini doldurur:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

*Sayfalar/Eğitmenler/Index.cshtml* Jilet Sayfasının sonuna aşağıdaki işaretlemeyi ekleyin:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Önceki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenle ilgili derslerin listesini görüntüler.

Uygulamayı test etme. Eğitmenler sayfasındaki **Seç** bağlantısına tıklayın.

### <a name="show-student-data"></a>Öğrenci verilerini gösterme

Bu bölümde, uygulama seçilen bir kursun öğrenci verilerini göstermek üzere güncelleştirilir.

`OnGetAsync` *Sayfalar/Eğitmenler/Index.cs.cs* yöntemindeki sorguyu aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

*Sayfaları/Eğitmenleri/Index.cshtml'i*güncelleştir. Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Önceki biçimlendirme, seçilen kursa kayıtlı öğrencilerin listesini görüntüler.

Sayfayı yenileyin ve bir eğitmen seçin. Kayıtlı öğrencilerin listesini ve notlarını görmek için bir kurs seçin.

![Eğitmenler Dizin sayfa öğretim görevlisi ve seçilen ders](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Tek Kullanma

Yöntem, `Single` `Where` yöntemi ayrı `Where` ayrı çağırmak yerine koşulda geçebilir:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Önceki `Single` yaklaşım kullanarak `Where`üzerinde hiçbir fayda sağlar. Bazı geliştiriciler `Single` yaklaşım stilini tercih ediyor.

## <a name="explicit-loading"></a>Açık yükleme

Geçerli kod için `Enrollments` istekli yükleme `Students`belirtir ve:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Kullanıcıların bir kurstaki kayıtları nadiren görmek istediklerini varsayalım. Bu durumda, en iyi duruma getirilmesi yalnızca istenirse kayıt verilerini yüklemek olacaktır. Bu bölümde, `OnGetAsync` açık yükleme `Enrollments` ve kullanmak `Students`için güncellenir .

Aşağıdaki `OnGetAsync` kodu ile güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Önceki kod, Kayıt ve öğrenci verileri için *ThenInclude* yöntemini düşürür. Bir kurs seçilirse, vurgulanan kod alır:

* `Enrollment` Seçilen kursun varlıkları.
* Her `Student` biri `Enrollment`için varlıklar .

Önceki kod açıklamalarının `.AsNoTracking()`dışına çıktığına dikkat edin. Gezinti özellikleri yalnızca izlenen varlıklar için açıkça yüklenebilir.

Uygulamayı test etme. Kullanıcı açısından bakıldığında, uygulama önceki sürümle aynı şekilde çalışır.

Sonraki öğretici, ilgili verilerin nasıl güncelleştirilebildiğini gösterir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü (part1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Bu öğretici Nin YouTube sürümü (part2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Önceki](xref:data/ef-rp/complex-data-model)
>[Sonraki](xref:data/ef-rp/update-related-data)

::: moniker-end
