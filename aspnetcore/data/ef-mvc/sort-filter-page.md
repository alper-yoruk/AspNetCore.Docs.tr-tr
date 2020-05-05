---
title: 'Öğretici: EF Core sıralama, filtreleme ve sayfalama-ASP.NET MVC ekleme'
description: Bu öğreticide, öğrenciler dizin sayfasına sıralama, filtreleme ve sayfalama işlevselliği ekleyeceksiniz. Ayrıca basit gruplandırma yapan bir sayfa oluşturacaksınız.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: d9cd3a74c35d531b5e8c91fc7f922b0cdf8e9558
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773516"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Öğretici: EF Core sıralama, filtreleme ve sayfalama-ASP.NET MVC ekleme

Önceki öğreticide, öğrenci varlıkları için temel CRUD işlemleri için bir dizi Web sayfası uyguladık. Bu öğreticide, öğrenciler dizin sayfasına sıralama, filtreleme ve sayfalama işlevselliği ekleyeceksiniz. Ayrıca basit gruplandırma yapan bir sayfa oluşturacaksınız.

Aşağıdaki çizimde, işiniz bittiğinde sayfanın nasıl görüneceği gösterilmektedir. Sütun başlıkları, kullanıcının sütuna göre sıralamak için tıkladığı bağlantılardır. Sütun başlığına tıklanması artan ve azalan sıralama düzeni arasında sürekli olarak geçiş yapar.

![Öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Sütun sıralama bağlantıları Ekle
> * Arama kutusu ekleme
> * Öğrenciler dizinine sayfalama ekleme
> * Dizin yöntemine sayfalama ekleme
> * Sayfalama bağlantıları Ekle
> * Hakkında bir sayfa oluşturun

## <a name="prerequisites"></a>Önkoşullar

* [CRUD Işlevlerini uygulama](crud.md)

## <a name="add-column-sort-links"></a>Sütun sıralama bağlantıları Ekle

Öğrenci dizini sayfasına sıralama eklemek için, öğrenciler denetleyicisinin `Index` yöntemini değiştirecek ve öğrenci dizini görünümüne kod ekleyeceksiniz.

### <a name="add-sorting-functionality-to-the-index-method"></a>Dizin yöntemine sıralama Işlevi ekleme

*StudentsController.cs*içinde, `Index` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Bu kod, URL `sortOrder` 'deki sorgu dizesinden bir parametre alır. Sorgu dizesi değeri, ASP.NET Core MVC tarafından eylem yöntemine bir parametre olarak sağlanır. Parametresi, "Name" veya "Date" adlı bir dize, isteğe bağlı olarak bir alt çizgi ve azalan sıralama belirtmek için "desc" dizesi olacaktır. Varsayılan sıralama düzeni artan.

Dizin sayfası ilk kez istendiğinde sorgu dizesi yoktur. Öğrenciler, son ada göre artan sırada görüntülenir, bu, `switch` deyimdeki en karşı durum ile oluşturulan varsayılandır. Kullanıcı bir sütun başlığı köprüsüne tıkladığında, sorgu dizesinde uygun `sortOrder` değer sağlanır.

İki `ViewData` öğe (namesortpara ve datesortpard), sütun başlığı köprülerini uygun sorgu dizesi değerleriyle yapılandırmak için görünüm tarafından kullanılır.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Bunlar Üçlü ifadelerdir. İlki `sortOrder` parametre null veya boş Ise, Namesortparı 'nin "name_desc" olarak ayarlanması gerektiğini belirtir; Aksi takdirde, boş bir dizeye ayarlanmalıdır. Bu iki deyim, görünümün sütun başlığı köprülerini şu şekilde ayarlamaya olanak tanır:

|  Geçerli sıralama düzeni  | Son ad Köprüsü | Tarih Köprüsü |
|:--------------------:|:-------------------:|:--------------:|
| Artan son ad  | descending          | ascending      |
| Azalan son ad | ascending           | ascending      |
| Artan Tarih       | ascending           | descending     |
| Azalan Tarih      | ascending           | ascending      |

Yöntemi, sıralama yapılacak sütunu belirtmek için LINQ to Entities kullanır. Kod, Switch ifadesinden `IQueryable` önce bir değişken oluşturur, Switch ifadesinde değiştirir ve `ToListAsync` `switch` deyimden sonra yöntemi çağırır. Değişkenleri oluşturup değiştirirken `IQueryable` veritabanına hiçbir sorgu gönderilmez. Sorgu, gibi `IQueryable` `ToListAsync`bir yöntemi çağırarak nesne bir koleksiyona dönüştürülene kadar yürütülmez. Bu nedenle, bu kod, `return View` ifadeye kadar Yürütülmeyen tek bir sorgu ile sonuçlanır.

Bu kod çok sayıda sütunla ayrıntı alabilir. [Bu serideki son öğretici,](advanced.md#dynamic-linq) bir dize değişkeninde `OrderBy` sütunun adını geçirmenize olanak sağlayan kodun nasıl yazılacağını gösterir.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Öğrenci dizini görünümüne sütun başlığı köprüleri ekleme

*Görünümler/öğrenciler/Index. cshtml*içindeki kodu, sütun başlığı köprüleri eklemek için aşağıdaki kodla değiştirin. Değiştirilen çizgiler vurgulanır.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Bu kod, uygun sorgu dizesi `ViewData` değerleriyle köprüler ayarlamak için özelliklerindeki bilgileri kullanır.

Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin ve sıralamanın çalıştığını doğrulamak Için **son ad** ve **kayıt tarihi** sütun başlıklarına tıklayın.

![Ad düzeninde öğrenciler Dizin sayfası](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Arama kutusu ekleme

Öğrenciler dizin sayfasına filtre eklemek için, görünüme bir metin kutusu ve Gönder düğmesi ekleyeceksiniz ve `Index` yöntemde ilgili değişiklikleri yapmanız gerekir. Metin kutusu, ilk adı ve soyadı alanlarını aramak için bir dize girmenizi sağlar.

### <a name="add-filtering-functionality-to-the-index-method"></a>Dizin yöntemine filtreleme işlevi ekleme

*StudentsController.cs*içinde, `Index` yöntemini aşağıdaki kodla değiştirin (değişiklikler vurgulanır).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

`Index` Yöntemine bir `searchString` parametre eklediniz. Arama dizesi değeri, dizin görünümüne ekleyeceğiniz bir metin kutusundan alınır. Ayrıca, yalnızca adı veya soyadı arama dizesini içeren öğrencileri seçen bir where yan tümcesine LINQ deyimi de eklediniz. WHERE yan tümcesini ekleyen deyimi, yalnızca aranacak bir değer varsa yürütülür.

> [!NOTE]
> Burada `Where` yöntemi bir `IQueryable` nesne üzerinde arıyorsanız ve filtrenin sunucuda işlenmesi gerekir. Bazı senaryolarda, `Where` bir bellek içi koleksiyonda yöntemi bir genişletme yöntemi olarak çağırmak isteyebilirsiniz. (Örneğin, başvurusunu, bir `_context.Students` `DbSet` `IEnumerable` koleksiyon döndüren bir depo yöntemine başvurduğu bir değer yerine, olarak değiştirdiğinizi varsayın.) Sonuç normalde aynı olur, ancak bazı durumlarda farklı olabilir.
>
>Örneğin, `Contains` yöntemi .NET Framework uygulama varsayılan olarak büyük/küçük harfe duyarlı bir karşılaştırma gerçekleştirir, ancak SQL Server bu, SQL Server örneğinin harmanlama ayarı tarafından belirlenir. Bu ayar varsayılan olarak büyük/küçük harfe duyarsız olur. Testi açık büyük/ `ToUpper` küçük harfe duyarsız yapmak için yöntemini çağırabilirsiniz: *burada (s => s. LastName. ToUpper (). Contains (searchString. ToUpper ())*. Bu, kodu daha sonra bir `IEnumerable` `IQueryable` nesne yerine bir koleksiyon döndüren depoyu kullanacak şekilde değiştirirseniz sonuçların aynı kalmasını sağlar. (Bir `Contains` `IEnumerable` koleksiyonda yöntemini çağırdığınızda .NET Framework uygulamasını alırsınız; bir `IQueryable` nesne üzerinde çağırdığınızda, veritabanı sağlayıcısı uygulamasını alırsınız.) Ancak, bu çözüm için bir performans cezası vardır. `ToUpper` Kod, TSQL SELECT ifadesinin WHERE yan tümcesine bir işlev koyar. Bu, iyileştiricinin bir dizin kullanmasını engelleyecek. SQL 'in çoğu büyük küçük harfe duyarsız olarak yüklendiği için, büyük/küçük harfe duyarlı bir `ToUpper` veri deposuna geçiş yapılıncaya kadar koddan kaçınmak en iyisidir.

### <a name="add-a-search-box-to-the-student-index-view"></a>Öğrenci dizini görünümüne arama kutusu ekleme

*Görünümler/öğrenci/Index. cshtml*'de, bir başlık, metin kutusu ve bir **arama** düğmesi oluşturmak için, açılan tablo etiketinden hemen önce vurgulanan kodu ekleyin.

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Bu kod, arama `<form>` metin kutusu ve düğme eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır. Varsayılan olarak, `<form>` etiket Yardımcısı form VERILERINI bir gönderiyle gönderir, yani Parametreler, URL 'de sorgu dizeleri olarak değil http ileti gövdesinde geçirilir. HTTP GET belirttiğinizde, form verileri URL 'ye sorgu dizeleri olarak geçirilir ve bu da kullanıcıların URL 'ye yer işareti eklemesini sağlar. W3C yönergeleri, eylem bir güncelleştirme ile sonuçlanmazsa Al ' ın kullanılması önerilir.

Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin, bir arama dizesi girin ve filtreleme 'nin çalıştığını doğrulamak için ara ' ya tıklayın.

![Bir filtreleme ile öğrenciler Dizin sayfası](sort-filter-page/_static/filtering.png)

URL 'nin arama dizesini içerdiğine dikkat edin.

```html
http://localhost:5813/Students?SearchString=an
```

Bu sayfaya yer işareti eklerseniz, yer işaretini kullandığınızda filtrelenmiş listeyi alırsınız. `form` Etiketine ekleme `method="get"` sorgu dizesinin oluşturulmasına neden olur.

Bu aşamada, bir sütun başlığı sıralama bağlantısını tıklatırsanız, **arama** kutusuna girdiğiniz filtre değerini kaybedersiniz. Sonraki bölümde bunu çözeceksiniz.

## <a name="add-paging-to-students-index"></a>Öğrenciler dizinine sayfalama ekleme

Öğrenciler dizin sayfasına sayfalama eklemek için, ve deyimlerini kullanarak, tablodaki verileri `PaginatedList` her zaman almak `Skip` yerine `Take` , bu verileri filtrelemek için ve deyimleri kullanan bir sınıf oluşturacaksınız. Daha sonra, `Index` yöntemde ek değişiklikler yapar ve `Index` görünüme sayfalama düğmeleri eklersiniz. Aşağıdaki çizimde sayfalama düğmeleri gösterilmektedir.

![Sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

Proje klasöründe, şablon kodunu oluşturun `PaginatedList.cs`ve aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

Bu `CreateAsync` koddaki yöntemi sayfa boyutunu ve sayfa numarasını alır ve ' a uygun `Skip` ve `Take` deyimlerini uygular. `IQueryable` `ToListAsync` Üzerinde çağrıldığında `IQueryable`, yalnızca Istenen sayfayı içeren bir liste döndürür. Özellikler `HasPreviousPage` ve `HasNextPage` **önceki** ve **sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılabilir.

Oluşturucular `CreateAsync` zaman uyumsuz kod çalıştıramadığından, `PaginatedList<T>` nesneyi oluşturmak için bir Oluşturucu yerine bir yöntem kullanılır.

## <a name="add-paging-to-index-method"></a>Dizin yöntemine sayfalama ekleme

*StudentsController.cs*içinde, `Index` yöntemini aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Bu kod, bir sayfa numarası parametresi, geçerli bir sıralama düzeni parametresi ve Yöntem imzasına geçerli bir filtre parametresi ekler.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Sayfa ilk kez görüntülenirken veya Kullanıcı bir sayfalama veya sıralama bağlantısına tıklamamışsa, tüm parametreler null olur.  Bir sayfalama bağlantısına tıklandıysanız, sayfa değişkeni görüntülenecek sayfa numarasını içerir.

CurrentSort adlı `ViewData` öğe, sayfalama bağlantılarına dahil edilmesi gerektiğinden, bu sıralama düzeni geçerli sıralama düzeni ile birlikte görüntülenir.

CurrentFilter adlı `ViewData` öğe, geçerli filtre dizesiyle görünüm sağlar. Bu değer, disk belleği sırasında filtre ayarlarını korumak için disk belleği bağlantılarına dahil olmalıdır ve sayfa yeniden görüntülenirken metin kutusuna geri yüklenmesi gerekir.

Arama dizesi sayfalama sırasında değiştirilmişse, yeni filtre farklı verilerin görüntülenmesini sağladığından sayfanın 1 olarak sıfırlanması gerekir. Metin kutusuna bir değer girildiğinde ve Gönder düğmesine basıldığında arama dizesi değişir. Bu durumda, `searchString` parametre null değil.

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

`Index` Yöntemin sonunda, `PaginatedList.CreateAsync` yöntemi öğrenci sorgusunu, sayfalama destekleyen bir koleksiyon türünde tek bir öğrenciye dönüştürür. Bu tek bir öğrenci sayfası daha sonra görünüme geçirilir.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

`PaginatedList.CreateAsync` Yöntemi bir sayfa numarası alır. İki soru işareti, null birleşim işlecini temsil eder. Null birleşim işleci, Nullable bir tür için varsayılan değeri tanımlar; ifade `(pageNumber ?? 1)` bir değer `pageNumber` içeriyorsa değerini döndürür veya null ise 1 `pageNumber` döndürür.

## <a name="add-paging-links"></a>Sayfalama bağlantıları Ekle

*Görünümler/öğrenciler/Index. cshtml*'de, mevcut kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Sayfanın `@model` üst kısmındaki ifade, görünümün artık `PaginatedList<T>` `List<T>` nesne yerine bir nesne aldığından emin olarak belirtir.

Sütun üst bilgisi bağlantıları, kullanıcının filtre sonuçları içinde sıralama yapabilmesi için geçerli arama dizesini denetleyiciye geçirmek için sorgu dizesini kullanır:

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

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.

![Sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

Disk belleğinin çalıştığından emin olmak için farklı sıralama emirlerindeki disk belleği bağlantılarına tıklayın. Daha sonra bir arama dizesi girin ve sayfalama ve filtreleme ile doğru şekilde çalıştığını doğrulamak için sayfalama işlemi yeniden deneyin.

## <a name="create-an-about-page"></a>Hakkında bir sayfa oluşturun

Contoso Üniversitesi web sitesinin **hakkında** sayfasında, her bir kayıt tarihi için kaç öğrenciye kaydolduğunu görüntüleyeceksiniz. Bu, gruplar üzerinde gruplandırma ve basit hesaplamalar gerektirir. Bunu gerçekleştirmek için aşağıdakileri yapmanız gerekir:

* Görünüme geçirmeniz gereken veriler için bir görünüm modeli sınıfı oluşturun.
* Giriş denetleyicisinde hakkında yöntemini oluşturun.
* Hakkında görünümünü oluşturun.

### <a name="create-the-view-model"></a>Görünüm modeli oluşturma

*Modeller* klasöründe bir *SchoolViewModels* klasörü oluşturun.

Yeni klasörde, *EnrollmentDateGroup.cs* bir sınıf dosyası ekleyin ve şablon kodunu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Ana denetleyiciyi değiştirme

*HomeController.cs*' de, aşağıdaki using deyimlerini dosyanın en üstüne ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Sınıf için açma küme ayracından hemen sonra veritabanı bağlamı için bir sınıf değişkeni ekleyin ve ASP.NET Core DI 'den bağlamın bir örneğini alın:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Aşağıdaki kodla `About` bir yöntem ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

LINQ beyanı, öğrenci varlıklarını kayıt tarihine göre gruplandırır, her bir gruptaki varlıkların sayısını hesaplar ve sonuçları bir `EnrollmentDateGroup` görünüm modeli nesneleri koleksiyonunda depolar.

### <a name="create-the-about-view"></a>Hakkında görünüm oluştur

Aşağıdaki kodla bir *Görünümler/Home/about. cshtml* dosyası ekleyin:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Uygulamayı çalıştırın ve hakkında sayfasına gidin. Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Sütun sıralama bağlantıları eklendi
> * Arama kutusu eklendi
> * Öğrenciler dizinine sayfalama eklendi
> * Dizin oluşturma yöntemine sayfalama eklendi
> * Sayfalama bağlantıları eklendi
> * Hakkında bir sayfa oluşturuldu

Geçiş kullanarak veri modeli değişikliklerini nasıl işleyebileceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sonraki: veri modeli değişikliklerini Işle](migrations.md)
