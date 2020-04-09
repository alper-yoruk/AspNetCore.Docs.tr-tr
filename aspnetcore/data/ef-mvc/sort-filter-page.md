---
title: 'Öğretici: Sıralama ekleme, filtreleme ve sayfalama - EF Core ile MVC ASP.NET'
description: Bu öğreticide Öğrenci Dizini sayfasına sıralama, filtreleme ve sayfalama işlevleri eklersiniz. Ayrıca, basit gruplandırma yapan bir sayfa da oluşturursunuz.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 99bf9ed59b47e8fbba838b97c3e032b9808f6a94
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657138"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Öğretici: Sıralama ekleme, filtreleme ve sayfalama - EF Core ile MVC ASP.NET

Önceki öğreticide, Öğrenci kuruluşları için temel CRUD işlemleri için bir web sayfası kümesi uygulamışsınız. Bu öğreticide Öğrenci Dizini sayfasına sıralama, filtreleme ve sayfalama işlevleri eklersiniz. Ayrıca, basit gruplandırma yapan bir sayfa da oluşturursunuz.

Aşağıdaki resimde, sayfanız bittiğinde nasıl görüneceği gösterilmektedir. Sütun başlıkları, kullanıcının bu sütuna göre sıralamak için tıklatabileceği bağlantılardır. Bir sütun başlığını tıklatmak, artan ve azalan sıralama sırası arasında tekrar tekrar geçiş eder.

![Öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Sütun sıralama bağlantıları ekleme
> * Arama kutusu ekleme
> * Öğrenci Endeksine Sayfa ekleme
> * Dizin yöntemine sayfa ekleme
> * Sayfalama bağlantıları ekleme
> * Hakkında sayfa oluşturma

## <a name="prerequisites"></a>Ön koşullar

* [CRUD İşlevselliğini Uygulayın](crud.md)

## <a name="add-column-sort-links"></a>Sütun sıralama bağlantıları ekleme

Öğrenci Dizini sayfasına sıralama eklemek için, `Index` Öğrenci denetleyicisinin yöntemini değiştirir ve Öğrenci Dizini görünümüne kod eklersiniz.

### <a name="add-sorting-functionality-to-the-index-method"></a>Dizin yöntemine sıralama İşlevselliği ekleme

*StudentsController.cs*olarak, `Index` yöntemi aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Bu kod, `sortOrder` URL'deki sorgu dizesinden bir parametre alır. Sorgu dize değeri, ASP.NET Core MVC tarafından eylem yönteminin parametresi olarak sağlanır. Parametre, isteğe bağlı olarak bir alt alt alt sayı ve azalan sırayı belirtmek için "desc" dizesi tarafından izlenen "Ad" veya "Tarih" dizesi olacaktır. Varsayılan sıralama sırası yükseliyor.

Dizin sayfası ilk kez istendiğinde, sorgu dizesi yoktur. Öğrenciler, `switch` ekstredeki düşme durumunda belirlenen varsayılan değer olan soyadına göre artan sırada görüntülenirler. Kullanıcı bir sütun başlığı köprü tıklattığında, `sortOrder` uygun değer sorgu dizesinde sağlanır.

İki `ViewData` öğe (NameSortParm ve DateSortParm) uygun sorgu dize değerleri ile sütun başlığı köprüler yapılandırmak için görünüm tarafından kullanılır.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Bunlar üçüncül ifadeler. İlkinde, parametre boş `sortOrder` veya boşsa, NameSortParm'ın "name_desc" olarak ayarlanılması gerektiği belirtilir; aksi takdirde, boş bir dize olarak ayarlanmalıdır. Bu iki deyim, görünümün sütun başlığı köprülerini aşağıdaki gibi ayarlamasını sağlar:

|  Geçerli sıralama sırası  | Soyadı Köprü | Tarih Köprü |
|:--------------------:|:-------------------:|:--------------:|
| Artan Soyadı  | descending          | ascending      |
| Soyadı azalan | ascending           | ascending      |
| Artan tarih       | ascending           | descending     |
| Azalan tarih      | ascending           | ascending      |

Yöntem, sıralamak için sütun belirtmek için Varlıklar LINQ kullanır. Kod, anahtar `IQueryable` deyiminden önce bir değişken oluşturur, anahtar deyiminde değiştirir `ToListAsync` ve `switch` deyiminden sonra yöntemi çağırır. Değişkenler oluşturup `IQueryable` değiştirdiğinizde, veritabanına sorgu gönderilmez. Sorgu, `IQueryable` `ToListAsync`nesneyi koleksiyona dönüştürene kadar yürütülmez. Bu nedenle, bu kod `return View` deyimi kadar yürütülmeyen tek bir sorgu ile sonuçlanır.

Bu kod, çok sayıda sütunla ayrıntılı olabilir. [Bu serinin son öğretici](advanced.md#dynamic-linq) bir `OrderBy` dize değişkeninde sütunun adını geçmenizi sağlayan kod yazmak için nasıl gösterir.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Öğrenci Dizini görünümüne sütun başlığı köprüleri ekleme

*Görünümler/Öğrenciler/Index.cshtml'deki*kodu sütun başlığı köprüleri eklemek için aşağıdaki kodla değiştirin. Değiştirilen satırlar vurgulanır.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Bu kod, uygun `ViewData` sorgu dize değerleri ile köprüler kurmak için özellikleri bilgileri kullanır.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve sıralamanın işe yaradığını doğrulamak için **Soyadı** ve **Kayıt Tarihi** sütunbaşlıklarını tıklatın.

![Ad sırasına göre öğrenci dizini sayfası](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Arama kutusu ekleme

Öğrenci Dizini sayfasına filtreleme eklemek için, görünüme bir metin kutusu ve gönder düğmesi `Index` ekler ve yöntemde ilgili değişiklikler yaparsınız. Metin kutusu, ad ve soyad alanlarında aramak için bir dize girmenizi sağlar.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dizin yöntemine filtreleme işlevi ekleme

*StudentsController.cs,* `Index` yöntemi aşağıdaki kodla değiştirin (değişiklikler vurgulanır).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Yönteme bir `searchString` parametre eklediniz. `Index` Arama dizesi değeri, Dizin görünümüne ekleyeceğiniz bir metin kutusundan alınır. Ayrıca LINQ deyimine yalnızca ad veya soyad arama dizesini içeren öğrencileri seçen bir yan tümce eklediniz. Nerede yan tümcesinin yürütüldüğü yalnızca aranacak bir değer varsa ekleyen deyim.

> [!NOTE]
> Burada bir `IQueryable` nesne `Where` üzerinde yöntemi çağırıyorsunuz ve filtre sunucuda işlenir. Bazı `Where` senaryolarda, yöntemi bellek içi bir koleksiyonda uzantı yöntemi olarak adlandırıyor olabilirsiniz. (Örneğin, başvuruyu, bir `_context.Students` EF `DbSet` yerine `IEnumerable` bir koleksiyonu döndüren bir depo yöntemine başvurur şekilde değiştirdiğinizi varsayalım.) Sonuç normalde aynı olurdu ama bazı durumlarda farklı olabilir.
>
>Örneğin, yöntemin `Contains` .NET Framework uygulaması varsayılan olarak büyük/küçük harf duyarlı bir karşılaştırma gerçekleştirir, ancak SQL Server'da bu, SQL Server örneğinin harmanlama ayarı tarafından belirlenir. Bu ayar varsayılan olarak büyük/küçük harf duyarsızı dır. Testi açıkça `ToUpper` büyük/küçük harf duyarsız yapmak için yöntemi arayabilirsiniz: *Where(s => s.LastName.ToUpper(). Içerir(searchString.ToUpper())*. Bu, kodu daha sonra `IEnumerable` `IQueryable` nesne yerine bir koleksiyon döndüren bir depo kullanmak üzere değiştirirseniz sonuçların aynı kalmasını sağlar. (Bir `Contains` `IEnumerable` koleksiyondaki yöntemi aradiğinizde,.NET Framework uygulamasını alırsınız; bir `IQueryable` nesne üzerinde aradiğinizde veritabanı sağlayıcısı uygulamasını alırsınız.) Ancak, bu çözüm için bir performans cezası var. Kod, `ToUpper` TSQL SELECT deyiminin WHERE yan tümcesi'ne bir işlev koyar. Bu, en iyi duruma getiricinin dizin kullanmasını engeller. SQL'in çoğunlukla büyük/küçük harf duyarlı olarak yüklendiğinden, `ToUpper` büyük/küçük harf duyarlı bir veri deposuna geçiş yapana kadar koddan kaçınmak en iyisidir.

### <a name="add-a-search-box-to-the-student-index-view"></a>Öğrenci Dizini Görünümüne Arama Kutusu Ekleme

*Görünümler/Öğrenci/Index.cshtml'de,* resim yazısı, metin kutusu ve **Arama** düğmesi oluşturmak için vurgulanan kodu açılış tablosu etiketinden hemen önce ekleyin.

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Bu kod, `<form>` arama metin kutusunu ve düğmesini eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır. Varsayılan olarak, `<form>` etiket yardımcısı form verilerini BIR POST ile gönderir, bu da parametrelerin SORGU dizeleri olarak URL'de değil, HTTP ileti gövdesinde geçirildiği anlamına gelir. HTTP GET'i belirttiğiniz zaman, form verileri URL'ye sorgu dizeleri olarak aktarılır ve bu da kullanıcıların URL'yi yer imi ne kadar işaretlemelerine olanak tanır. W3C yönergeleri, eylem bir güncelleştirmeyle sonuçlanmadığında GET'i kullanmanızı önerir.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin, bir arama dizesi girin ve filtrelemenin çalıştığını doğrulamak için Ara'yı tıklatın.

![Filtreleme ile öğrenci dizini sayfası](sort-filter-page/_static/filtering.png)

URL'nin arama dizesini içerdiğine dikkat edin.

```html
http://localhost:5813/Students?SearchString=an
```

Bu sayfayı yer imi ederseniz, yer imi kullandığınızda filtrelenmiş listeyi alırsınız. Etikete `method="get"` `form` ekleme, sorgu dizesinin oluşturulmasına neden olan şeydir.

Bu aşamada, bir sütun başlığı sıralama bağlantısını tıklattığınızda, **Arama** kutusuna girdiğiniz filtre değerini kaybedersiniz. Bunu bir sonraki bölümde düzelteceksin.

## <a name="add-paging-to-students-index"></a>Öğrenci Endeksine Sayfa ekleme

Öğrenci Dizini sayfasına sayfa ekleme eklemek için, tablonun tüm satırlarını her zaman almak yerine sunucudaki verileri filtrelemek için kullanan `PaginatedList` `Skip` ve `Take` ifade veren bir sınıf oluşturursunuz. Ardından `Index` yöntemde ek değişiklikler yapar ve görünüme sayfalama `Index` düğmeleri eklersiniz. Aşağıdaki resimde sayfalama düğmeleri gösterilmektedir.

![Sayfalama bağlantıları olan öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

Proje klasöründe, `PaginatedList.cs`şablon kodunu aşağıdaki kodla oluşturun ve değiştirin.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

Bu `CreateAsync` koddaki yöntem sayfa boyutunu ve sayfa numarasını `Skip` `Take` alır ve `IQueryable`uygun ve ifadeleri uygular. `IQueryable`Çağrıldığında, `ToListAsync` yalnızca istenen sayfayı içeren bir Liste döndürecektir. Özellikleri `HasPreviousPage` ve `HasNextPage` **önceki** ve **sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılabilir.

Oluşturucular eşzamanlı kodu `CreateAsync` çalıştıramadığından, `PaginatedList<T>` nesneyi oluşturmak için oluşturucu yerine bir yöntem kullanılır.

## <a name="add-paging-to-index-method"></a>Dizin yöntemine sayfa ekleme

*StudentsController.cs,* yöntemi `Index` aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Bu kod, yöntem imzasına bir sayfa numarası parametresi, geçerli sıralama sipariş parametresi ve geçerli filtre parametresi ekler.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Sayfa ilk kez görüntülendiğinde veya kullanıcı bir sayfalama veya sıralama bağlantısını tıklatmamışsa, tüm parametreler geçersiz olur.  Bir sayfalama bağlantısı tıklanırsa, sayfa değişkeni görüntülenecek sayfa numarasını içerir.

CurrentSort adlı `ViewData` öğe görünümü geçerli sıralama sırasıyla sağlar, çünkü bu, sıralama sırasını çağrıyaparken aynı tutmak için çağrı bağlantılarına dahil edilmelidir.

CurrentFilter adlı `ViewData` öğe, görünümü geçerli filtre dizesiyle sağlar. Sayfalama sırasında filtre ayarlarını korumak için bu değer sayfalama bağlantılarına eklenmelidir ve sayfa yeniden görüntülendiğinde metin kutusuna geri yüklenmelidir.

Arama dizesi sayfalama sırasında değiştirilirse, yeni filtre farklı verilerin görüntülenmesine neden olabileceğinden sayfanın 1 olarak sıfırlanması gerekir. Metin kutusuna bir değer girildiğinde ve Gönder düğmesine basıldığında arama dizesi değiştirilir. Bu durumda, `searchString` parametre null değildir.

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

`Index` Yöntemin sonunda, `PaginatedList.CreateAsync` yöntem öğrenci sorgusunu çağrılamayı destekleyen bir koleksiyon türündeki tek bir öğrenci sayfasına dönüştürür. Öğrencilerin bu tek sayfası daha sonra görünüme geçirilir.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

Yöntem `PaginatedList.CreateAsync` bir sayfa numarası alır. İki soru işareti null-coalescing operatör temsil eder. Null-coalescing işleci nullable türü için varsayılan bir değer tanımlar; ifade, `(pageNumber ?? 1)` bir değeri `pageNumber` varsa değerini döndürme veya null `pageNumber` ise 1'i döndürme anlamına gelir.

## <a name="add-paging-links"></a>Sayfalama bağlantıları ekleme

*Görünümler/Öğrenciler/Index.cshtml'de,* varolan kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Sayfanın üst kısmındaki deyim, `@model` görünümün artık nesne `PaginatedList<T>` `List<T>` yerine bir nesne aldığını belirtir.

Sütun üstbilgi bağlantıları, kullanıcının filtre sonuçları içinde sıralayabilmeleri için geçerli arama dizesini denetleyiciye geçirmek için sorgu dizesini kullanır:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Uygulamayı çalıştırın ve Öğrenciler sayfasına gidin.

![Sayfalama bağlantıları olan öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

Sayfalamanın çalıştığından emin olmak için farklı sıralama emirlerindeki çağrı bağlantılarını tıklatın. Ardından bir arama dizesi girin ve arama nın sıralama ve filtreleme ile de doğru çalıştığını doğrulamak için yeniden aramayı deneyin.

## <a name="create-an-about-page"></a>Hakkında sayfa oluşturma

Contoso Üniversitesi web sitesinin **Hakkında** sayfası için, her kayıt tarihi için kaç öğrencinin kaydolduğunu görüntülersiniz. Bu, gruplar üzerinde gruplandırma ve basit hesaplamalar gerektirir. Bunu başarmak için aşağıdakileri yaparsınız:

* Görünüme geçirmeniz gereken veriler için bir görünüm modeli sınıfı oluşturun.
* Giriş denetleyicisinde Hakkında yöntemini oluşturun.
* About görünümünü oluşturun.

### <a name="create-the-view-model"></a>Görünüm modelini oluşturma

*Modeller* klasöründe bir *SchoolViewModels* klasörü oluşturun.

Yeni klasörde, bir sınıf dosyası *EnrollmentDateGroup.cs* ekleyin ve şablon kodunu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Ev Denetleyicisini Değiştir

*HomeController.cs,* dosyanın üst kısmında aşağıdaki ifadeleri kullanarak ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Sınıf için kıvırcık ayraç açıldıktan hemen sonra veritabanı bağlamı için bir sınıf değişkeni ekleyin ve ASP.NET Core DI'den bağlamın bir örneğini alın:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Aşağıdaki `About` kodla bir yöntem ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

LINQ deyimi öğrenci varlıklarını kayıt tarihine göre gruplandırıyor, her gruptaki varlık sayısını hesaplar `EnrollmentDateGroup` ve sonuçları görünüm modeli nesneleri koleksiyonunda saklar.

### <a name="create-the-about-view"></a>Hakkında Görünüm Oluşturma

Aşağıdaki kodla *görünümler/ev/about.cshtml* dosyası ekleyin:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Uygulamayı çalıştırın ve Hakkında sayfasına gidin. Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Sütun sıralama bağlantıları eklendi
> * Arama kutusu eklendi
> * Öğrenci Endeksine Çağrı
> * Dizin yöntemine çağrı ekleme
> * Eklenen sayfalama bağlantıları
> * Hakkında sayfa oluşturma

Geçişleri kullanarak veri modeli değişikliklerini nasıl işleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sonraki: Veri modeli değişikliklerini işleme](migrations.md)
