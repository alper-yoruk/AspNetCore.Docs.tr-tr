---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Veri Modeli - 8'de 5
author: rick-anderson
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kuralları belirterek veri modelini özelleştirin.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665720"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="0ec58-103">ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Veri Modeli - 8'de 5</span><span class="sxs-lookup"><span data-stu-id="0ec58-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="0ec58-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0ec58-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0ec58-105">Önceki öğreticiler üç varlıktan oluşan temel bir veri modeli ile çalıştı.</span><span class="sxs-lookup"><span data-stu-id="0ec58-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="0ec58-106">Bu eğitimde:</span><span class="sxs-lookup"><span data-stu-id="0ec58-106">In this tutorial:</span></span>

* <span data-ttu-id="0ec58-107">Daha fazla varlık ve ilişki eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="0ec58-108">Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirterek özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="0ec58-109">Tamamlanan veri modeli aşağıdaki resimde gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-109">The completed data model is shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="0ec58-111">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="0ec58-111">The Student entity</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="0ec58-113">*Modeller/Student.cs'deki* kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="0ec58-114">Önceki kod bir `FullName` özellik ekler ve varolan özelliklere aşağıdaki öznitelikleri ekler:</span><span class="sxs-lookup"><span data-stu-id="0ec58-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="0ec58-115">FullName hesaplanan özellik</span><span class="sxs-lookup"><span data-stu-id="0ec58-115">The FullName calculated property</span></span>

<span data-ttu-id="0ec58-116">`FullName`diğer iki özelliği nkiçleme tarafından oluşturulan bir değeri döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="0ec58-117">`FullName`ayarlanamaz, bu nedenle yalnızca bir erişime sahip.</span><span class="sxs-lookup"><span data-stu-id="0ec58-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="0ec58-118">Veritabanında `FullName` sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="0ec58-119">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="0ec58-120">Öğrenci kayıt tarihleri için, yalnızca tarih alakalı olsa da, tüm sayfalar şu anda tarihle birlikte günün saatini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="0ec58-121">Veri ek açıklama özniteliklerini kullanarak, verileri gösteren her sayfada görüntü biçimini düzeltecek bir kod değişikliği yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="0ec58-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı içsel türünden daha özel bir veri türü belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="0ec58-123">Bu durumda tarih ve saat değil, yalnızca tarih görüntülenmelidir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="0ec58-124">[DataType Numaralandırma,](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="0ec58-125">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-125">For example:</span></span>

* <span data-ttu-id="0ec58-126">Bağlantı `mailto:` otomatik olarak `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="0ec58-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="0ec58-127">Tarih seçici çoğu tarayıcıda sağlanır. `DataType.Date`</span><span class="sxs-lookup"><span data-stu-id="0ec58-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="0ec58-128">Öznitelik `DataType` HTML 5 `data-` (belirgin veri tire) öznitelikleri yakar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="0ec58-129">Öznitelikler `DataType` doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="0ec58-130">DisplayFormat özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="0ec58-131">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="0ec58-132">Varsayılan olarak, tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)dayalı varsayılan biçimlerine göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="0ec58-133">Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="0ec58-134">Ayar, `ApplyFormatInEditMode` biçimlendirmenin edit UI'ye de uygulanması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="0ec58-135">Bazı alanlar kullanmamalıdır. `ApplyFormatInEditMode`</span><span class="sxs-lookup"><span data-stu-id="0ec58-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="0ec58-136">Örneğin, para birimi simgesi genellikle bir edit metin kutusunda görüntülenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="0ec58-137">Öznitelik `DisplayFormat` kendisi tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="0ec58-138">Özniteliği öznitelik ile `DataType` `DisplayFormat` kullanmak genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="0ec58-139">Öznitelik, `DataType` verilerin bir ekranda nasıl işlenirse, anlambilimini aktarıyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="0ec58-140">Öznitelik, `DataType` aşağıdaki avantajlarda `DisplayFormat`bulunmayan avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0ec58-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="0ec58-141">Tarayıcı HTML5 özelliklerini etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="0ec58-142">Örneğin, bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları ve istemci tarafı giriş doğrulaması gösterin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="0ec58-143">Varsayılan olarak, tarayıcı verileri yerel eki göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="0ec58-144">Daha fazla bilgi [ \<için, Yardımcı yı etiketleme> girişine](xref:mvc/views/working-with-forms#the-input-tag-helper)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="0ec58-145">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="0ec58-146">Veri doğrulama kuralları ve doğrulama hatası iletileri öznitelikleri ile belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="0ec58-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, veri alanında izin verilen en az ve en büyük karakter uzunluğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="0ec58-148">Gösterilen kod adları en fazla 50 karakterle sınırlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="0ec58-149">En düşük dize uzunluğunu ayarlayan bir örnek [daha sonra](#the-required-attribute)gösterilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="0ec58-150">Öznitelik, `StringLength` istemci tarafı ve sunucu tarafı doğrulaması da sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="0ec58-151">Minimum değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="0ec58-152">Öznitelik, `StringLength` kullanıcının bir ad için beyaz alan girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="0ec58-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="0ec58-154">Örneğin, aşağıdaki kod ilk karakterin büyük harf, kalan karakterlerin ise alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ec58-156">**SQL Server Object Explorer'da** (SSOX), **Öğrenci** tablosunu çift tıklatarak Öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Göçlerden önce SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="0ec58-158">Önceki resimde `Student` tablo için şema gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="0ec58-159">Ad alanları türü `nvarchar(MAX)`var.</span><span class="sxs-lookup"><span data-stu-id="0ec58-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="0ec58-160">Bu öğreticide bir geçiş oluşturulduğunda ve daha `nvarchar(50)` sonra uygulandığında, ad alanları dize uzunluk özniteliklerinin bir sonucu olarak olur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0ec58-162">SQLite aracınızda, tablonun sütun tanımlarını inceleyin. `Student`</span><span class="sxs-lookup"><span data-stu-id="0ec58-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="0ec58-163">Ad alanları türü `Text`var.</span><span class="sxs-lookup"><span data-stu-id="0ec58-163">The name fields have type `Text`.</span></span> <span data-ttu-id="0ec58-164">İlk ad alanının çağrıldığına `FirstMidName`dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="0ec58-165">Sonraki bölümde, bu sütunun adını ' `FirstName`da ' olarak değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="0ec58-166">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="0ec58-167">Öznitelikler, sınıfların ve özelliklerin veritabanına nasıl eşlenebileceğini denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="0ec58-168">`Student` Modelde, `Column` özellik veritabanındaki "FirstName" ile `FirstMidName` özelliğin adını eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="0ec58-169">Veritabanı oluşturulduğunda, modeldeki özellik adları sütun adları için `Column` (öznitelik kullanıldığı durumlar hariç) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="0ec58-170">Alan `Student` da `FirstMidName` bir göbek adı içerebilir, çünkü model ilk ad alanı için kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="0ec58-171">`[Column]` Öznitelik ile, `Student.FirstMidName` veri modeli nde `FirstName` `Student` tablonun sütununa eşler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="0ec58-172">Öznitelik eklenmesi `Column` modeli destekleyen `SchoolContext`değiştirir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="0ec58-173">Artık veritabanını `SchoolContext` destekleyen model eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="0ec58-174">Bu tutarsızlık daha sonra bu öğreticide bir geçiş eklenerek çözülecektir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="0ec58-175">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="0ec58-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="0ec58-176">Öznitelik `Required` ad özelliklerini gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="0ec58-177">Öznitelik `Required` değer türleri (örneğin, `DateTime`, `int`, ve). `double`gibi boşolmayan türleri için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="0ec58-178">Null olamaz türleri otomatik olarak gerekli alanlar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="0ec58-179">Öznitelik, `Required` uygulanacak olması `MinimumLength` `MinimumLength` için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="0ec58-180">`MinimumLength`ve `Required` doğrulamayı karşılamak için beyaz uzaya izin verin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="0ec58-181">Dize `RegularExpression` üzerinde tam denetim için özniteliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="0ec58-182">Görüntü özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="0ec58-183">Öznitelik, `Display` metin kutularıiçin alt yazının "Ad", "Soyadı", "Tam Ad" ve "Kayıt Tarihi" olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="0ec58-184">Varsayılan alt yazılarda sözcükleri bölen boşluk yoktu, örneğin "Soyadı" gibi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="0ec58-185">Geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-185">Create a migration</span></span>

<span data-ttu-id="0ec58-186">Uygulamayı çalıştırın ve Öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="0ec58-187">Bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-187">An exception is thrown.</span></span> <span data-ttu-id="0ec58-188">Öznitelik, `[Column]` EF'nin adlı `FirstName`bir sütun bulmayı beklemesine neden olur, ancak veritabanındaki sütun adı hala. `FirstMidName`</span><span class="sxs-lookup"><span data-stu-id="0ec58-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ec58-190">Hata iletisi aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="0ec58-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="0ec58-191">PMC'de, yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="0ec58-192">Bu komutlardan ilki aşağıdaki uyarı iletisini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="0ec58-193">Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="0ec58-194">Veritabanındaki bir ad 50'den fazla karaktere sahipse, 51'den sonkaraktere kadar olan karakter kaybolur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="0ec58-195">SSOX'ta Öğrenci tablosunu açın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-195">Open the Student table in SSOX:</span></span>

  ![Göçlerden sonra SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="0ec58-197">Geçiş uygulanmadan önce, ad sütunları [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türündeydi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="0ec58-198">Ad sütunları `nvarchar(50)`şimdi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="0ec58-199">Sütun adı ' `FirstMidName` ndan `FirstName`' a değiştirildi</span><span class="sxs-lookup"><span data-stu-id="0ec58-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0ec58-201">Hata iletisi aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="0ec58-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="0ec58-202">Proje klasöründe bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-202">Open a command window in the project folder.</span></span> <span data-ttu-id="0ec58-203">Yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="0ec58-204">Veritabanı güncelleştirme komutu aşağıdaki örnek gibi bir hata görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0ec58-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="0ec58-205">Bu öğretici için, bu hatayı aşmanın yolu ilk geçişi silmek ve yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="0ec58-206">Daha fazla bilgi için, [geçişler öğretici](xref:data/ef-rp/migrations)üst kısmında SQLite uyarı notu bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="0ec58-207">*Geçişler* klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="0ec58-208">Veritabanını bırakmak, yeni bir ilk geçiş oluşturmak ve geçişuygulamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="0ec58-209">SQLite aracınızdaki Öğrenci tablosunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="0ec58-210">FirstMidName olan sütun artık FirstName'dir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="0ec58-211">Uygulamayı çalıştırın ve Öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="0ec58-212">Tarihlerle birlikte zaman girişi nin veya görüntülenmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="0ec58-213">**Yeni Oluştur'u**seçin ve 50 karakterden uzun bir ad girmeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="0ec58-214">Aşağıdaki bölümlerde, uygulamayı bazı aşamalarda oluşturmak derleyici hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="0ec58-215">Yönergeler, uygulamanın ne zaman oluşturacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="0ec58-216">Eğitmen Varlık</span><span class="sxs-lookup"><span data-stu-id="0ec58-216">The Instructor Entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="0ec58-218">Aşağıdaki kodile *Modeller/Instructor.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="0ec58-219">Birden çok öznitelik tek satırda olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="0ec58-220">Öznitelikler `HireDate` aşağıdaki gibi yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="0ec58-221">Gezinme özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-221">Navigation properties</span></span>

<span data-ttu-id="0ec58-222">Ve `CourseAssignments` `OfficeAssignment` özellikleri navigasyon özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="0ec58-223">Bir eğitmen herhangi bir sayıda `CourseAssignments` ders verebilir, bu nedenle bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="0ec58-224">Bir eğitmen in en fazla bir `OfficeAssignment` ofisi olabilir, bu nedenle özellik tek `OfficeAssignment` bir varlık tutar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="0ec58-225">`OfficeAssignment`ofis atanmamışsa geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="0ec58-226">OfficeAssignment tüzel kişiliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-226">The OfficeAssignment entity</span></span>

![OfficeAssignment tüzel kişiliği](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="0ec58-228">Aşağıdaki kodla *Modeller/OfficeAssignment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="0ec58-229">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-229">The Key attribute</span></span>

<span data-ttu-id="0ec58-230">Özellik `[Key]` adı classnameID veya ID dışında bir şey olduğunda öznitelik birincil anahtar (PK) olarak bir özellik tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="0ec58-231">Varlıklarla varlıklar arasında `Instructor` `OfficeAssignment` bire bir ya da bir ilişki var.</span><span class="sxs-lookup"><span data-stu-id="0ec58-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="0ec58-232">Bir ofis ataması yalnızca atandığı eğitmenle ilgili olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="0ec58-233">`OfficeAssignment` PK aynı zamanda `Instructor` varlık için yabancı anahtarıdır (FK).</span><span class="sxs-lookup"><span data-stu-id="0ec58-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="0ec58-234">EF Core, kimlik veya `InstructorID` classnameID `OfficeAssignment` adlandırma `InstructorID` kuralını izlemediği için PK'yı otomatik olarak tanıyamaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="0ec58-235">Bu nedenle, `Key` öznitelik PK olarak tanımlamak `InstructorID` için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="0ec58-236">Varsayılan olarak, SÜTUN tanımlayıcı bir ilişki için olduğundan, EF Core anahtarı veritabanı oluşturulmayan olarak ele alır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="0ec58-237">Eğitmen navigasyon özelliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-237">The Instructor navigation property</span></span>

<span data-ttu-id="0ec58-238">Belirli `Instructor.OfficeAssignment` bir eğitmen için bir `OfficeAssignment` satır olmayabilir, çünkü gezinti özelliği geçersiz olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="0ec58-239">Bir eğitmenin ofis görevi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="0ec58-240">Yabancı `OfficeAssignment.Instructor` anahtar `InstructorID` türü , nullable olmayan bir `int`değer türü olduğundan navigasyon özelliği her zaman bir eğitmen varlık olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="0ec58-241">Bir ofis ödevi bir eğitmen olmadan var olamaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="0ec58-242">Bir `Instructor` varlığın ilgili `OfficeAssignment` bir varlığı varsa, her varlığın gezinti özelliğinde diğerine bir göndermesi vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0ec58-243">Kurs Varlığı</span><span class="sxs-lookup"><span data-stu-id="0ec58-243">The Course Entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="0ec58-245">*Modelleri/Course.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="0ec58-246">Varlığın `Course` yabancı bir anahtarı (FK) özelliği `DepartmentID`vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="0ec58-247">`DepartmentID`ilgili `Department` varlığa işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="0ec58-248">Varlığın `Course` bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="0ec58-249">EF Core, model ilgili bir varlık için bir gezinti özelliği ne zaman bir veri modeli için yabancı bir anahtar özelliği gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="0ec58-250">EF Core, ihtiyaç duyulan her yerde veritabanında otomatik olarak FK'lar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="0ec58-251">EF Core, otomatik olarak oluşturulan FK'lar için [gölge özellikleri](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="0ec58-252">Ancak, veri modeline FK'yı açıkça dahil etmek güncelleştirmeleri daha basit ve daha verimli hale getirebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="0ec58-253">Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün.</span><span class="sxs-lookup"><span data-stu-id="0ec58-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="0ec58-254">Bir kurs varlığı, bir ders varlığının edinimi için getirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="0ec58-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="0ec58-255">Özellik `Department` açıkça yüklenmezse geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="0ec58-256">Kurs varlığını güncelleştirmek `Department` için öncelikle varlığın getirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="0ec58-257">FK özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden `Department` önce varlığı almanıza gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="0ec58-258">DatabaseOluşturulan öznitelik</span><span class="sxs-lookup"><span data-stu-id="0ec58-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="0ec58-259">Öznitelik, `[DatabaseGenerated(DatabaseGeneratedOption.None)]` PK'nın veritabanı tarafından oluşturulmadı yerine uygulama tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="0ec58-260">Varsayılan olarak, EF Core PK değerlerinin veritabanı tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="0ec58-261">Veritabanı tarafından oluşturulan genellikle en iyi yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="0ec58-262">Varlıklar `Course` için, kullanıcı PK'yı belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="0ec58-263">Örneğin, matematik bölümü için 1000 serisi, İngilizce bölümü için 2000 serisi gibi bir ders numarası.</span><span class="sxs-lookup"><span data-stu-id="0ec58-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="0ec58-264">Öznitelik, `DatabaseGenerated` varsayılan değerleri oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="0ec58-265">Örneğin, veritabanı, bir satırın oluşturulduğu veya güncelleştirıldığı tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="0ec58-266">Daha fazla bilgi [için, oluşturulan özellikler](/ef/core/modeling/generated-properties)e bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0ec58-267">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="0ec58-268">`Course` Varlıktaki yabancı anahtar (FK) özellikleri ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="0ec58-269">Bir bölüme bir kurs atanır, `DepartmentID` bu yüzden `Department` bir FK ve navigasyon özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="0ec58-270">Bir kursa herhangi bir sayıda öğrenci kaydolabilir, bu nedenle `Enrollments` navigasyon özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="0ec58-271">Bir kurs birden çok eğitmen tarafından `CourseAssignments` öğretilebilir, bu nedenle navigasyon özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="0ec58-272">`CourseAssignment`daha [sonra](#many-to-many-relationships)açıklanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="0ec58-273">Bölüm tüzel kişiliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-273">The Department entity</span></span>

![Bölüm tüzel kişiliği](complex-data-model/_static/department-entity.png)

<span data-ttu-id="0ec58-275">Aşağıdaki kodile *Modeller/Department.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="0ec58-276">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-276">The Column attribute</span></span>

<span data-ttu-id="0ec58-277">Daha önce `Column` öznitelik sütun adı eşlemi değiştirmek için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="0ec58-278">Varlığın `Department` kodunda, `Column` öznitelik SQL veri türü eşlemi değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="0ec58-279">Sütun `Budget` veritabanında SQL Server para türü kullanılarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="0ec58-280">Sütun eşleme genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-280">Column mapping is generally not required.</span></span> <span data-ttu-id="0ec58-281">EF Core, özellik için CLR türüne göre uygun SQL Server veri türünü seçer.</span><span class="sxs-lookup"><span data-stu-id="0ec58-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="0ec58-282">CLR `decimal` türü, SQL Server `decimal` türüyle eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="0ec58-283">`Budget`para birimi içindir ve para veri türü para birimi için daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0ec58-284">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="0ec58-285">FK ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="0ec58-286">Bir bölümün bir yöneticisi olabilir veya olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="0ec58-287">Yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-287">An administrator is always an instructor.</span></span> <span data-ttu-id="0ec58-288">Bu `InstructorID` nedenle özellik `Instructor` varlık için FK olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="0ec58-289">Gezinti özelliği adlandırılmış, `Administrator` `Instructor` ancak bir varlık tutar:</span><span class="sxs-lookup"><span data-stu-id="0ec58-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="0ec58-290">Önceki koddaki soru işareti (?) özelliğin geçersiz olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="0ec58-291">Bir bölümün birçok dersi olabilir, bu nedenle Kurslar navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="0ec58-292">Geçersiz kılmaya göre, EF Core nullable OLMAYAN FKs ve çok-çok ilişkiler için basamaklı silme sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="0ec58-293">Bu varsayılan davranış dairesel basamaklı silme kuralları neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="0ec58-294">Dairesel basamaklı silme kuralları, geçiş eklendiğinde özel bir özel durum neden olur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="0ec58-295">Örneğin, `Department.InstructorID` özellik nullable olarak tanımlanmışsa, EF Core basamaklı silme kuralını yapılandırırdı.</span><span class="sxs-lookup"><span data-stu-id="0ec58-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="0ec58-296">Bu durumda, yönetici olarak atanan eğitmen silindiğinde bölüm silinir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="0ec58-297">Bu senaryoda, bir kısıtlama kuralı daha mantıklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="0ec58-298">Aşağıdaki [akıcı API](#fluent-api-alternative-to-attributes) bir kısıtlama kuralı ayarlar ve basamaklı silme devre dışı bırak.</span><span class="sxs-lookup"><span data-stu-id="0ec58-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="0ec58-299">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="0ec58-299">The Enrollment entity</span></span>

<span data-ttu-id="0ec58-300">Kayıt kaydı, bir öğrencitarafından alınan bir ders için dir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-300">An enrollment record is for one course taken by one student.</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="0ec58-302">*Modelleri/Enrollment.cs'yi* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0ec58-303">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="0ec58-304">FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="0ec58-305">Kayıt kaydı tek bir kurs içindir, `CourseID` bu nedenle `Course` bir FK özelliği ve navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="0ec58-306">Kayıt kaydı bir öğrenci içindir, bu `StudentID` nedenle bir `Student` FK özelliği ve navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="0ec58-307">Çok-Çok İlişkiler</span><span class="sxs-lookup"><span data-stu-id="0ec58-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="0ec58-308">Varlıklar `Student` la `Course` çok arasında çok büyük bir ilişki var.</span><span class="sxs-lookup"><span data-stu-id="0ec58-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="0ec58-309">Varlık, `Enrollment` veritabanında *yükü olan* çok-çok birleştirme tablosu olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="0ec58-310">"Yük ile" `Enrollment` tablo (bu durumda, PK ve) `Grade`birleştirilmiş tablolar için FKs yanında ek veri içerdiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="0ec58-311">Aşağıdaki resimde, bu ilişkilerin varlık diyagramında nasıl göründüğü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="0ec58-312">(Bu diyagram EF 6.x için [EF Güç Araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="0ec58-313">Diyagramı oluşturmak öğreticinin bir parçası değildir.)</span><span class="sxs-lookup"><span data-stu-id="0ec58-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Öğrenci-Kurs birçok ilişki için çok](complex-data-model/_static/student-course.png)

<span data-ttu-id="0ec58-315">Her ilişki satırının bir ucunda 1, diğerucunda bir yıldız (\*) vardır ve bu da bir-çok ilişkisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="0ec58-316">`Enrollment` Tablo not bilgilerini içermiyorsa, yalnızca iki FK'yı (ve)`CourseID` `StudentID`içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="0ec58-317">Yükü olmayan çok-çok birleştirme tablosu bazen saf birleştirme tablosu (PJT) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="0ec58-318">Ve `Instructor` `Course` varlıklar saf bir birleştirme tablosu nu kullanarak çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="0ec58-319">Not: EF 6.x, çok-çok ilişkiler için örtülü birleştirme tablolarını destekler, ancak EF Core desteklemez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="0ec58-320">Daha fazla bilgi [için, EF Core 2.0'daki çok-çok ilişkilere](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="0ec58-321">Ders Atama sı</span><span class="sxs-lookup"><span data-stu-id="0ec58-321">The CourseAssignment entity</span></span>

![Ders Atama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="0ec58-323">Aşağıdaki kodile *Modeller/DersAtama.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="0ec58-324">Derslerden Derslere çok sayıda ilişki bir birleştirme tablosu gerektirir ve bu katılma tablosunun varlığı Ders Atamasi'dır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="0ec58-326">Birleştirilmiş varlık `EntityName1EntityName2`adlamak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="0ec58-327">Örneğin, Bu deseni kullanarak Eğitmen-Kurslar a)'ya katılma tablosu olacaktır. `CourseInstructor`</span><span class="sxs-lookup"><span data-stu-id="0ec58-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="0ec58-328">Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="0ec58-329">Veri modelleri basit başlar ve büyür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-329">Data models start out simple and grow.</span></span> <span data-ttu-id="0ec58-330">Birleştirme tabloları nayük olmadan (PJTs) sık sık yükü içerecek şekilde gelişir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="0ec58-331">Açıklayıcı bir varlık adı ile başlayarak, birleştirme tablosu değiştiğinde adın değişmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="0ec58-332">İdeal olarak, birleştirme varlığı iş alanında kendi doğal (muhtemelen tek kelime) adı olurdu.</span><span class="sxs-lookup"><span data-stu-id="0ec58-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="0ec58-333">Örneğin, Kitaplar ve Müşteriler Derecelendirme adlı bir birleştirme varlığıyla bağlantılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="0ec58-334">Eğitmen-to-Kurslar için çok-çok ilişki, `CourseAssignment` yerine `CourseInstructor`tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="0ec58-335">Kompozit anahtar</span><span class="sxs-lookup"><span data-stu-id="0ec58-335">Composite key</span></span>

<span data-ttu-id="0ec58-336">İki FKs `CourseAssignment` (`InstructorID` `CourseID`ve ) birlikte benzersiz `CourseAssignment` tablonun her satırı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="0ec58-337">`CourseAssignment`özel bir PK gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="0ec58-338">Ve `InstructorID` `CourseID` özellikleri bileşik PK olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="0ec58-339">BILEŞIK PK'ları EF Core'a belirtmenin tek yolu *akıcı API'dir.*</span><span class="sxs-lookup"><span data-stu-id="0ec58-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="0ec58-340">Sonraki bölümde, bileşik PK'nın nasıl yapılandırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="0ec58-341">Kompozit anahtar şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0ec58-341">The composite key ensures that:</span></span>

* <span data-ttu-id="0ec58-342">Bir kurs için birden fazla satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="0ec58-343">Bir eğitmen için birden fazla satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="0ec58-344">Aynı eğitmen ve kurs için birden fazla satıra izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="0ec58-345">Birleştirme `Enrollment` varlığı kendi PK'sını tanımlar, bu nedenle bu tür yinelemeler mümkündür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="0ec58-346">Bu tür yinelemeleri önlemek için:</span><span class="sxs-lookup"><span data-stu-id="0ec58-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="0ec58-347">FK alanlarına benzersiz bir dizin ekleme veya</span><span class="sxs-lookup"><span data-stu-id="0ec58-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="0ec58-348">'ye `Enrollment` `CourseAssignment`benzer bir birincil bileşik anahtarla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="0ec58-349">Daha fazla bilgi için [Dizinler'e](/ef/core/modeling/indexes)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="0ec58-350">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0ec58-350">Update the database context</span></span>

<span data-ttu-id="0ec58-351">*Verileri/SchoolContext.cs'yi* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="0ec58-352">Önceki kod yeni varlıkları ekler ve varlığın `CourseAssignment` bileşik PK'sını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="0ec58-353">Özniteliklere akıcı API alternatifi</span><span class="sxs-lookup"><span data-stu-id="0ec58-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="0ec58-354">Önceki `OnModelCreating` koddaki yöntem, EF Core davranışını yapılandırmak için *akıcı API* kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="0ec58-355">GENELLIKLE bir dizi yöntem çağrısını tek bir deyimde bir araya getirerek kullanıldığından API'ye "akıcı" denir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="0ec58-356">[Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) akıcı API bir örnektir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="0ec58-357">Bu öğreticide, akıcı API yalnızca özniteliklerle yapılabilen veritabanı eşlemeleri için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="0ec58-358">Ancak, akıcı API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="0ec58-359">Akıcı API `MinimumLength` ile uygulanamıyor gibi bazı öznitelikler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="0ec58-360">`MinimumLength`şema değişmez, yalnızca minimum uzunluk doğrulama kuralı nı uygular.</span><span class="sxs-lookup"><span data-stu-id="0ec58-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="0ec58-361">Bazı geliştiriciler, varlık sınıflarını "temiz" tutabilmek için akıcı API'yi yalnızca kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="0ec58-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="0ec58-362">Öznitelikler ve akıcı API karıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="0ec58-363">Yalnızca akıcı API (bileşik PK belirterek) ile yapılabilir bazı yapılandırmaları vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="0ec58-364">Yalnızca özniteliklerle yapılabilecek bazı yapılandırmalar`MinimumLength`vardır ( ).</span><span class="sxs-lookup"><span data-stu-id="0ec58-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="0ec58-365">Akıcı API veya öznitelikleri kullanmak için önerilen uygulama:</span><span class="sxs-lookup"><span data-stu-id="0ec58-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="0ec58-366">Bu iki yaklaşımdan birini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="0ec58-367">Seçilen yaklaşımı mümkün olduğunca tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="0ec58-368">Bu öğreticide kullanılan özniteliklerden bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="0ec58-369">Yalnızca doğrulama (örneğin, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="0ec58-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="0ec58-370">Yalnızca EF Core yapılandırması `HasKey`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="0ec58-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="0ec58-371">Doğrulama ve EF Core yapılandırması `[StringLength(50)]`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="0ec58-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="0ec58-372">Öznitelikler ve akıcı API hakkında daha fazla bilgi için yapılandırma [yöntemlerine](/ef/core/modeling/)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="0ec58-373">Varlık diyagramı</span><span class="sxs-lookup"><span data-stu-id="0ec58-373">Entity diagram</span></span>

<span data-ttu-id="0ec58-374">Aşağıdaki resimde, EF Power Tools'un tamamlanan Okul modeli için oluşturduğu diyagram gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="0ec58-376">Önceki diyagram da gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="0ec58-377">Birkaç bir-to-çok ilişki satırları \*(1 için).</span><span class="sxs-lookup"><span data-stu-id="0ec58-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="0ec58-378">Varlıklar ve `Instructor` `OfficeAssignment` varlıklar arasındaki birden sıfıra veya bir ilişkisi satırına (1-0.1).</span><span class="sxs-lookup"><span data-stu-id="0ec58-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="0ec58-379">Varlıklar ve `Instructor` `Department` varlıklar arasındaki sıfır veya bir-birden çok ilişki çizgisi (0..1 ile \*) arasındaki ilişki çizgisi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0ec58-380">Veritabanını tohumla</span><span class="sxs-lookup"><span data-stu-id="0ec58-380">Seed the database</span></span>

<span data-ttu-id="0ec58-381">*Veri/DbInitializer.cs*kodu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="0ec58-382">Önceki kod, yeni varlıklar için tohum verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="0ec58-383">Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="0ec58-384">Örnek veriler sınama için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-384">The sample data is used for testing.</span></span> <span data-ttu-id="0ec58-385">Kaç-çok `Enrollments` birleştirme tablolarının tohumlanabildiğini görün. `CourseAssignments`</span><span class="sxs-lookup"><span data-stu-id="0ec58-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="0ec58-386">Geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="0ec58-386">Add a migration</span></span>

<span data-ttu-id="0ec58-387">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ec58-389">PMC'de aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="0ec58-390">Önceki komut, olası veri kaybı yla ilgili bir uyarı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="0ec58-391">`database update` Komut çalıştırılırsa, aşağıdaki hata üretilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="0ec58-392">Sonraki bölümde, bu hata hakkında ne yapacağınızı görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0ec58-394">Geçiş ekler ve komutu `database update` çalıştırırsanız, aşağıdaki hata üretilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="0ec58-395">Sonraki bölümde, bu hatayı nasıl önleyebildiğinizi görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="0ec58-396">Geçiş veya bırakma ve yeniden oluşturma uygulama</span><span class="sxs-lookup"><span data-stu-id="0ec58-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="0ec58-397">Artık varolan bir veritabanınız olduğuna göre, bu veritabanına nasıl değişiklik uygulayacağınızı düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="0ec58-398">Bu öğretici iki alternatif gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="0ec58-399">[Veritabanını bırakın ve yeniden oluşturun.](#drop)</span><span class="sxs-lookup"><span data-stu-id="0ec58-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="0ec58-400">SQLite kullanıyorsanız bu bölümü seçin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="0ec58-401">[Geçişi varolan veritabanına uygulayın.](#applyexisting)</span><span class="sxs-lookup"><span data-stu-id="0ec58-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="0ec58-402">Bu bölümdeki talimatlar yalnızca SQL Server için çalışır, **SQLite için değil.**</span><span class="sxs-lookup"><span data-stu-id="0ec58-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="0ec58-403">Her iki seçenek de SQL Server için çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="0ec58-404">Uygulama-geçiş yöntemi daha karmaşık ve zaman alıcı olsa da, gerçek dünya, üretim ortamları için tercih edilen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="0ec58-405">Veritabanını bırakma ve yeniden oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-405">Drop and re-create the database</span></span>

<span data-ttu-id="0ec58-406">SQL Server kullanıyorsanız ve aşağıdaki bölümde uygula-geçiş yaklaşımını yapmak istiyorsanız [bu bölümü atlayın.](#apply-the-migration)</span><span class="sxs-lookup"><span data-stu-id="0ec58-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="0ec58-407">EF Core'u yeni bir veritabanı oluşturmaya zorlamak için veritabanını bırakın ve güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0ec58-409">Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="0ec58-410">*Geçişler* klasörünü silin ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0ec58-412">Komut penceresini açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="0ec58-413">Proje klasörü *ContosoUniversity.csproj* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="0ec58-414">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="0ec58-415">*Geçişler* klasörünü silin ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="0ec58-416">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-416">Run the app.</span></span> <span data-ttu-id="0ec58-417">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemi çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="0ec58-418">Yeni `DbInitializer.Initialize` veritabanını dolduruyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ec58-420">Veritabanını SSOX'ta açın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="0ec58-421">SSOX daha önce açılmışsa, **Yenile** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="0ec58-422">**Tablolar** düğümlerini genişletin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-422">Expand the **Tables** node.</span></span> <span data-ttu-id="0ec58-423">Oluşturulan tablolar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-423">The created tables are displayed.</span></span>

  ![SSOX tabloları](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="0ec58-425">Ders **Atama** tablosunu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="0ec58-426">**Ders Atama** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="0ec58-427">Ders **Atama** tablosunun veri içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX'ta Ders Atama verileri](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0ec58-430">Veritabanını incelemek için SQLite aracınızı kullanın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="0ec58-431">Yeni tablolar ve sütunlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-431">New tables and columns.</span></span>
* <span data-ttu-id="0ec58-432">Tablolarda tohumlanmış veriler, örneğin **Ders Atama** tablosu.</span><span class="sxs-lookup"><span data-stu-id="0ec58-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="0ec58-433">Geçişi uygulayın</span><span class="sxs-lookup"><span data-stu-id="0ec58-433">Apply the migration</span></span>

<span data-ttu-id="0ec58-434">Bu bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-434">This section is optional.</span></span> <span data-ttu-id="0ec58-435">Bu adımlar yalnızca SQL Server LocalDB için çalışır ve yalnızca önceki [Açılan'ı](#drop) atlayıp veritabanı bölümünü yeniden oluşturursanız.</span><span class="sxs-lookup"><span data-stu-id="0ec58-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="0ec58-436">Geçişler varolan verilerle çalıştırıldığında, varolan verilerle memnun olmayan FK kısıtlamaları olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="0ec58-437">Üretim verileriyle, varolan verileri geçirmek için adımlar atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="0ec58-438">Bu bölümde FK kısıtlama ihlalleri nin düzeltilmesi ne örnek verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="0ec58-439">Yedekleme olmadan bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="0ec58-440">Önceki Açılan'ı tamamlayıp veritabanı bölümünü yeniden oluşturduysanız bu kod değişikliklerini [yapmayın.](#drop)</span><span class="sxs-lookup"><span data-stu-id="0ec58-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="0ec58-441">*{timestamp}_ComplexDataModel.cs* dosyası aşağıdaki kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="0ec58-442">Önceki kod `DepartmentID` `Course` tabloya nullable olmayan bir FK ekler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="0ec58-443">Önceki öğreticideki `Course`veritabanı, tablonun geçişler tarafından güncelleştirilemeyeceği için satırlar içerir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="0ec58-444">Geçişin `ComplexDataModel` varolan verilerle çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="0ec58-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="0ec58-445">Yeni sütuna varsayılan`DepartmentID`değer vermek için kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="0ec58-446">Varsayılan departman olarak hareket etmek için "Temp" adlı sahte bir departman oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0ec58-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="0ec58-447">Yabancı anahtar kısıtlamalarını düzeltme</span><span class="sxs-lookup"><span data-stu-id="0ec58-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="0ec58-448">`ComplexDataModel` Geçiş sınıfında, yöntemi `Up` güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="0ec58-449">*{timestamp}_ComplexDataModel.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="0ec58-450">`DepartmentID` Sütunu tabloya ekleyen kod satırının `Course` dışına yorum yapın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="0ec58-451">Aşağıdaki vurgulanan kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-451">Add the following highlighted code.</span></span> <span data-ttu-id="0ec58-452">Yeni kod `.CreateTable( name: "Department"` bloğun ardından gider:</span><span class="sxs-lookup"><span data-stu-id="0ec58-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="0ec58-453">Önceki değişikliklerle, varolan `Course` satırlar `ComplexDataModel.Up` yöntem çalıştırdıktan sonra "Geçici" bölümüyle ilişkilendirilecektir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="0ec58-454">Burada gösterilen durumu ele alma yolu bu öğretici için basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="0ec58-455">Bir üretim uygulaması:</span><span class="sxs-lookup"><span data-stu-id="0ec58-455">A production app would:</span></span>

* <span data-ttu-id="0ec58-456">Yeni `Department` satırlara satır `Department` ve ilgili `Course` satırlar eklemek için kod veya komut dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="0ec58-457">"Temp" bölümünü veya varsayılan değeri `Course.DepartmentID`kullanma.</span><span class="sxs-lookup"><span data-stu-id="0ec58-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0ec58-459">Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="0ec58-460">`DbInitializer.Initialize` Yöntem yalnızca boş bir veritabanıyla çalışacak şekilde tasarlandığından, Öğrenci ve Kurs tablolarındaki tüm satırları silmek için SSOX'u kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="0ec58-461">(Basamaklı silme Kayıt tablosunun ilgilenir.)</span><span class="sxs-lookup"><span data-stu-id="0ec58-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0ec58-463">Visual Studio Code ile SQL Server LocalDB kullanıyorsanız aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="0ec58-464">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-464">Run the app.</span></span> <span data-ttu-id="0ec58-465">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemi çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="0ec58-466">Yeni `DbInitializer.Initialize` veritabanını dolduruyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0ec58-467">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="0ec58-467">Next steps</span></span>

<span data-ttu-id="0ec58-468">Sonraki iki öğretici, ilgili verilerin nasıl okunup güncelleştirilebildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0ec58-469">[Önceki öğretici](xref:data/ef-rp/migrations)
> [Sonraki öğretici](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="0ec58-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0ec58-470">Önceki öğreticiler üç varlıktan oluşan temel bir veri modeli ile çalıştı.</span><span class="sxs-lookup"><span data-stu-id="0ec58-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="0ec58-471">Bu eğitimde:</span><span class="sxs-lookup"><span data-stu-id="0ec58-471">In this tutorial:</span></span>

* <span data-ttu-id="0ec58-472">Daha fazla varlık ve ilişki eklenir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="0ec58-473">Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirterek özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="0ec58-474">Tamamlanan veri modelinin varlık sınıfları aşağıdaki resimde gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="0ec58-476">Çözemediğiniz sorunlarla karşılaştıysanız, tamamlanan [uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="0ec58-477">Veri modelini özniteliklerle özelleştirme</span><span class="sxs-lookup"><span data-stu-id="0ec58-477">Customize the data model with attributes</span></span>

<span data-ttu-id="0ec58-478">Bu bölümde, veri modeli öznitelikleri kullanılarak özelleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="0ec58-479">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-479">The DataType attribute</span></span>

<span data-ttu-id="0ec58-480">Öğrenci sayfaları şu anda kayıt tarihinin saatini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="0ec58-481">Genellikle, tarih alanları yalnızca tarih değil, saat gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="0ec58-482">Aşağıdaki vurgulanan kod ile *Modelleri/Student.cs'yi* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="0ec58-483">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı içsel türünden daha özel bir veri türü belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="0ec58-484">Bu durumda tarih ve saat değil, yalnızca tarih görüntülenmelidir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="0ec58-485">[DataType Numaralandırma,](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="0ec58-486">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-486">For example:</span></span>

* <span data-ttu-id="0ec58-487">Bağlantı `mailto:` otomatik olarak `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="0ec58-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="0ec58-488">Tarih seçici çoğu tarayıcıda sağlanır. `DataType.Date`</span><span class="sxs-lookup"><span data-stu-id="0ec58-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="0ec58-489">Öznitelik `DataType` HTML 5 `data-` (telaffuz veri tire) html 5 tarayıcılar tüketmek öznitelikleri yakar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="0ec58-490">Öznitelikler `DataType` doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="0ec58-491">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="0ec58-492">Varsayılan olarak, tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)dayalı varsayılan biçimlerine göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="0ec58-493">Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="0ec58-494">Ayar, `ApplyFormatInEditMode` biçimlendirmenin edit UI'ye de uygulanması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="0ec58-495">Bazı alanlar kullanmamalıdır. `ApplyFormatInEditMode`</span><span class="sxs-lookup"><span data-stu-id="0ec58-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="0ec58-496">Örneğin, para birimi simgesi genellikle bir edit metin kutusunda görüntülenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="0ec58-497">Öznitelik `DisplayFormat` kendisi tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="0ec58-498">Özniteliği öznitelik ile `DataType` `DisplayFormat` kullanmak genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="0ec58-499">Öznitelik, `DataType` verilerin bir ekranda nasıl işlenirse, anlambilimini aktarıyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="0ec58-500">Öznitelik, `DataType` aşağıdaki avantajlarda `DisplayFormat`bulunmayan avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0ec58-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="0ec58-501">Tarayıcı HTML5 özelliklerini etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="0ec58-502">Örneğin, bir takvim denetimi, yerel duruma uygun para birimi simgesi, e-posta bağlantıları, istemci tarafı giriş doğrulaması vb. gösterin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="0ec58-503">Varsayılan olarak, tarayıcı verileri yerel eki göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="0ec58-504">Daha fazla bilgi [ \<için, Yardımcı yı etiketleme> girişine](xref:mvc/views/working-with-forms#the-input-tag-helper)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="0ec58-505">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-505">Run the app.</span></span> <span data-ttu-id="0ec58-506">Öğrenci Dizini sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="0ec58-507">Süreler artık görüntülenmiyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-507">Times are no longer displayed.</span></span> <span data-ttu-id="0ec58-508">`Student` Modeli kullanan her görünüm, tarihi zaman olmadan görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Tarihlerini saatsiz gösteren öğrenci dizini sayfası](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="0ec58-510">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-510">The StringLength attribute</span></span>

<span data-ttu-id="0ec58-511">Veri doğrulama kuralları ve doğrulama hatası iletileri öznitelikleri ile belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="0ec58-512">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, veri alanında izin verilen en az ve en büyük karakter uzunluğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="0ec58-513">Öznitelik, `StringLength` istemci tarafı ve sunucu tarafı doğrulaması da sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="0ec58-514">Minimum değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="0ec58-515">Modeli `Student` aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="0ec58-516">Önceki kod adları en fazla 50 karakterle sınırlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="0ec58-517">Öznitelik, `StringLength` kullanıcının bir ad için beyaz alan girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="0ec58-518">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="0ec58-519">Örneğin, aşağıdaki kod ilk karakterin büyük harf, kalan karakterlerin ise alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="0ec58-520">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-520">Run the app:</span></span>

* <span data-ttu-id="0ec58-521">Öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="0ec58-522">**Yeni Oluştur'u**seçin ve 50 karakterden uzun bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="0ec58-523">**Oluştur'u**seçin, istemci tarafı doğrulamabir hata iletisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-523">Select **Create**, client-side validation shows an error message.</span></span>

![Dize uzunluğu hatalarını gösteren öğrenci dizini sayfası](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="0ec58-525">**SQL Server Object Explorer'da** (SSOX), **Öğrenci** tablosunu çift tıklatarak Öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Göçlerden önce SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="0ec58-527">Önceki resimde `Student` tablo için şema gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="0ec58-528">Geçişler DB'de çalıştırılamadığı için ad alanları türüne `nvarchar(MAX)` sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="0ec58-529">Geçişler daha sonra bu öğreticide çalıştırıldığında, ad alanları `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="0ec58-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="0ec58-530">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-530">The Column attribute</span></span>

<span data-ttu-id="0ec58-531">Öznitelikler, sınıfların ve özelliklerin veritabanına nasıl eşlenebileceğini denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="0ec58-532">Bu bölümde, `Column` özellik db "FirstName" `FirstMidName` için özelliğin adını eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="0ec58-533">DB oluşturulduğunda, modeldeki özellik adları sütun adları için kullanılır `Column` (öznitelik kullanıldığı durumlar hariç).</span><span class="sxs-lookup"><span data-stu-id="0ec58-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="0ec58-534">Alan `Student` da `FirstMidName` bir göbek adı içerebilir, çünkü model ilk ad alanı için kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="0ec58-535">Aşağıdaki vurgulanan kod ile *Student.cs* dosyasını güncelleştirme:</span><span class="sxs-lookup"><span data-stu-id="0ec58-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="0ec58-536">Önceki değişiklikle, `Student.FirstMidName` uygulamadaki tablonun `FirstName` sütununa `Student` eşler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="0ec58-537">Öznitelik eklenmesi `Column` modeli destekleyen `SchoolContext`değiştirir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="0ec58-538">Artık veritabanını `SchoolContext` destekleyen model eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="0ec58-539">Uygulama geçişleri uygulamadan önce çalıştırılırsa, aşağıdaki özel durum oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="0ec58-540">DB'yi güncellemek için:</span><span class="sxs-lookup"><span data-stu-id="0ec58-540">To update the DB:</span></span>

* <span data-ttu-id="0ec58-541">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-541">Build the project.</span></span>
* <span data-ttu-id="0ec58-542">Proje klasöründe bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-542">Open a command window in the project folder.</span></span> <span data-ttu-id="0ec58-543">Yeni bir geçiş oluşturmak ve DB'yi güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="0ec58-546">Komut `migrations add ColumnFirstName` aşağıdaki uyarı iletisini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="0ec58-547">Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="0ec58-548">DB'deki bir ismin 50'den fazla karakteri olsaydı, 51'den sonkaraktere kadar olan karakter kaybolur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="0ec58-549">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="0ec58-549">Test the app.</span></span>

<span data-ttu-id="0ec58-550">SSOX'ta Öğrenci tablosunu açın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-550">Open the Student table in SSOX:</span></span>

![Göçlerden sonra SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="0ec58-552">Geçiş uygulanmadan önce, ad sütunları [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türündendi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="0ec58-553">Ad sütunları `nvarchar(50)`şimdi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="0ec58-554">Sütun adı ' `FirstMidName` ndan `FirstName`' a değiştirildi</span><span class="sxs-lookup"><span data-stu-id="0ec58-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="0ec58-555">Aşağıdaki bölümde, uygulamayı bazı aşamalarda oluşturmak derleyici hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="0ec58-556">Yönergeler, uygulamanın ne zaman oluşturacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="0ec58-557">Öğrenci varlık güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="0ec58-557">Student entity update</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="0ec58-559">*Modelleri/Student.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="0ec58-560">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="0ec58-560">The Required attribute</span></span>

<span data-ttu-id="0ec58-561">Öznitelik `Required` ad özelliklerini gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="0ec58-562">Öznitelik, `Required` değer türleri`DateTime`(, , `int`, `double`vb.) gibi kullanışsız türler için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="0ec58-563">Null olamaz türleri otomatik olarak gerekli alanlar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="0ec58-564">Öznitelik, `Required` öznitelikteki `StringLength` en az uzunluk parametresi ile değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="0ec58-565">Görüntü özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-565">The Display attribute</span></span>

<span data-ttu-id="0ec58-566">Öznitelik, `Display` metin kutularıiçin alt yazının "Ad", "Soyadı", "Tam Ad" ve "Kayıt Tarihi" olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="0ec58-567">Varsayılan alt yazılarda sözcükleri bölen boşluk yoktu, örneğin "Soyadı" gibi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="0ec58-568">FullName hesaplanan özellik</span><span class="sxs-lookup"><span data-stu-id="0ec58-568">The FullName calculated property</span></span>

<span data-ttu-id="0ec58-569">`FullName`diğer iki özelliği nkiçleme tarafından oluşturulan bir değeri döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="0ec58-570">`FullName`ayarlanamazsa, yalnızca bir erişime sahip olur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="0ec58-571">Veritabanında `FullName` sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="0ec58-572">Eğitmen Varlığını Oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-572">Create the Instructor Entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="0ec58-574">Aşağıdaki kodile *Modeller/Instructor.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="0ec58-575">Birden çok öznitelik tek satırda olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="0ec58-576">Öznitelikler `HireDate` aşağıdaki gibi yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="0ec58-577">Ders Atamaları ve OfisAtama navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="0ec58-578">Ve `CourseAssignments` `OfficeAssignment` özellikleri navigasyon özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="0ec58-579">Bir eğitmen herhangi bir sayıda `CourseAssignments` ders verebilir, bu nedenle bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="0ec58-580">Bir gezinti özelliği birden çok varlık barındırAn:</span><span class="sxs-lookup"><span data-stu-id="0ec58-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="0ec58-581">Girişlerin eklenebileceği, silinebileceği ve güncelleştirilebileceği bir liste türü olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="0ec58-582">Gezinme özelliği türleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="0ec58-583">`ICollection<T>` Belirtilirse, EF Core `HashSet<T>` varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="0ec58-584">Varlık, `CourseAssignment` çok-çok ilişkiler bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="0ec58-585">Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla bir ofis te sahip olabileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="0ec58-586">Özellik `OfficeAssignment` tek `OfficeAssignment` bir varlık tutar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="0ec58-587">`OfficeAssignment`ofis atanmamışsa geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="0ec58-588">OfficeAtama tüzel kişiliğini oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-588">Create the OfficeAssignment entity</span></span>

![OfficeAssignment tüzel kişiliği](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="0ec58-590">Aşağıdaki kodla *Modeller/OfficeAssignment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="0ec58-591">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-591">The Key attribute</span></span>

<span data-ttu-id="0ec58-592">Özellik `[Key]` adı classnameID veya ID dışında bir şey olduğunda öznitelik birincil anahtar (PK) olarak bir özellik tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="0ec58-593">Varlıklarla varlıklar arasında `Instructor` `OfficeAssignment` bire bir ya da bir ilişki var.</span><span class="sxs-lookup"><span data-stu-id="0ec58-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="0ec58-594">Bir ofis ataması yalnızca atandığı eğitmenle ilgili olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="0ec58-595">`OfficeAssignment` PK aynı zamanda `Instructor` varlık için yabancı anahtarıdır (FK).</span><span class="sxs-lookup"><span data-stu-id="0ec58-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="0ec58-596">EF Core, `InstructorID` `OfficeAssignment` aşağıdakilerden dolayı PK olarak otomatik olarak tanıyamaz:</span><span class="sxs-lookup"><span data-stu-id="0ec58-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="0ec58-597">`InstructorID`kimlik veya classnameID adlandırma kuralını izlemez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="0ec58-598">Bu nedenle, `Key` öznitelik PK olarak tanımlamak `InstructorID` için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="0ec58-599">Varsayılan olarak, SÜTUN tanımlayıcı bir ilişki için olduğundan, EF Core anahtarı veritabanı oluşturulmayan olarak ele alır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="0ec58-600">Eğitmen navigasyon özelliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-600">The Instructor navigation property</span></span>

<span data-ttu-id="0ec58-601">Varlığın `OfficeAssignment` `Instructor` gezinti özelliği geçersizdir, çünkü:</span><span class="sxs-lookup"><span data-stu-id="0ec58-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="0ec58-602">Başvuru türleri (sınıflar gibi geçersizdir).</span><span class="sxs-lookup"><span data-stu-id="0ec58-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="0ec58-603">Bir eğitmenin ofis görevi olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="0ec58-604">Varlığın `OfficeAssignment` nullable olmayan `Instructor` bir navigasyon özelliği vardır, çünkü:</span><span class="sxs-lookup"><span data-stu-id="0ec58-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="0ec58-605">`InstructorID`hükümsüzdür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="0ec58-606">Bir ofis ödevi bir eğitmen olmadan var olamaz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="0ec58-607">Bir `Instructor` varlığın ilgili `OfficeAssignment` bir varlığı varsa, her varlığın gezinti özelliğinde diğerine bir göndermesi vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="0ec58-608">Öznitelik `[Required]` `Instructor` gezinti özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="0ec58-609">Önceki kod, ilgili bir eğitmen olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="0ec58-610">`InstructorID` Yabancı anahtar (aynı zamanda PK) geçersiz olduğundan, önceki kod gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="0ec58-611">Kurs Varlığını Değiştir</span><span class="sxs-lookup"><span data-stu-id="0ec58-611">Modify the Course Entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="0ec58-613">*Modelleri/Course.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="0ec58-614">Varlığın `Course` yabancı bir anahtarı (FK) özelliği `DepartmentID`vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="0ec58-615">`DepartmentID`ilgili `Department` varlığa işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="0ec58-616">Varlığın `Course` bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="0ec58-617">MODEL ilgili bir varlık için bir gezinti özelliği olduğunda EF Core bir veri modeli için bir FK özelliği gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="0ec58-618">EF Core, ihtiyaç duyulan her yerde veritabanında otomatik olarak FK'lar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="0ec58-619">EF Core, otomatik olarak oluşturulan FK'lar için [gölge özellikleri](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="0ec58-620">Veri modelinde FK olması güncellemeleri daha basit ve daha verimli hale getirebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="0ec58-621">Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün.</span><span class="sxs-lookup"><span data-stu-id="0ec58-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="0ec58-622">Bir kurs varlığı, bir ders varlığının edinimi için getirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="0ec58-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="0ec58-623">Varlık `Department` açıkça yüklenmezse geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="0ec58-624">Kurs varlığını güncelleştirmek `Department` için öncelikle varlığın getirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="0ec58-625">FK özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden `Department` önce varlığı almanıza gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="0ec58-626">DatabaseOluşturulan öznitelik</span><span class="sxs-lookup"><span data-stu-id="0ec58-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="0ec58-627">Öznitelik, `[DatabaseGenerated(DatabaseGeneratedOption.None)]` PK'nın veritabanı tarafından oluşturulmadı yerine uygulama tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="0ec58-628">Varsayılan olarak, EF Core PK değerlerinin DB tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="0ec58-629">DB oluşturulan PK değerleri genellikle en iyi yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="0ec58-630">Varlıklar `Course` için, kullanıcı PK'yı belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="0ec58-631">Örneğin, matematik bölümü için 1000 serisi, İngilizce bölümü için 2000 serisi gibi bir ders numarası.</span><span class="sxs-lookup"><span data-stu-id="0ec58-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="0ec58-632">Öznitelik, `DatabaseGenerated` varsayılan değerleri oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="0ec58-633">Örneğin, DB, bir satırın oluşturulduğu veya güncelleştirıldığı tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="0ec58-634">Daha fazla bilgi [için, oluşturulan özellikler](/ef/core/modeling/generated-properties)e bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0ec58-635">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="0ec58-636">`Course` Varlıktaki yabancı anahtar (FK) özellikleri ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="0ec58-637">Bir bölüme bir kurs atanır, `DepartmentID` bu yüzden `Department` bir FK ve navigasyon özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="0ec58-638">Bir kursa herhangi bir sayıda öğrenci kaydolabilir, bu nedenle `Enrollments` navigasyon özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="0ec58-639">Bir kurs birden çok eğitmen tarafından `CourseAssignments` öğretilebilir, bu nedenle navigasyon özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="0ec58-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="0ec58-640">`CourseAssignment`daha [sonra](#many-to-many-relationships)açıklanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="0ec58-641">Bölüm tüzel kişiliğini oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-641">Create the Department entity</span></span>

![Bölüm tüzel kişiliği](complex-data-model/_static/department-entity.png)

<span data-ttu-id="0ec58-643">Aşağıdaki kodile *Modeller/Department.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="0ec58-644">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="0ec58-644">The Column attribute</span></span>

<span data-ttu-id="0ec58-645">Daha önce `Column` öznitelik sütun adı eşlemi değiştirmek için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="0ec58-646">Varlığın `Department` kodunda, `Column` öznitelik SQL veri türü eşlemi değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="0ec58-647">Sütun, `Budget` DB'deki SQL Server para türü kullanılarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="0ec58-648">Sütun eşleme genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-648">Column mapping is generally not required.</span></span> <span data-ttu-id="0ec58-649">EF Core genellikle özellik için CLR türüne göre uygun SQL Server veri türünü seçer.</span><span class="sxs-lookup"><span data-stu-id="0ec58-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="0ec58-650">CLR `decimal` türü, SQL Server `decimal` türüyle eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="0ec58-651">`Budget`para birimi içindir ve para veri türü para birimi için daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0ec58-652">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="0ec58-653">FK ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="0ec58-654">Bir bölümün bir yöneticisi olabilir veya olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="0ec58-655">Yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-655">An administrator is always an instructor.</span></span> <span data-ttu-id="0ec58-656">Bu `InstructorID` nedenle özellik `Instructor` varlık için FK olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="0ec58-657">Gezinti özelliği adlandırılmış, `Administrator` `Instructor` ancak bir varlık tutar:</span><span class="sxs-lookup"><span data-stu-id="0ec58-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="0ec58-658">Önceki koddaki soru işareti (?) özelliğin geçersiz olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="0ec58-659">Bir bölümün birçok dersi olabilir, bu nedenle Kurslar navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="0ec58-660">Not: Sözleşmeye göre, EF Core nullable olmayan FK'lar ve çok-çok ilişkiler için basamaklı silme sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="0ec58-661">Basamaklı silme dairesel basamaklı silme kurallarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="0ec58-662">Dairesel basamaklı silme kuralları, geçiş eklendiğinde özel bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="0ec58-663">Örneğin, `Department.InstructorID` özellik nullable olarak tanımlanmışsa:</span><span class="sxs-lookup"><span data-stu-id="0ec58-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="0ec58-664">EF Core, eğitmen silindiğinde bölümü silmek için basamaklı silme kuralını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="0ec58-665">Eğitmen silindiğinde bölümün silinmesi amaçlanan davranış değildir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="0ec58-666">Aşağıdaki [akıcı API](#fluent-api-alternative-to-attributes) basamaklı yerine bir kısıtlama kuralı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="0ec58-667">Önceki kod bölüm-eğitmen ilişkisinde basamaklı silme devre dışı kalır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="0ec58-668">Kayıt varlığını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0ec58-668">Update the Enrollment entity</span></span>

<span data-ttu-id="0ec58-669">Kayıt kaydı, bir öğrencitarafından alınan bir ders için dir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-669">An enrollment record is for one course taken by one student.</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="0ec58-671">*Modelleri/Enrollment.cs'yi* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="0ec58-672">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="0ec58-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="0ec58-673">FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="0ec58-674">Kayıt kaydı tek bir kurs içindir, `CourseID` bu nedenle `Course` bir FK özelliği ve navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="0ec58-675">Kayıt kaydı bir öğrenci içindir, bu `StudentID` nedenle bir `Student` FK özelliği ve navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="0ec58-676">Çok-Çok İlişkiler</span><span class="sxs-lookup"><span data-stu-id="0ec58-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="0ec58-677">Varlıklar `Student` la `Course` çok arasında çok büyük bir ilişki var.</span><span class="sxs-lookup"><span data-stu-id="0ec58-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="0ec58-678">Varlık, `Enrollment` veritabanında *yükü olan* çok-çok birleştirme tablosu olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="0ec58-679">"Yük ile" `Enrollment` tablo (bu durumda, PK ve) `Grade`birleştirilmiş tablolar için FKs yanında ek veri içerdiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="0ec58-680">Aşağıdaki resimde, bu ilişkilerin varlık diyagramında nasıl göründüğü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="0ec58-681">(Bu diyagram EF 6.x için [EF Güç Araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="0ec58-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="0ec58-682">Diyagramı oluşturmak öğreticinin bir parçası değildir.)</span><span class="sxs-lookup"><span data-stu-id="0ec58-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Öğrenci-Kurs birçok ilişki için çok](complex-data-model/_static/student-course.png)

<span data-ttu-id="0ec58-684">Her ilişki satırının bir ucunda 1, diğerucunda bir yıldız (\*) vardır ve bu da bir-çok ilişkisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="0ec58-685">`Enrollment` Tablo not bilgilerini içermiyorsa, yalnızca iki FK'yı (ve)`CourseID` `StudentID`içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="0ec58-686">Yükü olmayan çok-çok birleştirme tablosu bazen saf birleştirme tablosu (PJT) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="0ec58-687">Ve `Instructor` `Course` varlıklar saf bir birleştirme tablosu nu kullanarak çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="0ec58-688">Not: EF 6.x, çok-çok ilişkiler için örtülü birleştirme tablolarını destekler, ancak EF Core desteklemez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="0ec58-689">Daha fazla bilgi [için, EF Core 2.0'daki çok-çok ilişkilere](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="0ec58-690">Ders Atama sı</span><span class="sxs-lookup"><span data-stu-id="0ec58-690">The CourseAssignment entity</span></span>

![Ders Atama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="0ec58-692">Aşağıdaki kodile *Modeller/DersAtama.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0ec58-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="0ec58-693">Eğitmen-Kurslar</span><span class="sxs-lookup"><span data-stu-id="0ec58-693">Instructor-to-Courses</span></span>

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="0ec58-695">Eğitmen-to-Kurslar çok-çok ilişki:</span><span class="sxs-lookup"><span data-stu-id="0ec58-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="0ec58-696">Bir varlık kümesi tarafından temsil edilmesi gereken bir birleştirme tablosu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="0ec58-697">Saf birleştirme tablosudur (yüksüz tablo).</span><span class="sxs-lookup"><span data-stu-id="0ec58-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="0ec58-698">Birleştirilmiş varlık `EntityName1EntityName2`adlamak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="0ec58-699">Örneğin, Bu deseni kullanarak Eğitmen-Kurslara `CourseInstructor`katılma tablosu .</span><span class="sxs-lookup"><span data-stu-id="0ec58-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="0ec58-700">Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="0ec58-701">Veri modelleri basit başlar ve büyür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-701">Data models start out simple and grow.</span></span> <span data-ttu-id="0ec58-702">Yük yok birleştirmeleri (PJT'ler) sık sık yükü içerecek şekilde gelişir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="0ec58-703">Açıklayıcı bir varlık adı ile başlayarak, birleştirme tablosu değiştiğinde adın değişmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="0ec58-704">İdeal olarak, birleştirme varlığı iş alanında kendi doğal (muhtemelen tek kelime) adı olurdu.</span><span class="sxs-lookup"><span data-stu-id="0ec58-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="0ec58-705">Örneğin, Kitaplar ve Müşteriler Derecelendirme adlı bir birleştirme varlığıyla bağlantılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="0ec58-706">Eğitmen-to-Kurslar için çok-çok ilişki, `CourseAssignment` yerine `CourseInstructor`tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="0ec58-707">Kompozit anahtar</span><span class="sxs-lookup"><span data-stu-id="0ec58-707">Composite key</span></span>

<span data-ttu-id="0ec58-708">FK'lar geçersiz değildir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-708">FKs are not nullable.</span></span> <span data-ttu-id="0ec58-709">İki FKs `CourseAssignment` (`InstructorID` `CourseID`ve ) birlikte benzersiz `CourseAssignment` tablonun her satırı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="0ec58-710">`CourseAssignment`özel bir PK gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="0ec58-711">Ve `InstructorID` `CourseID` özellikleri bileşik PK olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="0ec58-712">BILEŞIK PK'ları EF Core'a belirtmenin tek yolu *akıcı API'dir.*</span><span class="sxs-lookup"><span data-stu-id="0ec58-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="0ec58-713">Sonraki bölümde, bileşik PK'nın nasıl yapılandırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="0ec58-714">Kompozit anahtar şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0ec58-714">The composite key ensures:</span></span>

* <span data-ttu-id="0ec58-715">Bir kurs için birden fazla satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="0ec58-716">Bir eğitmen için birden fazla satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="0ec58-717">Aynı eğitmen ve kurs için birden fazla satıra izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="0ec58-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="0ec58-718">Birleştirme `Enrollment` varlığı kendi PK'sını tanımlar, bu nedenle bu tür yinelemeler mümkündür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="0ec58-719">Bu tür yinelemeleri önlemek için:</span><span class="sxs-lookup"><span data-stu-id="0ec58-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="0ec58-720">FK alanlarına benzersiz bir dizin ekleme veya</span><span class="sxs-lookup"><span data-stu-id="0ec58-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="0ec58-721">'ye `Enrollment` `CourseAssignment`benzer bir birincil bileşik anahtarla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="0ec58-722">Daha fazla bilgi için [Dizinler'e](/ef/core/modeling/indexes)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="0ec58-723">DB bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0ec58-723">Update the DB context</span></span>

<span data-ttu-id="0ec58-724">*Data/SchoolContext.cs'ye*aşağıdaki vurgulanan kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="0ec58-725">Önceki kod yeni varlıkları ekler ve varlığın `CourseAssignment` bileşik PK'sını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="0ec58-726">Özniteliklere akıcı API alternatifi</span><span class="sxs-lookup"><span data-stu-id="0ec58-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="0ec58-727">Önceki `OnModelCreating` koddaki yöntem, EF Core davranışını yapılandırmak için *akıcı API* kullanır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="0ec58-728">GENELLIKLE bir dizi yöntem çağrısını tek bir deyimde bir araya getirerek kullanıldığından API'ye "akıcı" denir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="0ec58-729">[Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) akıcı API bir örnektir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="0ec58-730">Bu öğreticide, akıcı API yalnızca özniteliklerle yapılamaz DB eşleme için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="0ec58-731">Ancak, akıcı API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="0ec58-732">Akıcı API `MinimumLength` ile uygulanamıyor gibi bazı öznitelikler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="0ec58-733">`MinimumLength`şema değişmez, yalnızca minimum uzunluk doğrulama kuralı nı uygular.</span><span class="sxs-lookup"><span data-stu-id="0ec58-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="0ec58-734">Bazı geliştiriciler, varlık sınıflarını "temiz" tutabilmek için akıcı API'yi yalnızca kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="0ec58-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="0ec58-735">Öznitelikler ve akıcı API karıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="0ec58-736">Yalnızca akıcı API (bileşik PK belirterek) ile yapılabilir bazı yapılandırmaları vardır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="0ec58-737">Yalnızca özniteliklerle yapılabilecek bazı yapılandırmalar`MinimumLength`vardır ( ).</span><span class="sxs-lookup"><span data-stu-id="0ec58-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="0ec58-738">Akıcı API veya öznitelikleri kullanmak için önerilen uygulama:</span><span class="sxs-lookup"><span data-stu-id="0ec58-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="0ec58-739">Bu iki yaklaşımdan birini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="0ec58-740">Seçilen yaklaşımı mümkün olduğunca tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="0ec58-741">Bu öğreticide kullanılan özniteliklerden bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0ec58-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="0ec58-742">Yalnızca doğrulama (örneğin, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="0ec58-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="0ec58-743">Yalnızca EF Core yapılandırması `HasKey`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="0ec58-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="0ec58-744">Doğrulama ve EF Core yapılandırması `[StringLength(50)]`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="0ec58-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="0ec58-745">Öznitelikler ve akıcı API hakkında daha fazla bilgi için yapılandırma [yöntemlerine](/ef/core/modeling/)bakın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="0ec58-746">İlişkileri Gösteren Varlık Diyagramı</span><span class="sxs-lookup"><span data-stu-id="0ec58-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="0ec58-747">Aşağıdaki resimde, EF Power Tools'un tamamlanan Okul modeli için oluşturduğu diyagram gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="0ec58-749">Önceki diyagram da gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="0ec58-750">Birkaç bir-to-çok ilişki satırları \*(1 için).</span><span class="sxs-lookup"><span data-stu-id="0ec58-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="0ec58-751">Varlıklar ve `Instructor` `OfficeAssignment` varlıklar arasındaki birden sıfıra veya bir ilişkisi satırına (1-0.1).</span><span class="sxs-lookup"><span data-stu-id="0ec58-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="0ec58-752">Varlıklar ve `Instructor` `Department` varlıklar arasındaki sıfır veya bir-birden çok ilişki çizgisi (0..1 ile \*) arasındaki ilişki çizgisi.</span><span class="sxs-lookup"><span data-stu-id="0ec58-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="0ec58-753">Test Verileri ile DB Tohum</span><span class="sxs-lookup"><span data-stu-id="0ec58-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="0ec58-754">*Veri/DbInitializer.cs*kodu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="0ec58-755">Önceki kod, yeni varlıklar için tohum verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="0ec58-756">Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="0ec58-757">Örnek veriler sınama için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-757">The sample data is used for testing.</span></span> <span data-ttu-id="0ec58-758">Kaç-çok `Enrollments` birleştirme tablolarının tohumlanabildiğini görün. `CourseAssignments`</span><span class="sxs-lookup"><span data-stu-id="0ec58-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="0ec58-759">Geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="0ec58-759">Add a migration</span></span>

<span data-ttu-id="0ec58-760">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="0ec58-763">Önceki komut, olası veri kaybı yla ilgili bir uyarı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="0ec58-764">`database update` Komut çalıştırılırsa, aşağıdaki hata üretilir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="0ec58-765">Geçişi uygulayın</span><span class="sxs-lookup"><span data-stu-id="0ec58-765">Apply the migration</span></span>

<span data-ttu-id="0ec58-766">Artık varolan bir veritabanınız olduğuna göre, gelecekteki değişiklikleri nasıl uygulayacağınızı düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="0ec58-767">Bu öğretici iki yaklaşım gösterir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="0ec58-768">Veritabanını bırakma ve yeniden oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="0ec58-769">[Geçişi varolan veritabanına uygulayın.](#applyexisting)</span><span class="sxs-lookup"><span data-stu-id="0ec58-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="0ec58-770">Bu yöntem daha karmaşık ve zaman alıcı olsa da, gerçek dünya, üretim ortamları için tercih edilen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="0ec58-771">**Not**: Bu, öğreticinin isteğe bağlı bir bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="0ec58-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="0ec58-772">Açılan adımları yapabilir ve yeniden oluşturabilir ve bu bölümü atlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0ec58-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="0ec58-773">Bu bölümdeki adımları izlemek istiyorsanız, bırakma ve yeniden oluşturma adımlarını yapmayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="0ec58-774">Veritabanını bırakma ve yeniden oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ec58-774">Drop and re-create the database</span></span>

<span data-ttu-id="0ec58-775">Güncelleştirilen `DbInitializer` kod, yeni varlıklar için tohum verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="0ec58-776">EF Core'u yeni bir DB oluşturmaya zorlamak için DB'yi bırakın ve güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ec58-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ec58-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0ec58-778">Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="0ec58-779">Yardım `Get-Help about_EntityFrameworkCore` bilgileri almak için PMC'den çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ec58-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ec58-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0ec58-781">Komut penceresini açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="0ec58-782">Proje klasörü *Startup.cs* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="0ec58-783">Komut penceresine aşağıdakileri girin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="0ec58-784">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-784">Run the app.</span></span> <span data-ttu-id="0ec58-785">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemi çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="0ec58-786">Yeni `DbInitializer.Initialize` DB'yi dolduruyor.</span><span class="sxs-lookup"><span data-stu-id="0ec58-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="0ec58-787">SSOX'ta DB'yi açın:</span><span class="sxs-lookup"><span data-stu-id="0ec58-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="0ec58-788">SSOX daha önce açılmışsa, **Yenile** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="0ec58-789">**Tablolar** düğümlerini genişletin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-789">Expand the **Tables** node.</span></span> <span data-ttu-id="0ec58-790">Oluşturulan tablolar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-790">The created tables are displayed.</span></span>

![SSOX tabloları](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="0ec58-792">Ders **Atama** tablosunu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="0ec58-793">**Ders Atama** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="0ec58-794">Ders **Atama** tablosunun veri içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-794">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX'ta Ders Atama verileri](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="0ec58-796">Geçişi varolan veritabanına uygulama</span><span class="sxs-lookup"><span data-stu-id="0ec58-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="0ec58-797">Bu bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-797">This section is optional.</span></span> <span data-ttu-id="0ec58-798">Bu adımlar yalnızca önceki Açılan'ı atlayıp veritabanı bölümünü [yeniden oluşturduğunuzda](#drop) çalışır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="0ec58-799">Geçişler varolan verilerle çalıştırıldığında, varolan verilerle memnun olmayan FK kısıtlamaları olabilir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="0ec58-800">Üretim verileriyle, varolan verileri geçirmek için adımlar atılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0ec58-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="0ec58-801">Bu bölümde FK kısıtlama ihlalleri nin düzeltilmesi ne örnek verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="0ec58-802">Yedekleme olmadan bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="0ec58-803">Önceki bölümü tamamlayıp veritabanını güncelleştirdiyseniz bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="0ec58-804">*{timestamp}_ComplexDataModel.cs* dosyası aşağıdaki kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="0ec58-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="0ec58-805">Önceki kod `DepartmentID` `Course` tabloya nullable olmayan bir FK ekler.</span><span class="sxs-lookup"><span data-stu-id="0ec58-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="0ec58-806">Önceki öğreticideki DB, tablonun `Course`geçişler tarafından güncelleştirilemesin diye satırlar içerir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="0ec58-807">Geçişin `ComplexDataModel` varolan verilerle çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="0ec58-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="0ec58-808">Yeni sütuna varsayılan`DepartmentID`değer vermek için kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="0ec58-809">Varsayılan departman olarak hareket etmek için "Temp" adlı sahte bir departman oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0ec58-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="0ec58-810">Yabancı anahtar kısıtlamalarını düzeltme</span><span class="sxs-lookup"><span data-stu-id="0ec58-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="0ec58-811">Sınıflar `Up` `ComplexDataModel` yöntemini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0ec58-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="0ec58-812">*{timestamp}_ComplexDataModel.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="0ec58-813">`DepartmentID` Sütunu tabloya ekleyen kod satırının `Course` dışına yorum yapın.</span><span class="sxs-lookup"><span data-stu-id="0ec58-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="0ec58-814">Aşağıdaki vurgulanan kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-814">Add the following highlighted code.</span></span> <span data-ttu-id="0ec58-815">Yeni kod `.CreateTable( name: "Department"` bloğun ardından gider:</span><span class="sxs-lookup"><span data-stu-id="0ec58-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="0ec58-816">Önceki değişikliklerle, varolan `Course` satırlar `ComplexDataModel` `Up` yöntem çalıştırdıktan sonra "Geçici" bölümüyle ilişkilendirilecektir.</span><span class="sxs-lookup"><span data-stu-id="0ec58-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="0ec58-817">Bir üretim uygulaması:</span><span class="sxs-lookup"><span data-stu-id="0ec58-817">A production app would:</span></span>

* <span data-ttu-id="0ec58-818">Yeni `Department` satırlara satır `Department` ve ilgili `Course` satırlar eklemek için kod veya komut dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0ec58-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="0ec58-819">"Temp" bölümünü veya varsayılan değeri `Course.DepartmentID`kullanma.</span><span class="sxs-lookup"><span data-stu-id="0ec58-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="0ec58-820">Sonraki öğretici ilgili verileri kapsar.</span><span class="sxs-lookup"><span data-stu-id="0ec58-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0ec58-821">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0ec58-821">Additional resources</span></span>

* [<span data-ttu-id="0ec58-822">Bu öğretici YouTube sürümü (Bölüm 1)</span><span class="sxs-lookup"><span data-stu-id="0ec58-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="0ec58-823">Bu öğretici YouTube sürümü (Bölüm 2)</span><span class="sxs-lookup"><span data-stu-id="0ec58-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="0ec58-824">[Önceki](xref:data/ef-rp/migrations)
> [Sonraki](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="0ec58-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
