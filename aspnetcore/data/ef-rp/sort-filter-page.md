---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Sıralama, Filtre, Sayfalama - 8'de 3
author: rick-anderson
description: Bu öğreticide, Core ve Entity Framework Core'u ASP.NET kullanarak bir Razor sayfasına sıralama, filtreleme ve sayfalama işlevleri eklersiniz.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 9563f3ef52ce429eb0a58b468acb8e9cd7b276e2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656466"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Sıralama, Filtre, Sayfalama - 8'de 3

Yazar: [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), ve Jon [P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, Öğrenciler sayfalarına sıralama, filtreleme ve sayfalama işlevleri ekler.

Aşağıdaki resimde tamamlanmış bir sayfa gösterilmektedir. Sütun başlıkları sütunu sıralamak için tıklanabilir bağlantılardır. Artan ve azalan sıralama sırası arasında geçiş yapmak için bir sütun başlığını tekrar tekrar tıklatın.

![Öğrenci dizini sayfası](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Sıralama ekleme

Sıralama eklemek için *Sayfalar/Öğrenciler/Index.cshtml.cs'deki* kodu aşağıdaki kodla değiştirin.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

Yukarıdaki kod:

* Sıralama parametrelerini içerecek özellikler ekler.
* Özelliğin `Student` adını ' `Students`da ' olarak değiştirir
* `OnGetAsync` Yöntemdeki kodu değiştirir.

Yöntem, URL'deki `OnGetAsync` sorgu dizesinden bir `sortOrder` parametre alır. URL (sorgu dizesi dahil) [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)tarafından oluşturulur.

`sortOrder` Parametre ya "Ad" ya da "Tarih" olur. Parametre, `sortOrder` azalan sırayı belirtmek için isteğe bağlı olarak "_desc" tarafından izlenir. Varsayılan sıralama sırası yükseliyor.

**Öğrenciler** bağlantısından Dizin sayfası istendiğinde, sorgu dizesi yoktur. Öğrenciler soyadları ile artan sırada görüntülenir. Soyadına göre artan `switch` sıra, deyimdeki varsayılan (düşme örneği) olur. Kullanıcı bir sütun başlığı bağlantısını tıklattığında, sorgu dizesi değerinde uygun `sortOrder` değer sağlanır.

`NameSort`ve `DateSort` uygun sorgu dize değerleri ile sütun başlığı köprüler yapılandırmak için Razor Page tarafından kullanılır:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Kod C# koşullu işleci kullanır [?:](/dotnet/csharp/language-reference/operators/conditional-operator). Operatör `?:` bir üçlü işleç (üç operands alır). İlk satır, boş veya `sortOrder` boş olduğunda `NameSort` "name_desc" olarak ayarlanır. `sortOrder` Null veya boş **değilse,** `NameSort` boş bir dize olarak ayarlanır.

Bu iki deyim, sayfanın sütun başlığı köprülerini aşağıdaki gibi ayarlamasını sağlar:

| Geçerli sıralama sırası   | Soyadı Köprü | Tarih Köprü |
|:--------------------:|:-------------------:|:--------------:|
| Artan Soyadı  | descending          | ascending      |
| Soyadı azalan | ascending           | ascending      |
| Artan tarih       | ascending           | descending     |
| Azalan tarih      | ascending           | ascending      |

Yöntem, sıralamak için sütun belirtmek için Varlıklar LINQ kullanır. Kod, anahtar bildiriminden önce bir `IQueryable<Student>` parola yılaştırır ve anahtar deyiminde değiştirir:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Bir`IQueryable` oluşturulduğunda veya değiştirildiğinde, veritabanına sorgu gönderilmez. `IQueryable` Nesne koleksiyona dönüştürülene kadar sorgu yürütülmez. `IQueryable`gibi bir yöntem çağırılarak koleksiyona `ToListAsync`dönüştürülür. Bu nedenle, `IQueryable` kod aşağıdaki ifadeye kadar yürütülmeyen tek bir sorguyla sonuçlanır:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`çok sayıda sıralanabilir sütun ile ayrıntılı alabilirsiniz. Bu işlevselliği kodlamanın alternatif bir yolu hakkında bilgi için, bu öğretici serinin MVC sürümünde [kodu basitleştirmek için dinamik LINQ kullanın'a](xref:data/ef-mvc/advanced#dynamic-linq) bakın.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Öğrenci Dizini sayfasına sütun başlığı köprüleri ekleme

*Students/Index.cshtml'deki*kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Yukarıdaki kod:

* Ve `LastName` `EnrollmentDate` sütun başlıklarına köprüler ekler.
* Geçerli sıralama `NameSort` sırası `DateSort` değerleriyle köprüler kurmak ve bilgileri kullanır.
* Dizin'den Öğrencilere sayfa başlığını değiştirir.
* `Model.Student` 'de `Model.Students`değişiklikler.

Bu sıralamanın işe yaradığını doğrulamak için:

* Uygulamayı çalıştırın ve **Öğrenciler** sekmesini seçin.
* Sütun başlıklarını tıklatın.

## <a name="add-filtering"></a>Filtreleme ekleme

Öğrenci Dizini sayfasına filtreleme eklemek için:

* Razor Page'e bir metin kutusu ve gönderme düğmesi eklenir. Metin kutusu, ad veya soyad üzerinde bir arama dizesi sağlar.
* Sayfa modeli metin kutusu değerini kullanmak için güncelleştirilir.

### <a name="update-the-ongetasync-method"></a>OnGetAsync yöntemini güncelleştirin

*Students/Index.cshtml.cs'deki* kodu aşağıdaki kodla değiştirerek filtreleme ekleyin:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

Yukarıdaki kod:

* Yönteme `searchString` parametre ekler ve `CurrentFilter` özellikteki parametre değerini kaydeder. `OnGetAsync` Arama dizesi değeri, bir sonraki bölüme eklenen bir metin kutusundan alınır.
* LINQ deyimine bir `Where` yan tümce ekler. Yan `Where` tümce yalnızca ad veya soyad arama dizesini içeren öğrencileri seçer. LINQ deyimi yalnızca aranacak bir değer varsa yürütülür.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable vs. IEnumerable

Kod bir `IQueryable` `Where` nesne üzerinde yöntemi çağırır ve filtre sunucuda işlenir. Bazı senaryolarda, uygulama `Where` yöntemi bellek içi bir koleksiyonda uzantı yöntemi olarak adlandırıyor olabilir. Örneğin, EF `_context.Students` Core'dan `DbSet` koleksiyonu `IEnumerable` döndüren bir depo yöntemine yapılan değişiklikleri varsayalım. Sonuç normalde aynı olurdu ama bazı durumlarda farklı olabilir.

Örneğin, .NET Framework uygulaması `Contains` varsayılan olarak büyük/küçük harf duyarlı bir karşılaştırma gerçekleştirir. SQL Server'da büyük/küçük harf duyarlılığı, `Contains` SQL Server örneğinin harmanlama ayarı ile belirlenir. SQL Server varsayılan olarak büyük/küçük harf duyarsızı olarak karşıdır. SQLite varsayılan olarak büyük/küçük harf duyarlıdır. `ToUpper`testin açıkça büyük/küçük harf duyarsız yapmak için çağrılabilir:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Önceki kod, `Where` yöntem bir `IEnumerable` veya SQLite üzerinde çalıştığında bile filtrenin büyük/küçük harf duyarlı olmasını sağlar.

Bir `Contains` `IEnumerable` koleksiyon çağrıldığında ,.NET Core uygulaması kullanılır. Bir `Contains` `IQueryable` nesneye çağrıldığında, veritabanı uygulaması kullanılır.

Bir `Contains` `IQueryable` arama genellikle performans nedenleriyle tercih edilir. Ile `IQueryable`, filtreleme veritabanı sunucusu tarafından yapılır. Önce `IEnumerable` bir tane oluşturulursa, tüm satırların veritabanı sunucusundan döndürülür.

Aramanın `ToUpper`bir performans cezası var. Kod, `ToUpper` TSQL SELECT deyiminin WHERE yan tümcesi bir işlev ekler. Eklenen işlev en iyi duruma getiricinin dizin kullanmasını engeller. SQL'in büyük/küçük harf duyarsız olarak yüklendiğinden, `ToUpper` gerekli olmadığında çağrıdan kaçınmak en iyisidir.

Daha fazla bilgi için, [sqlite sağlayıcısı ile büyük/küçük harf duyarsız sorgu nasıl kullanılır](https://github.com/aspnet/EntityFrameworkCore/issues/11414)bakın.

### <a name="update-the-razor-page"></a>Razor sayfasını güncelleştirin

**Arama** düğmesi ve çeşitli chrome oluşturmak için *Sayfalar/Öğrenciler/Index.cshtml'deki* kodu değiştirin.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Önceki kod, arama `<form>` metin kutusunu ve düğmesini eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır. Varsayılan olarak, `<form>` etiket yardımcısı form verilerini POST ile gönderir. POST ile parametreler URL'de değil, HTTP ileti gövdesinde geçirilir. HTTP GET kullanıldığında, form verileri URL'de sorgu dizeleri olarak geçirilir. Verileri sorgu dizeleriyle aktarmak, kullanıcıların URL'yi yer imi eklemesine olanak tanır. [W3C yönergeleri,](https://www.w3.org/2001/tag/doc/whenToUseGet.html) eylem bir güncelleştirmeyle sonuçlanmadığında GET'in kullanılmasını önerir.

Uygulamayı test edin:

* **Öğrenciler** sekmesini seçin ve bir arama dizesi girin. SQLite kullanıyorsanız, filtre yalnızca daha önce gösterilen isteğe bağlı `ToUpper` kodu uyguladıysanız büyük/küçük harf duyarsızdır.

* **Ara'yı**seçin.

URL'nin arama dizesini içerdiğine dikkat edin. Örneğin:

```
https://localhost:<port>/Students?SearchString=an
```

Sayfa yer imi varsa, yer imi sayfanın `SearchString` URL'sini ve sorgu dizesini içerir. Etiketteki `method="get"` `form` sorgu dizesi oluşturulmasına neden olan şeydir.

Şu anda, bir sütun başlığı sıralama bağlantısı seçildiğinde, **Arama** kutusundaki filtre değeri kaybolur. Kaybolan filtre değeri sonraki bölümde sabitlenir.

## <a name="add-paging"></a>Sayfalama ekleme

Bu bölümde, `PaginatedList` sayfalama yı desteklemek için bir sınıf oluşturulur. Sınıf, `PaginatedList` `Skip` tablonun tüm satırlarını almak yerine sunucudaki verileri filtrelemek için kullanır ve `Take` deyimler kullanır. Aşağıdaki resimde sayfalama düğmeleri gösterilmektedir.

![Sayfalama bağlantıları olan öğrenci dizini sayfası](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>PaginatedList sınıfını oluşturma

Proje klasöründe, `PaginatedList.cs` aşağıdaki kodla oluşturun:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

Önceki `CreateAsync` koddaki yöntem sayfa boyutunu ve sayfa numarasını alır `Skip` `Take` ve uygun `IQueryable`ve ifadeleri . `IQueryable`Çağrıldığında, `ToListAsync` yalnızca istenen sayfayı içeren bir Liste döndürür. Özellikler `HasPreviousPage` ve `HasNextPage` **Önceki** ve **Sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılır.

Yöntem `CreateAsync` oluşturmak için `PaginatedList<T>`kullanılır. Bir oluşturucu nesneyi `PaginatedList<T>` oluşturamaz; yapıcılar eşzamanlı kod çalıştıramaz.

### <a name="add-paging-to-the-pagemodel-class"></a>PageModel sınıfına sayfa ekleme

Sayfalama eklemek için *Students/Index.cshtml.cs'deki* kodu değiştirin.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

Yukarıdaki kod:

* `Students` Özelliğin türünü ' `IList<Student>` den `PaginatedList<Student>`' e değiştirir
* Sayfa dizini, geçerli `sortOrder`ve `currentFilter` yöntem `OnGetAsync` imzasını ekler.
* CurrentSort özelliğindeki sıralama sırasını kaydeder.
* Yeni bir arama dizesi olduğunda sayfa dizini 1 olarak sıfırlar.
* Öğrenci `PaginatedList` varlıklarını almak için sınıfı kullanır.

Aşağıdaki leri `OnGetAsync` aldığında alınan tüm parametreler null'dur:

* Sayfa **Öğrenciler** bağlantısından çağrılır.
* Kullanıcı bir sayfalama veya sıralama bağlantısını tıklatmadı.

Bir sayfalama bağlantısı tıklandığında, sayfa dizini değişkeni görüntülenecek sayfa numarasını içerir.

Özellik, `CurrentSort` Razor Page'e geçerli sıralama sırasını sağlar. Geçerli sıralama sırası, çağrı sırasında sıralama sırasını tutmak için çağrı bağlantılarına eklenmelidir.

Özellik, `CurrentFilter` Geçerli filtre dizesini ile Razor Page sağlar. Değer: `CurrentFilter`

* Sayfalama sırasında filtre ayarlarını korumak için sayfalama bağlantılarına dahil edilmelidir.
* Sayfa yeniden görüntülendiğinde metin kutusuna geri yüklenmelidir.

Arama dizesi çağrı sırasında değiştirilirse, sayfa 1 olarak sıfırlanır. Yeni filtre farklı verilerin görüntülenmesine neden olabileceğinden sayfanın 1 olarak sıfırlanması gerekir. Bir arama değeri girildiğinde ve **Gönder** seçildiğinde:

  * Arama dizesi değiştirildi.
  * Parametre `searchString` null değil.

  Yöntem, `PaginatedList.CreateAsync` öğrenci sorgusunu, çağrıyı destekleyen bir koleksiyon türündeki tek bir öğrenci sayfasına dönüştürür. Öğrencilerin tek sayfası Jilet Sayfası'na aktarılır.

  Çağrıdan sonraki `pageIndex` iki soru işareti [null-coalescing operatör](/dotnet/csharp/language-reference/operators/null-conditional-operator)temsil eder. `PaginatedList.CreateAsync` Null-coalescing işleci nullable türü için varsayılan bir değer tanımlar. İfade, `(pageIndex ?? 1)` bir değeri `pageIndex` varsa değerini döndürme anlamına gelir. Bir `pageIndex` değeri yoksa, 1 döndürün.

### <a name="add-paging-links-to-the-razor-page"></a>Razor Page'e sayfalama bağlantıları ekleme

*Students/Index.cshtml'deki* kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Sütun üstbilgi bağlantıları, geçerli arama dizesini yönteme geçirmek için sorgu dizesini `OnGetAsync` kullanır:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.

* Sayfalamanın çalıştığından emin olmak için, farklı sıralama siparişlerinde sayfalama bağlantılarını tıklatın.
* Arama nın sıralama ve filtreleme yle doğru çalıştığını doğrulamak için bir arama dizesi girin ve aramayı deneyin.

![sayfalama bağlantıları ile öğrenci indeks sayfası](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Gruplama ekle

Bu bölümde, her kayıt tarihi için kaç öğrencinin kaydolduğunu gösteren bir Hakkında sayfası oluşturulur. Güncelleştirme gruplandırmayı kullanır ve aşağıdaki adımları içerir:

* **Hakkında** sayfası tarafından kullanılan veriler için bir görünüm modeli oluşturun.
* Görünüm modelini kullanmak için Hakkında sayfasını güncelleştirin.

### <a name="create-the-view-model"></a>Görünüm modelini oluşturma

*Modeller/SchoolViewModels* klasörü oluşturun.

Aşağıdaki kodla *SchoolViewModels/EnrollmentDateGroup.cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Jilet Sayfası Oluştur

Aşağıdaki kodla bir *Pages/About.cshtml* dosyası oluşturun:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Sayfa modelini oluşturma

Aşağıdaki kodile bir *Pages/About.cshtml.cs* dosyası oluşturun:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

LINQ deyimi öğrenci varlıklarını kayıt tarihine göre gruplandırıyor, her gruptaki varlık sayısını hesaplar `EnrollmentDateGroup` ve sonuçları görünüm modeli nesneleri koleksiyonunda saklar.

Uygulamayı çalıştırın ve Hakkında sayfasına gidin. Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.

![Sayfa hakkında](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki öğreticide, uygulama veri modelini güncelleştirmek için geçişleri kullanır.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/crud)
> [Sonraki öğretici](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, sıralama, filtreleme, gruplandırma ve sayfalama, işlevsellik eklenir.

Aşağıdaki resimde tamamlanmış bir sayfa gösterilmektedir. Sütun başlıkları sütunu sıralamak için tıklanabilir bağlantılardır. Bir sütun başlığını tıklatmak, artan ve azalan sıralama sırası arasında sürekli geçiş ler.

![Öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

Çözemediğiniz sorunlarla karşılaştıysanız, tamamlanan [uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

## <a name="add-sorting-to-the-index-page"></a>Dizin sayfasına sıralama ekleme

Sıralama parametrelerini içerecek şekilde *Öğrencilere/Index.cshtml.cs'ye* `PageModel` dizeleri ekleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

*Öğrenciler/Index.cshtml.cs'leri* `OnGetAsync` aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Önceki kod, URL'deki sorgu dizesinden bir `sortOrder` parametre alır. URL (sorgu dizesi dahil) [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
) tarafından oluşturulur

`sortOrder` Parametre ya "Ad" ya da "Tarih" olur. Parametre, `sortOrder` azalan sırayı belirtmek için isteğe bağlı olarak "_desc" tarafından izlenir. Varsayılan sıralama sırası yükseliyor.

**Öğrenciler** bağlantısından Dizin sayfası istendiğinde, sorgu dizesi yoktur. Öğrenciler soyadları ile artan sırada görüntülenir. Soyadına göre artan `switch` sıra, deyimdeki varsayılan (düşme örneği) olur. Kullanıcı bir sütun başlığı bağlantısını tıklattığında, sorgu dizesi değerinde uygun `sortOrder` değer sağlanır.

`NameSort`ve `DateSort` uygun sorgu dize değerleri ile sütun başlığı köprüler yapılandırmak için Razor Page tarafından kullanılır:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Aşağıdaki kod C# koşullu içerir [?: işleç](/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

İlk satır, boş veya `sortOrder` boş olduğunda `NameSort` "name_desc" olarak ayarlanır. `sortOrder` Null veya boş **değilse,** `NameSort` boş bir dize olarak ayarlanır.

Üçüncül `?: operator` işleç olarak da bilinir.

Bu iki deyim, sayfanın sütun başlığı köprülerini aşağıdaki gibi ayarlamasını sağlar:

| Geçerli sıralama sırası | Soyadı Köprü | Tarih Köprü |
|:--------------------:|:-------------------:|:--------------:|
| Artan Soyadı | descending        | ascending      |
| Soyadı azalan | ascending           | ascending      |
| Artan tarih       | ascending           | descending     |
| Azalan tarih      | ascending           | ascending      |

Yöntem, sıralamak için sütun belirtmek için Varlıklar LINQ kullanır. Kod, anahtar bildiriminden önce bir `IQueryable<Student>` parola yılaştırır ve anahtar deyiminde değiştirir:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Bir`IQueryable` oluşturulduğunda veya değiştirildiğinde, veritabanına sorgu gönderilmez. `IQueryable` Nesne koleksiyona dönüştürülene kadar sorgu yürütülmez. `IQueryable`gibi bir yöntem çağırılarak koleksiyona `ToListAsync`dönüştürülür. Bu nedenle, `IQueryable` kod aşağıdaki ifadeye kadar yürütülmeyen tek bir sorguyla sonuçlanır:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`çok sayıda sıralanabilir sütun ile ayrıntılı alabilirsiniz.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Öğrenci Dizini sayfasına sütun başlığı köprüleri ekleme

*Öğrenciler/Index.cshtml'deki*kodu aşağıdaki vurgulanmış kodla değiştirin:

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Yukarıdaki kod:

* Ve `LastName` `EnrollmentDate` sütun başlıklarına köprüler ekler.
* Geçerli sıralama `NameSort` sırası `DateSort` değerleriyle köprüler kurmak ve bilgileri kullanır.

Bu sıralamanın işe yaradığını doğrulamak için:

* Uygulamayı çalıştırın ve **Öğrenciler** sekmesini seçin.
* **Soyadı'nı**tıklatın.
* **Kayıt Tarihi'ni**tıklatın.

Kodu daha iyi anlamak için:

* *Öğrenciler/Index.cshtml.cs*olarak , bir `switch (sortOrder)`kesme noktası ayarlayın .
* Için `NameSort` bir saat `DateSort`ekleyin ve .
* *Öğrenciler/Index.cshtml'de*bir kesme `@Html.DisplayNameFor(model => model.Student[0].LastName)`noktası ayarlayın.

Hata ayıklamadan geç.

## <a name="add-a-search-box-to-the-students-index-page"></a>Öğrenci Dizini sayfasına Arama Kutusu Ekleme

Öğrenci Dizini sayfasına filtreleme eklemek için:

* Razor Page'e bir metin kutusu ve gönderme düğmesi eklenir. Metin kutusu, ad veya soyad üzerinde bir arama dizesi sağlar.
* Sayfa modeli metin kutusu değerini kullanmak için güncelleştirilir.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dizin yöntemine filtreleme işlevi ekleme

*Öğrenciler/Index.cshtml.cs'leri* `OnGetAsync` aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Yukarıdaki kod:

* `OnGetAsync` Yönteme `searchString` parametre ekler. Arama dizesi değeri, bir sonraki bölüme eklenen bir metin kutusundan alınır.
* LINQ deyimine bir `Where` yan tümce eklendi. Yan `Where` tümce yalnızca ad veya soyad arama dizesini içeren öğrencileri seçer. LINQ deyimi yalnızca aranacak bir değer varsa yürütülür.

Not: Önceki kod bir `Where` `IQueryable` nesne üzerinde yöntemi çağırır ve filtre sunucuda işlenir. Bazı senaryolarda, uygulama `Where` yöntemi bellek içi bir koleksiyonda uzantı yöntemi olarak adlandırıyor olabilir. Örneğin, EF `_context.Students` Core'dan `DbSet` koleksiyonu `IEnumerable` döndüren bir depo yöntemine yapılan değişiklikleri varsayalım. Sonuç normalde aynı olurdu ama bazı durumlarda farklı olabilir.

Örneğin, .NET Framework uygulaması `Contains` varsayılan olarak büyük/küçük harf duyarlı bir karşılaştırma gerçekleştirir. SQL Server'da büyük/küçük harf duyarlılığı, `Contains` SQL Server örneğinin harmanlama ayarı ile belirlenir. SQL Server varsayılan olarak büyük/küçük harf duyarsızı olarak karşıdır. `ToUpper`testin açıkça büyük/küçük harf duyarsız yapmak için çağrılabilir:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Önceki kod, kod kullanılacak `IEnumerable`şekilde değişirse sonuçların büyük/küçük harf duyarsız olmasını sağlar. Bir `Contains` `IEnumerable` koleksiyon çağrıldığında ,.NET Core uygulaması kullanılır. Bir `Contains` `IQueryable` nesneye çağrıldığında, veritabanı uygulaması kullanılır. Bir depodan bir `IEnumerable` geri dönmek önemli bir performans cezası olabilir:

1. Tüm satırlar DB sunucusundan döndürülür.
1. Filtre, uygulamadaki tüm döndürülen satırlara uygulanır.

Aramanın `ToUpper`bir performans cezası var. Kod, `ToUpper` TSQL SELECT deyiminin WHERE yan tümcesi bir işlev ekler. Eklenen işlev en iyi duruma getiricinin dizin kullanmasını engeller. SQL'in büyük/küçük harf duyarsız olarak yüklendiğinden, `ToUpper` gerekli olmadığında çağrıdan kaçınmak en iyisidir.

### <a name="add-a-search-box-to-the-student-index-page"></a>Öğrenci Dizini sayfasına Arama Kutusu Ekleme

*Sayfalar/Öğrenciler/Index.cshtml'de,* **Arama** düğmesi ve çeşitli krom oluşturmak için aşağıdaki vurgulanan kodu ekleyin.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Önceki kod, arama `<form>` metin kutusunu ve düğmesini eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır. Varsayılan olarak, `<form>` etiket yardımcısı form verilerini POST ile gönderir. POST ile parametreler URL'de değil, HTTP ileti gövdesinde geçirilir. HTTP GET kullanıldığında, form verileri URL'de sorgu dizeleri olarak geçirilir. Verileri sorgu dizeleriyle aktarmak, kullanıcıların URL'yi yer imi eklemesine olanak tanır. [W3C yönergeleri,](https://www.w3.org/2001/tag/doc/whenToUseGet.html) eylem bir güncelleştirmeyle sonuçlanmadığında GET'in kullanılmasını önerir.

Uygulamayı test edin:

* **Öğrenciler** sekmesini seçin ve bir arama dizesi girin.
* **Ara'yı**seçin.

URL'nin arama dizesini içerdiğine dikkat edin.

```html
http://localhost:5000/Students?SearchString=an
```

Sayfa yer imi varsa, yer imi sayfanın `SearchString` URL'sini ve sorgu dizesini içerir. Etiketteki `method="get"` `form` sorgu dizesi oluşturulmasına neden olan şeydir.

Şu anda, bir sütun başlığı sıralama bağlantısı seçildiğinde, **Arama** kutusundaki filtre değeri kaybolur. Kaybolan filtre değeri sonraki bölümde sabitlenir.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Öğrenci Dizini sayfasına sayfalama işlevi ekleme

Bu bölümde, `PaginatedList` sayfalama yı desteklemek için bir sınıf oluşturulur. Sınıf, `PaginatedList` `Skip` tablonun tüm satırlarını almak yerine sunucudaki verileri filtrelemek için kullanır ve `Take` deyimler kullanır. Aşağıdaki resimde sayfalama düğmeleri gösterilmektedir.

![Sayfalama bağlantıları olan öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

Proje klasöründe, `PaginatedList.cs` aşağıdaki kodla oluşturun:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

Önceki `CreateAsync` koddaki yöntem sayfa boyutunu ve sayfa numarasını alır `Skip` `Take` ve uygun `IQueryable`ve ifadeleri . `IQueryable`Çağrıldığında, `ToListAsync` yalnızca istenen sayfayı içeren bir Liste döndürür. Özellikler `HasPreviousPage` ve `HasNextPage` **Önceki** ve **Sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılır.

Yöntem `CreateAsync` oluşturmak için `PaginatedList<T>`kullanılır. Bir oluşturucu nesneyi `PaginatedList<T>` oluşturamaz, yapıcılar eşzamanlı kodu çalıştıramaz.

## <a name="add-paging-functionality-to-the-index-method"></a>Dizin yöntemine sayfalama işlevi ekleme

*Öğrenciler/Index.cshtml.cs*olarak , `Student` gelen `IList<Student>` türü `PaginatedList<Student>`güncelleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

*Öğrenciler/Index.cshtml.cs'leri* `OnGetAsync` aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Önceki kod sayfa dizini, geçerli `sortOrder`ve `currentFilter` yöntem imzasını ekler.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Aşağıdaki zaman tüm parametreler null:

* Sayfa **Öğrenciler** bağlantısından çağrılır.
* Kullanıcı bir sayfalama veya sıralama bağlantısını tıklatmadı.

Bir sayfalama bağlantısı tıklandığında, sayfa dizini değişkeni görüntülenecek sayfa numarasını içerir.

`CurrentSort`Geçerli sıralama sırasını Razor Page sağlar. Geçerli sıralama sırası, çağrı sırasında sıralama sırasını tutmak için çağrı bağlantılarına eklenmelidir.

`CurrentFilter`geçerli filtre dizesini ile Razor Page sağlar. Değer: `CurrentFilter`

* Sayfalama sırasında filtre ayarlarını korumak için sayfalama bağlantılarına dahil edilmelidir.
* Sayfa yeniden görüntülendiğinde metin kutusuna geri yüklenmelidir.

Arama dizesi çağrı sırasında değiştirilirse, sayfa 1 olarak sıfırlanır. Yeni filtre farklı verilerin görüntülenmesine neden olabileceğinden sayfanın 1 olarak sıfırlanması gerekir. Bir arama değeri girildiğinde ve **Gönder** seçildiğinde:

* Arama dizesi değiştirildi.
* Parametre `searchString` null değil.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

Yöntem, `PaginatedList.CreateAsync` öğrenci sorgusunu, çağrıyı destekleyen bir koleksiyon türündeki tek bir öğrenci sayfasına dönüştürür. Öğrencilerin tek sayfası Jilet Sayfası'na aktarılır.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

İki soru işareti `PaginatedList.CreateAsync` [null-coalescing operatör](/dotnet/csharp/language-reference/operators/null-conditional-operator)temsil eder. Null-coalescing işleci nullable türü için varsayılan bir değer tanımlar. İfade, `(pageIndex ?? 1)` bir değeri `pageIndex` varsa değerini döndürme anlamına gelir. Bir `pageIndex` değeri yoksa, 1 döndürün.

## <a name="add-paging-links-to-the-student-razor-page"></a>Öğrenci Razor Page'e sayfalama bağlantıları ekleyin

*Öğrenciler/Index.cshtml'deki*biçimlendirmeyi güncelleştirin. Değişiklikler vurgulanır:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Sütun üstbilgi bağlantıları, kullanıcının filtre sonuçları içinde `OnGetAsync` sıralayabilmeleri için geçerli arama dizesini yönteme geçirmek için sorgu dizesini kullanır:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.

* Sayfalamanın çalıştığından emin olmak için, farklı sıralama siparişlerinde sayfalama bağlantılarını tıklatın.
* Arama nın sıralama ve filtreleme yle doğru çalıştığını doğrulamak için bir arama dizesi girin ve aramayı deneyin.

![sayfalama bağlantıları ile öğrenci indeks sayfası](sort-filter-page/_static/paging.png)

Kodu daha iyi anlamak için:

* *Öğrenciler/Index.cshtml.cs*olarak , bir `switch (sortOrder)`kesme noktası ayarlayın .
* Için bir `NameSort`saat `DateSort` `CurrentSort`ekle `Model.Student.PageIndex`, , , ve .
* *Öğrenciler/Index.cshtml'de*bir kesme `@Html.DisplayNameFor(model => model.Student[0].LastName)`noktası ayarlayın.

Hata ayıklamadan geç.

## <a name="update-the-about-page-to-show-student-statistics"></a>Öğrenci istatistiklerini göstermek için Hakkında sayfasını güncelleştirin

Bu adımda, *Pages/About.cshtml* her kayıt tarihi için kaç öğrencinin kaydolduğunu görüntülemek üzere güncelleştirilir. Güncelleştirme gruplandırmayı kullanır ve aşağıdaki adımları içerir:

* **Hakkında** Sayfa tarafından kullanılan veriler için bir görünüm modeli oluşturun.
* Görünüm modelini kullanmak için Hakkında sayfasını güncelleştirin.

### <a name="create-the-view-model"></a>Görünüm modelini oluşturma

*Modeller* klasöründe bir *SchoolViewModels* klasörü oluşturun.

*SchoolViewModels* klasörüne, aşağıdaki kodla birlikte bir *EnrollmentDateGroup.cs* ekleyin:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Hakkında sayfa modelini güncelleştir

ASP.NET Core 2.2'deki web şablonları Hakkında sayfasını içermez. Core 2.2ASP.NET kullanıyorsanız, Hakkında Jilet Sayfası oluşturun.

*Pages/About.cshtml.cs* dosyasını aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

LINQ deyimi öğrenci varlıklarını kayıt tarihine göre gruplandırıyor, her gruptaki varlık sayısını hesaplar `EnrollmentDateGroup` ve sonuçları görünüm modeli nesneleri koleksiyonunda saklar.

### <a name="modify-the-about-razor-page"></a>Jilet Hakkında Sayfayı Değiştir

*Pages/About.cshtml* dosyasındaki kodu aşağıdaki kodla değiştirin:

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

Uygulamayı çalıştırın ve Hakkında sayfasına gidin. Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.

Çözemediğiniz sorunlarla karşılaştıysanız, bu [aşama için tamamlanan uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting)indirin.

![Sayfa hakkında](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Ek kaynaklar

* [Core 2.x kaynak ASP.NET hata ayıklama](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=MDs7PFpoMqI)

Bir sonraki öğreticide, uygulama veri modelini güncelleştirmek için geçişleri kullanır.

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/crud)
> [Sonraki](xref:data/ef-rp/migrations)

::: moniker-end

