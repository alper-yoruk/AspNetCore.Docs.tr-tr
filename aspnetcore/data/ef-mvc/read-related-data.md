---
title: 'Öğretici: İlgili verileri okuyun - EF Core ile ASP.NET MVC'
description: Bu eğitimde, ilgili verileri , yani Varlık Çerçevesi'nin gezinti özelliklerine yüklediğiniz verileri okuyup görüntüleceksiniz.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: a6e63723101ab09219db81ee9796c3938a612226
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657110"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Öğretici: İlgili verileri okuyun - EF Core ile ASP.NET MVC

Önceki eğitimde, Okul veri modelini tamamladınız. Bu eğitimde, ilgili verileri okuyacak ve görüntüleceksiniz -- yani Varlık Çerçevesi'nin gezinti özelliklerine yüklediğiniz verileri.

Aşağıdaki çizimler, birlikte çalışacağınız sayfaları gösterir.

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * İlgili verileri nasıl yükleyeceklerini öğrenin
> * Kurslar sayfası oluşturma
> * Eğitmenler sayfası oluşturma
> * Açık yükleme hakkında bilgi edinin

## <a name="prerequisites"></a>Ön koşullar

* [Karmaşık veri modeli oluşturma](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>İlgili verileri nasıl yükleyeceklerini öğrenin

Entity Framework gibi Nesne İlişkisel Eşleme (ORM) yazılımının ilgili verileri bir varlığın gezinti özelliklerine yükleyebildiği birkaç yöntem vardır:

* Hevesli yükleme. Varlık okunduğunda, ilgili veriler onunla birlikte alınır. Bu genellikle, gereken tüm verileri alan tek bir birleştirme sorgusuyla sonuçlanır. Taraf Framework Core'da istekli `Include` yüklemeyi `ThenInclude` ve yöntemleri kullanarak belirtirsiniz.

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)

  Bazı verileri ayrı sorgularda alabilirsiniz ve EF gezinti özelliklerini "düzeltir".  Diğer bir deyişle, EF, daha önce alınan varlıkların gezinti özelliklerine ait oldukları ayrı olarak alınan varlıkları otomatik olarak ekler. İlgili verileri alan sorgu için, bir `Load` liste veya nesne döndüren bir yöntem `ToList` yerine `Single`yöntemi kullanabilirsiniz, örneğin veya .

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

* Açık yükleme. Varlık ilk okunduğunda, ilgili veriler alınmaz. Gerektiğinde ilgili verileri alan kod yazarsınız. Ayrı sorgularla istekli yükleme durumunda olduğu gibi, açık yükleme veritabanına gönderilen birden çok sorguyla sonuçlanır. Fark, açık yükleme ile, kod yüklenecek navigasyon özelliklerini belirtir. Varlık Framework Core 1.1'de `Load` açık yükleme yapmak için yöntemi kullanabilirsiniz. Örneğin:

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* Tembel yükleme. Varlık ilk okunduğunda, ilgili veriler alınmaz. Ancak, bir gezinti özelliğine ilk kez erişmeye çalıştığınızda, bu gezinti özelliği için gereken veriler otomatik olarak alınır. Bir gezinti özelliğinden ilk kez veri almaya çalıştığınızda veritabanına bir sorgu gönderilir. Entity Framework Core 1.0 tembel yüklemeyi desteklemez.

### <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Alınan her varlık için ilgili verilere ihtiyacınız olduğunu biliyorsanız, veritabanına gönderilen tek bir sorgu alınan her varlık için ayrı sorgulardan genellikle daha verimli olduğundan, hızlı yükleme genellikle en iyi performansı sunar. Örneğin, her bölümün ilgili on dersi olduğunu varsayalım. İlgili tüm verilerin hevesle yüklenmesi, yalnızca tek bir (birleştirme) sorgusu na ve veritabanına tek bir gidiş-dönüş seyahatle sonuçlanır. Her bölüm için kurslar için ayrı bir sorgu veritabanına on bir gidiş-dönüş geziler neden olacaktır. Veritabanına yapılan ekstra gidiş-dönüş yolculuklar, gecikme oranı yüksek olduğunda performansa özellikle zarar verir.

Diğer taraftan, bazı senaryolarda ayrı sorgular daha verimlidir. İlgili tüm verilerin tek bir sorguda hevesle yüklenmesi, SQL Server'ın verimli bir şekilde işlenemeyebileceği çok karmaşık bir birleştirme oluşturulmasına neden olabilir. Veya yalnızca işlediğiniz varlıkların kümesinin bir alt kümesi için bir varlığın gezinti özelliklerine erişmeniz gerekiyorsa, her şeyin önceden yüklenilen istekli yüklenmesi ihtiyacınız olandan daha fazla veri alacağı için ayrı sorgular daha iyi performans gösterebilir. Performans kritikse, en iyi seçimi yapmak için performansı her iki şekilde de test etmek en iyisidir.

## <a name="create-a-courses-page"></a>Kurslar sayfası oluşturma

Kurs tüzel kişiliği, kursun atandığı bölümün Bölüm tüzel kişiliğini içeren bir navigasyon özelliği içerir. Atanan bölümün adını bir ders listesinde görüntülemek için, gezinti özelliğinde bulunan Bölüm kuruluşundan Ad `Course.Department` özelliğini almanız gerekir.

Aşağıdaki resimde gösterildiği gibi, Daha önce Öğrenci denetleyicisi için yaptığınız Entity Framework iskelesini **kullanarak, mvc denetleyicisi** için görünümlere sahip aynı seçenekleri kullanarak, Ders varlık türü için CoursesController adında bir denetleyici oluşturun:

![Ders Ekle denetleyicisi](read-related-data/_static/add-courses-controller.png)

*CoursesController.cs* açın ve `Index` yöntemi inceleyin. Otomatik iskele `Department` `Include` yöntemini kullanarak navigasyon özelliği için istekli yükleme belirtmiştir.

Yöntemi, Ders varlıklarını döndüren varlıklar `IQueryable` için daha uygun bir`courses` ad `schoolContext`kullanan aşağıdaki kodla değiştirin ( bunun yerine): `Index`

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

*Görünümler/Kurslar/Index.cshtml'i* açın ve şablon kodunu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

İskele kodunda aşağıdaki değişiklikleri yaptınız:

* Başlığı Index'ten Derslere değiştirdi.

* Özellik değerini gösteren bir Sayı sütunu eklendi. **Number** `CourseID` Varsayılan olarak, birincil anahtarlar genellikle son kullanıcılar için anlamsız olduğundan İskele yekpişmez. Ancak, bu durumda birincil anahtar anlamlıdır ve bunu göstermek istiyorsunuz.

* Bölüm adını görüntülemek için **Bölüm** sütunu değiştirildi. Kod, gezinti `Name` özelliğine yüklenen Bölüm kuruluşunun `Department` özelliğini görüntüler:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Uygulamayı çalıştırın ve bölüm adlarının yer alan listesini görmek için **Dersler** sekmesini seçin.

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Eğitmenler sayfası oluşturma

Bu bölümde, Eğitmenler sayfasını görüntülemek için Eğitmen varlığı için bir denetleyici ve görünüm oluşturacaksınız:

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

Bu sayfa, ilgili verileri aşağıdaki şekillerde okur ve görüntüler:

* Eğitmenler listesi OfficeAssignment kuruluşundan ilgili verileri görüntüler. Eğitmen ve OfficeAtama varlıkları birden sıfıra veya bir ilişkisindedir. OfficeAssignment varlıkları için istekli yükleme kullanırsınız. Daha önce açıklandığı gibi, birincil tablonun tüm alınan satırları için ilgili verilere ihtiyaç duyduğunuzda, istekli yükleme genellikle daha verimlidir. Bu durumda, görüntülenen tüm eğitmenler için ofis atamaları görüntülemek istiyorsunuz.

* Kullanıcı bir eğitmen seçtiğinde, ilgili Kurs varlıkları görüntülenir. Eğitmen ve Kurs kuruluşları çok-çok ilişkisi vardır. Kurs kuruluşları ve ilgili Bölüm kuruluşları için istekli yükleme kullanırsınız. Bu durumda, yalnızca seçilen eğitmen için kurslara ihtiyacınız olduğundan ayrı sorgular daha verimli olabilir. Ancak, bu örnek, navigasyon özellikleri nde kendileri olan varlıklar içinde gezinme özellikleri için istekli yükleme nasıl kullanılacağını gösterir.

* Kullanıcı bir kurs seçtiğinde, Kayıtlar varlık kümesinden ilgili veriler görüntülenir. Kurs ve Kayıt kuruluşları bir-çok ilişkisi vardır. Kayıt kuruluşları ve bunların ilgili Öğrenci varlıkları için ayrı sorgular kullanırsınız.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Eğitmen Dizini görünümü için bir görünüm modeli oluşturma

Eğitmenler sayfası üç farklı tablodan verileri gösterir. Bu nedenle, her biri tablolardan birinin verilerini tutan üç özellik içeren bir görünüm modeli oluşturursunuz.

*SchoolViewModels* klasöründe, *InstructorIndexData.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Eğitmen denetleyicisini ve görünümlerini oluşturun

Aşağıdaki resimde gösterildiği gibi EF okuma/yazma eylemlerine sahip bir Eğitmen denetleyicisi oluşturun:

![Eğitmenler denetleyicisi ekle](read-related-data/_static/add-instructors-controller.png)

*InstructorsController.cs* açın ve Görünüm Modelleri ad alanı için bir kullanma deyimi ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

İlgili verilerin hevesle yüklenmesi için Dizin yöntemini aşağıdaki kodla değiştirin ve görünüm modeline koyun.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

Yöntem, seçilen eğitmenin`id`ve seçilen kursun kimlik`courseID`değerlerini sağlayan isteğe bağlı rota verilerini ( ) ve sorgu dize parametresini ( ) kabul eder. Parametreler, sayfadaki **Seç** köprüleri tarafından sağlanır.

Kod, görünüm modelinin bir örneğini oluşturarak ve eğitmen listesini koyarak başlar. Kod ve `Instructor.OfficeAssignment` `Instructor.CourseAssignments` navigasyon özellikleri için istekli yükleme belirtir. `CourseAssignments` Özellik içinde, `Course` özellik yüklenir ve bunun `Enrollments` içinde, `Department` ve özellikleri yüklenir ve her `Enrollment` varlık içinde `Student` özellik yüklenir.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Görünüm her zaman OfficeAssignment tüzel kişiliğini gerektirdiğinden, aynı sorguda bunu almak daha verimliolur. Web sayfasında bir eğitmen seçildiğinde ders varlıkları gereklidir, bu nedenle tek bir sorgu birden çok sorgudan daha iyidir, ancak sayfa daha sık bir dersle seçilirse daha iyidir.

Kod yineler `CourseAssignments` `Course` ve çünkü 'den `Course`iki özelliğe ihtiyacınız vardır. İlk `ThenInclude` çağrı dizisi `CourseAssignment.Course`, `Course.Enrollments`ve `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

Kodun bu noktasında, `ThenInclude` başka bir navigasyon `Student`özellikleri için , hangi gerekmez olacaktır. Ama `Include` arama özellikleri `Instructor` ile baştan başlar, bu yüzden tekrar zincir `Course.Department` üzerinden `Course.Enrollments`gitmek zorunda, bu kez yerine belirten .

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

Bir eğitmen seçildiğinde aşağıdaki kod çalıştırılır. Seçilen eğitmen görünüm modelindeki eğitmenler listesinden alınır. Görünüm modelinin `Courses` özelliği daha sonra o eğitmenin `CourseAssignments` gezinti özelliğinden Ders varlıklarıyla yüklenir.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

Yöntem `Where` bir koleksiyon döndürür, ancak bu durumda bu yönteme geçirilen ölçütler yalnızca tek bir Eğitmen varlığı döndürülür sonuçlanır. Yöntem, `Single` koleksiyonu tek bir Eğitmen varlığına dönüştürür ve bu da `CourseAssignments` bu varlığın özelliğine erişmenizi sağlar. Özellik, `CourseAssignments` `CourseAssignment` yalnızca ilgili `Course` varlıkları istediğiniz varlıkları içerir.

Koleksiyonun `Single` yalnızca bir öğesi olacağını bildiğinizde, bir koleksiyonda yöntemi kullanırsınız. Tek yöntem, koleksiyon boşsa veya birden fazla öğe varsa bir özel durum oluşturur. Alternatif, `SingleOrDefault`koleksiyon boşsa varsayılan değeri (bu durumda null) döndüren dir. Ancak, yine de bir özel durum (null reference `Courses` bir özellik bulmaya çalışırken) neden olur ve özel durum iletisi daha az açıkça sorunun nedenini gösterir. `Single` Yöntemi aradığınızda, `Where` yöntemi ayrı ayrı aramak yerine Nerede koşulu'ndan da geçebilirsiniz:

```csharp
.Single(i => i.ID == id.Value)
```

Onun yerine:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Daha sonra, bir kurs seçilirse, seçilen kurs görünüm modelindeki dersler listesinden alınır. Ardından görünüm modelinin `Enrollments` özelliği, o kursun `Enrollments` gezinti özelliğinden Kayıt varlıklarıyla yüklenir.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Eğitmen Dizini görünümünü değiştirme

*Görünümler/Eğitmenler/Index.cshtml'de*şablon kodunu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Varolan kodda aşağıdaki değişiklikleri yaptınız:

* Model sınıfını `InstructorIndexData`' niçin değiştirildi

* Sayfa başlığı **Dizin'den** **Eğitmenler'e**değiştirildi.

* Yalnızca null `item.OfficeAssignment` değilse `item.OfficeAssignment.Location` görüntüleyen bir **Office** sütunu eklendi. (Bu bire sıfır veya bir ilişkisi olduğundan, ilgili bir OfficeAssignment tüzel kişiliği olmayabilir.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Her eğitmen tarafından öğretilen dersleri görüntüleyen bir **Kurslar** sütunu eklendi. Daha fazla bilgi için, Razor sözdizimi makalesinin [Açık satır geçiş](xref:mvc/views/razor#explicit-line-transition) bölümüne bakın.

* Seçilen eğitmenin `class="success"` `tr` öğesine dinamik olarak eklenen kod eklendi. Bu, Bootstrap sınıfını kullanarak seçili satır için bir arka plan rengi ayarlar.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Seçili eğitmenin kimliğinin `Index` yönteme gönderilmesine neden olan, her satırdaki diğer bağlantılardan hemen önce **Seç** etiketli yeni bir köprü eklendi.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Uygulamayı çalıştırın ve **Eğitmenler** sekmesini seçin. Sayfa, ilgili OfficeAssignment varlıklarının Konum özelliğini ve ilgili OfficeAssignment varlığı olmadığında boş bir tablo hücresini görüntüler.

![Eğitmenler Dizin sayfası hiçbir şey seçildi](read-related-data/_static/instructors-index-no-selection.png)

*Görünümler/Eğitmenler/Index.cshtml* dosyasında, kapanış tablosu öğesinden sonra (dosyanın sonunda) aşağıdaki kodu ekleyin. Bu kod, bir eğitmen seçildiğinde bir eğitmenle ilgili derslerin listesini görüntüler.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Bu kod, `Courses` bir ders listesini görüntülemek için görünüm modelinin özelliğini okur. Ayrıca, seçili kursun kimliğini `Index` eylem yöntemine gönderen bir **Seç** köprü sağlar.

Sayfayı yenileyin ve bir eğitmen seçin. Şimdi seçilen eğitmene atanan dersleri görüntüleyen bir ızgara görüyorsunuz ve her kurs için atanan bölümün adını görüyorsunuz.

![Eğitmenler Dizin sayfası eğitmeni seçildi](read-related-data/_static/instructors-index-instructor-selected.png)

Eklediğiniz kod bloğundan sonra aşağıdaki kodu ekleyin. Bu, o ders seçildiğinde bir kursa kayıtlı olan öğrencilerin listesini görüntüler.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Bu kod, kursa kayıtlı öğrencilerin listesini görüntülemek için görünüm modelinin Kayıtlar özelliğini okur.

Sayfayı yeniden yenileyin ve bir eğitmen seçin. Ardından, kayıtlı öğrencilerin listesini ve notlarını görmek için bir kurs seçin.

![Eğitmenler Dizin sayfa öğretim görevlisi ve seçilen ders](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>Açık yükleme hakkında

*InstructorsController.cs'daki*eğitmenlerin listesini aldığınızda, `CourseAssignments` gezinti özelliği için istekli yükleme yi belirttiniz.

Kullanıcıların belirli bir eğitmen ve kurstaki kayıtları nadiren görmek istemelerini beklediğiniz varsayalım. Bu durumda, kayıt verilerini yalnızca istendiğinde yüklemek isteyebilirsiniz. Açık yüklemenin nasıl yapılacağını görmek için, `Index` metodun kayıtları ve bu özelliği açıkça yükleyen yüklemeleri kaldıran aşağıdaki kodla değiştirin. Kod değişiklikleri vurgulanır.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

Yeni kod, Eğitmen varlıklarını alan koddan kayıt verilerini çağıran *ThenInclude* yöntemini düşürür. Ayrıca düşer. `AsNoTracking`  Bir eğitmen ve kurs seçilirse, vurgulanan kod seçilen kurs için Kayıt varlıklarını ve her Kayıt için Öğrenci varlıklarını alır.

Uygulamayı çalıştırın, şimdi Eğitmenler Dizini sayfasına gidin ve verilerin nasıl alındığını değiştirmiş olsanız da sayfada görüntülenenler arasında hiçbir fark görmezsiniz.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * İlgili verilerin nasıl yüklenir öğrenilir
> * Kurslar sayfası oluşturuldu
> * Eğitmenler sayfası oluşturuldu
> * Açık yükleme hakkında bilgi edinilmiş

İlgili verileri nasıl güncelleştirtini öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [İlgili verileri güncelleştirme](update-related-data.md)
