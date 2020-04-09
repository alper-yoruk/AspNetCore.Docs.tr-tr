---
title: 'Öğretici: CRUD İşlevselliği Uygulayın - EF Core ile mvc ASP.NET'
description: Bu eğitimde, MVC iskelesinin denetleyicilerde ve görünümlerde sizin için otomatik olarak oluşturduğu CRUD (oluşturma, oku, güncelleme, silme) kodunu gözden geçirip özelleştireceksiniz.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/crud
ms.openlocfilehash: 2aa4ef48509b9a34f3b25eb657b1ecac51c1374b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416214"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Öğretici: CRUD İşlevselliği Uygulayın - EF Core ile mvc ASP.NET

Önceki öğreticide, Entity Framework ve SQL Server LocalDB'yi kullanarak verileri depolayan ve görüntüleyen bir MVC uygulaması oluşturdunuz. Bu eğitimde, MVC iskelesinin denetleyicilerde ve görünümlerde sizin için otomatik olarak oluşturduğu CRUD (oluşturma, oku, güncelleme, silme) kodunu gözden geçirip özelleştireceksiniz.

> [!NOTE]
> Denetleyiciniz ve veri erişim katmanı arasında bir soyutlama katmanı oluşturmak için depo deseni uygulamak yaygın bir uygulamadır. Bu öğreticileri basit tutmak ve Entity Framework'ün kendisini nasıl kullanacağını öğretmeye odaklanmak için depolar kullanmaz. EF ile depolar hakkında daha fazla bilgi için [bu serinin son öğreticibakın.](advanced.md)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Ayrıntılar sayfasını özelleştirin
> * Oluştur sayfasını güncelleştirin
> * Edit sayfasını güncelleştir
> * Sil sayfasını güncelleştir
> * Veritabanı bağlantılarını kapatma

## <a name="prerequisites"></a>Ön koşullar

* [EF Core ve ASP.NET Core MVC ile başlayın](intro.md)

## <a name="customize-the-details-page"></a>Ayrıntılar sayfasını özelleştirin

Öğrenci Dizini sayfasının iskele kodu, bu `Enrollments` özelliğin bir koleksiyona sahip olması nedeniyle özelliği dışarıda bıraktı. **Ayrıntılar** sayfasında, koleksiyonun içeriğini bir HTML tablosunda görüntülersiniz.

*Denetleyiciler/StudentsController.cs 'de*Ayrıntılar görünümü için eylem `SingleOrDefaultAsync` yöntemi tek `Student` bir varlığı almak için yöntemi kullanır. Çağıran `Include`kod ekle. `ThenInclude`ve `AsNoTracking` yöntemler, aşağıdaki vurgulanan kodda gösterildiği gibi.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Ve `Include` `ThenInclude` yöntemler, bağlamın gezinti `Student.Enrollments` özelliğini yüklemesine ve `Enrollment.Course` her kayıt içinde gezinti özelliğine neden olur.  Bu yöntemler hakkında daha fazla [read related data](read-related-data.md) bilgi edinebilirsiniz.

Yöntem, `AsNoTracking` döndürülen varlıkların geçerli bağlamın ömrü nde güncelleştirilmeyeceği senaryolarda performansı artırır. Bu eğitimin sonunda `AsNoTracking` hakkında daha fazla bilgi edineceksiniz.

### <a name="route-data"></a>Veri yönlendirme

`Details` Yönteme geçirilen anahtar değer *rota verilerinden*gelir. Rota verileri, model bağlayıcısının URL'nin bir kesiminde bulduğu verilerdir. Örneğin, varsayılan rota denetleyici, eylem ve id segmentleri belirtir:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

Aşağıdaki URL'de, varsayılan rota Eğitmeni denetleyici olarak, Dizin eylem olarak ve 1 id olarak eşler; bunlar rota veri değerleridir.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

URL'nin son bölümü ("?courseID=2021") bir sorgu dize değeridir. Bir sorgu dize değeri olarak geçerseniz model bağlayıcısı da `Index` yöntem `id` parametrekimlik değeri geçecektir:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Dizin sayfasında, köprü URL'leri Razor görünümünde etiket yardımcısı ifadeleri tarafından oluşturulur. Aşağıdaki Razor `id` kodunda, parametre varsayılan rotayla `id` eşleşir, böylece rota verilerine eklenir.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Bu, 6 olduğunda `item.ID` aşağıdaki HTML'yi oluşturur:

```html
<a href="/Students/Edit/6">Edit</a>
```

Aşağıdaki Razor kodunda, `studentID` varsayılan rotadaki bir parametreyle eşleşmez, bu nedenle sorgu dizesi olarak eklenir.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Bu, 6 olduğunda `item.ID` aşağıdaki HTML'yi oluşturur:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Etiket yardımcıları hakkında daha fazla <xref:mvc/views/tag-helpers/intro>bilgi için bkz.

### <a name="add-enrollments-to-the-details-view"></a>Ayrıntılar görünümüne kayıt ekleme

*Açık Görünümler/Öğrenciler/Ayrıntılar.cshtml*. Her alan, `DisplayNameFor` aşağıdaki `DisplayFor` örnekte gösterildiği gibi, aşağıdaki şekilde kullanılarak ve yardımcılar kullanılarak görüntülenir:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Son alandan sonra ve `</dl>` kapanış etiketinden hemen önce, kayıt listesini görüntülemek için aşağıdaki kodu ekleyin:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Kodu yapıştırdıktan sonra kod girintisi yanlışsa, düzeltmek için CTRL-K-D tuşuna basın.

Bu kod, gezinti özelliğindeki `Enrollments` varlıklar arasında döngüler. Her kayıt için ders unvanını ve notu görüntüler. Kurs unvanı, Kayıtlar kuruluşunun `Course` navigasyon özelliğinde depolanan Kurs kuruluşundan alınır.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve bir öğrenciiçin **Ayrıntılar** bağlantısını tıklatın. Seçilen öğrencinin ders ve notlarının listesini görürsünüz:

![Öğrenci Detayları sayfası](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Oluştur sayfasını güncelleştirin

*StudentsController.cs,* bir try-catch bloğu ekleyerek ve öznitelik kimliği kaldırarak HttpPost `Create` yöntemini `Bind` değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Bu kod, ASP.NET Core MVC modeli bağlayıcısı tarafından oluşturulan Öğrenci varlığını Öğrenci varlık kümesine ekler ve sonra değişiklikleri veritabanına kaydeder. (Model bağlayıcısı, form tarafından gönderilen verilerle çalışmanızı kolaylaştıran ASP.NET Core MVC işlevini ifade eder; bir model bağlayıcısı deftere nakledilen form değerlerini CLR türlerine dönüştürür ve bunları parametrelerdeki eylem yöntemine geçirir. Bu durumda, model bağlayıcısı Form koleksiyonundaki özellik değerlerini kullanarak öğrenci varlığını anında alır.)

ID, `ID` SQL `Bind` Server'ın satır eklendiğinde otomatik olarak ayarladığı birincil anahtar değeri olduğu için öznitelikten kaldırıldınız. Kullanıcıdan giriş, kimlik değerini belirlemez.

Öznitelik `Bind` dışında, try-catch bloğu, iskele kodunda yaptığınız tek değişikliktir. Değişiklikler kaydedilirken türeyen `DbUpdateException` bir özel durum yakalanırsa, genel bir hata iletisi görüntülenir. `DbUpdateException`özel durumlar bazen bir programlama hatası yerine uygulamanın dışında bir şey neden olur, bu nedenle kullanıcının yeniden denemesi önerilir. Bu örnekte uygulanmasa da, üretim kalitesi uygulaması özel durumu günlüğe kaydeder. Daha fazla bilgi için İzleme [ve Telemetri (Azure ile Gerçek Dünya Bulut Uygulamaları Oluşturma)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry) **bölümündeki öngörü** ler için Günlük bölümüne bakın.

Öznitelik, `ValidateAntiForgeryToken` siteler arası istek sahteciliği (CSRF) saldırılarını önlemeye yardımcı olur. Belirteç, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) tarafından görüntüye otomatik olarak enjekte edilir ve form kullanıcı tarafından gönderildiğinde dahil edilir. Belirteç öznitelik tarafından `ValidateAntiForgeryToken` doğrulanır. CSRF hakkında daha fazla bilgi için [Anti-İstek Sahteciliği'ne](../../security/anti-request-forgery.md)bakın.

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Aşırı gönderme ile ilgili güvenlik notu

`Bind` İskele koduyöntemde `Create` içerdiği öznitelik, oluşturma senaryolarında aşırıya katlamaya karşı korumanın bir yoludur. Örneğin, Öğrenci tüzel kişiliğinin bu web sayfasının ayarlamasını istemediğiniz bir `Secret` özellik içerdiğini varsayalım.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Web sayfasında bir `Secret` alanınız olmasa bile, bir bilgisayar korsanı `Secret` form değeri göndermek için Fiddler gibi bir araç kullanabilir veya bazı JavaScript yazabilir. `Bind` Model bağlayıcısının Öğrenci örneğini oluştururken kullandığı alanları sınırlayan öznitelik olmadan, model bağlayıcısı `Secret` bu form değerini alır ve Öğrenci varlık örneğini oluşturmak için kullanır. Daha sonra, `Secret` form alanı için belirtilen bilgisayar korsanı değeri veritabanınızda güncelleştirilir. Aşağıdaki resimde Fiddler aracı, `Secret` deftere nakledilen form değerlerine alanı ("OverPost" değeriyle) ekleyerek gösterir.

![Fiddler Gizli alan ekleme](crud/_static/fiddler.png)

"OverPost" değeri daha sonra eklenen satırın `Secret` özelliğine başarıyla eklenir, ancak web sayfasının bu özelliği ayarlayabilmesi ni hiç amaçlamamanıza rağmen.

Önce veritabanından varlığı okuyup sonra açık izin verilen özellikler listesinden geçerek çağırarak `TryUpdateModel`düzenleme senaryolarında aşırı deftere nakilyi önleyebilirsiniz. Bu öğreticilerde kullanılan yöntem bu.

Birçok geliştirici tarafından tercih edilen aşırı deftere nakilleri önlemenin alternatif bir yolu, model bağlamalı varlık sınıfları yerine görünüm modellerini kullanmaktır. Görünüm modelinde yalnızca güncelleştirmek istediğiniz özellikleri ekleyin. MVC model bağlayıcısı tamamlandıktan sonra, isteğe bağlı olarak AutoMapper gibi bir araç kullanarak görünüm modeli özelliklerini varlık örneğine kopyalayın. Durum `_context.Entry` `Unchanged`durumunu ayarlamak ve ardından görünüm modelinde `Property("PropertyName").IsModified` yer alan her varlık özelliğinde doğru olarak ayarlamak için varlık örneğini kullanın. Bu yöntem hem düzenlemesi hem de senaryooluşturmada çalışır.

### <a name="test-the-create-page"></a>Oluştur sayfasını test edin

*Görünümler/Öğrenciler/Create.cshtml'deki* `label`kod, `input` `span` her alan için yardımcıları ve (doğrulama iletileri için) kullanır.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve **Yeni Oluştur'u**tıklatın.

Adları ve tarihi girin. Tarayıcınız bunu yapmanıza izin veriyorsa geçersiz bir tarih girmeyi deneyin. (Bazı tarayıcılar sizi tarih seçici kullanmaya zorlar.) Ardından hata iletisini görmek için **Oluştur'u** tıklatın.

![Tarih doğrulama hatası](crud/_static/date-error.png)

Bu, varsayılan olarak aldığınız sunucu tarafı doğrulamasI; daha sonraki bir öğreticide, istemci tarafı doğrulaması için kod oluşturacak özniteliklerin nasıl ekleyeceğinide de göreceksiniz. Aşağıdaki vurgulanan kod yöntemde model doğrulama `Create` denetimini gösterir.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Tarihi geçerli bir değerle değiştirin ve yeni öğrencinin **Dizin** sayfasında görünmesini görmek için **Oluştur'u** tıklatın.

## <a name="update-the-edit-page"></a>Edit sayfasını güncelleştir

StudentController.cs'da, *StudentController.cs*HttpGet `Edit` yöntemi (özniteliği olmayan `HttpPost` yöntem) `SingleOrDefaultAsync` `Details` yöntemde gördüğünüz gibi seçili Öğrenci varlığını almak için yöntemi kullanır. Bu yöntemi değiştirmeniz gerekmez.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Önerilen HttpPost Edit kodu: Okuma ve güncelleme

HttpPost Edit eylem yöntemini aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Bu değişiklikler, aşırı göndermeyi önlemek için bir güvenlik en iyi uygulama uygular. İskele klasörü bir `Bind` öznitelik oluşturdu ve model bağlayıcısı tarafından oluşturulan varlığı `Modified` bir bayrak ile ayarlanan varlığa ekledi. `Bind` Öznitelik `Include` parametrede listelenmemiş alanlarda önceden varolan verileri temizler, çünkü bu kod birçok senaryo için önerilmez.

Yeni kod, varolan varlığı `TryUpdateModel` okur ve [deftere nakledilen form verilerindeki kullanıcı girişine dayalı](xref:mvc/models/model-binding)olarak alınan varlıktaki alanları güncelleştirmek için çağrılar. Varlık Çerçevesi'nin otomatik değişiklik `Modified` izleme formu girişi ile değiştirilen alanlarda bayrağı ayarlar. `SaveChanges` Yöntem çağrıldığında, Varlık Çerçevesi veritabanı satırını güncelleştirmek için SQL deyimleri oluşturur. Eşzamanlılık çakışmaları yoksayılır ve yalnızca kullanıcı tarafından güncelleştirilen tablo sütunları veritabanında güncelleştirilir. (Daha sonraki bir öğretici eşzamanlılık çakışmaları nasıl ele alınılacağını gösterir.)

Aşırı deftere nakil önlemek için en iyi yöntem olarak, **Düzenleme** sayfası tarafından güncelleştirilebilmesini istediğiniz alanlar `TryUpdateModel` parametrelerde beyaz listelenir. (Parametre listesindeki alanların listesinden önceki boş dize, form alanları adlarıyla kullanılacak bir önek içindir.) Şu anda koruduğunuz ek alan yok, ancak model bağlayıcısının bağlanmasını istediğiniz alanları listelemek, gelecekte veri modeline alan eklerseniz, bunları açıkça buraya ekleyene kadar otomatik olarak korunduklarını sağlar.

Bu değişikliklerin bir sonucu olarak, HttpPost `Edit` yönteminin yöntem imzası HttpGet `Edit` yöntemiyle aynıdır; bu nedenle yöntemi `EditPost`yeniden adlandırdın.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Alternatif HttpPost Edit kodu: Oluştur ve ekle

Önerilen HttpPost düzenleme kodu, yalnızca değiştirilen sütunların güncelleştirilmesini sağlar ve model bağlama için dahil edilmesini istemediğiniz özelliklerdeki verileri korur. Ancak, ilk okuma yaklaşımı fazladan bir veritabanı okuması gerektirir ve eşzamanlılık çakışmaları işlemek için daha karmaşık kodlar neden olabilir. Alternatif, model bağlayıcısı tarafından oluşturulan bir varlığı EF bağlamına eklemek ve değiştirilmiş olarak işaretlemektir. (Projenizi bu kodla güncellemeyin, yalnızca isteğe bağlı bir yaklaşımı göstermek için gösterilir.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Web sayfası Kullanıcı GSürümü varlıktaki tüm alanları içerdiğinde ve bunlardan herhangi birini güncelleştirebildiği zaman bu yaklaşımı kullanabilirsiniz.

İskele kodu oluşturma ve iliştirme yaklaşımını kullanır, `DbUpdateConcurrencyException` ancak yalnızca özel durumları yakalar ve 404 hata kodlarını döndürür.  Gösterilen örnek, herhangi bir veritabanı güncelleştirme özel durum yakalar ve bir hata iletisi görüntüler.

### <a name="entity-states"></a>Taraf Devletleri

Veritabanı bağlamı, bellekteki varlıkların veritabanındaki karşılık gelen satırlarla eşitlenip eşit olmadığını izler ve bu `SaveChanges` bilgiler yöntemi çağırdığınızda ne olacağını belirler. Örneğin, `Add` yönteme yeni bir varlık geçtiğinde, o varlığın `Added`durumu . Daha sonra `SaveChanges` yöntemi çağırdığınızda, veritabanı bağlamı bir SQL INSERT komutu yayınlar.

Bir varlık aşağıdaki durumlardan birinde olabilir:

* `Added`. Varlık henüz veritabanında yok. Yöntem `SaveChanges` bir INSERT deyimi yayınlar.

* `Unchanged`. Bu varlıkla `SaveChanges` yöntemle hiçbir şey yapılamamalıdır. Veritabanından bir varlık okuduğunuzda, varlık bu durumla başlar.

* `Modified`. Varlığın özellik değerlerinin bir kısmı veya tümü değiştirildi. Yöntem, `SaveChanges` UPDATE deyimini yayınlar.

* `Deleted`. Varlık silinmesi için işaretlenmiştir. Yöntem, `SaveChanges` DELETE deyimi ni yayınlar.

* `Detached`. Varlık veritabanı bağlamı tarafından izlenmiyor.

Bir masaüstü uygulamasında durum değişiklikleri genellikle otomatik olarak ayarlanır. Bir varlığı okur ve bazı özellik değerlerinde değişiklik yaparsınız. Bu, varlık durumunun otomatik olarak `Modified`'' olarak değiştirilmesine neden olur. Daha sonra `SaveChanges`, Entity Framework'u aradiğinizde, yalnızca değiştirdiğiniz gerçek özellikleri güncelleştiren bir SQL UPDATE deyimi oluşturur.

Bir web uygulamasında, `DbContext` başlangıçta bir varlığı okuyan ve düzenlenecek verileri görüntüleyen sayfa işlendikten sonra atılır. HttpPost `Edit` eylem yöntemi çağrıldığında, yeni bir web isteği yapılır ve `DbContext`yeni bir örnek var. Varlığı bu yeni bağlamda yeniden okursanız, masaüstü işlemeyi simüle elersiniz.

Ancak, ek okuma işlemini yapmak istemiyorsanız, model bağlayıcısı tarafından oluşturulan varlık nesnesini kullanmanız gerekir.  Bunu yapmanın en basit yolu, daha önce gösterilen alternatif HttpPost Edit kodunda yapıldığı gibi varlık durumunu Değiştirilecek şekilde ayarlamaktır. Ardından, `SaveChanges`bağlam değiştirdiğiniz özellikleri bilmenin bir yolu olmadığından, Varlık Çerçevesi veritabanı satırının tüm sütunlarını güncelleştirir.

İlk okuma yaklaşımından kaçınmak istiyorsanız, ancak SQL UPDATE deyiminin yalnızca kullanıcının gerçekten değiştirdiği alanları güncelleştirmesini istiyorsanız, kod daha karmaşıktır. Orijinal değerleri bir şekilde kaydetmeniz gerekir (gizli alanları kullanarak gibi) böylece HttpPost `Edit` yöntemi çağrıldığında kullanılabilir olurlar. Daha sonra özgün değerleri kullanarak bir Öğrenci `Attach` varlığı oluşturabilir, varlığın özgün sürümüyle yöntemi arayabilir, varlığın değerlerini `SaveChanges`yeni değerlere güncelleyebilir ve sonra .

### <a name="test-the-edit-page"></a>Edit sayfasını test edin

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve ardından bir **Edit** köprüünü tıklatın.

![Öğrenciler sayfayı edin](crud/_static/student-edit.png)

Bazı verileri değiştirin ve **Kaydet'i**tıklatın. **Dizin** sayfası açılır ve değiştirilen verileri görürsünüz.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

*StudentController.cs'* de, HttpGet `Delete` yönteminin şablon `SingleOrDefaultAsync` kodu, Ayrıntılar ve Edit yöntemlerinde gördüğünüz gibi seçili Öğrenci varlığını almak için yöntemi kullanır. Ancak, arama `SaveChanges` başarısız olduğunda özel bir hata iletisi uygulamak için, bu yönteme ve ilgili görünümüne bazı işlevler eklersiniz.

Güncelleştirme ve oluşturma işlemleri için gördüğünüz gibi, silme işlemleri iki eylem yöntemi gerektirir. GET isteğine yanıt olarak çağrılan yöntem, kullanıcıya silme işlemini onaylama veya iptal etme şansı veren bir görünüm görüntüler. Kullanıcı onaylarsa, bir POST isteği oluşturulur. Bu durumda, HttpPost `Delete` yöntemi çağrılır ve daha sonra bu yöntem aslında silme işlemi gerçekleştirir.

Veritabanı güncelleştirildiğinde oluşabilecek hataları işlemek `Delete` için HttpPost yöntemine bir try-catch bloğu eklersiniz. Bir hata oluşursa, HttpPost Delete yöntemi HttpGet Delete yöntemini çağırır ve bir hata oluştuğunu belirten bir parametre yi geçer. HttpGet Delete yöntemi daha sonra onay sayfasını hata iletisiyle birlikte yeniden görüntüler ve kullanıcıya iptal etme veya yeniden deneme fırsatı verir.

Hata raporlamayı `Delete` yöneten aşağıdaki kodla HttpGet eylem yöntemini değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Bu kod, yöntemin değişiklikleri kaydetme hatasından sonra çağrılıp çağrılmadığını gösteren isteğe bağlı bir parametre kabul eder. Bu parametre, Daha önce `Delete` bir hata olmadan HttpGet yöntemi çağrıldığında yanlıştır. Bir veritabanı güncelleştirme hatasına `Delete` yanıt olarak HttpPost yöntemi tarafından çağrıldığında, parametre doğrudur ve görünüme bir hata iletisi iletilir.

### <a name="the-read-first-approach-to-httppost-delete"></a>HttpPost Delete için ilk okuma yaklaşımı

Gerçek silme `Delete` işlemini gerçekleştiren `DeleteConfirmed`ve veritabanı güncelleştirme hatalarını yakalayan Aşağıdaki kodla HttpPost eylem yöntemini (adlandırılmış) değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Bu kod seçili varlığı alır, `Remove` sonra varlığın durumunu `Deleted`'da ayarlamak için yöntemi çağırır. Çağrıldığında, `SaveChanges` bir SQL DELETE komutu oluşturulur.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>HttpPost Delete'e oluşturma ve iliştirme yaklaşımı

Yüksek hacimli bir uygulamada performansı artırmak bir öncelikse, öğrenci varlığını yalnızca birincil anahtar değerini kullanarak anında ayarlayarak ve varlık `Deleted`durumunu 'ya ayarlayarak gereksiz bir SQL sorgusundan kaçınabilirsiniz. Varlık Çerçevesi'nin varlığı silmek için ihtiyacı olan tek şey bu. (Bu kodu projenize koymayın; sadece bir alternatif göstermek için buradadır.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Varlığın da silinmesi gereken ilgili verileri varsa, basamaklı silmenin veritabanında yapılandırıldığından emin olun. Varlık silme bu yaklaşım ile, EF silinecek ilgili varlıklar olduğunu fark olmayabilir.

### <a name="update-the-delete-view"></a>Sil görünümünü güncelleştir

*Görünümler/Öğrenci/Delete.cshtml'de,* aşağıdaki örnekte gösterildiği gibi h2 başlığı ile h3 başlığı arasında bir hata iletisi ekleyin:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve **Sil** köprüünü tıklatın:

![Onay sayfasını silme](crud/_static/student-delete.png)

**Sil'i**tıklatın. Dizin sayfası silinen öğrenci olmadan görüntülenir. (Eşzamanlılık öğreticisinde eylem hata işleme kodunun bir örneğini görürsünüz.)

## <a name="close-database-connections"></a>Veritabanı bağlantılarını kapatma

Veritabanı bağlantısının tuttuğu kaynakları boşaltmak için, bağlam örneğinin, bu bağlantıyla yaptığınız iş bittiğinde mümkün olan en kısa sürede imha edilmesi gerekir. ASP.NET Core yerleşik [bağımlılık enjeksiyonu](../../fundamentals/dependency-injection.md) sizin için bu görevi halletmek.

*Startup.cs,* ASP.NET Core DI kapsayıcısında `DbContext` sınıf sağlamak için [AddDbContext uzantı yöntemini](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) çağırırsınız. Bu yöntem, hizmet `Scoped` ömrünü varsayılan olarak ayarlar. `Scoped`bağlam nesnesi yaşam süresi web isteği yaşam `Dispose` süresi ile çakışıyor anlamına gelir ve yöntem web isteğinin sonunda otomatik olarak çağrılacak.

## <a name="handle-transactions"></a>Hareketleri işleme

Varsayılan olarak Varlık Çerçevesi işlemleri dolaylı olarak uygular. Birden çok satırda veya tabloda değişiklik yaptığınız `SaveChanges`ve ardından "Varlık Çerçevesi" dediğiniz senaryolarda, Varlık Çerçevesi otomatik olarak tüm değişikliklerinizin başarılı olmasını veya hepsinin başarısız olmasını sağlar. Bazı değişiklikler önce yapılırsa ve sonra bir hata olursa, bu değişiklikler otomatik olarak geri alınır. Daha fazla denetime ihtiyaç duyduğunuz senaryolar için (örneğin, bir harekette Entity Framework dışında yapılan işlemleri eklemek istiyorsanız), [Hareketler'e](/ef/core/saving/transactions)bakın.

## <a name="no-tracking-queries"></a>Sorguları izlememe

Veritabanı bağlamı tablo satırlarını aldığında ve bunları temsil eden varlık nesneleri oluşturduğunda, varsayılan olarak bellekteki varlıkların veritabanındakilerle eşitlenip eşitolmadığını izler. Bellekteki veriler önbellek görevi görür ve bir varlığı güncelleştirdiğinizde kullanılır. Bağlam örnekleri genellikle kısa ömürlü olduğundan (her istek için yeni bir tane oluşturulur ve atılır) ve varlığı okuyan bağlam genellikle bu varlık yeniden kullanılmadan önce atılır, çünkü bu önbelleğe alma genellikle bir web uygulamasında gereksizdir.

`AsNoTracking` Yöntemi çağırarak bellekteki varlık nesnelerinin izlemeyi devre dışı kullanabilirsiniz. Yapmak isteyebileceğin tipik senaryolar şunlardır:

* Bağlam ömrü boyunca herhangi bir varlığı güncelleştirmeniz gerekmez ve gezinti özelliklerini [ayrı sorgular tarafından alınan varlıklarla otomatik olarak yüklemek](read-related-data.md)için EF'ye ihtiyacınız yoktur. Bu koşullar genellikle bir denetleyicinin HttpGet eylem yöntemleriyle karşılanır.

* Büyük miktarda veri alan bir sorgu çalıştırıyorsunuz ve döndürülen verilerin yalnızca küçük bir bölümü güncelleştirildi. Büyük sorgu için izlemeyi kapatmak ve daha sonra güncelleştirilmesi gereken birkaç varlık için bir sorgu çalıştırmak daha verimli olabilir.

* Bir varlığı güncelleştirmek için eklemek istiyorsunuz, ancak daha önce farklı bir amaç için aynı varlığı aldınız. Varlık zaten veritabanı bağlamı tarafından izleniyor olduğundan, değiştirmek istediğiniz varlığı ekemezsiniz. Bu durumu ele almak için `AsNoTracking` bir yolu önceki sorgu çağırmaktır.

Daha fazla bilgi için izleme [ve İzleme Yok'](/ef/core/querying/tracking)a bakın.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Ayrıntılar sayfasını özelleştirmiş
> * Oluştur sayfasını güncelleştir
> * Edit sayfasını güncelleştir
> * Sil sayfasını güncelleştir
> * Kapalı veritabanı bağlantıları

Sıralama, filtreleme ve sayfalama ekleyerek **Dizin** sayfasının işlevselliğini nasıl genişleteceklerini öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Sonraki: Sıralama, filtreleme ve sayfalama](sort-filter-page.md)
