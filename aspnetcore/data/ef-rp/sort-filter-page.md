---
title: Bölüm 3, Razor ASP.NET Core sıralama, filtreleme, sayfalama EF Core olan sayfalar
author: rick-anderson
description: RazorSayfaların 3. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 496221bb4e34e1f9e4177d1934786a77d8c9b411
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212541"
---
# <a name="part-3-razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging"></a>Bölüm 3, Razor ASP.NET Core sıralama, filtreleme, sayfalama EF Core olan sayfalar

By [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, öğrenciler sayfalarına sıralama, filtreleme ve sayfalama işlevselliği ekler.

Aşağıdaki çizimde tamamlanmış bir sayfa gösterilmektedir. Sütun başlıkları sütunu sıralamak için tıklatılabilir bağlantılardır. Artan ve azalan sıralama düzeni arasında geçiş yapmak için bir sütun başlığına tekrar tekrar tıklayın.

![Öğrenciler Dizin sayfası](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Sıralama Ekle

*Pages/öğrenciler/Index. cshtml. cs* içindeki kodu, sıralama eklemek için aşağıdaki kodla değiştirin.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

Yukarıdaki kod:

* Sıralama parametrelerini içeren özellikleri ekler.
* `Student`Özelliğin adını olarak değiştirir `Students` .
* Yöntemindeki kodu değiştirir `OnGetAsync` .

`OnGetAsync`Yöntemi, `sortOrder` URL 'deki sorgu dizesinden bir parametre alır. URL (sorgu dizesi dahil), [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)tarafından oluşturulur.

`sortOrder`Parametre "ad" ya da "Tarih" dır. `sortOrder`Parametre, isteğe bağlı olarak azalan sıra belirtmek için "_DESC" tarafından izlenir. Varsayılan sıralama düzeni artan.

**Öğrenciler** bağlantısından Dizin sayfası istendiğinde sorgu dizesi yoktur. Öğrenciler, son ada göre artan sırada görüntülenir. Son ada göre artan sıralama, deyimindeki varsayılan (gelen durumdur) `switch` . Kullanıcı bir sütun başlığı bağlantısına tıkladığında, `sortOrder` sorgu dizesi değerinde uygun değer sağlanır.

`NameSort`ve `DateSort` Razor sütun başlığı köprülerini uygun sorgu dizesi değerleriyle yapılandırmak için kullanılır:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Kod C# koşullu işlecini kullanır [?:](/dotnet/csharp/language-reference/operators/conditional-operator). `?:`İşleci üçlü bir işleçtir (üç işlenen alır). İlk satır, `sortOrder` null veya boş olduğunu belirtir, `NameSort` "name_desc" olarak ayarlanır. `sortOrder`Null veya **not** boş değilse `NameSort` boş bir dize olarak ayarlanır.

Bu iki deyim, sayfanın sütun başlığı köprülerini şu şekilde ayarlamanızı sağlar:

| Geçerli sıralama düzeni   | Son ad Köprüsü | Tarih Köprüsü |
|:--------------------:|:-------------------:|:--------------:|
| Artan son ad  | descending          | ascending      |
| Azalan son ad | ascending           | ascending      |
| Artan Tarih       | ascending           | descending     |
| Azalan Tarih      | ascending           | ascending      |

Yöntemi, sıralama yapılacak sütunu belirtmek için LINQ to Entities kullanır. Kod, `IQueryable<Student>` Switch ifadesinden önce bir başlatır ve Switch ifadesinde onu değiştirir:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Bir `IQueryable` oluşturulduğunda veya değiştirildiğinde, veritabanına hiçbir sorgu gönderilmez. `IQueryable`Nesne bir koleksiyona dönüştürülene kadar sorgu yürütülmez. `IQueryable`, gibi bir yöntemi çağırarak bir koleksiyona dönüştürülür `ToListAsync` . Bu nedenle, `IQueryable` kod, aşağıdaki deyime kadar yürütülemeyen tek bir sorgu ile sonuçlanır:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`çok sayıda sıralanabilir sütunla ayrıntı alabilir. Bu işlevi kodun alternatif bir yolu hakkında daha fazla bilgi için, bu öğretici serisinin MVC sürümünde [kodu basitleştirmek için dınamık LINQ kullanma](xref:data/ef-mvc/advanced#dynamic-linq) konusuna bakın.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Öğrenci dizini sayfasına sütun başlığı köprüleri ekleme

*Öğrenciler/Index. cshtml*içindeki kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Yukarıdaki kod:

* `LastName`Ve sütun başlıklarına köprüler ekler `EnrollmentDate` .
* , Ve içindeki bilgileri `NameSort` kullanarak `DateSort` geçerli sıralama düzeni değerleriyle köprüler ayarlar.
* Sayfa başlığını dizinden öğrencilerle değiştirir.
* Üzerinde yapılan değişiklikler `Model.Student` `Model.Students` .

Sıralamanın çalıştığını doğrulamak için:

* Uygulamayı çalıştırın ve **öğrenciler** sekmesini seçin.
* Sütun başlıklarına tıklayın.

## <a name="add-filtering"></a>Filtre ekleme

Öğrenciler dizin sayfasına filtre eklemek için:

* Sayfaya bir metin kutusu ve bir Gönder düğmesi eklenir Razor . Metin kutusu, ad veya soyadı üzerinde bir arama dizesi sağlar.
* Sayfa modeli metin kutusu değerini kullanacak şekilde güncelleştirilir.

### <a name="update-the-ongetasync-method"></a>OnGetAsync yöntemini güncelleştirme

*Öğrenciler/Index. cshtml. cs* dosyasındaki kodu, filtreleme eklemek için aşağıdaki kodla değiştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

Yukarıdaki kod:

* `searchString`Yöntemine parametresini ekler `OnGetAsync` ve parametre değerini `CurrentFilter` özelliğine kaydeder. Arama dizesi değeri bir sonraki bölüme eklenen bir metin kutusundan alınır.
* LINQ deyimi a `Where` yan tümcesine ekler. `Where`Yan tümce yalnızca adı veya soyadı arama dizesini içeren öğrencileri seçer. LINQ deyimleri yalnızca aranacak bir değer varsa yürütülür.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable vs. IEnumerable

Kod, `Where` yöntemi bir nesne üzerinde çağırır `IQueryable` ve filtre sunucuda işlenir. Bazı senaryolarda, uygulama `Where` bir bellek içi koleksiyonda bir genişletme yöntemi olarak yöntemi çağırıyor olabilir. Örneğin, `_context.Students` EF Core 'den `DbSet` bir koleksiyonu döndüren bir depo yöntemine yapılan değişiklikleri varsayın `IEnumerable` . Sonuç normalde aynı olur, ancak bazı durumlarda farklı olabilir.

Örneğin, uygulamasının .NET Framework uygulanması `Contains` Varsayılan olarak büyük/küçük harfe duyarlı bir karşılaştırma gerçekleştirir. SQL Server, `Contains` büyük/küçük harf duyarlılığı SQL Server örneğinin harmanlama ayarına göre belirlenir. SQL Server varsayılan olarak büyük/küçük harfe duyarlı değildir. SQLite, büyük/küçük harfe duyarlı olur. `ToUpper`testi açık büyük/küçük harfe duyarsız hale getirmek için çağrılabilir:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Yukarıdaki kod, `Where` yöntemin bir veya bir SQLite üzerinde çağrılması durumunda bile filtrenin büyük/küçük harf duyarsız olmasını güvence altına `IEnumerable` alır.

`Contains`Bir koleksiyon üzerinde çağrıldığında `IEnumerable` .NET Core uygulamasını kullanır. `Contains`Bir nesne üzerinde çağrıldığında `IQueryable` , veritabanı uygulamasını kullanır.

`Contains`Bir üzerinde çağırmak `IQueryable` , genellikle performans nedenleriyle tercih edilir. İle `IQueryable` , filtreleme veritabanı sunucusu tarafından yapılır. Önce bir `IEnumerable` oluşturulduysa, tüm satırların veritabanı sunucusundan döndürülmesi gerekir.

Çağırmak için bir performans cezası vardır `ToUpper` . `ToUpper`Kod, TSQL Select IFADESININ WHERE yan tümcesine bir işlev ekler. Eklenen işlev, iyileştiricinin bir dizin kullanmasını önler. SQL, büyük/küçük harfe duyarsız olarak yüklendiği için, gerekli olmadığında çağrının önüne geçmek en iyisidir `ToUpper` .

Daha fazla bilgi için bkz. [SQLite sağlayıcı ile büyük/küçük harfe duyarsız sorgu kullanma](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-razor-page"></a>Sayfayı Güncelleştir Razor

*Sayfalar/öğrenciler/Index. cshtml* içindeki kodu, bir **arama** düğmesi ve assıralanan Chrome oluşturmak için değiştirin.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Yukarıdaki kod, `<form>` Arama metin kutusu ve düğme eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır. Varsayılan olarak, `<form>` etiket Yardımcısı form verilerini BIR gönderiyle gönderir. POST ile parametreler, URL 'de değil HTTP ileti gövdesine geçirilir. HTTP GET kullanıldığında, form verileri URL 'ye sorgu dizeleri olarak geçirilir. Verilerin sorgu dizelerine geçirilmesi, kullanıcıların URL 'YI yer işaretine eklemesini sağlar. [W3C yönergeleri](https://www.w3.org/2001/tag/doc/whenToUseGet.html) , eylem bir güncelleştirme ile SONUÇLANMAZSA, Get 'in kullanılması önerilir.

Uygulamayı test etme:

* **Öğrenciler** sekmesini seçin ve bir arama dizesi girin. SQLite kullanıyorsanız, filtre yalnızca daha önce gösterilen isteğe bağlı kodu uyguladıysanız, büyük/küçük harfe duyarlıdır `ToUpper` .

* **Ara**' yı seçin.

URL 'nin arama dizesini içerdiğine dikkat edin. Örnek:

```
https://localhost:<port>/Students?SearchString=an
```

Sayfa yer işaretiyle, yer işareti sayfanın URL 'sini ve `SearchString` sorgu dizesini içerir. `method="get"` `form` Etiketi, sorgu dizesinin oluşturulmasına neden oldu.

Şu anda, bir sütun başlığı sıralama bağlantısı seçildiğinde, **arama** kutusundaki filtre değeri kaybedilir. Kayıp filtre değeri bir sonraki bölümde düzeltilir.

## <a name="add-paging"></a>Sayfalama Ekle

Bu bölümde, `PaginatedList` sayfalama desteği için bir sınıf oluşturulur. `PaginatedList`Sınıfı, `Skip` `Take` tablodaki tüm satırları almak yerine, sunucudaki verileri filtrelemek için ve deyimlerini kullanır. Aşağıdaki çizimde sayfalama düğmeleri gösterilmektedir.

![Sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Sayfalı liste sınıfını oluşturma

Proje klasöründe `PaginatedList.cs` aşağıdaki kodla oluşturun:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

`CreateAsync`Yukarıdaki koddaki yöntemi sayfa boyutunu ve sayfa numarasını alır ve ' a uygun `Skip` ve `Take` deyimlerini uygular `IQueryable` . `ToListAsync`Üzerinde çağrıldığında `IQueryable` , yalnızca istenen sayfayı Içeren bir liste döndürür. Özellikler `HasPreviousPage` ve `HasNextPage` **önceki** ve **sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılır.

`CreateAsync`Yöntemi oluşturmak için kullanılır `PaginatedList<T>` . Oluşturucu `PaginatedList<T>` nesneyi oluşturamıyor; oluşturucular zaman uyumsuz kod çalıştıramıyor.

### <a name="add-paging-to-the-pagemodel-class"></a>PageModel sınıfına sayfalama ekleme

*Öğrenciler/Index. cshtml. cs* ' deki kodu, sayfalama eklemek için değiştirin.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

Yukarıdaki kod:

* `Students`Özelliğinin türünü `IList<Student>` olarak değiştirir `PaginatedList<Student>` .
* Sayfa dizinini, geçerli `sortOrder` ve öğesini `currentFilter` `OnGetAsync` Yöntem imzasına ekler.
* Sıralama düzenini CurrentSort özelliğine kaydeder.
* Yeni bir arama dizesi olduğunda sayfa dizinini 1 olarak sıfırlar.
* `PaginatedList`Öğrenci varlıklarını almak için sınıfını kullanır.

`OnGetAsync`Şu durumlarda alan tüm parametreler null:

* Sayfa **öğrenciler** bağlantısından çağrılır.
* Kullanıcı bir sayfalama veya sıralama bağlantısına tıklamadı.

Bir sayfalama bağlantısına tıklandığında, sayfa dizin değişkeni görüntülenecek sayfa numarasını içerir.

`CurrentSort`Özelliği, sayfa için Razor geçerli sıralama düzenini sağlar. Disk belleği sırasında sıralama düzenini korumak için geçerli sıralama düzeni, sayfalama bağlantılarına eklenmelidir.

`CurrentFilter`Özelliği, Razor sayfayı geçerli filtre dizesiyle birlikte sağlar. `CurrentFilter`Değer:

* Disk belleği sırasında filtre ayarlarını korumak için disk belleği bağlantılarına eklenmelidir.
* Sayfa yeniden görüntülendiğinde metin kutusuna geri yüklenmelidir.

Sayfalama sırasında arama dizesi değiştirilirse sayfa 1 ' e sıfırlanır. Yeni filtre farklı verilerin görüntülenmesini sağladığından sayfanın 1 olarak sıfırlanması. Bir arama değeri girildiğinde ve **Gönder** seçildiğinde:

  * Arama dizesi değiştirildi.
  * `searchString`Parametre null değil.

  `PaginatedList.CreateAsync`Yöntemi, öğrenci sorgusunu, sayfalama destekleyen bir koleksiyon türündeki tek bir öğrenci sayfasına dönüştürür. Bu tek öğrenci sayfası Razor sayfaya geçirilir.

  Çağrıdan sonraki iki soru işareti, `pageIndex` `PaginatedList.CreateAsync` [null birleşim işlecini](/dotnet/csharp/language-reference/operators/null-conditional-operator)temsil eder. Null birleşim işleci, null yapılabilir bir tür için varsayılan değeri tanımlar. İfade, `(pageIndex ?? 1)` bir değer içeriyorsa değerini döndürür anlamına gelir `pageIndex` . `pageIndex`Değer yoksa 1 döndürün.

### <a name="add-paging-links-to-the-razor-page"></a>Sayfaya sayfalama bağlantıları ekleyin Razor

*Öğrenciler/Index. cshtml* içindeki kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Sütun üst bilgisi bağlantıları, geçerli arama dizesini yönteme geçirmek için sorgu dizesini kullanır `OnGetAsync` :

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.

* Sayfalama 'nin çalıştığından emin olmak için, farklı sıralama emirlerindeki disk belleği bağlantılarına tıklayın.
* Disk belleğinin sıralama ve filtreleme ile düzgün çalıştığını doğrulamak için bir arama dizesi girin ve sayfalama yapmayı deneyin.

![sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Gruplama ekle

Bu bölüm, her kayıt tarihi için kaç öğrenciye kaydolduğunu görüntüleyen bir hakkında sayfası oluşturur. Güncelleştirme gruplamayı kullanır ve aşağıdaki adımları içerir:

* **Hakkında** sayfasında kullanılan veriler için bir görünüm modeli oluşturun.
* Görünüm modelini kullanmak için hakkında sayfasını güncelleştirin.

### <a name="create-the-view-model"></a>Görünüm modeli oluşturma

*Modeller/SchoolViewModels* klasörü oluşturun.

Aşağıdaki kodla *SchoolViewModels/kayıtlarını Mentdategroup. cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Sayfayı oluşturun Razor

Aşağıdaki kodla bir *Pages/about. cshtml* dosyası oluşturun:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Sayfa modelini oluşturma

Aşağıdaki kodla bir *Pages/about. cshtml. cs* dosyası oluşturun:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

LINQ beyanı, öğrenci varlıklarını kayıt tarihine göre gruplandırır, her bir gruptaki varlıkların sayısını hesaplar ve sonuçları bir `EnrollmentDateGroup` görünüm modeli nesneleri koleksiyonunda depolar.

Uygulamayı çalıştırın ve hakkında sayfasına gidin. Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.

![Sayfa hakkında](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide, uygulama, veri modelini güncelleştirmek için geçişleri kullanır.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/crud) 
>  [Sonraki öğretici](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide sıralama, filtreleme, gruplama ve sayfalama işlevleri eklenmiştir.

Aşağıdaki çizimde tamamlanmış bir sayfa gösterilmektedir. Sütun başlıkları sütunu sıralamak için tıklatılabilir bağlantılardır. Sütun başlığına tıklanması, artan ve azalan sıralama düzeni arasında sürekli olarak geçiş yapar.

![Öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

Çözemediğiniz sorunlarla karşılaşırsanız, [Tamamlanmış uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

## <a name="add-sorting-to-the-index-page"></a>Dizin sayfasına sıralama Ekle

Sıralama parametrelerini içerecek şekilde *öğrenciler/Index. cshtml. cs* öğesine dizeler ekleyin `PageModel` :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

*Öğrenciler/Index. cshtml. cs* ' i `OnGetAsync` aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Yukarıdaki kod, `sortOrder` URL 'deki sorgu dizesinden bir parametre alır. URL (sorgu dizesi dahil), [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
) tarafından oluşturulur

`sortOrder`Parametre "ad" ya da "Tarih" dır. `sortOrder`Parametre, isteğe bağlı olarak azalan sıra belirtmek için "_DESC" tarafından izlenir. Varsayılan sıralama düzeni artan.

**Öğrenciler** bağlantısından Dizin sayfası istendiğinde sorgu dizesi yoktur. Öğrenciler, son ada göre artan sırada görüntülenir. Son ada göre artan sıralama, deyimindeki varsayılan (gelen durumdur) `switch` . Kullanıcı bir sütun başlığı bağlantısına tıkladığında, `sortOrder` sorgu dizesi değerinde uygun değer sağlanır.

`NameSort`ve `DateSort` Razor sütun başlığı köprülerini uygun sorgu dizesi değerleriyle yapılandırmak için kullanılır:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Aşağıdaki kod C# koşullu [?: işlecini](/dotnet/csharp/language-reference/operators/conditional-operator)içerir:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

İlk satır, `sortOrder` null veya boş olduğunu belirtir, `NameSort` "name_desc" olarak ayarlanır. `sortOrder`Null veya **not** boş değilse `NameSort` boş bir dize olarak ayarlanır.

, `?: operator` Üçlü işleç olarak da bilinir.

Bu iki deyim, sayfanın sütun başlığı köprülerini şu şekilde ayarlamanızı sağlar:

| Geçerli sıralama düzeni | Son ad Köprüsü | Tarih Köprüsü |
|:--------------------:|:-------------------:|:--------------:|
| Artan son ad | descending        | ascending      |
| Azalan son ad | ascending           | ascending      |
| Artan Tarih       | ascending           | descending     |
| Azalan Tarih      | ascending           | ascending      |

Yöntemi, sıralama yapılacak sütunu belirtmek için LINQ to Entities kullanır. Kod, `IQueryable<Student>` Switch ifadesinden önce bir başlatır ve Switch ifadesinde onu değiştirir:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Bir `IQueryable` oluşturulduğunda veya değiştirildiğinde, veritabanına hiçbir sorgu gönderilmez. `IQueryable`Nesne bir koleksiyona dönüştürülene kadar sorgu yürütülmez. `IQueryable`, gibi bir yöntemi çağırarak bir koleksiyona dönüştürülür `ToListAsync` . Bu nedenle, `IQueryable` kod, aşağıdaki deyime kadar yürütülemeyen tek bir sorgu ile sonuçlanır:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`çok sayıda sıralanabilir sütunla ayrıntı alabilir.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Öğrenci dizini sayfasına sütun başlığı köprüleri ekleme

*Öğrenciler/Index. cshtml*içindeki kodu aşağıdaki vurgulanmış kodla değiştirin:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Yukarıdaki kod:

* `LastName`Ve sütun başlıklarına köprüler ekler `EnrollmentDate` .
* , Ve içindeki bilgileri `NameSort` kullanarak `DateSort` geçerli sıralama düzeni değerleriyle köprüler ayarlar.

Sıralamanın çalıştığını doğrulamak için:

* Uygulamayı çalıştırın ve **öğrenciler** sekmesini seçin.
* **Son ad**' a tıklayın.
* **Kayıt tarihi**' ne tıklayın.

Kodu daha iyi anlamak için:

* *Öğrenciler/Index. cshtml. cs*dosyasında, üzerinde bir kesme noktası ayarlayın `switch (sortOrder)` .
* Ve için bir izleme `NameSort` ekleyin `DateSort` .
* *Öğrenciler/Index. cshtml*'de, üzerinde bir kesme noktası ayarlayın `@Html.DisplayNameFor(model => model.Student[0].LastName)` .

Hata ayıklayıcıda adım adım.

## <a name="add-a-search-box-to-the-students-index-page"></a>Öğrenciler dizin sayfasına bir arama kutusu ekleyin

Öğrenciler dizin sayfasına filtre eklemek için:

* Sayfaya bir metin kutusu ve bir Gönder düğmesi eklenir Razor . Metin kutusu, ad veya soyadı üzerinde bir arama dizesi sağlar.
* Sayfa modeli metin kutusu değerini kullanacak şekilde güncelleştirilir.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dizin yöntemine filtreleme işlevi ekleme

*Öğrenciler/Index. cshtml. cs* ' i `OnGetAsync` aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Yukarıdaki kod:

* `searchString`Yöntemine parametresini ekler `OnGetAsync` . Arama dizesi değeri bir sonraki bölüme eklenen bir metin kutusundan alınır.
* LINQ deyimi bir `Where` yan tümcesine eklendi. `Where`Yan tümce yalnızca adı veya soyadı arama dizesini içeren öğrencileri seçer. LINQ deyimleri yalnızca aranacak bir değer varsa yürütülür.

Note: Yukarıdaki kod, `Where` metodu bir nesne üzerinde çağırır `IQueryable` ve filtre sunucuda işlenir. Bazı senaryolarda, uygulama `Where` bir bellek içi koleksiyonda bir genişletme yöntemi olarak yöntemi çağırıyor olabilir. Örneğin, `_context.Students` EF Core 'den `DbSet` bir koleksiyonu döndüren bir depo yöntemine yapılan değişiklikleri varsayın `IEnumerable` . Sonuç normalde aynı olur, ancak bazı durumlarda farklı olabilir.

Örneğin, uygulamasının .NET Framework uygulanması `Contains` Varsayılan olarak büyük/küçük harfe duyarlı bir karşılaştırma gerçekleştirir. SQL Server, `Contains` büyük/küçük harf duyarlılığı SQL Server örneğinin harmanlama ayarına göre belirlenir. SQL Server varsayılan olarak büyük/küçük harfe duyarlı değildir. `ToUpper`testi açık büyük/küçük harfe duyarsız hale getirmek için çağrılabilir:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Yukarıdaki kod, kodun kullanım için değişiklik yaptığı durumlarda sonuçların büyük/küçük harf duyarsız olmasını sağlar `IEnumerable` . `Contains`Bir koleksiyon üzerinde çağrıldığında `IEnumerable` .NET Core uygulamasını kullanır. `Contains`Bir nesne üzerinde çağrıldığında `IQueryable` , veritabanı uygulamasını kullanır. `IEnumerable`Bir depodan döndürmek önemli bir performans cezasına sahip olabilir:

1. Tüm satırlar DB sunucusundan döndürülür.
1. Filtre, uygulamadaki tüm döndürülen satırlara uygulanır.

Çağırmak için bir performans cezası vardır `ToUpper` . `ToUpper`Kod, TSQL Select IFADESININ WHERE yan tümcesine bir işlev ekler. Eklenen işlev, iyileştiricinin bir dizin kullanmasını önler. SQL, büyük/küçük harfe duyarsız olarak yüklendiği için, gerekli olmadığında çağrının önüne geçmek en iyisidir `ToUpper` .

### <a name="add-a-search-box-to-the-student-index-page"></a>Öğrenci dizin sayfasına bir arama kutusu ekleyin

*Sayfalar/öğrenciler/Index. cshtml*' de, bir **arama** düğmesi ve asi grafik Chrome oluşturmak için aşağıdaki vurgulanmış kodu ekleyin.

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Yukarıdaki kod, `<form>` Arama metin kutusu ve düğme eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır. Varsayılan olarak, `<form>` etiket Yardımcısı form verilerini BIR gönderiyle gönderir. POST ile parametreler, URL 'de değil HTTP ileti gövdesine geçirilir. HTTP GET kullanıldığında, form verileri URL 'ye sorgu dizeleri olarak geçirilir. Verilerin sorgu dizelerine geçirilmesi, kullanıcıların URL 'YI yer işaretine eklemesini sağlar. [W3C yönergeleri](https://www.w3.org/2001/tag/doc/whenToUseGet.html) , eylem bir güncelleştirme ile SONUÇLANMAZSA, Get 'in kullanılması önerilir.

Uygulamayı test etme:

* **Öğrenciler** sekmesini seçin ve bir arama dizesi girin.
* **Ara**' yı seçin.

URL 'nin arama dizesini içerdiğine dikkat edin.

```html
http://localhost:5000/Students?SearchString=an
```

Sayfa yer işaretiyle, yer işareti sayfanın URL 'sini ve `SearchString` sorgu dizesini içerir. `method="get"` `form` Etiketi, sorgu dizesinin oluşturulmasına neden oldu.

Şu anda, bir sütun başlığı sıralama bağlantısı seçildiğinde, **arama** kutusundaki filtre değeri kaybedilir. Kayıp filtre değeri bir sonraki bölümde düzeltilir.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Öğrenciler dizin sayfasına sayfalama işlevselliği ekleme

Bu bölümde, `PaginatedList` sayfalama desteği için bir sınıf oluşturulur. `PaginatedList`Sınıfı, `Skip` `Take` tablodaki tüm satırları almak yerine, sunucudaki verileri filtrelemek için ve deyimlerini kullanır. Aşağıdaki çizimde sayfalama düğmeleri gösterilmektedir.

![Sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

Proje klasöründe `PaginatedList.cs` aşağıdaki kodla oluşturun:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

`CreateAsync`Yukarıdaki koddaki yöntemi sayfa boyutunu ve sayfa numarasını alır ve ' a uygun `Skip` ve `Take` deyimlerini uygular `IQueryable` . `ToListAsync`Üzerinde çağrıldığında `IQueryable` , yalnızca istenen sayfayı Içeren bir liste döndürür. Özellikler `HasPreviousPage` ve `HasNextPage` **önceki** ve **sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılır.

`CreateAsync`Yöntemi oluşturmak için kullanılır `PaginatedList<T>` . Bir Oluşturucu `PaginatedList<T>` nesneyi oluşturamaz, oluşturucular zaman uyumsuz kod çalıştıramıyorum.

## <a name="add-paging-functionality-to-the-index-method"></a>Dizin yöntemine sayfalama işlevselliği ekleme

*Öğrenciler/Index. cshtml. cs*dosyasında, türünü `Student` ile arasında güncelleştirin `IList<Student>` `PaginatedList<Student>` :

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

*Öğrenciler/Index. cshtml. cs* ' i `OnGetAsync` aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Yukarıdaki kod, sayfa dizinini, geçerli `sortOrder` ve öğesini `currentFilter` Yöntem imzasına ekler.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Şu durumlarda tüm parametreler null:

* Sayfa **öğrenciler** bağlantısından çağrılır.
* Kullanıcı bir sayfalama veya sıralama bağlantısına tıklamadı.

Bir sayfalama bağlantısına tıklandığında, sayfa dizin değişkeni görüntülenecek sayfa numarasını içerir.

`CurrentSort`Razorgeçerli sıralama düzeninde sayfa sağlar. Disk belleği sırasında sıralama düzenini korumak için geçerli sıralama düzeni, sayfalama bağlantılarına eklenmelidir.

`CurrentFilter`Razorgeçerli Filtre dizesinin bulunduğu sayfayı sağlar. `CurrentFilter`Değer:

* Disk belleği sırasında filtre ayarlarını korumak için disk belleği bağlantılarına eklenmelidir.
* Sayfa yeniden görüntülendiğinde metin kutusuna geri yüklenmelidir.

Sayfalama sırasında arama dizesi değiştirilirse sayfa 1 ' e sıfırlanır. Yeni filtre farklı verilerin görüntülenmesini sağladığından sayfanın 1 olarak sıfırlanması. Bir arama değeri girildiğinde ve **Gönder** seçildiğinde:

* Arama dizesi değiştirildi.
* `searchString`Parametre null değil.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync`Yöntemi, öğrenci sorgusunu, sayfalama destekleyen bir koleksiyon türündeki tek bir öğrenci sayfasına dönüştürür. Bu tek öğrenci sayfası Razor sayfaya geçirilir.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

İçindeki iki soru işareti `PaginatedList.CreateAsync` , [null birleşim işlecini](/dotnet/csharp/language-reference/operators/null-conditional-operator)temsil eder. Null birleşim işleci, null yapılabilir bir tür için varsayılan değeri tanımlar. İfade, `(pageIndex ?? 1)` bir değer içeriyorsa değerini döndürür anlamına gelir `pageIndex` . `pageIndex`Değer yoksa 1 döndürün.

## <a name="add-paging-links-to-the-student-razor-page"></a>Öğrenci sayfasına sayfalama bağlantıları ekleme Razor

*Öğrenciler/Index. cshtml*'de biçimlendirmeyi güncelleştirin. Değişiklikler vurgulanır:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Sütun üst bilgisi bağlantıları, `OnGetAsync` kullanıcının filtre sonuçları içinde sıralama yapabilmesi için geçerli arama dizesini yöntemine geçirmek üzere sorgu dizesini kullanır:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.

* Sayfalama 'nin çalıştığından emin olmak için, farklı sıralama emirlerindeki disk belleği bağlantılarına tıklayın.
* Disk belleğinin sıralama ve filtreleme ile düzgün çalıştığını doğrulamak için bir arama dizesi girin ve sayfalama yapmayı deneyin.

![sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

Kodu daha iyi anlamak için:

* *Öğrenciler/Index. cshtml. cs*dosyasında, üzerinde bir kesme noktası ayarlayın `switch (sortOrder)` .
* ,, Ve için bir izleme ekleyin `NameSort` `DateSort` `CurrentSort` `Model.Student.PageIndex` .
* *Öğrenciler/Index. cshtml*'de, üzerinde bir kesme noktası ayarlayın `@Html.DisplayNameFor(model => model.Student[0].LastName)` .

Hata ayıklayıcıda adım adım.

## <a name="update-the-about-page-to-show-student-statistics"></a>Öğrenci istatistiklerini göstermek için hakkında sayfasını güncelleştirin

Bu adımda, *Sayfalar/about. cshtml* , her bir kayıt tarihi için kaç öğrenciye kaydolduğunu görüntüleyecek şekilde güncelleştirilir. Güncelleştirme gruplamayı kullanır ve aşağıdaki adımları içerir:

* **Hakkında** sayfasında kullanılan veriler için bir görünüm modeli oluşturun.
* Görünüm modelini kullanmak için hakkında sayfasını güncelleştirin.

### <a name="create-the-view-model"></a>Görünüm modeli oluşturma

*Modeller* klasöründe bir *SchoolViewModels* klasörü oluşturun.

*SchoolViewModels* klasöründe aşağıdaki kodla bir *EnrollmentDateGroup.cs* ekleyin:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Hakkında sayfa modelini Güncelleştir

ASP.NET Core 2,2 ' deki Web şablonları hakkında sayfasını içermez. ASP.NET Core 2,2 kullanıyorsanız hakkında Razor sayfasını oluşturun.

*Pages/about. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

LINQ beyanı, öğrenci varlıklarını kayıt tarihine göre gruplandırır, her bir gruptaki varlıkların sayısını hesaplar ve sonuçları bir `EnrollmentDateGroup` görünüm modeli nesneleri koleksiyonunda depolar.

### <a name="modify-the-about-razor-page"></a>Hakkında sayfasını değiştirme Razor

*Pages/about. cshtml* dosyasındaki kodu aşağıdaki kodla değiştirin:

[!code-cshtml[](intro/samples/cu21/Pages/About.cshtml)]

Uygulamayı çalıştırın ve hakkında sayfasına gidin. Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.

Çözemediğiniz sorunlarla karşılaşırsanız, [Bu aşama için tamamlanmış uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)indirin.

![Sayfa hakkında](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [2. x kaynağı ASP.NET Core hata ayıklaması](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=MDs7PFpoMqI)

Sonraki öğreticide, uygulama, veri modelini güncelleştirmek için geçişleri kullanır.

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/crud) 
>  [Sonraki](xref:data/ef-rp/migrations)

::: moniker-end

