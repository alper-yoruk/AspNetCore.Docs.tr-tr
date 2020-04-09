---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - CRUD - 2/8
author: rick-anderson
description: EF Core ile nasıl oluşturulup okunup güncelleştirilir, silinir.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/crud
ms.openlocfilehash: 05519852fab22bd3ad5b77e3494b49191448286f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665650"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a>ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - CRUD - 2/8

Yazar: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), ve Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, iskeleye sarılmış CRUD (oluşturma, oku, güncelleme, silme) kodu gözden geçirilir ve özelleştirilmiştir.

## <a name="no-repository"></a>Depo yok

Bazı geliştiriciler, UI (Razor Pages) ve veri erişim katmanı arasında bir soyutlama katmanı oluşturmak için bir hizmet katmanı veya depo deseni kullanır. Bu öğretici bunu yapmaz. Karmaşıklığı en aza indirmek ve öğreticiyi EF Core'a odaklamak için, EF Core kodu doğrudan sayfa modeli sınıflarına eklenir. 

## <a name="update-the-details-page"></a>Ayrıntılar sayfasını güncelleştirin

Öğrenciler sayfaları için iskele kodu kayıt verilerini içermez. Bu bölümde, Ayrıntılar sayfasına kayıt eklersiniz.

### <a name="read-enrollments"></a>Kayıtları okuma

Bir öğrencinin kayıt verilerini sayfada görüntülemek için okumanız gerekir. *Sayfalar/Öğrenciler/Details.cshtml.cs'deki* iskele kodu, Kayıt verileri olmadan yalnızca Öğrenci verilerini okur:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Seçilen `OnGetAsync` öğrencinin kayıt verilerini okumak için yöntemi aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

Ekle ve [Sonra Ekle](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) yöntemleri bağlamın `Student.Enrollments` gezinti özelliğini yüklemesine `Enrollment.Course` ve her kayıt içinde gezinti özelliğine neden olur. [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) Bu [yöntemler, Okuma ile ilgili veri](xref:data/ef-rp/read-related-data) öğretici ayrıntılı olarak incelenir.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) yöntemi, döndürülen varlıkların geçerli bağlamda güncelleştirilemediğini senaryolarda performansı artırır. `AsNoTracking`daha sonra bu öğretici ele alınmıştır.

### <a name="display-enrollments"></a>Kayıtları görüntüleme

Kayıt listesini görüntülemek için *Sayfalar/Öğrenciler/Details.cshtml'deki* kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Önceki kod, gezinti özelliğindeki varlıklar `Enrollments` arasında döngüler. Her kayıt için ders unvanını ve notu görüntüler. Kurs unvanı, Kayıtlar kuruluşunun `Course` navigasyon özelliğinde depolanan Kurs kuruluşundan alınır.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve bir öğrenciiçin **Ayrıntılar** bağlantısını tıklatın. Seçilen öğrencinin ders ve notlistesi görüntülenir.

### <a name="ways-to-read-one-entity"></a>Bir varlığı okuma yolları

Oluşturulan kod, bir varlığı okumak için [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) kullanır. Hiçbir şey bulunmazsa bu yöntem null döndürür; aksi takdirde, sorgu filtresi ölçütlerini karşılayan bulunan ilk satırı döndürür. `FirstOrDefaultAsync`genellikle aşağıdaki alternatiflerden daha iyi bir seçimdir:

* [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Sorgu filtresini karşılayan birden fazla varlık varsa özel durum oluşturur. Sorgu tarafından birden fazla satır Döndürülebilir olup `SingleOrDefaultAsync` olmadığını belirlemek için, birden çok satır almaya çalışır. Sorgu, benzersiz bir anahtarda arama yaptığı gibi yalnızca bir varlık döndürebiliyorsa, bu ek çalışma gereksizdir.
* [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Birincil anahtara (PK) sahip bir varlık bulur. PK'lı bir varlık bağlam tarafından izleniyorsa, veritabanına istek olmadan döndürülür. Bu yöntem tek bir varlığı aramak için en iyi `Include` duruma `FindAsync`getirilmiş, ancak '' ile arayama  Yani ilgili veriler gerekiyorsa, `FirstOrDefaultAsync` daha iyi bir seçimdir.

### <a name="route-data-vs-query-string"></a>Rota verileri ve sorgu dizesi

Ayrıntılar sayfasının URL'si `https://localhost:<port>/Students/Details?id=1`. Varlığın birincil anahtar değeri sorgu dizesindedir. Bazı geliştiriciler rota verilerindeki anahtar değeri `https://localhost:<port>/Students/Details/1`geçirmeyi tercih ediyor: . Daha fazla bilgi için [bkz.](xref:tutorials/razor-pages/da1#update-the-generated-code)

## <a name="update-the-create-page"></a>Oluştur sayfasını güncelleştirin

Oluştur sayfasının `OnPostAsync` iskele kodu [aşırıya katlamaya](#overposting)karşı savunmasızdır. `OnPostAsync` *Sayfalar/Öğrenciler/Create.cshtml.cs'deki* yöntemi aşağıdaki kodla değiştirin.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Önceki kod bir Öğrenci nesnesi oluşturur ve öğrenci nesnesinin özelliklerini güncelleştirmek için deftere nakledilen form alanlarını kullanır. [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) yöntemi:

* [PageModel'deki](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) özelliğinden gönderilen form değerlerini kullanır.
* Yalnızca listelenen özellikleri`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`güncelleştirir ( ).
* "Öğrenci" öneki olan form alanlarını arar. Örneğin, `Student.FirstMidName`. Bu vaka hassasiyeti değil.
* Form [model binding](xref:mvc/models/model-binding) değerlerini dizeleri `Student` modeldeki türlere dönüştürmek için model bağlama sistemini kullanır. Örneğin, `EnrollmentDate` DateTime dönüştürülür gerekir.

Uygulamayı çalıştırın ve Oluştur sayfasını test etmek için bir öğrenci varlığı oluşturun.

## <a name="overposting"></a>Aşırı gönderme

Alanları `TryUpdateModel` deftere nakledilen değerlerle güncelleştirmek için kullanmak, aşırı deftere nakil etmeyi önlediği için en iyi güvenlik uygulamasıdır. Örneğin, Öğrenci kuruluşunun bu `Secret` web sayfasının güncelleştirmemesi veya eklememesi gereken bir özellik içerdiğini varsayalım:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Uygulamanın Razor Page oluşturma `Secret` veya güncelleme alanında bir alanı olmasa bile, `Secret` bir bilgisayar korsanı aşırı yayılarak değeri ayarlayabilir. Bir bilgisayar korsanı, form değeri göndermek için Fiddler gibi `Secret` bir araç kullanabilir veya bazı JavaScript yazabilir. Özgün kod, model bağlayıcısının Öğrenci örneği oluştururken kullandığı alanları sınırlamaz.

`Secret` Form alanı için belirtilen bilgisayar korsanı değeri veritabanında güncelleştirilir. Aşağıdaki resimde Fiddler aracı, `Secret` deftere nakledilen form değerlerine alanı ("OverPost" değeriyle) ekleyerek gösterir.

![Fiddler Gizli alan ekleme](../ef-mvc/crud/_static/fiddler.png)

"OverPost" değeri eklenen satırın `Secret` özelliğine başarıyla eklenir. Bu durum, uygulama tasarımcısı özelliğin `Secret` Oluştur sayfasıyla ayarlanmasını hiç istememiş olsa da gerçekleşir.

### <a name="view-model"></a>Modeli görüntüle

Görünüm modelleri, aşırı göndermeyi önlemek için alternatif bir yol sağlar.

Uygulama modeli genellikle etki alanı modeli olarak adlandırılır. Etki alanı modeli genellikle veritabanında ilgili varlık tarafından gerekli tüm özellikleri içerir. Görünüm modeli yalnızca ui için kullanılan özellikleri (örneğin, Oluştur sayfası) içerir.

Görünüm modeline ek olarak, bazı uygulamalar Razor Pages sayfa modeli sınıfı ile tarayıcı arasında veri aktarmak için bağlayıcı bir model veya giriş modeli kullanır. 

Aşağıdaki `Student` görünüm modelini göz önünde bulundurun:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Aşağıdaki kod, `StudentVM` yeni bir öğrenci oluşturmak için görünüm modelini kullanır:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) yöntemi, başka bir [Özellik Değerleri](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) nesnesinden değerleri okuyarak bu nesnenin değerlerini ayarlar. `SetValues`özellik adı eşleştirme kullanır. Görünüm modeli türü model türü ile ilgili olması gerekmez, sadece eşleşen özelliklere sahip olması gerekir.

Kullanmak, `StudentVM` [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) yerine `StudentVM` kullanmak `Student`üzere güncelleştirilmelidir.

## <a name="update-the-edit-page"></a>Edit sayfasını güncelleştir

*Sayfalar/Öğrenciler/Edit.cshtml.cs*olarak, `OnGetAsync` aşağıdaki `OnPostAsync` kod ile ve yöntemleri değiştirin.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Kod değişiklikleri birkaç özel durum dışında Oluştur sayfasına benzer:

* `FirstOrDefaultAsync`[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)ile değiştirildi. İlgili verileri eklemeniz gerekmediğinde, `FindAsync` daha verimlidir.
* `OnPostAsync`bir `id` parametresi vardır.
* Geçerli öğrenci boş bir öğrenci oluşturmak yerine veritabanından getirilir.

Uygulamayı çalıştırın ve bir öğrenci oluşturarak ve düzenleyerek test edin.

## <a name="entity-states"></a>Taraf Devletleri

Veritabanı bağlamı, bellekteki varlıkların veritabanındaki karşılık gelen satırlarla eşitlenip eşitolmadığını izler. Bu izleme [bilgileri, SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında ne olacağını belirler. Örneğin, [addasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) yöntemine yeni bir varlık geçirildiğinde, bu varlığın durumu [Eklendi](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)olarak ayarlanır. Çağrıldığında, `SaveChangesAsync` veritabanı bağlamı bir SQL INSERT komutu yayınlar.

Bir varlık [aşağıdaki durumlardan](/dotnet/api/microsoft.entityframeworkcore.entitystate)birinde olabilir:

* `Added`: Varlık henüz veritabanında yok. Yöntem `SaveChanges` bir INSERT deyimi yayınlar.

* `Unchanged`: Bu varlıkla birlikte kaydedilmesi gerekmez. Bir varlık veritabanından okunduğunda bu duruma sahiptir.

* `Modified`: Varlığın özellik değerlerinin bir kısmı veya tamamı değiştirildi. Yöntem, `SaveChanges` UPDATE deyimini yayınlar.

* `Deleted`: Varlık silinmesi için işaretlenmiştir. Yöntem, `SaveChanges` DELETE deyimi ni yayınlar.

* `Detached`: Varlık veritabanı bağlamı tarafından izlenmiyor.

Bir masaüstü uygulamasında durum değişiklikleri genellikle otomatik olarak ayarlanır. Bir varlık okunur, değişiklikler yapılır ve varlık durumu otomatik `Modified`olarak ' olarak değiştirilir. Arama, `SaveChanges` yalnızca değiştirilen özellikleri güncelleştiren bir SQL UPDATE deyimi oluşturur.

Bir web uygulamasında, `DbContext` bir varlığı okur ve verileri görüntüler bir sayfa işlendikten sonra atılır. Bir sayfanın `OnPostAsync` yöntemi çağrıldığında, yeni bir web isteği yapılır `DbContext`ve yeni bir web örneği ile . Bu yeni bağlamda varlığı yeniden okumak masaüstü işleme benzetimi.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

Bu bölümde, çağrı `SaveChanges` başarısız olduğunda özel bir hata iletisi uygularsınız.

*Sayfalar/Öğrenciler/Sil.cshtml.cs'deki* kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır `using` (ifadelerin temizlenmesi dışında).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Önceki kod yöntem imzasına `saveChangesError` isteğe `OnGetAsync` bağlı parametre ekler. `saveChangesError`öğrenci nesnesinin silinmemesi üzerine yöntemin çağrılıp çağrılmadığını gösterir. Silme işlemi geçici ağ sorunları nedeniyle başarısız olabilir. Veritabanı bulutta olduğunda geçici ağ hataları daha olasıdır. `saveChangesError` Parametre, Kullanıcı Arabirimi'nden Delete sayfası `OnGetAsync` çağrıldığında yanlıştır. Tarafından `OnGetAsync` `OnPostAsync` çağrıldığında (silme işlemi başarısız `saveChangesError` olduğundan), parametre doğrudur.

Yöntem `OnPostAsync` seçili varlığı alır, sonra [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) varlığın durumunu `Deleted`'da ayarlamak için Kaldır yöntemini çağırır. Çağrıldığında, `SaveChanges` bir SQL DELETE komutu oluşturulur. Başarısız `Remove` olursa:

* Veritabanı özel durumu yakalanır.
* Sayfaları `OnGetAsync` Sil yöntemi . `saveChangesError=true`

Delete Razor Sayfasına hata iletisi ekleyin (*Sayfalar/Öğrenciler/Delete.cshtml):*

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Uygulamayı çalıştırın ve Sil sayfasını test etmek için bir öğrenciyi silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/intro)
> [Sonraki öğretici](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, iskeleye sarılmış CRUD (oluşturma, oku, güncelleme, silme) kodu gözden geçirilir ve özelleştirilmiştir.

Karmaşıklığı en aza indirmek ve bu öğreticileri EF Core'a odaklamak için sayfa modellerinde EF Core kodu kullanılır. Bazı geliştiriciler, UI (Razor Pages) ve veri erişim katmanı arasında bir soyutlama katmanı oluşturmak için bir hizmet katmanı veya depo deseni kullanır.

Bu öğreticide, *Öğrenciler* klasöründeki Oluştur, Düzenle, Sil ve Ayrıntılar Jilet Sayfaları incelenmiştir.

İskele kodu, Sayfaları Oluştur, Düzenle ve Sil için aşağıdaki deseni kullanır:

* Http GET yöntemi `OnGetAsync`ile istenen verileri alın ve görüntüleyin.
* HTTP POST yöntemi `OnPostAsync`yle verilerdeki değişiklikleri kaydedin.

Dizin ve Ayrıntılar sayfaları, istenen verileri HTTP GET yöntemiyle alır ve görüntüler`OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync vs. FirstOrDefaultAsync

Oluşturulan kod genellikle [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)üzerinde tercih edilir [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)kullanır.

 `FirstOrDefaultAsync`bir varlığı `SingleOrDefaultAsync` alma da daha verimlidir:

* Kodun sorgudan döndürülen birden fazla varlık olmadığını doğrulaması gerekmediği sürece.
* `SingleOrDefaultAsync`daha fazla veri getirir ve gereksiz işler yapar.
* `SingleOrDefaultAsync`filtre kısmına uyan birden fazla varlık varsa bir özel durum oluşturur.
* `FirstOrDefaultAsync`filtre kısmına uyan birden fazla varlık varsa atmaz.

<a name="FindAsync"></a>

### <a name="findasync"></a>FindAsync

İskele kodu çok, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) yerine `FirstOrDefaultAsync`kullanılabilir.

`FindAsync`:

* Birincil anahtara (PK) sahip bir varlık bulur. PK'lı bir varlık bağlam tarafından izleniyorsa, DB'ye istek tespül etmeden döndürülür.
* Basit ve özlü.
* Tek bir varlığı aramak için optimize edilsin.
* Bazı durumlarda perf yararları olabilir, ancak bu nadiren tipik web uygulamaları için olur.
* Implicitly [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) yerine [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)kullanır.

Ama diğer varlıklar `Include` istiyorsanız, o `FindAsync` zaman artık uygun değildir. Bu, uygulamanız ilerledikçe `FindAsync` bir sorgudan vazgeçmeniz ve bir sorguya geçmeniz gerekebileceği anlamına gelir.

## <a name="customize-the-details-page"></a>Ayrıntılar sayfasını özelleştirin

Sayfaya `Pages/Students` göz atın. **Düzenleme,** **Ayrıntılar**ve **Sil** *bağlantıları, Sayfalar/Öğrenciler/Index.cshtml* dosyasındaki [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Uygulamayı çalıştırın ve **Ayrıntılar** bağlantısını seçin. URL formu `http://localhost:5000/Students/Details?id=2`. Öğrenci Kimliği bir sorgu dizesi kullanılarak geçirilir (`?id=2`).

Rota şablonunu kullanmak için Düzenle, `"{id:int}"` Ayrıntıları ve Jilet Sayfalarını Sil'i güncelleştirin. Bu sayfaların her biri için `@page` `@page "{id:int}"`sayfa yönergesini ' den ' e değiştirin

Tamsayı rota değeri **içermeyen** "{id:int}" rota şablonuna sahip sayfaya gelen bir istek, http 404 (bulunamadı) hatası döndürür. Örneğin, `http://localhost:5000/Students/Details` bir 404 hatası döndürür. Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:

 ```cshtml
@page "{id:int?}"
```

Uygulamayı çalıştırın, Ayrıntılar bağlantısını tıklayın ve URL'nin kimlikten rota`http://localhost:5000/Students/Details/2`verisi olarak geçtiğini doğrulayın ( ).

Genel olarak ana `@page` `@page "{id:int}"`sayfaya ve sayfa oluşturmaya bağlantılar alabilme yitirmemeye.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>İlgili verileri ekleme

Öğrenci Dizini sayfasının iskele kodu `Enrollments` özelliği içermez. Bu bölümde, koleksiyonun `Enrollments` içeriği Ayrıntılar sayfasında görüntülenir.

`OnGetAsync` *Sayfalar/Öğrenciler/Details.cshtml.cs* yöntemi tek `Student` `FirstOrDefaultAsync` bir varlığı almak için bu yöntemi kullanır. Aşağıdaki vurgulanan kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

Ekle ve [Sonra Ekle](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) yöntemleri bağlamın `Student.Enrollments` gezinti özelliğini yüklemesine `Enrollment.Course` ve her kayıt içinde gezinti özelliğine neden olur. [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) Bu yöntemler okuma ile ilgili veri öğretici ayrıntılı olarak incelenir.

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) yöntemi, döndürülen varlıklar geçerli bağlamda güncelleştirildiğinde senaryolarda performansı artırır. `AsNoTracking`daha sonra bu öğretici ele alınmıştır.

### <a name="display-related-enrollments-on-the-details-page"></a>Ayrıntılar sayfasında ilgili kayıtları görüntüleme

*Sayfaları Aç/Öğrenci/Details.cshtml*. Bir kayıt listesini görüntülemek için aşağıdaki vurgulanmış kodu ekleyin:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Kod yapıştırıldıktan sonra kod girintisi yanlışsa, düzeltmek için CTRL-K-D tuşuna basın.

Önceki kod, gezinti özelliğindeki varlıklar `Enrollments` arasında döngüler. Her kayıt için ders unvanını ve notu görüntüler. Kurs unvanı, Kayıtlar kuruluşunun `Course` navigasyon özelliğinde depolanan Kurs kuruluşundan alınır.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve bir öğrenciiçin **Ayrıntılar** bağlantısını tıklatın. Seçilen öğrencinin ders ve notlistesi görüntülenir.

## <a name="update-the-create-page"></a>Oluştur sayfasını güncelleştirin

`OnPostAsync` *Sayfalar/Öğrenciler/Create.cshtml.cs'deki* yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) kodunu inceleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

Önceki `TryUpdateModelAsync<Student>` kodda, `emptyStudent` [PageModel'deki](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) özelliğinden deftere nakledilen form değerlerini kullanarak nesneyi güncelleştirmeye çalışır. `TryUpdateModelAsync`yalnızca listelenen özellikleri`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`güncelleştirir ( ).

Önceki örnekte:

* İkinci bağımsız`"student", // Prefix`değişken ( ) değerleri aramak için önek kullanır. Bu vaka hassasiyeti değil.
* Deftere nakledilen form [değerleri,](xref:mvc/models/model-binding)model `Student` bağlama kullanılarak modeldeki türlere dönüştürülür.

<a id="overpost"></a>

### <a name="overposting"></a>Aşırı gönderme

Alanları `TryUpdateModel` deftere nakledilen değerlerle güncelleştirmek için kullanmak, aşırı deftere nakil etmeyi önlediği için en iyi güvenlik uygulamasıdır. Örneğin, Öğrenci kuruluşunun bu `Secret` web sayfasının güncelleştirmemesi veya eklememesi gereken bir özellik içerdiğini varsayalım:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Uygulamanın Oluşturma/Güncelle Jilet Sayfası'nda bir `Secret` alanı olmasa bile, `Secret` bir bilgisayar korsanı aşırı yayılarak değeri ayarlayabilir. Bir bilgisayar korsanı, form değeri göndermek için Fiddler gibi `Secret` bir araç kullanabilir veya bazı JavaScript yazabilir. Özgün kod, model bağlayıcısının Öğrenci örneği oluştururken kullandığı alanları sınırlamaz.

`Secret` Form alanı için belirtilen bilgisayar korsanı değeri db'de güncelleştirilir. Aşağıdaki resimde Fiddler aracı, `Secret` deftere nakledilen form değerlerine alanı ("OverPost" değeriyle) ekleyerek gösterir.

![Fiddler Gizli alan ekleme](../ef-mvc/crud/_static/fiddler.png)

"OverPost" değeri eklenen satırın `Secret` özelliğine başarıyla eklenir. Uygulama tasarımcısı özelliğin `Secret` Oluştur sayfasıyla ayarlanmasını asla amaçlamadınız.

<a name="vm"></a>

### <a name="view-model"></a>Modeli görüntüle

Görünüm modeli genellikle uygulama tarafından kullanılan modele dahil özellikleribir alt kümesi içerir. Uygulama modeli genellikle etki alanı modeli olarak adlandırılır. Etki alanı modeli genellikle DB'deki ilgili varlığın gerektirdiği tüm özellikleri içerir. Görünüm modeli yalnızca UI katmanı için gereken özellikleri (örneğin, Oluştur sayfası) içerir. Görünüm modeline ek olarak, bazı uygulamalar Razor Pages sayfa modeli sınıfı ile tarayıcı arasında veri aktarmak için bağlayıcı bir model veya giriş modeli kullanır. Aşağıdaki `Student` görünüm modelini göz önünde bulundurun:

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Görünüm modelleri, aşırı göndermeyi önlemek için alternatif bir yol sağlar. Görünüm modeli yalnızca görüntülenecek (görüntülenecek) veya güncellenecek özellikleri içerir.

Aşağıdaki kod, `StudentVM` yeni bir öğrenci oluşturmak için görünüm modelini kullanır:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) yöntemi, başka bir [Özellik Değerleri](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) nesnesinden değerleri okuyarak bu nesnenin değerlerini ayarlar. `SetValues`özellik adı eşleştirme kullanır. Görünüm modeli türü model türü ile ilgili olması gerekmez, sadece eşleşen özelliklere sahip olması gerekir.

Using `StudentVM` [createVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) yerine `Student`kullanmak `StudentVM` için güncelleştirilmelidir.

Razor Pages'de `PageModel` türetilmiş sınıf görünüm modelidir.

## <a name="update-the-edit-page"></a>Edit sayfasını güncelleştir

Düzenleme sayfasının sayfa modelini güncelleştirin. Önemli değişiklikler vurgulanır:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Kod değişiklikleri birkaç özel durum dışında Oluştur sayfasına benzer:

* `OnPostAsync`isteğe `id` bağlı bir parametreye sahiptir.
* Mevcut öğrenci boş bir öğrenci oluşturmak yerine DB'den getirilir.
* `FirstOrDefaultAsync`[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)ile değiştirildi. `FindAsync`birincil anahtardan bir varlık seçerken iyi bir seçimdir. Daha fazla bilgi için [FindAsync'e](#FindAsync) bakın.

### <a name="test-the-edit-and-create-pages"></a>Sayfaları Daha Iyi Yap ve Oluştur'da Test Et

Birkaç öğrenci varlığı oluşturun ve edinin.

## <a name="entity-states"></a>Taraf Devletleri

DB bağlamı, bellekteki varlıkların DB'deki karşılık gelen satırlarıyla eşitlenip eşit olmadığını izler. DB bağlam eşitleme bilgileri [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında ne olacağını belirler. Örneğin, [addasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) yöntemine yeni bir varlık geçirildiğinde, bu varlığın durumu [Eklendi](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)olarak ayarlanır. Çağrıldığında, `SaveChangesAsync` DB bağlamı bir SQL INSERT komutu yayınlar.

Bir varlık [aşağıdaki durumlardan](/dotnet/api/microsoft.entityframeworkcore.entitystate)birinde olabilir:

* `Added`: Varlık henüz DB'de yok. Yöntem `SaveChanges` bir INSERT deyimi yayınlar.

* `Unchanged`: Bu varlıkla birlikte kaydedilmesi gerekmez. DB'den okunduğunda bir varlık bu duruma sahiptir.

* `Modified`: Varlığın özellik değerlerinin bir kısmı veya tamamı değiştirildi. Yöntem, `SaveChanges` UPDATE deyimini yayınlar.

* `Deleted`: Varlık silinmesi için işaretlenmiştir. Yöntem, `SaveChanges` DELETE deyimi ni yayınlar.

* `Detached`: Varlık DB bağlamı tarafından izlenmiyor.

Bir masaüstü uygulamasında durum değişiklikleri genellikle otomatik olarak ayarlanır. Bir varlık okunur, değişiklikler yapılır ve varlık durumu otomatik `Modified`olarak ' olarak değiştirilir. Arama, `SaveChanges` yalnızca değiştirilen özellikleri güncelleştiren bir SQL UPDATE deyimi oluşturur.

Bir web uygulamasında, `DbContext` bir varlığı okur ve verileri görüntüler bir sayfa işlendikten sonra atılır. Bir sayfanın `OnPostAsync` yöntemi çağrıldığında, yeni bir web isteği yapılır `DbContext`ve yeni bir web örneği ile . Varlığı bu yeni bağlamda yeniden okumak masaüstü işlemeyi simüle eder.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

Bu bölümde, arama başarısız olduğunda özel bir hata `SaveChanges` iletisi uygulamak için kod eklenir. Olası hata iletilerini içerecek bir dize ekleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

`OnGetAsync` yöntemini aşağıdaki kod ile değiştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Önceki kod isteğe bağlı `saveChangesError`parametreyi içerir. `saveChangesError`öğrenci nesnesinin silinmemesi üzerine yöntemin çağrılıp çağrılmadığını gösterir. Silme işlemi geçici ağ sorunları nedeniyle başarısız olabilir. Bulutta geçici ağ hataları daha olasıdır. `saveChangesError`Kullanıcı Arabirimi'nden `OnGetAsync` Delete sayfası çağrıldığında yanlıştır. Tarafından `OnGetAsync` `OnPostAsync` çağrıldığında (silme işlemi başarısız `saveChangesError` olduğundan), parametre doğrudur.

### <a name="the-delete-pages-onpostasync-method"></a>Sayfaları Sil OnPostAsync yöntemi

Aşağıdaki `OnPostAsync` kodu değiştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Önceki kod seçili varlığı alır, sonra [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) varlığın durumunu `Deleted`'da ayarlamak için Kaldır yöntemini çağırır. Çağrıldığında, `SaveChanges` bir SQL DELETE komutu oluşturulur. Başarısız `Remove` olursa:

* DB özel durum yakalanır.
* Sayfaları `OnGetAsync` Sil yöntemi . `saveChangesError=true`

### <a name="update-the-delete-razor-page"></a>Sil Jilet Sayfasını Güncelleştir

Jilet Sil Sayfasına aşağıdaki vurgulanan hata iletisini ekleyin.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Sil'i Test Edin.

## <a name="common-errors"></a>Sık karşılaşılan hatalar

Öğrenciler/Dizini veya diğer bağlantılar çalışmıyor:

Razor Page'in doğru `@page` yönergeleri içerdiğini doğrulayın. Örneğin, Öğrenciler/Dizin Jilet Sayfası bir rota şablonu **içermemelidir:**

```cshtml
@page "{id:int}"
```

Her Razor Page `@page` yönergeyi içermelidir.



## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/intro)
> [Sonraki](xref:data/ef-rp/sort-filter-page)

::: moniker-end
