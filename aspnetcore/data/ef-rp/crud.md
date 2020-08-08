---
title: Bölüm 2, Razor ASP.NET Core EF Core olan sayfalar-CRUD
author: rick-anderson
description: RazorSayfaların 2. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.date: 07/22/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/crud
ms.openlocfilehash: f205e7741c8e901e9219bec2028c7bee98129161
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018370"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a>Bölüm 2, Razor ASP.NET Core EF Core olan sayfalar-CRUD

, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, scafkatan CRUD (oluşturma, okuma, güncelleştirme, silme) kodu incelenir ve özelleştirilir.

## <a name="no-repository"></a>Depo yok

Bazı geliştiriciler, Kullanıcı arabirimi ( Razor Sayfalar) ve veri erişim katmanı arasında bir soyutlama katmanı oluşturmak için bir hizmet katmanı veya depo deseninin kullanılmasını sağlar. Bu öğretici bunu yapmaz. Karmaşıklığı en aza indirmek ve öğreticiyi EF Core odaklanmasını sağlamak için EF Core kodu doğrudan sayfa modeli sınıflarına eklenir. 

## <a name="update-the-details-page"></a>Ayrıntılar sayfasını Güncelleştir

Öğrenciler sayfaları için yapı iskelesi kodu kayıt verilerini içermez. Bu bölümde, ayrıntılar sayfasına kayıtları eklersiniz.

### <a name="read-enrollments"></a>Kayıtları oku

Sayfada öğrenciye ait kayıt verilerini göstermek için, onu okumanız gerekir. *Pages/öğrenciler/details. cshtml. cs* içindeki scafkatlama kodu, kayıt verileri olmadan yalnızca öğrenci verilerini okur:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

Yöntemi, `OnGetAsync` Seçili öğrenci için kayıt verilerini okumak üzere aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) ve [thenınclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) yöntemleri, içeriğin `Student.Enrollments` gezinti özelliğini yüklemesine ve her kaydın gezinti özelliği içine olmasına neden olur `Enrollment.Course` . Bu yöntemler, [okuma ilgili verileri](xref:data/ef-rp/read-related-data) öğreticisinde ayrıntılı olarak incelendi.

[Asnotracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) yöntemi, döndürülen varlıkların geçerli bağlamda güncelleştirilmediği senaryolarda performansı geliştirir. `AsNoTracking`Bu öğreticinin ilerleyen kısımlarında ele alınmıştır.

### <a name="display-enrollments"></a>Kayıtları görüntüle

*Sayfalar/öğrenciler/details. cshtml* içindeki kodu aşağıdaki kodla değiştirin ve kayıtlar listesini görüntüleyin. Değişiklikler vurgulanır.

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

Yukarıdaki kod, Gezinti özelliğindeki varlıklar aracılığıyla döngü başlatır `Enrollments` . Her kayıt için kurs başlığını ve sınıfı görüntüler. Kurs başlığı, kayıt varlığının gezinti özelliğinde depolanan kurs varlığından alınır `Course` .

Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin ve bir öğrenci için **Ayrıntılar** bağlantısına tıklayın. Seçili öğrenci için Kurslar ve notlar listesi görüntülenir.

### <a name="ways-to-read-one-entity"></a>Bir varlığı okuma yolları

Oluşturulan kod, bir varlığı okumak için [Firstordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) kullanır. Bu yöntem, hiçbir şey bulunamazsa null değerini döndürür; Aksi takdirde, sorgu filtresi ölçütlerine uyan bulunan ilk satırı döndürür. `FirstOrDefaultAsync`genellikle aşağıdaki alternatiflere göre daha iyi bir seçimdir:

* [Singleordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) -sorgu filtresini karşılayan birden fazla varlık varsa bir özel durum oluşturur. Sorgu tarafından birden fazla satır döndürülüp döndürülmeyeceğini anlamak için `SingleOrDefaultAsync` birden çok satır getirmeyi dener. Sorgu yalnızca bir varlık döndürebiliyorsanız ve benzersiz bir anahtarda arama yaptığında bu ek çalışma gereksizdir.
* [Findadsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) -birincil ANAHTARLA (PK) bir varlık bulur. PK 'ye sahip bir varlık bağlam tarafından izleniyorsa, veritabanına bir istek olmadan döndürülür. Bu yöntem tek bir varlık aramak için en iyi duruma getirilmiştir, ancak ile çağrılamaz `Include` `FindAsync` .  Bu nedenle, ilgili veriler gerekliyse, `FirstOrDefaultAsync` daha iyi bir seçimdir.

### <a name="route-data-vs-query-string"></a>Veri yönlendirme ve sorgu dizesi karşılaştırması

Ayrıntılar sayfasının URL 'SI `https://localhost:<port>/Students/Details?id=1` . Varlığın birincil anahtar değeri, sorgu dizesinde bulunur. Bazı geliştiriciler anahtar değerini rota verilerinde geçirmeye tercih eder: `https://localhost:<port>/Students/Details/1` . Daha fazla bilgi için bkz. [oluşturulan kodu güncelleştirme](xref:tutorials/razor-pages/da1#update-the-generated-code).

## <a name="update-the-create-page"></a>Oluştur sayfasını Güncelleştir

`OnPostAsync`Oluşturma sayfası için yapı iskelesi kodu, [aşırı nakme](#overposting)açıktır. `OnPostAsync` *Pages/öğrenciler/Create. cshtml. cs* içindeki yöntemi aşağıdaki kodla değiştirin.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

Yukarıdaki kod bir öğrenci nesnesi oluşturur ve ardından, öğrenci nesnesinin özelliklerini güncelleştirmek için, postalanan form alanlarını kullanır. [Tryupdatemodelasync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) yöntemi:

* [Pagemodel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)Içindeki [pagecontext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) özelliğinden gönderilen form değerlerini kullanır.
* Yalnızca listelenen () özellikleri güncelleştirir `s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate` .
* "Öğrenci" ön ekine sahip form alanlarını arar. Örneğin, `Student.FirstMidName`. Büyük/küçük harfe duyarlı değildir.
* , Dizelerdeki form değerlerini modeldeki türlere dönüştürmek için [model bağlama](xref:mvc/models/model-binding) sistemini kullanır `Student` . Örneğin, `EnrollmentDate` DateTime 'a dönüştürülmesi gerekir.

Uygulamayı çalıştırın ve oluştur sayfasını test etmek için bir öğrenci varlığı oluşturun.

## <a name="overposting"></a>Fazla nakil

`TryUpdateModel`Deftere nakledilen değerler içeren alanları güncelleştirmek için kullanmak, aşırı nakletmeyi önlediği için en iyi güvenlik yöntemidir. Örneğin, öğrenci varlığının `Secret` Bu Web sayfasının güncelleştirmesi veya eklemesi gereken bir özelliği içerdiğini varsayalım:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Uygulamanın, `Secret` Oluştur veya Güncelleştir sayfasında bir alanı olmasa bile Razor , bir korsan `Secret` değeri aşırı nakme ile ayarlayabilir. Bir korsan, Fiddler gibi bir araç kullanabilir veya bir form değeri göndermek için bazı JavaScript yazabilir `Secret` . Özgün kod, bir öğrenci örneği oluştururken model cildin kullandığı alanları sınırlamaz.

Form alanı için belirtilen korsanın hangi değeri `Secret` veritabanında güncelleştirildiği. Aşağıdaki görüntüde, `Secret` alanı ("OverPost" değeri ile), postalanan form değerlerine ekleyen Fiddler aracı gösterilmektedir.

![Fiddler gizli alanı ekleniyor](../ef-mvc/crud/_static/fiddler.png)

"OverPost" değeri `Secret` eklenen satırın özelliğine başarıyla eklendi. Bu durum, Uygulama Tasarımcısı hiçbir `Secret` şekilde özelliği oluştur sayfasıyla ayarlamaya yönelik değildir.

### <a name="view-model"></a>Modeli görüntüleme

Model görüntüleme, fazla nakletmeyi önlemenin alternatif bir yolunu sağlar.

Uygulama modeli genellikle etki alanı modeli olarak adlandırılır. Etki alanı modeli genellikle veritabanında ilgili varlık için gereken tüm özellikleri içerir. Görünüm modeli yalnızca için kullanılan Kullanıcı arabirimi için gereken özellikleri içerir (örneğin, oluşturma sayfası).

Görünüm modeline ek olarak, bazı uygulamalar sayfa Razor modeli sınıfı ve tarayıcı arasında veri geçirmek için bir bağlama modeli veya giriş modeli kullanır. 

Aşağıdaki görünüm modelini göz önünde bulundurun `Student` :

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

Aşağıdaki kod, `StudentVM` Yeni bir öğrenci oluşturmak için görünüm modelini kullanır:

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) yöntemi, başka bir [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) nesnesinden değerleri okuyarak bu nesnenin değerlerini ayarlar. `SetValues`Özellik adı eşleştirme kullanır. Görünüm modeli türünün model türüyle ilgili olması gerekmez, yalnızca eşleşen özellikleri olması gerekir.

Kullanılması `StudentVM` Için [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) kullanılması gerekir `StudentVM` `Student` .

## <a name="update-the-edit-page"></a>Düzenleme sayfasını Güncelleştir

*Sayfalar/öğrenciler/Edit. cshtml. cs*' de, `OnGetAsync` ve `OnPostAsync` yöntemlerini aşağıdaki kodla değiştirin.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

Kod değişiklikleri, birkaç özel durum dışında oluşturma sayfasına benzerdir:

* `FirstOrDefaultAsync`, [Findadsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)ile değiştirilmiştir. İlgili verileri dahil etmeniz gerekmiyorsa, `FindAsync` daha etkilidir.
* `OnPostAsync`bir `id` parametreye sahiptir.
* Geçerli öğrenci boş bir öğrenci oluşturmak yerine veritabanından getirilir.

Uygulamayı çalıştırın ve bir öğrenci oluşturup düzenleyerek test edin.

## <a name="entity-states"></a>Varlık durumları

Veritabanı bağlamı, bellekteki varlıkların veritabanında karşılık gelen satırlarıyla eşitlenmiş olup olmadığını izler. Bu izleme bilgileri, [Savechangesasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında ne olacağını belirler. Örneğin, yeni bir varlık [Addadsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) yöntemine geçirildiğinde, bu varlığın durumu [eklendi](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)olarak ayarlanır. `SaveChangesAsync`Çağrıldığında, veritabanı bağlamı BIR SQL INSERT komutu yayınlar.

Bir varlık [aşağıdaki durumlardan](/dotnet/api/microsoft.entityframeworkcore.entitystate)birinde olabilir:

* `Added`: Varlık veritabanında henüz yok. `SaveChanges`Yöntemi BIR INSERT ifadesini yayınlar.

* `Unchanged`: Bu varlıkla birlikte hiçbir değişiklik kaydedilmesi gerekmiyor. Bir varlık veritabanından okurken bu durumu içerir.

* `Modified`: Varlığın özellik değerlerinin bazıları veya tümü değiştirildi. `SaveChanges`Yöntemi BIR Update ifadesini yayınlar.

* `Deleted`: Varlık silinmek üzere işaretlendi. `SaveChanges`Yöntemi BIR DELETE ifadesini yayınlar.

* `Detached`: Varlık veritabanı bağlamı tarafından izlenmiyor.

Bir masaüstü uygulamasında durum değişiklikleri genellikle otomatik olarak ayarlanır. Bir varlık okundu, değişiklikler yapılır ve varlık durumu otomatik olarak olarak değiştirilir `Modified` . Çağıran `SaveChanges` yalnızca değiştirilen özellikleri güncelleştiren BIR SQL Update bildirisi oluşturur.

Bir Web uygulamasında, bir `DbContext` varlığı okur ve bir sayfa işlendikten sonra verileri görüntüler. Bir sayfanın `OnPostAsync` yöntemi çağrıldığında, yeni bir Web isteği oluşturulur ve yeni bir örneğine sahiptir `DbContext` . Okuyarak bu yeni bağlamdaki varlığı, masaüstü işlemesini benzetir.

## <a name="update-the-delete-page"></a>Silme sayfasını Güncelleştir

Bu bölümde, çağrısı başarısız olduğunda özel bir hata iletisi uygulayacağınızı görürsünüz `SaveChanges` .

*Pages/öğrenciler/delete. cshtml. cs* dosyasındaki kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır (deyimler temizliği dışında `using` ).

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

Yukarıdaki kod, isteğe bağlı parametresini `saveChangesError` `OnGetAsync` Yöntem imzasına ekler. `saveChangesError`öğrenci nesnesini silme hatasından sonra yöntemin çağrılıp çağrılmadığını gösterir. Geçici ağ sorunları nedeniyle silme işlemi başarısız olabilir. Geçici ağ hataları, veritabanı bulutta olduğunda daha olasıdır. `saveChangesError`Sil sayfası kullanıcı arabiriminden çağrıldığında parametre false 'tur `OnGetAsync` . `OnGetAsync`Tarafından çağrıldığında `OnPostAsync` (silme işlemi başarısız olduğundan), `saveChangesError` parametresi true olur.

`OnPostAsync`Yöntemi seçili varlığı alır, ardından varlığın durumunu olarak ayarlamak Için [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) yöntemini çağırır `Deleted` . `SaveChanges`Çağrıldığında, BIR SQL DELETE komutu oluşturulur. `Remove`Başarısız olursa:

* Veritabanı özel durumu yakalandı.
* Sayfaları Sil `OnGetAsync` yöntemi ile çağırılır `saveChangesError=true` .

Silme sayfasına bir hata iletisi ekleyin Razor (*Sayfalar/öğrenciler/delete. cshtml*):

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

Uygulamayı çalıştırın ve Sil sayfasını test etmek için bir öğrenci silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/intro) 
>  [Sonraki öğretici](xref:data/ef-rp/sort-filter-page)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, scafkatan CRUD (oluşturma, okuma, güncelleştirme, silme) kodu incelenir ve özelleştirilir.

Karmaşıklığı en aza indirmek ve bu öğreticilerin EF Core odaklanmasını sağlamak için, EF Core kod sayfa modellerinde kullanılır. Bazı geliştiriciler, Kullanıcı arabirimi ( Razor Sayfalar) ve veri erişim katmanı arasında bir soyutlama katmanı oluşturmak için ' de bir hizmet katmanı veya depo deseninin kullanılmasını sağlar.

Bu öğreticide, öğrenciler klasöründeki oluştur, Düzenle, Sil ve Ayrıntılar Razor sayfaları incelenir. *Students*

Scafkatlanmış kod, sayfa oluşturma, düzenleme ve silme için aşağıdaki kalıbı kullanır:

* HTTP GET yöntemiyle istenen verileri alın ve görüntüleyin `OnGetAsync` .
* HTTP POST yöntemiyle verilerde yapılan değişiklikleri kaydedin `OnPostAsync` .

Dizin ve ayrıntı sayfaları, HTTP GET yöntemiyle istenen verileri alır ve görüntüler`OnGetAsync`

## <a name="singleordefaultasync-vs-firstordefaultasync"></a>SingleOrDefaultAsync ile FirstOrDefaultAsync

Oluşturulan kod, genellikle [Singleordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)üzerinden tercih edilen [firstordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)kullanır.

 `FirstOrDefaultAsync`, `SingleOrDefaultAsync` bir varlık getirenden daha verimlidir:

* Kodun sorgudan döndürülen birden fazla varlık olmadığını doğrulaması gerekmiyorsa.
* `SingleOrDefaultAsync`daha fazla veri getirir ve gereksiz işler.
* `SingleOrDefaultAsync`Filtre bölümüne uyan birden fazla varlık varsa bir özel durum oluşturur.
* `FirstOrDefaultAsync`Filtre bölümüne uyan birden fazla varlık varsa oluşturmaz.

<a name="FindAsync"></a>

### <a name="findasync"></a>Findadsync

Yapı iskelesi kodunun büyük bir kısmında, yerine [Findadsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) kullanılabilir `FirstOrDefaultAsync` .

`FindAsync`:

* Birincil anahtarla (PK) bir varlık bulur. PK 'ye sahip bir varlık bağlam tarafından izleniyorsa, VERITABANıNA bir istek olmadan döndürülür.
* Basittir ve kısadır.
* Tek bir varlığı aramak için iyileştirilmiştir.
* Bazı durumlarda performans avantajlarına sahip olabilir, ancak tipik Web uygulamaları için nadiren meydana gelir.
* , [Maleasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)yerine dolaylı olarak [firstasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) kullanır.

Ancak `Include` başka varlıklar istiyorsanız `FindAsync` artık uygun değildir. Bu, `FindAsync` uygulamanız ilerledikçe bir sorguyu iptal etmeniz ve bir sorguya taşımanız gerekebileceği anlamına gelir.

## <a name="customize-the-details-page"></a>Ayrıntılar sayfasını özelleştirme

Sayfaya gidin `Pages/Students` . **Düzenle**, **Ayrıntılar**ve **Sil** bağlantıları, *Sayfalar/öğrenciler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

Uygulamayı çalıştırın ve bir **Ayrıntılar** bağlantısı seçin. URL, formundadır `http://localhost:5000/Students/Details?id=2` . Öğrenci KIMLIĞI bir sorgu dizesi () kullanılarak geçirilir `?id=2` .

RazorYol şablonunu kullanmak Için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin `"{id:int}"` . Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` .

Bir tamsayı yol **değeri içermeyen "** {id: Int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor. Örneğin, `http://localhost:5000/Students/Details` 404 hatası döndürür. KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:

 ```cshtml
@page "{id:int?}"
```

Uygulamayı çalıştırın, Ayrıntılar bağlantısına tıklayın ve URL 'nin KIMLIĞI yönlendirme verileri () olarak geçirdiğini doğrulayın `http://localhost:5000/Students/Details/2` .

' I genel olarak değiştirmeyin `@page` `@page "{id:int}"` , bunu yaparak giriş bağlantılarını keser ve sayfa oluşturabilirsiniz.

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a>İlgili verileri ekleme

Öğrenciler dizin sayfasının yapı iskelesi kodu `Enrollments` özelliği içermez. Bu bölümde, `Enrollments` koleksiyonun Içeriği ayrıntılar sayfasında görüntülenir.

`OnGetAsync` *Pages/öğrenciler/details. cshtml. cs* yöntemi, `FirstOrDefaultAsync` tek bir varlığı almak için yöntemini kullanır `Student` . Aşağıdaki vurgulanmış kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) ve [thenınclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) yöntemleri, içeriğin `Student.Enrollments` gezinti özelliğini yüklemesine ve her kaydın gezinti özelliği içine olmasına neden olur `Enrollment.Course` . Bu yöntemler, okuma ile ilgili veri öğreticisinde ayrıntılı olarak incelenmelidir.

[Asnotracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) yöntemi, döndürülen varlıkların geçerli bağlamda güncelleştirilmediği durumlarda, senaryolarda performansı geliştirir. `AsNoTracking`Bu öğreticinin ilerleyen kısımlarında ele alınmıştır.

### <a name="display-related-enrollments-on-the-details-page"></a>Ayrıntılar sayfasında ilgili kayıtları görüntüleme

*Sayfaları/öğrencileri/ayrıntıları. cshtml*'yi açın. Kayıtlar listesini göstermek için aşağıdaki vurgulanmış kodu ekleyin:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

Kod yapıştırıldıktan sonra kod girintisi yanlışsa, düzeltmek için CTRL-K-D ' a basın.

Yukarıdaki kod, Gezinti özelliğindeki varlıklar aracılığıyla döngü başlatır `Enrollments` . Her kayıt için kurs başlığını ve sınıfı görüntüler. Kurs başlığı, kayıt varlığının gezinti özelliğinde depolanan kurs varlığından alınır `Course` .

Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin ve bir öğrenci için **Ayrıntılar** bağlantısına tıklayın. Seçili öğrenci için Kurslar ve notlar listesi görüntülenir.

## <a name="update-the-create-page"></a>Oluştur sayfasını Güncelleştir

`OnPostAsync` *Pages/öğrenciler/Create. cshtml. cs* dosyasındaki yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a>TryUpdateModelAsync

[Tryupdatemodelasync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) kodunu inceleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

Önceki kodda, `TryUpdateModelAsync<Student>` `emptyStudent` [Pagemodel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel)içindeki [pagecontext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) özelliğinden gönderilen form değerlerini kullanarak nesneyi güncelleştirmeye çalışır. `TryUpdateModelAsync`yalnızca listelenen () özellikleri güncelleştirir `s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate` .

Yukarıdaki örnekte:

* İkinci bağımsız değişken ( `"student", // Prefix` ), ön ek değerleri aramak için kullanılır. Büyük/küçük harfe duyarlı değildir.
* Postalanan form değerleri model `Student` [bağlama](xref:mvc/models/model-binding)kullanılarak modeldeki türlere dönüştürülür.

<a id="overpost"></a>

### <a name="overposting"></a>Fazla nakil

`TryUpdateModel`Deftere nakledilen değerler içeren alanları güncelleştirmek için kullanmak, aşırı nakletmeyi önlediği için en iyi güvenlik yöntemidir. Örneğin, öğrenci varlığının `Secret` Bu Web sayfasının güncelleştirmesi veya eklemesi gereken bir özelliği içerdiğini varsayalım:

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

Uygulamanın `Secret` Oluştur/Güncelleştir sayfasında bir alanı olmasa bile Razor , bir korsan `Secret` değeri fazla nakme ile ayarlayabilir. Bir korsan, Fiddler gibi bir araç kullanabilir veya bir form değeri göndermek için bazı JavaScript yazabilir `Secret` . Özgün kod, bir öğrenci örneği oluştururken model cildin kullandığı alanları sınırlamaz.

Form alanı için belirtilen korsanın hangi değeri `Secret` veritabanında güncelleştirildiği. Aşağıdaki görüntüde, `Secret` alanı ("OverPost" değeri ile), postalanan form değerlerine ekleyen Fiddler aracı gösterilmektedir.

![Fiddler gizli alanı ekleniyor](../ef-mvc/crud/_static/fiddler.png)

"OverPost" değeri `Secret` eklenen satırın özelliğine başarıyla eklendi. Uygulama Tasarımcısı hiçbir `Secret` şekilde hiçbir özelliği oluştur sayfasıyla ayarlamaya yönelik değildir.

<a name="vm"></a>

### <a name="view-model"></a>Modeli görüntüleme

Bir görünüm modeli, genellikle uygulama tarafından kullanılan modelde bulunan özelliklerin bir alt kümesini içerir. Uygulama modeli genellikle etki alanı modeli olarak adlandırılır. Etki alanı modeli genellikle VERITABANıNDAKI karşılık gelen varlık için gereken tüm özellikleri içerir. Görünüm modeli yalnızca UI katmanı için gereken özellikleri içerir (örneğin, Oluştur sayfası). Görünüm modeline ek olarak, bazı uygulamalar sayfa Razor modeli sınıfı ve tarayıcı arasında veri geçirmek için bir bağlama modeli veya giriş modeli kullanır. Aşağıdaki görünüm modelini göz önünde bulundurun `Student` :

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

Model görüntüleme, fazla nakletmeyi önlemenin alternatif bir yolunu sağlar. Görünüm modeli yalnızca görüntüleme (görüntüleme) veya güncelleştirme özelliklerini içerir.

Aşağıdaki kod, `StudentVM` Yeni bir öğrenci oluşturmak için görünüm modelini kullanır:

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) yöntemi, başka bir [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) nesnesinden değerleri okuyarak bu nesnenin değerlerini ayarlar. `SetValues`Özellik adı eşleştirme kullanır. Görünüm modeli türünün model türüyle ilgili olması gerekmez, yalnızca eşleşen özellikleri olması gerekir.

Kullanmak `StudentVM` Için [createvm gerekir. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) , yerine kullanılmak üzere güncelleştirilir `StudentVM` `Student` .

RazorSayfalarda, `PageModel` türetilmiş sınıf görünüm modelidir.

## <a name="update-the-edit-page"></a>Düzenleme sayfasını Güncelleştir

Düzenleme sayfasının sayfa modelini güncelleştirin. Büyük değişiklikler vurgulanır:

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

Kod değişiklikleri, birkaç özel durum dışında oluşturma sayfasına benzerdir:

* `OnPostAsync`isteğe bağlı bir `id` parametreye sahiptir.
* Geçerli öğrenci boş bir öğrenci oluşturmak yerine DB 'den getirilir.
* `FirstOrDefaultAsync`, [Findadsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)ile değiştirilmiştir. `FindAsync`birincil anahtardan bir varlık seçerken iyi bir seçimdir. Daha fazla bilgi için bkz. [Findadsync](#FindAsync) .

### <a name="test-the-edit-and-create-pages"></a>Düzenleme ve oluşturma sayfalarını test etme

Birkaç öğrenci varlığı oluşturun ve düzenleyin.

## <a name="entity-states"></a>Varlık durumları

DB bağlamı, bellekteki varlıkların VERITABANıNDA karşılık gelen satırlarıyla eşitlenmiş olup olmadığını izler. VERITABANı bağlamı eşitleme bilgileri, [Savechangesasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında ne olacağını belirler. Örneğin, yeni bir varlık [Addadsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) yöntemine geçirildiğinde, bu varlığın durumu [eklendi](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)olarak ayarlanır. `SaveChangesAsync`Çağrıldığında, DB bağlamı BIR SQL INSERT komutu yayınlar.

Bir varlık [aşağıdaki durumlardan](/dotnet/api/microsoft.entityframeworkcore.entitystate)birinde olabilir:

* `Added`: Varlık VERITABANıNDA henüz yok. `SaveChanges`Yöntemi BIR INSERT ifadesini yayınlar.

* `Unchanged`: Bu varlıkla birlikte hiçbir değişiklik kaydedilmesi gerekmiyor. Bir varlık, DB 'den okurken bu duruma sahip olur.

* `Modified`: Varlığın özellik değerlerinin bazıları veya tümü değiştirildi. `SaveChanges`Yöntemi BIR Update ifadesini yayınlar.

* `Deleted`: Varlık silinmek üzere işaretlendi. `SaveChanges`Yöntemi BIR DELETE ifadesini yayınlar.

* `Detached`: Varlık DB bağlamı tarafından izlenmiyor.

Bir masaüstü uygulamasında durum değişiklikleri genellikle otomatik olarak ayarlanır. Bir varlık okundu, değişiklikler yapılır ve varlık durumu otomatik olarak olarak değiştirilecek `Modified` . Çağıran `SaveChanges` yalnızca değiştirilen özellikleri güncelleştiren BIR SQL Update bildirisi oluşturur.

Bir Web uygulamasında, bir `DbContext` varlığı okur ve bir sayfa işlendikten sonra verileri görüntüler. Bir sayfanın `OnPostAsync` yöntemi çağrıldığında, yeni bir Web isteği oluşturulur ve yeni bir örneğine sahiptir `DbContext` . Yeni bağlamdaki varlığı yeniden okumak masaüstü işlemesini benzetir.

## <a name="update-the-delete-page"></a>Silme sayfasını Güncelleştir

Bu bölümde, çağrı başarısız olduğunda özel bir hata iletisi uygulamak için kod eklenir `SaveChanges` . Olası hata iletilerini içeren bir dize ekleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

`OnGetAsync` yöntemini aşağıdaki kod ile değiştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

Önceki kod isteğe bağlı parametresini içerir `saveChangesError` . `saveChangesError`öğrenci nesnesini silme hatasından sonra yöntemin çağrılıp çağrılmadığını gösterir. Geçici ağ sorunları nedeniyle silme işlemi başarısız olabilir. Geçici ağ hataları, bulutta daha olasıdır. `saveChangesError`, silme sayfası `OnGetAsync` kullanıcı arabiriminden çağrıldığında false 'tur. `OnGetAsync`Tarafından çağrıldığında `OnPostAsync` (silme işlemi başarısız olduğundan), `saveChangesError` parametresi true olur.

### <a name="the-delete-pages-onpostasync-method"></a>Sayfaları sil OnPostAsync yöntemi

Öğesini `OnPostAsync` aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

Önceki kod seçili varlığı alır, ardından varlığın durumunu olarak ayarlamak için [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) yöntemini çağırır `Deleted` . `SaveChanges`Çağrıldığında, BIR SQL DELETE komutu oluşturulur. `Remove`Başarısız olursa:

* DB özel durumu yakalandı.
* Sayfaları Sil `OnGetAsync` yöntemi ile çağırılır `saveChangesError=true` .

### <a name="update-the-delete-no-locrazor-page"></a>Silme sayfasını Güncelleştir Razor

Aşağıdaki Vurgulanan hata iletisini silme Razor sayfasına ekleyin.
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

Test silme.

## <a name="common-errors"></a>Sık karşılaşılan hatalar

Öğrenciler/dizin veya diğer bağlantılar çalışmaz:

RazorSayfanın doğru yönergeyi içerdiğini doğrulayın `@page` . Örneğin, öğrenciler/Dizin Razor sayfası bir yol şablonu **not** içermemelidir:

```cshtml
@page "{id:int}"
```

Her Razor sayfa `@page` yönergesini içermelidir.



## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/intro) 
>  [Sonraki](xref:data/ef-rp/sort-filter-page)

::: moniker-end
