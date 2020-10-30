---
title: Bölüm 6, Razor ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku
author: rick-anderson
description: RazorSayfaların 6. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061073"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>Bölüm 6, Razor ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku

, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, ilgili verilerin nasıl okunacağı ve görüntüleneceği gösterilmektedir. İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.

Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>Eager, açık ve yavaş yükleme

EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:

* [Eager yükleniyor](/ef/core/querying/related-data#eager-loading). Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur. Bir varlık okunmadığınızda ilgili veriler alınır. Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur. EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak. Birden çok sorgu verme, çok büyük paketlerini tek sorgusundan daha verimli olabilir. Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:

  * Ana sorgu için bir sorgu 
  * Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.

* Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir". "Düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir. İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  **Note:** EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklara gezinti özelliklerini otomatik olarak düzeltir. Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.

* [Açık yükleme](/ef/core/querying/related-data#explicit-loading). Varlık ilk kez okunmadıysa ilgili veriler alınmadı. Gerektiğinde ilgili verileri almak için kodun yazılması gerekir. Ayrı sorgularla açık yükleme, veritabanına birden çok sorgu gönderilmesine neden olur. Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir. `Load`Açık yükleme yapmak için yöntemini kullanın. Örneğin:

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* [Yavaş yükleme](/ef/core/querying/related-data#lazy-loading). Varlık ilk kez okunmadıysa ilgili veriler alınmadı. Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır. Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu veritabanına gönderilir. Yavaş yükleme performansı zarar verebilir, örneğin, geliştiriciler N + 1 desenler kullanırken, bir üst öğe yüklerken ve alt öğeler aracılığıyla numaralandırıyor.

## <a name="create-course-pages"></a>Kurs sayfaları oluşturma

`Course`Varlık, ilgili varlığı içeren bir gezinti özelliği içerir `Department` .

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

Bir kurs için atanan departmanın adını göstermek için:

* İlgili `Department` varlığı `Course.Department` gezinti özelliğine yükleyin.
* `Department`Varlığın özelliğinden adı alın `Name` .

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Yapı iskelesi kurs sayfaları

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aşağıdaki özel durumlarla birlikte [Yapı Fkatlama öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) yönergeleri izleyin:

  * Bir *Sayfalar/kurslar* klasörü oluşturun.
  * `Course`Model sınıfı için kullanın.
  * Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir *Sayfalar/kurslar* klasörü oluşturun.

* Kurs sayfalarını iskele almak için aşağıdaki komutu çalıştırın.

  **Windows 'da:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **Linux veya macOS 'ta:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* *Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin. Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` . `Include`Yöntemi Eager yüklemeyi belirtir.

* Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin. Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.

### <a name="display-the-department-name"></a>Departmanın adını görüntüleme

Pages/kurslar/Index. cshtml. cs dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

Önceki kod, `Course` özelliği olarak değiştirir `Courses` ve ekler `AsNoTracking` . `AsNoTracking` Döndürülen varlıklar izlenmediğinden performansı geliştirir. Varlıkların geçerli bağlamda güncelleştirilmediği için izlenmesi gerekmez.

*Pages/kurslar/Index. cshtml* 'yi aşağıdaki kodla güncelleştirin.

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:

* `Course`Özellik adı olarak değiştirildi `Courses` .
* Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` . Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir. Ancak, bu durumda birincil anahtar anlamlı olur.
* Departman adını göstermek için **Departman** sütunu değiştirildi. Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select ile ilgili verileri yükleme

`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` . `Select`Yöntemi, yalnızca gerekli ilgili verileri yükleyen bir alternatiftir. Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır. Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.

Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

Yukarıdaki kod hiçbir varlık türü döndürmez, bu nedenle hiçbir izleme yapılmaz. EF izleme hakkında daha fazla bilgi için bkz [. Tracking vs. No-Tracking sorguları](/ef/core/querying/tracking).

`CourseViewModel`Şunları yapın:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .

## <a name="create-instructor-pages"></a>Eğitmen sayfaları oluşturma

Bu bölüm, eğitmen sayfalarını derler ve ilgili Kurslar ve kayıtları eğitmenler dizin sayfasına ekler.

<a name="IP"></a>
![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)

Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:

* Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler. `Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir. Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` . Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir. Bu durumda, Eğitmenler için Office atamaları görüntülenir.
* Kullanıcı bir eğitmen seçtiğinde ilgili `Course` varlıklar görüntülenir. `Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir. Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` . Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir. Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.
* Kullanıcı bir kurs seçtiğinde, varlıktaki ilgili veriler `Enrollments` görüntülenir. Önceki görüntüde öğrenci adı ve sınıf görüntülenir. `Course`Ve `Enrollment` varlıkları bire çok ilişkidir.

### <a name="create-a-view-model"></a>Görünüm modeli oluşturma

Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir. Üç tabloyu temsil eden üç özellik içeren bir görünüm modeli gerekir.

Aşağıdaki kodla *SchoolViewModels/ıncpctorındexdata. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Yapı iskelesi eğitmeni sayfaları

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Öğrenci sayfalarını aşağıdaki özel durumlarla birlikte [Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) içindeki yönergeleri izleyin:

  * Bir *sayfa/eğitmenler* klasörü oluşturun.
  * `Instructor`Model sınıfı için kullanın.
  * Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir *sayfa/eğitmenler* klasörü oluşturun.

* Eğitmen sayfalarını iskele almak için aşağıdaki komutu çalıştırın.

  **Windows 'da:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **Linux veya macOS 'ta:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

Yapı iskelesi sayfasının güncelleştirmeden önce nasıl göründüğünü görmek için, uygulamayı çalıştırın ve eğitmenler sayfasına gidin.

*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.

*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

Kod, aşağıdaki gezinti özellikleri için Eager yüklemeyi belirtir:

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

Ve `Include` `ThenInclude` için ve yöntemlerinin yinelendiğine dikkat `CourseAssignments` edin `Course` . Bu yineleme, varlığın iki gezinti özelliği için Eager yüklemesi belirtmek için gereklidir `Course` .

Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır. Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .

`Where`Yöntemi bir koleksiyon döndürür. Ancak bu durumda, filtre tek bir varlık seçer. bu nedenle, `Single` yöntemi koleksiyonu tek bir varlığa dönüştürmek için çağırılır `Instructor` . `Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` . `CourseAssignments` ilgili varlıklara erişim sağlar `Course` .

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır. `Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur. `SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.

Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>Eğitmenler Dizin sayfasını Güncelleştir

*Pages/eğitmenler/Index. cshtml* dosyasını aşağıdaki kodla güncelleştirin.

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* `page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` . `"{id:int?}"` bir yol şablonudur. Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir. Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:

  `https://localhost:5001/Instructors?id=2`

  Sayfa yönergesi olduğunda `@page "{id:int?}"` URL şu şekilde olur:

  `https://localhost:5001/Instructors/2`

* Yalnızca null değilse görüntülenen bir **Office** sütunu ekler `item.OfficeAssignment.Location` `item.OfficeAssignment` . Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu ekler. Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .

* `class="success"` `tr` Seçilen eğitmenin ve kursun öğesine dinamik olarak ekleyen kodu ekler. Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select** etiketli yeni bir köprü ekler. Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* Seçili eğitmen için bir kurs tablosu ekler.

* Seçili kurs için bir öğrenci kayıtları tablosu ekler.

Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` . `OfficeAssignment`Null ise boş bir tablo hücresi görüntülenir.

Bir eğitmenin **Seç** bağlantısına tıklayın. Bu eğitmenin atandığı satır stili değişiklikleri ve kurslar görüntülenir.

Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>Tek kullanımı

`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

`Single`Bir Where koşulunun kullanımı, kişisel tercihden bağımsız olarak kullanılır. Yöntemi kullanılarak herhangi bir avantaj sağlamaz `Where` .

## <a name="explicit-loading"></a>Belirtik yükleme

Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım. Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır. Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .

*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin.

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır. Bir kurs seçilirse, açık yükleme kodu alır:

* `Enrollment`Seçili kurs için varlıklar.
* `Student`Her biri için varlıklar `Enrollment` .

Yukarıdaki kodun yorumdığına dikkat edin `.AsNoTracking()` . Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.

Uygulamayı test etme. Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.

>[!div class="step-by-step"]
>[Önceki öğretici](xref:data/ef-rp/complex-data-model) 
> [Sonraki öğretici](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide ilgili veriler okundu ve görüntülenir. İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.

Sorun yaşıyorsanız, bu [uygulamayı indiremez veya görüntüleyemezsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yönergeleri indirin](xref:index#how-to-download-a-sample).

Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>İlgili verilerin Eager, açık ve geç yüklemesi

EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:

* [Eager yükleniyor](/ef/core/querying/related-data#eager-loading). Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur. Varlık okunmadığınızda ilgili veriler alınır. Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur. EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak. Birden çok sorgu verilmesi, EF6 içindeki bazı sorgular için tek bir sorgunun bulunduğu durumda daha verimli olabilir. Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:

  * Ana sorgu için bir sorgu 
  * Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.

* Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir". "düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir. İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  Note: EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklar için gezinti özelliklerini otomatik olarak düzeltir. Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.

* [Açık yükleme](/ef/core/querying/related-data#explicit-loading). Varlık ilk kez okunmadıysa ilgili veriler alınmadı. Gerektiğinde ilgili verileri almak için kodun yazılması gerekir. Ayrı sorgularla açık yükleme, VERITABANıNA birden çok sorgu gönderilmesine neden olur. Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir. `Load`Açık yükleme yapmak için yöntemini kullanın. Örneğin:

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* [Yavaş yükleme](/ef/core/querying/related-data#lazy-loading). [Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading). Varlık ilk kez okunmadıysa ilgili veriler alınmadı. Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır. Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu VERITABANıNA gönderilir.

* `Select`İşleci yalnızca gerekli ilgili verileri yükler.

## <a name="create-a-course-page-that-displays-department-name"></a>Bölüm adını görüntüleyen bir kurs sayfası oluşturma

Kurs varlığı, varlığı içeren bir gezinti özelliği içerir `Department` . `Department`Varlık, kursun atandığı departmanı içerir.

Bir kurs listesinde atanan departmanın adını göstermek için:

* `Name`Varlıktaki özelliği alın `Department` .
* `Department`Varlık, `Course.Department` Gezinti özelliğinden gelir.

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Kurs modelini temklesi

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Course` model sınıfı için kullanma bölümündeki yönergeleri izleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

Yukarıdaki komut, modeli bir daha yasaklıyor `Course` . Projeyi Visual Studio'da açın.

*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin. Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` . `Include`Yöntemi Eager yüklemeyi belirtir.

Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin. Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.

`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

Yukarıdaki kod ekler `AsNoTracking` . `AsNoTracking` Döndürülen varlıklar izlenmediğinden performansı geliştirir. Geçerli bağlamda güncelleştirilmemiş oldukları için varlıklar izlenmiyor.

*Pages/kurslar/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:

* Başlık dizinden kurslar olarak değiştirildi.
* Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` . Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir. Ancak, bu durumda birincil anahtar anlamlı olur.
* Departman adını göstermek için **Departman** sütunu değiştirildi. Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>Select ile ilgili verileri yükleme

`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` :

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select`İşleci yalnızca gerekli ilgili verileri yükler. Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır. Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.

Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`Şunları yapın:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>Kurslar ve kayıtları gösteren bir eğitmenler sayfası oluşturun

Bu bölümde, Eğitmenler sayfası oluşturulur.

<a name="IP"></a>
![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:

* Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler. `Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir. Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` . Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir. Bu durumda, Eğitmenler için Office atamaları görüntülenir.
* Kullanıcı bir eğitmen (önceki görüntüde Haruı) seçtiğinde ilgili `Course` varlıklar görüntülenir. `Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir. Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` . Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir. Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.
* Kullanıcı bir kurs seçtiğinde (önceki görüntüde Chemistry), varlıktaki ilgili veriler `Enrollments` görüntülenir. Önceki görüntüde öğrenci adı ve sınıf görüntülenir. `Course`Ve `Enrollment` varlıkları bire çok ilişkidir.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Eğitmen dizini görünümü için bir görünüm modeli oluşturun

Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir. Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.

*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Eğitmen modelini scafkatlama

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Instructor` model sınıfı için kullanma bölümündeki yönergeleri izleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

Yukarıdaki komut, modeli bir daha yasaklıyor `Instructor` . 
Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.

*Pages/eğitmenler/Index. cshtml. cs* öğesini şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.

*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

Sorgunun iki içerme vardır:

* `OfficeAssignment`: [Eğitmenler görünümünde](#IP)görüntülenir.
* `CourseAssignments`: Kurslar taöğretme.

### <a name="update-the-instructors-index-page"></a>Eğitmenler Dizin sayfasını Güncelleştir

*Sayfaları/eğitmenler/Index. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yapar:

* `page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` . `"{id:int?}"` bir yol şablonudur. Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir. Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:

  `http://localhost:1234/Instructors?id=2`

  Sayfa yönergesi olduğunda `@page "{id:int?}"` , ÖNCEKI URL şu şekilde olur:

  `http://localhost:1234/Instructors/2`

* Sayfa başlığı **eğitmenler** ' dir.
* Yalnızca null olmaması halinde görüntülenen bir **Office** sütunu eklendi `item.OfficeAssignment.Location` `item.OfficeAssignment` . Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu eklendi. Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .

* `class="success"`Seçilen eğitmenin öğesine dinamik olarak ekleyen kod eklendi `tr` . Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* **Select** etiketli yeni bir köprü eklendi. Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` . Eğer OfficeAssignment ' null ise boş bir tablo hücresi görüntülenir.

**Seç** bağlantısına tıklayın. Satır stili değişir.

### <a name="add-courses-taught-by-selected-instructor"></a>Seçili eğitmen 'e göre kurslar ekleyin

`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* dosyasındaki yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

Ekleyemiyorum `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

Güncelleştirilmiş sorguyu inceleyin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

Önceki sorgu `Department` varlıkları ekler.

Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür. Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır. Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where`Yöntemi bir koleksiyon döndürür. Önceki `Where` yöntemde yalnızca tek bir `Instructor` varlık döndürülür. `Single`Yöntemi, koleksiyonu tek bir `Instructor` varlığa dönüştürür. `Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` . `CourseAssignments` ilgili varlıklara erişim sağlar `Course` .

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır. `Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur. `SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir. `SingleOrDefault`Boş bir koleksiyonda kullanma:

* Bir özel durum sonucu oluşur ( `Courses` null başvuru üzerinde bir özellik bulmaya çalışırken).
* Özel durum iletisi sorunun nedenini daha az gösterir.

Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

*Pages/eğitmenler/Index. cshtml* sayfasının sonuna aşağıdaki biçimlendirmeyi ekleyin Razor :

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

Yukarıdaki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenin ilgili kursların bir listesini görüntüler.

Uygulamayı test etme. Eğitmenler sayfasında bir **seçme** bağlantısına tıklayın.

### <a name="show-student-data"></a>Öğrenci verilerini göster

Bu bölümde, uygulama seçili bir kurs için öğrenci verilerini gösterecek şekilde güncelleştirilir.

`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* içindeki yöntemdeki sorguyu aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Güncelleştirme *sayfaları/eğitmenler/Index. cshtml* . Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

Yukarıdaki biçimlendirme, seçili kursa kayıtlı öğrencilerin bir listesini görüntüler.

Sayfayı yenileyin ve bir eğitmen seçin. Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>Tek kullanımı

`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

Yukarıdaki `Single` yaklaşım, kullanarak herhangi bir avantaj sağlamaz `Where` . Bazı geliştiriciler `Single` yaklaşım stilini tercih eder.

## <a name="explicit-loading"></a>Belirtik yükleme

Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım. Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır. Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .

`OnGetAsync`Aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır. Bir kurs seçilirse vurgulanan kod şunu alır:

* `Enrollment`Seçili kurs için varlıklar.
* `Student`Her biri için varlıklar `Enrollment` .

Yukarıdaki kod açıklamalarının olduğunu fark edin `.AsNoTracking()` . Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.

Uygulamayı test etme. Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.

Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü (part1)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [Bu öğreticinin YouTube sürümü (part2)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[Önceki](xref:data/ef-rp/complex-data-model) 
> [Sonraki](xref:data/ef-rp/update-related-data)

::: moniker-end
