---
title: Bölüm 5, Razor ASP.NET Core veri modelinde EF Core olan sayfalar
author: rick-anderson
description: RazorSayfaların 5. bölümü ve öğretici serisinin Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1ac9d6303daac82f3973c5d027fe1f453dc32e02
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054105"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="f1238-103">Bölüm 5, Razor ASP.NET Core veri modelinde EF Core olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="f1238-103">Part 5, Razor Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="f1238-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="f1238-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1238-105">Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır.</span><span class="sxs-lookup"><span data-stu-id="f1238-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="f1238-106">Bu öğreticide:</span><span class="sxs-lookup"><span data-stu-id="f1238-106">In this tutorial:</span></span>

* <span data-ttu-id="f1238-107">Daha fazla varlık ve ilişki eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f1238-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="f1238-108">Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="f1238-109">Tamamlanan veri modeli aşağıdaki çizimde gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f1238-109">The completed data model is shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="f1238-111">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-111">The Student entity</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="f1238-113">*Modeller/öğrenci. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="f1238-114">Yukarıdaki kod, bir `FullName` özelliği ekler ve var olan özelliklere aşağıdaki öznitelikleri ekler:</span><span class="sxs-lookup"><span data-stu-id="f1238-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="f1238-115">FullName hesaplanmış özelliği</span><span class="sxs-lookup"><span data-stu-id="f1238-115">The FullName calculated property</span></span>

<span data-ttu-id="f1238-116">`FullName` , iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="f1238-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="f1238-117">`FullName` ayarlanamaz, bu nedenle yalnızca bir get erişimcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="f1238-118">`FullName`Veritabanında hiçbir sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="f1238-119">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="f1238-120">Öğrenci kayıt tarihleri için, tüm sayfalar şu anda tarihle birlikte tarih ile görüntülenir, ancak yalnızca tarihin ilgili olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="f1238-121">Veri ek açıklaması özniteliklerini kullanarak, verileri gösteren her sayfada görüntü biçimini giderecek bir kod değişikliği yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f1238-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="f1238-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="f1238-123">Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir.</span><span class="sxs-lookup"><span data-stu-id="f1238-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="f1238-124">Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="f1238-125">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f1238-125">For example:</span></span>

* <span data-ttu-id="f1238-126">`mailto:`Bağlantı için otomatik olarak oluşturulur `DataType.EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="f1238-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="f1238-127">Tarih Seçici çoğu tarayıcıda için verilmiştir `DataType.Date` .</span><span class="sxs-lookup"><span data-stu-id="f1238-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="f1238-128">`DataType`ÖZNITELIK HTML 5 (bir `data-` veri Dash) özniteliklerini yayar.</span><span class="sxs-lookup"><span data-stu-id="f1238-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="f1238-129">`DataType`Öznitelikler doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="f1238-130">DisplayFormat özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="f1238-131">`DataType.Date` görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="f1238-132">Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="f1238-133">`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="f1238-134">`ApplyFormatInEditMode`Ayar, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="f1238-135">Bazı alanlar kullanmamanız gerekir `ApplyFormatInEditMode` .</span><span class="sxs-lookup"><span data-stu-id="f1238-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="f1238-136">Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="f1238-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="f1238-137">`DisplayFormat`Özniteliği kendi başına kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="f1238-138">Özniteliği özniteliği ile kullanmak genellikle iyi bir fikirdir `DataType` `DisplayFormat` .</span><span class="sxs-lookup"><span data-stu-id="f1238-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="f1238-139">`DataType`Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır.</span><span class="sxs-lookup"><span data-stu-id="f1238-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="f1238-140">`DataType`Özniteliği, içinde kullanılamayan aşağıdaki avantajları sağlar `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="f1238-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="f1238-141">Tarayıcı HTML5 özelliklerini etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="f1238-142">Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını ve istemci tarafı giriş doğrulamasını gösterin.</span><span class="sxs-lookup"><span data-stu-id="f1238-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="f1238-143">Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="f1238-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="f1238-144">Daha fazla bilgi için bkz. [ \<input> etiket Yardımcısı belgeleri](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f1238-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="f1238-145">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="f1238-146">Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="f1238-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="f1238-148">Gösterilen kod, adları 50 karakterden fazla olmayacak şekilde sınırlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="f1238-149">En küçük dize uzunluğunu ayarlayan bir örnek [daha sonra](#the-required-attribute)gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f1238-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="f1238-150">`StringLength`Öznitelik Ayrıca istemci tarafı ve sunucu tarafı doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="f1238-151">En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="f1238-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="f1238-152">Öznitelik, bir `StringLength` kullanıcının ad için boşluk girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="f1238-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="f1238-153">[Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) özniteliği, girişe kısıtlamalar uygulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="f1238-154">Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="f1238-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1238-156">**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="f1238-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="f1238-158">Önceki görüntüde tablo için şema gösterilmektedir `Student` .</span><span class="sxs-lookup"><span data-stu-id="f1238-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="f1238-159">Ad alanlarının türü vardır `nvarchar(MAX)` .</span><span class="sxs-lookup"><span data-stu-id="f1238-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="f1238-160">Bu öğreticide daha sonra bir geçiş oluşturulup uygulandığında, ad alanları `nvarchar(50)` dize uzunluğu özniteliklerinin bir sonucu olarak olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f1238-162">SQLite aracınız içinde tablo için sütun tanımlarını inceleyin `Student` .</span><span class="sxs-lookup"><span data-stu-id="f1238-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="f1238-163">Ad alanlarının türü vardır `Text` .</span><span class="sxs-lookup"><span data-stu-id="f1238-163">The name fields have type `Text`.</span></span> <span data-ttu-id="f1238-164">İlk ad alanının çağrıldığından emin olun `FirstMidName` .</span><span class="sxs-lookup"><span data-stu-id="f1238-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="f1238-165">Sonraki bölümde, bu sütunun adını olarak değiştirirsiniz `FirstName` .</span><span class="sxs-lookup"><span data-stu-id="f1238-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="f1238-166">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="f1238-167">Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="f1238-168">`Student`Modelinde `Column` öznitelik, `FirstMidName` özelliğin adını veritabanında "FirstName" olarak eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="f1238-169">Veritabanı oluşturulduğunda, modeldeki Özellik adları sütun adları için kullanılır ( `Column` özniteliği kullanıldığı durumlar dışında).</span><span class="sxs-lookup"><span data-stu-id="f1238-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="f1238-170">`Student`Model, `FirstMidName` birinci ad alanı için kullanılır çünkü alan de bir orta ad içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="f1238-171">Özniteliği ile `[Column]` , `Student.FirstMidName` veri modelinde `FirstName` tablonun sütunuyla eşlenir `Student` .</span><span class="sxs-lookup"><span data-stu-id="f1238-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="f1238-172">`Column`Özniteliği ekleme modeli, öğesini yedekleyen olarak değiştirir `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="f1238-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="f1238-173">' İ destekleyen model `SchoolContext` artık veritabanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="f1238-174">Bu tutarsızlık, daha sonra bu öğreticide bir geçiş eklenerek çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="f1238-175">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="f1238-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="f1238-176">`Required`Özniteliği, ad özellikleri gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="f1238-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="f1238-177">`Required`Öznitelik, değer türleri (örneğin,, `DateTime` `int` ve) gibi null yapılamayan türler için gerekli değildir `double` .</span><span class="sxs-lookup"><span data-stu-id="f1238-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="f1238-178">Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="f1238-179">`Required`Özniteliğinin zorlanmak üzere ile birlikte kullanılması gerekir `MinimumLength` `MinimumLength` .</span><span class="sxs-lookup"><span data-stu-id="f1238-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="f1238-180">`MinimumLength` ve `Required` doğrulamanın doğrulanmasını karşılamamak için boşluk.</span><span class="sxs-lookup"><span data-stu-id="f1238-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="f1238-181">`RegularExpression`Dize üzerinde tam denetim için özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f1238-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="f1238-182">Display özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="f1238-183">`Display`Öznitelik, metin kutuları için başlığın "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="f1238-184">Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".</span><span class="sxs-lookup"><span data-stu-id="f1238-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="f1238-185">Geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1238-185">Create a migration</span></span>

<span data-ttu-id="f1238-186">Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f1238-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="f1238-187">Bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1238-187">An exception is thrown.</span></span> <span data-ttu-id="f1238-188">`[Column]`Özniteliği, EF 'in adlı bir sütun bulmasını beklemesine neden olur `FirstName` , ancak veritabanındaki sütun adı hala kalır `FirstMidName` .</span><span class="sxs-lookup"><span data-stu-id="f1238-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1238-190">Hata iletisi aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="f1238-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="f1238-191">PMC 'de yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="f1238-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="f1238-192">Bu komutlardan ilki aşağıdaki uyarı iletisini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1238-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="f1238-193">Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1238-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="f1238-194">Veritabanındaki bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.</span><span class="sxs-lookup"><span data-stu-id="f1238-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="f1238-195">Öğrenci tablosunu SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="f1238-195">Open the Student table in SSOX:</span></span>

  ![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="f1238-197">Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi.</span><span class="sxs-lookup"><span data-stu-id="f1238-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="f1238-198">Ad sütunları artık `nvarchar(50)` .</span><span class="sxs-lookup"><span data-stu-id="f1238-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="f1238-199">Sütun adı `FirstMidName` olarak değiştirildi `FirstName` .</span><span class="sxs-lookup"><span data-stu-id="f1238-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f1238-201">Hata iletisi aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="f1238-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="f1238-202">Proje klasöründe bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="f1238-202">Open a command window in the project folder.</span></span> <span data-ttu-id="f1238-203">Yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="f1238-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="f1238-204">Veritabanı güncelleştirme komutu aşağıdaki örnekte olduğu gibi bir hata görüntüler:</span><span class="sxs-lookup"><span data-stu-id="f1238-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="f1238-205">Bu öğreticide, bu hatayı geçmenin yolu ilk geçişi silmek ve yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="f1238-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="f1238-206">Daha fazla bilgi için, [geçiş öğreticisinin](xref:data/ef-rp/migrations)en üstündeki SQLite uyarı notuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f1238-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="f1238-207">*Geçişler* klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="f1238-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="f1238-208">Veritabanını bırakmak, yeni bir başlangıç geçişi oluşturmak ve geçişi uygulamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="f1238-209">SQLite araclarınızın öğrenci tablosunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="f1238-210">FirstMidName sütunu artık FirstName.</span><span class="sxs-lookup"><span data-stu-id="f1238-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="f1238-211">Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f1238-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="f1238-212">Saatin giriş veya tarih ile birlikte görüntülenmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f1238-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="f1238-213">**Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girmeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="f1238-214">Aşağıdaki bölümlerde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="f1238-215">Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="f1238-216">Eğitmen varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-216">The Instructor Entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="f1238-218">Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="f1238-219">Birden çok öznitelik tek bir satırda olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="f1238-220">`HireDate`Öznitelikler aşağıdaki gibi yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="f1238-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="f1238-221">Gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-221">Navigation properties</span></span>

<span data-ttu-id="f1238-222">`CourseAssignments`Ve `OfficeAssignment` özellikleri gezinti özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="f1238-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="f1238-223">Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="f1238-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="f1238-224">Bir eğitmenin en fazla bir ofisi olabilir, bu nedenle `OfficeAssignment` özellik tek bir varlık içerir `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="f1238-225">`OfficeAssignment` hiçbir Office atanmamışsa null olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="f1238-226">OfficeAssignment varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-226">The OfficeAssignment entity</span></span>

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="f1238-228">Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="f1238-229">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-229">The Key attribute</span></span>

<span data-ttu-id="f1238-230">Öznitelik, özellik `[Key]` adı Classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="f1238-231">Ve varlıkları arasında bire sıfır veya-bir ilişkisi vardır `Instructor` `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="f1238-232">Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="f1238-233">`OfficeAssignment`PK Ayrıca varlığa ait yabancı anahtardır (FK) `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="f1238-234">EF Core `InstructorID` , `OfficeAssignment` `InstructorID` ID veya classnameıd adlandırma kuralını takip ettiğinden, ' ın ' ın ' i tarafından otomatik olarak tanıyamamaktadır.</span><span class="sxs-lookup"><span data-stu-id="f1238-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="f1238-235">Bu nedenle, `Key` ÖZNITELIĞI PK olarak tanımlamak için kullanılır `InstructorID` :</span><span class="sxs-lookup"><span data-stu-id="f1238-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="f1238-236">Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="f1238-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="f1238-237">Eğitmen gezintisi özelliği</span><span class="sxs-lookup"><span data-stu-id="f1238-237">The Instructor navigation property</span></span>

<span data-ttu-id="f1238-238">`Instructor.OfficeAssignment`Belirtilen bir eğitmen için bir satır olmadığı için, gezinti özelliği null olabilir `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="f1238-239">Bir eğitmenin Office ataması olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="f1238-240">`OfficeAssignment.Instructor`Yabancı anahtar `InstructorID` türü `int` null yapılamayan bir değer türü olduğundan, gezinti özelliği her zaman bir eğitmen varlığına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="f1238-241">Bir Office ataması, bir eğitmen olmadan bulunamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="f1238-242">Bir `Instructor` varlık ilişkili bir varlığa sahip olduğunda `OfficeAssignment` , her varlığın gezinti özelliğinde diğer bir başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="f1238-243">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-243">The Course Entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="f1238-245">*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="f1238-246">`Course`Varlığın yabancı anahtar (FK) özelliği vardır `DepartmentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="f1238-247">`DepartmentID` ilgili varlığa işaret eder `Department` .</span><span class="sxs-lookup"><span data-stu-id="f1238-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="f1238-248">`Course`Varlığın bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="f1238-249">EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için yabancı anahtar özelliği gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="f1238-250">EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="f1238-251">EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="f1238-252">Ancak, doğrudan veri modelinde FK dahil edilmesi, güncelleştirmelerin daha basit ve daha verimli olmasını sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="f1238-253">Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün.</span><span class="sxs-lookup"><span data-stu-id="f1238-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="f1238-254">Bir kurs varlığı düzenlemek üzere getirilirken:</span><span class="sxs-lookup"><span data-stu-id="f1238-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="f1238-255">`Department`Açık bir şekilde yüklenmediyse özelliği null olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="f1238-256">Kurs varlığını güncelleştirmek için `Department` önce varlığın getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="f1238-257">FK özelliği `DepartmentID` veri modeline dahil edildiğinde, `Department` bir güncelleştirmeden önce varlığı getirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="f1238-258">DatabaseGenerated özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="f1238-259">`[DatabaseGenerated(DatabaseGeneratedOption.None)]`Özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="f1238-260">Varsayılan olarak, EF Core PK değerlerinin veritabanı tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="f1238-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="f1238-261">Veritabanı tarafından oluşturulan genellikle en iyi yaklaşım vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="f1238-262">`Course`Varlıklar için Kullanıcı PK 'yi belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="f1238-263">Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.</span><span class="sxs-lookup"><span data-stu-id="f1238-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="f1238-264">`DatabaseGenerated`Öznitelik varsayılan değerler oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="f1238-265">Örneğin, veritabanı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="f1238-266">Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="f1238-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="f1238-267">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="f1238-268">Varlıktaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri `Course` aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="f1238-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="f1238-269">Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="f1238-270">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="f1238-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="f1238-271">Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur:</span><span class="sxs-lookup"><span data-stu-id="f1238-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="f1238-272">`CourseAssignment`[daha sonra](#many-to-many-relationships)açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f1238-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="f1238-273">Departman varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-273">The Department entity</span></span>

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

<span data-ttu-id="f1238-275">Aşağıdaki kodla *modeller/departman. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="f1238-276">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-276">The Column attribute</span></span>

<span data-ttu-id="f1238-277">Daha önce `Column` öznitelik, sütun adı eşlemesini değiştirmek için kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="f1238-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="f1238-278">`Department`Varlığın kodunda, `Column` özniteliği SQL veri türü eşlemesini değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="f1238-279">`Budget`Sütun, veritabanında SQL Server para türü kullanılarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="f1238-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="f1238-280">Sütun eşlemesi genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f1238-280">Column mapping is generally not required.</span></span> <span data-ttu-id="f1238-281">EF Core, özelliğin CLR türüne göre uygun SQL Server veri türünü seçer.</span><span class="sxs-lookup"><span data-stu-id="f1238-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="f1238-282">CLR `decimal` türü SQL Server bir `decimal` türe eşlenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="f1238-283">`Budget` para birimi için, para veri türü ise para birimi için daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="f1238-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="f1238-284">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="f1238-285">FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="f1238-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="f1238-286">Bir departman yönetici olabilir veya olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="f1238-287">Yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="f1238-287">An administrator is always an instructor.</span></span> <span data-ttu-id="f1238-288">Bu nedenle, `InstructorID` özelliği VARLıĞA FK olarak dahil edilir `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="f1238-289">Gezinti özelliği adlandırılır `Administrator` ancak bir `Instructor` varlık barındırır:</span><span class="sxs-lookup"><span data-stu-id="f1238-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="f1238-290">Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.</span><span class="sxs-lookup"><span data-stu-id="f1238-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="f1238-291">Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="f1238-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="f1238-292">Kural gereği EF Core, null yapılamayan ve çok-çok ilişkiler için basamaklı silme imkanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="f1238-293">Bu varsayılan davranış, dairesel basamaklı silme kurallarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="f1238-294">Bir geçiş eklendiğinde dairesel basamaklı silme kuralları bir özel duruma neden olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="f1238-295">Örneğin, `Department.InstructorID` özellik null yapılamayan olarak tanımlanmışsa EF Core basamaklı silme kuralı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="f1238-296">Bu durumda, yönetici olarak atanan eğitmen silindiğinde departman silinir.</span><span class="sxs-lookup"><span data-stu-id="f1238-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="f1238-297">Bu senaryoda kısıtlama kuralı daha anlamlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f1238-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="f1238-298">Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) bir kısıtlama kuralı ayarlar ve art arda silmeyi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="f1238-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="f1238-299">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-299">The Enrollment entity</span></span>

<span data-ttu-id="f1238-300">Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.</span><span class="sxs-lookup"><span data-stu-id="f1238-300">An enrollment record is for one course taken by one student.</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="f1238-302">*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="f1238-303">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="f1238-304">FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="f1238-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="f1238-305">Kayıt kaydı tek bir kurs için olduğundan, `CourseID` FK özelliği ve bir `Course` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="f1238-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="f1238-306">Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve bir `Student` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="f1238-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="f1238-307">Çoktan çoğa Ilişkiler</span><span class="sxs-lookup"><span data-stu-id="f1238-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="f1238-308">Ve varlıkları arasında çoktan çoğa bir ilişki vardır `Student` `Course` .</span><span class="sxs-lookup"><span data-stu-id="f1238-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="f1238-309">`Enrollment`Varlık, veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="f1238-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="f1238-310">"Yükle", `Enrollment` tablonun birleştirilmiş tablolar Için FKs 'ler (Bu durumda, PK ve) gibi ek veriler içerdiği anlamına gelir `Grade` .</span><span class="sxs-lookup"><span data-stu-id="f1238-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="f1238-311">Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="f1238-312">(Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="f1238-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="f1238-313">Diyagram oluşturmak öğreticinin bir parçası değildir.)</span><span class="sxs-lookup"><span data-stu-id="f1238-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Çok fazla ilişki Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="f1238-315">Her ilişki satırında 1 bir sonda ve diğeri de bir yıldız işareti (\*) bulunur. Bu, bire çok ilişkiyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="f1238-316">Tablo, `Enrollment` sınıf bilgileri içermiyorsa, yalnızca Iki FKs ( `CourseID` ve) içermesi gerekir `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="f1238-317">Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="f1238-318">`Instructor`Ve `Course` varlıklarının saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="f1238-319">Note: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir.</span><span class="sxs-lookup"><span data-stu-id="f1238-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="f1238-320">Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f1238-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="f1238-321">Courseatama varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-321">The CourseAssignment entity</span></span>

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="f1238-323">Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="f1238-324">Eğitmenden çok-çok ilişkisi için bir JOIN tablosu gerekir ve bu ekleme tablosu için varlık kurs atamasıdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="f1238-326">Bir JOIN varlığına ad vermek yaygındır `EntityName1EntityName2` .</span><span class="sxs-lookup"><span data-stu-id="f1238-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="f1238-327">Örneğin, bu model kullanılarak eğitmen-kurslar 'a katılması tablosu olacaktır `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="f1238-328">Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f1238-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="f1238-329">Veri modelleri basit ve büyümeye başlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-329">Data models start out simple and grow.</span></span> <span data-ttu-id="f1238-330">Yük (PJTs) olmayan ekleme tabloları genellikle yükü içerecek şekilde gelişmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="f1238-331">Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="f1238-332">İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="f1238-333">Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="f1238-334">Eğitmenin kursa çok-çok ilişkisi için `CourseAssignment` tercih edilir `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="f1238-335">Bileşik anahtar</span><span class="sxs-lookup"><span data-stu-id="f1238-335">Composite key</span></span>

<span data-ttu-id="f1238-336">(Ve) içindeki iki FKs, `CourseAssignment` `InstructorID` `CourseID` tablonun her satırını benzersiz bir şekilde tanımlar `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="f1238-337">`CourseAssignment` adanmış bir PK gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="f1238-338">`InstructorID`Ve `CourseID` özellikleri BILEŞIK bir PK olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="f1238-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="f1238-339">EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir.</span><span class="sxs-lookup"><span data-stu-id="f1238-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="f1238-340">Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="f1238-341">Bileşik anahtar şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="f1238-341">The composite key ensures that:</span></span>

* <span data-ttu-id="f1238-342">Tek bir kurs için birden çok satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="f1238-343">Birden çok satıra bir eğitmen için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="f1238-344">Aynı eğitmen ve kurs için birden çok satıra izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="f1238-345">`Enrollment`JOIN varlığı kendı PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür.</span><span class="sxs-lookup"><span data-stu-id="f1238-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="f1238-346">Bu tür yinelemeleri engellemek için:</span><span class="sxs-lookup"><span data-stu-id="f1238-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="f1238-347">FK alanlara benzersiz bir dizin ekleyin veya</span><span class="sxs-lookup"><span data-stu-id="f1238-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="f1238-348">`Enrollment`İle benzer bir birincil bileşik anahtarla yapılandırın `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="f1238-349">Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="f1238-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="f1238-350">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="f1238-350">Update the database context</span></span>

<span data-ttu-id="f1238-351">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="f1238-352">Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın BILEŞIK PK 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="f1238-353">Tutarlı API 'nin özniteliklere alternatif</span><span class="sxs-lookup"><span data-stu-id="f1238-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="f1238-354">`OnModelCreating`Önceki koddaki yöntemi EF Core davranışını yapılandırmak için *Fluent API* kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1238-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="f1238-355">Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="f1238-356">[Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="f1238-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="f1238-357">Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını veritabanı eşlemesi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="f1238-358">Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="f1238-359">Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="f1238-360">`MinimumLength` şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="f1238-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="f1238-361">Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="f1238-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="f1238-362">Öznitelikler ve Fluent API karışık olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="f1238-363">Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="f1238-364">Yalnızca özniteliklerle () yapılabilecek bazı konfigürasyonlar vardır `MinimumLength` .</span><span class="sxs-lookup"><span data-stu-id="f1238-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="f1238-365">Fluent API veya özniteliklerini kullanmak için önerilen uygulama:</span><span class="sxs-lookup"><span data-stu-id="f1238-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="f1238-366">Bu iki yaklaşımdan birini seçin.</span><span class="sxs-lookup"><span data-stu-id="f1238-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="f1238-367">Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="f1238-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="f1238-368">Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f1238-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="f1238-369">Yalnızca doğrulama (örneğin, `MinimumLength` ).</span><span class="sxs-lookup"><span data-stu-id="f1238-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="f1238-370">Yalnızca yapılandırma EF Core (örneğin, `HasKey` ).</span><span class="sxs-lookup"><span data-stu-id="f1238-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="f1238-371">Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]` ).</span><span class="sxs-lookup"><span data-stu-id="f1238-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="f1238-372">Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="f1238-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="f1238-373">Varlık diyagramı</span><span class="sxs-lookup"><span data-stu-id="f1238-373">Entity diagram</span></span>

<span data-ttu-id="f1238-374">Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="f1238-376">Önceki diyagramda şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="f1238-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="f1238-377">Birden çok çoktan çoğa ilişki satırı (1 ile \* ).</span><span class="sxs-lookup"><span data-stu-id="f1238-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="f1238-378">Ve varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1) `Instructor` `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="f1238-379">Ve varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-\*) `Instructor` `Department` .</span><span class="sxs-lookup"><span data-stu-id="f1238-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="f1238-380">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="f1238-380">Seed the database</span></span>

<span data-ttu-id="f1238-381">*Data/Dbınizer. cs* dosyasındaki kodu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="f1238-382">Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="f1238-383">Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="f1238-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="f1238-384">Örnek veriler test için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-384">The sample data is used for testing.</span></span> <span data-ttu-id="f1238-385">`Enrollments` `CourseAssignments` Birden çok-çok JOIN tablosunun nasıl çalıştırılabilir olduğunu gösteren örnekler için bkz. ve.</span><span class="sxs-lookup"><span data-stu-id="f1238-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="f1238-386">Geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="f1238-386">Add a migration</span></span>

<span data-ttu-id="f1238-387">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1238-389">PMC 'de aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1238-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="f1238-390">Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f1238-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="f1238-391">`database update`Komut çalıştırıldığında, aşağıdaki hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1238-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="f1238-392">Sonraki bölümde, bu hatayla ilgili ne yapılacağını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="f1238-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f1238-394">Bir geçiş ekler ve `database update` komutu çalıştırırsanız, aşağıdaki hata üretilir:</span><span class="sxs-lookup"><span data-stu-id="f1238-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="f1238-395">Sonraki bölümde, bu hatanın nasıl önleneceğini görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="f1238-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="f1238-396">Geçişi uygulayın veya bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="f1238-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="f1238-397">Artık var olan bir veritabanınız olduğuna göre, değişikliklere nasıl uygulanacağını düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="f1238-398">Bu öğreticide iki alternatif gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f1238-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="f1238-399">[Veritabanını bırakıp yeniden oluşturun](#drop).</span><span class="sxs-lookup"><span data-stu-id="f1238-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="f1238-400">SQLite kullanıyorsanız bu bölümü seçin.</span><span class="sxs-lookup"><span data-stu-id="f1238-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="f1238-401">[Geçişi mevcut veritabanına uygulayın](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="f1238-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="f1238-402">Bu bölümdeki yönergeler yalnızca SQL Server için geçerlidir, **SQLite için değildir**.</span><span class="sxs-lookup"><span data-stu-id="f1238-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="f1238-403">Her iki seçenek de SQL Server için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f1238-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="f1238-404">Apply-Migration yöntemi daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="f1238-405">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="f1238-405">Drop and re-create the database</span></span>

<span data-ttu-id="f1238-406">SQL Server kullanıyorsanız ve aşağıdaki bölümde uygulanacak geçiş yaklaşımını yapmak istiyorsanız [Bu bölümü atlayın](#apply-the-migration) .</span><span class="sxs-lookup"><span data-stu-id="f1238-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="f1238-407">Yeni bir veritabanı oluşturmak için EF Core zorlamak için veritabanını bırakıp güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1238-409">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="f1238-410">*Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f1238-412">Bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f1238-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="f1238-413">Proje klasörü *Contosouniversity. csproj* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="f1238-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="f1238-414">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="f1238-415">*Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="f1238-416">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1238-416">Run the app.</span></span> <span data-ttu-id="f1238-417">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="f1238-418">`DbInitializer.Initialize`Yeni veritabanını doldurur.</span><span class="sxs-lookup"><span data-stu-id="f1238-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1238-420">Veritabanını SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="f1238-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="f1238-421">Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="f1238-422">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="f1238-422">Expand the **Tables** node.</span></span> <span data-ttu-id="f1238-423">Oluşturulan tablolar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-423">The created tables are displayed.</span></span>

  ![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="f1238-425">**Courseatama** tablosunu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f1238-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="f1238-426">**Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f1238-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="f1238-427">**Courseatama** tablosunun veri içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f1238-430">Veritabanını incelemek için SQLite aracınızı kullanın:</span><span class="sxs-lookup"><span data-stu-id="f1238-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="f1238-431">Yeni tablolar ve sütunlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-431">New tables and columns.</span></span>
* <span data-ttu-id="f1238-432">Tablolardaki verileri, örneğin, **Courseatama** tablosu.</span><span class="sxs-lookup"><span data-stu-id="f1238-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="f1238-433">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="f1238-433">Apply the migration</span></span>

<span data-ttu-id="f1238-434">Bu bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-434">This section is optional.</span></span> <span data-ttu-id="f1238-435">Bu adımlar yalnızca SQL Server LocalDB için çalışır ve yalnızca önceki [bırakma ve veritabanını yeniden oluştur](#drop) bölümünü atladıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="f1238-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="f1238-436">Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="f1238-437">Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="f1238-438">Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="f1238-439">Bu kod değişikliklerini yedekleme olmadan yapmayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="f1238-440">Önceki [bırakmayı tamamlayıp veritabanını yeniden oluşturduktan sonra](#drop) Bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="f1238-441">*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="f1238-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="f1238-442">Yukarıdaki kod, tabloya null yapılamayan bir `DepartmentID` FK ekler `Course` .</span><span class="sxs-lookup"><span data-stu-id="f1238-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="f1238-443">Önceki öğreticideki veritabanı, içindeki satırları içerir `Course` , böylece tablo geçişler tarafından güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="f1238-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="f1238-444">`ComplexDataModel`Geçişin mevcut verilerle çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="f1238-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="f1238-445">Yeni sütuna () varsayılan değer vermek için kodu değiştirin `DepartmentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="f1238-446">Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f1238-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="f1238-447">Yabancı anahtar kısıtlamalarını çözme</span><span class="sxs-lookup"><span data-stu-id="f1238-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="f1238-448">`ComplexDataModel`Geçiş sınıfında, `Up` yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="f1238-449">*{Timestamp} _ComplexDataModel. cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="f1238-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="f1238-450">Sütunu tabloya ekleyen kod satırını açıklama `DepartmentID` olarak yapın `Course` .</span><span class="sxs-lookup"><span data-stu-id="f1238-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="f1238-451">Aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-451">Add the following highlighted code.</span></span> <span data-ttu-id="f1238-452">Yeni kod bloğundan sonra geçer `.CreateTable( name: "Department"` :</span><span class="sxs-lookup"><span data-stu-id="f1238-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="f1238-453">Önceki değişikliklerle, varolan satırlar, `Course` Yöntem çalıştıktan sonra "geçici" departmanıyla ilişkilendirilir `ComplexDataModel.Up` .</span><span class="sxs-lookup"><span data-stu-id="f1238-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="f1238-454">Burada gösterilen durumu işlemenin yolu, bu öğretici için basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f1238-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="f1238-455">Bir üretim uygulaması şöyle olacaktır:</span><span class="sxs-lookup"><span data-stu-id="f1238-455">A production app would:</span></span>

* <span data-ttu-id="f1238-456">`Department`Yeni satırlara satırlar ve ilgili satırlar eklemek için kod veya komut dosyaları ekleyin `Course` `Department` .</span><span class="sxs-lookup"><span data-stu-id="f1238-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="f1238-457">İçin "geçici" Departmanı veya varsayılan değeri kullanmayın `Course.DepartmentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1238-459">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="f1238-460">`DbInitializer.Initialize`Yöntemi yalnızca boş bir veritabanıyla çalışacak şekilde tasarlandığından, öğrenci ve kurs tablolarındaki tüm satırları silmek IÇIN SSOX kullanın.</span><span class="sxs-lookup"><span data-stu-id="f1238-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="f1238-461">(Cascade silme, kayıt tablosundan işlem gerçekleştirir.)</span><span class="sxs-lookup"><span data-stu-id="f1238-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f1238-463">Visual Studio Code ile SQL Server LocalDB kullanıyorsanız şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="f1238-464">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1238-464">Run the app.</span></span> <span data-ttu-id="f1238-465">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="f1238-466">`DbInitializer.Initialize`Yeni veritabanını doldurur.</span><span class="sxs-lookup"><span data-stu-id="f1238-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f1238-467">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="f1238-467">Next steps</span></span>

<span data-ttu-id="f1238-468">Sonraki iki öğretici ilgili verilerin nasıl okunacağını ve güncelleştirilmesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f1238-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f1238-469">[Önceki öğretici](xref:data/ef-rp/migrations) 
>  [Sonraki öğretici](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="f1238-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1238-470">Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır.</span><span class="sxs-lookup"><span data-stu-id="f1238-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="f1238-471">Bu öğreticide:</span><span class="sxs-lookup"><span data-stu-id="f1238-471">In this tutorial:</span></span>

* <span data-ttu-id="f1238-472">Daha fazla varlık ve ilişki eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f1238-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="f1238-473">Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="f1238-474">Tamamlanan veri modeli için varlık sınıfları aşağıdaki çizimde gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f1238-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="f1238-476">Çözemediğiniz sorunlarla karşılaşırsanız, [Tamamlanmış uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.</span><span class="sxs-lookup"><span data-stu-id="f1238-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="f1238-477">Veri modelini özniteliklerle özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f1238-477">Customize the data model with attributes</span></span>

<span data-ttu-id="f1238-478">Bu bölümde, veri modeli öznitelikler kullanılarak özelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="f1238-479">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-479">The DataType attribute</span></span>

<span data-ttu-id="f1238-480">Öğrenci sayfaları Şu anda kayıt tarihinin saatini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f1238-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="f1238-481">Genellikle, tarih alanları saati değil yalnızca tarihi gösterir.</span><span class="sxs-lookup"><span data-stu-id="f1238-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="f1238-482">*Modelleri/öğrenci. cs* 'yi aşağıdaki vurgulanmış kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="f1238-483">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="f1238-484">Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir.</span><span class="sxs-lookup"><span data-stu-id="f1238-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="f1238-485">Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="f1238-486">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f1238-486">For example:</span></span>

* <span data-ttu-id="f1238-487">`mailto:`Bağlantı için otomatik olarak oluşturulur `DataType.EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="f1238-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="f1238-488">Tarih Seçici çoğu tarayıcıda için verilmiştir `DataType.Date` .</span><span class="sxs-lookup"><span data-stu-id="f1238-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="f1238-489">`DataType`ÖZNITELIĞI `data-` HTML 5 TARAYıCıLARıNıN kullandığı HTML 5 (bir veri Dash) özniteliklerini yayar.</span><span class="sxs-lookup"><span data-stu-id="f1238-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="f1238-490">`DataType`Öznitelikler doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="f1238-491">`DataType.Date` görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="f1238-492">Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="f1238-493">`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f1238-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="f1238-494">`ApplyFormatInEditMode`Ayar, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="f1238-495">Bazı alanlar kullanmamanız gerekir `ApplyFormatInEditMode` .</span><span class="sxs-lookup"><span data-stu-id="f1238-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="f1238-496">Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="f1238-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="f1238-497">`DisplayFormat`Özniteliği kendi başına kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="f1238-498">Özniteliği özniteliği ile kullanmak genellikle iyi bir fikirdir `DataType` `DisplayFormat` .</span><span class="sxs-lookup"><span data-stu-id="f1238-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="f1238-499">`DataType`Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır.</span><span class="sxs-lookup"><span data-stu-id="f1238-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="f1238-500">`DataType`Özniteliği, içinde kullanılamayan aşağıdaki avantajları sağlar `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="f1238-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="f1238-501">Tarayıcı HTML5 özelliklerini etkinleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="f1238-502">Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını, istemci tarafı giriş doğrulamasını vb. göster</span><span class="sxs-lookup"><span data-stu-id="f1238-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="f1238-503">Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="f1238-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="f1238-504">Daha fazla bilgi için bkz. [ \<input> etiket Yardımcısı belgeleri](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f1238-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="f1238-505">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1238-505">Run the app.</span></span> <span data-ttu-id="f1238-506">Öğrenciler dizin sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f1238-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="f1238-507">Süreler artık görüntülenmiyor.</span><span class="sxs-lookup"><span data-stu-id="f1238-507">Times are no longer displayed.</span></span> <span data-ttu-id="f1238-508">Modeli kullanan her görünüm `Student` tarihi zaman içinde görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f1238-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Öğrenciler Dizin sayfası tarihleri zamansız gösterme](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="f1238-510">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-510">The StringLength attribute</span></span>

<span data-ttu-id="f1238-511">Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="f1238-512">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="f1238-513">`StringLength`Öznitelik Ayrıca istemci tarafı ve sunucu tarafı doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="f1238-514">En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="f1238-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="f1238-515">`Student`Modeli aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="f1238-516">Yukarıdaki kod, adları 50 karakterden fazla olmayacak şekilde sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="f1238-517">Öznitelik, bir `StringLength` kullanıcının ad için boşluk girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="f1238-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="f1238-518">[Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) özniteliği, girişe kısıtlamalar uygulamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="f1238-519">Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="f1238-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="f1238-520">Uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-520">Run the app:</span></span>

* <span data-ttu-id="f1238-521">Öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f1238-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="f1238-522">**Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="f1238-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="f1238-523">**Oluştur**' u seçin, istemci tarafı doğrulama bir hata iletisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="f1238-523">Select **Create**, client-side validation shows an error message.</span></span>

![Öğrenciler Dizin sayfası dize uzunluğu hatalarını gösteriyor](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="f1238-525">**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="f1238-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="f1238-527">Önceki görüntüde tablo için şema gösterilmektedir `Student` .</span><span class="sxs-lookup"><span data-stu-id="f1238-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="f1238-528">`nvarchar(MAX)`Veritabanı üzerinde geçişler çalıştırılmadığından ad alanlarının türü var.</span><span class="sxs-lookup"><span data-stu-id="f1238-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="f1238-529">Bu öğreticide daha sonra geçişler çalıştırıldığında, ad alanları olur `nvarchar(50)` .</span><span class="sxs-lookup"><span data-stu-id="f1238-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="f1238-530">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-530">The Column attribute</span></span>

<span data-ttu-id="f1238-531">Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="f1238-532">Bu bölümde, özniteliği, `Column` `FirstMidName` ÖZELLIĞIN adını DB 'Deki "FirstName" olarak eşlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="f1238-533">DB oluşturulduğunda modeldeki Özellik adları sütun adları için kullanılır ( `Column` özniteliği kullanıldığı durumlar dışında).</span><span class="sxs-lookup"><span data-stu-id="f1238-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="f1238-534">`Student`Model, `FirstMidName` birinci ad alanı için kullanılır çünkü alan de bir orta ad içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="f1238-535">*Student.cs* dosyasını aşağıdaki vurgulanmış kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="f1238-536">Uygulamanın önceki değişikliği ile `Student.FirstMidName` `FirstName` tablonun sütunuyla eşlenir `Student` .</span><span class="sxs-lookup"><span data-stu-id="f1238-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="f1238-537">`Column`Özniteliği ekleme modeli, öğesini yedekleyen olarak değiştirir `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="f1238-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="f1238-538">' İ destekleyen model `SchoolContext` artık veritabanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="f1238-539">Geçiş uygulamadan önce uygulama çalışıyorsa aşağıdaki özel durum oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1238-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="f1238-540">DB 'yi güncelleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="f1238-540">To update the DB:</span></span>

* <span data-ttu-id="f1238-541">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-541">Build the project.</span></span>
* <span data-ttu-id="f1238-542">Proje klasöründe bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="f1238-542">Open a command window in the project folder.</span></span> <span data-ttu-id="f1238-543">Yeni bir geçiş oluşturmak ve DB 'yi güncelleştirmek için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="f1238-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="f1238-546">`migrations add ColumnFirstName`Komut aşağıdaki uyarı iletisini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1238-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="f1238-547">Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1238-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="f1238-548">VERITABANıNDAKI bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.</span><span class="sxs-lookup"><span data-stu-id="f1238-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="f1238-549">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="f1238-549">Test the app.</span></span>

<span data-ttu-id="f1238-550">Öğrenci tablosunu SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="f1238-550">Open the Student table in SSOX:</span></span>

![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="f1238-552">Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi.</span><span class="sxs-lookup"><span data-stu-id="f1238-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="f1238-553">Ad sütunları artık `nvarchar(50)` .</span><span class="sxs-lookup"><span data-stu-id="f1238-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="f1238-554">Sütun adı `FirstMidName` olarak değiştirildi `FirstName` .</span><span class="sxs-lookup"><span data-stu-id="f1238-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="f1238-555">Aşağıdaki bölümde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="f1238-556">Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="f1238-557">Öğrenci varlık güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="f1238-557">Student entity update</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="f1238-559">*Modelleri/öğrenci. cs* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="f1238-560">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="f1238-560">The Required attribute</span></span>

<span data-ttu-id="f1238-561">`Required`Özniteliği, ad özellikleri gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="f1238-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="f1238-562">`Required`Öznitelik, değer türleri ( `DateTime` , `int` , vb.) gibi null yapılamayan türler için gerekli değildir `double` .</span><span class="sxs-lookup"><span data-stu-id="f1238-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="f1238-563">Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="f1238-564">Öznitelik, `Required` özniteliğinde bir minimum length parametresiyle değiştirilebilir `StringLength` :</span><span class="sxs-lookup"><span data-stu-id="f1238-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="f1238-565">Display özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-565">The Display attribute</span></span>

<span data-ttu-id="f1238-566">`Display`Öznitelik, metin kutuları için başlığın "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="f1238-567">Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".</span><span class="sxs-lookup"><span data-stu-id="f1238-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="f1238-568">FullName hesaplanmış özelliği</span><span class="sxs-lookup"><span data-stu-id="f1238-568">The FullName calculated property</span></span>

<span data-ttu-id="f1238-569">`FullName` , iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="f1238-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="f1238-570">`FullName` ayarlanamaz, yalnızca bir get erişimcisine sahip.</span><span class="sxs-lookup"><span data-stu-id="f1238-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="f1238-571">`FullName`Veritabanında hiçbir sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="f1238-572">Eğitmen varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1238-572">Create the Instructor Entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="f1238-574">Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="f1238-575">Birden çok öznitelik tek bir satırda olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="f1238-576">`HireDate`Öznitelikler aşağıdaki gibi yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="f1238-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="f1238-577">Courseatamalar ve OfficeAssignment gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="f1238-578">`CourseAssignments`Ve `OfficeAssignment` özellikleri gezinti özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="f1238-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="f1238-579">Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="f1238-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="f1238-580">Bir gezinti özelliği birden çok varlık tutuyorsa:</span><span class="sxs-lookup"><span data-stu-id="f1238-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="f1238-581">Girişlerin eklenebileceği, silinebileceği ve güncelleştirilebileceği bir liste türü olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="f1238-582">Gezinti özelliği türleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f1238-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="f1238-583">`ICollection<T>`Belirtilmişse, EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="f1238-584">`CourseAssignment`Varlık, çoktan çoğa ilişkilerin bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f1238-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="f1238-585">Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla bir ofisiniz olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="f1238-586">`OfficeAssignment`Özelliği tek bir varlık içerir `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="f1238-587">`OfficeAssignment` hiçbir Office atanmamışsa null olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="f1238-588">OfficeAssignment varlığını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1238-588">Create the OfficeAssignment entity</span></span>

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="f1238-590">Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="f1238-591">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-591">The Key attribute</span></span>

<span data-ttu-id="f1238-592">Öznitelik, özellik `[Key]` adı Classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="f1238-593">Ve varlıkları arasında bire sıfır veya-bir ilişkisi vardır `Instructor` `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="f1238-594">Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="f1238-595">`OfficeAssignment`PK Ayrıca varlığa ait yabancı anahtardır (FK) `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="f1238-596">EF Core, şu nedenle otomatik olarak tanıyamaz `InstructorID` `OfficeAssignment` :</span><span class="sxs-lookup"><span data-stu-id="f1238-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="f1238-597">`InstructorID` ID veya Classnameıd adlandırma kuralını takip etmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="f1238-598">Bu nedenle, `Key` ÖZNITELIĞI PK olarak tanımlamak için kullanılır `InstructorID` :</span><span class="sxs-lookup"><span data-stu-id="f1238-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="f1238-599">Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="f1238-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="f1238-600">Eğitmen gezintisi özelliği</span><span class="sxs-lookup"><span data-stu-id="f1238-600">The Instructor navigation property</span></span>

<span data-ttu-id="f1238-601">`OfficeAssignment`Varlık için gezinti özelliği `Instructor` null yapılabilir çünkü:</span><span class="sxs-lookup"><span data-stu-id="f1238-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="f1238-602">Başvuru türleri (örneğin, sınıflar Nullable).</span><span class="sxs-lookup"><span data-stu-id="f1238-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="f1238-603">Bir eğitmenin Office ataması olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="f1238-604">`OfficeAssignment`Varlık null atanamaz bir `Instructor` gezinti özelliğine sahip, çünkü:</span><span class="sxs-lookup"><span data-stu-id="f1238-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="f1238-605">`InstructorID` null atanamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="f1238-606">Bir Office ataması, bir eğitmen olmadan bulunamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="f1238-607">Bir `Instructor` varlık ilişkili bir varlığa sahip olduğunda `OfficeAssignment` , her varlığın gezinti özelliğinde diğer bir başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="f1238-608">`[Required]`Öznitelik, `Instructor` gezinti özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="f1238-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="f1238-609">Yukarıdaki kod, ilgili bir eğitmen olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="f1238-610">`InstructorID`Yabancı anahtar (aynı zamanda PK) null değer atanamaz olduğundan, yukarıdaki kod gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="f1238-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="f1238-611">Kurs varlığını değiştirme</span><span class="sxs-lookup"><span data-stu-id="f1238-611">Modify the Course Entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="f1238-613">*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="f1238-614">`Course`Varlığın yabancı anahtar (FK) özelliği vardır `DepartmentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="f1238-615">`DepartmentID` ilgili varlığa işaret eder `Department` .</span><span class="sxs-lookup"><span data-stu-id="f1238-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="f1238-616">`Course`Varlığın bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="f1238-617">EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için FK özelliği gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="f1238-618">EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="f1238-619">EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1238-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="f1238-620">FK 'in veri modelinde olması, güncelleştirmeleri daha basit ve daha verimli hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f1238-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="f1238-621">Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün.</span><span class="sxs-lookup"><span data-stu-id="f1238-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="f1238-622">Bir kurs varlığı düzenlemek üzere getirilirken:</span><span class="sxs-lookup"><span data-stu-id="f1238-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="f1238-623">`Department`Açık bir şekilde yüklenmediyse varlık null olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="f1238-624">Kurs varlığını güncelleştirmek için `Department` önce varlığın getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="f1238-625">FK özelliği `DepartmentID` veri modeline dahil edildiğinde, `Department` bir güncelleştirmeden önce varlığı getirme gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="f1238-626">DatabaseGenerated özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="f1238-627">`[DatabaseGenerated(DatabaseGeneratedOption.None)]`Özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="f1238-628">Varsayılan olarak, EF Core PK değerlerinin DB tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="f1238-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="f1238-629">VERITABANı tarafından oluşturulan PK değerleri genellikle en iyi yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="f1238-630">`Course`Varlıklar için Kullanıcı PK 'yi belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="f1238-631">Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.</span><span class="sxs-lookup"><span data-stu-id="f1238-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="f1238-632">`DatabaseGenerated`Öznitelik varsayılan değerler oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="f1238-633">Örneğin, VERITABANı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="f1238-634">Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="f1238-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="f1238-635">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="f1238-636">Varlıktaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri `Course` aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="f1238-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="f1238-637">Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="f1238-638">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="f1238-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="f1238-639">Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur:</span><span class="sxs-lookup"><span data-stu-id="f1238-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="f1238-640">`CourseAssignment`[daha sonra](#many-to-many-relationships)açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f1238-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="f1238-641">Departman varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="f1238-641">Create the Department entity</span></span>

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

<span data-ttu-id="f1238-643">Aşağıdaki kodla *modeller/departman. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="f1238-644">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="f1238-644">The Column attribute</span></span>

<span data-ttu-id="f1238-645">Daha önce `Column` öznitelik, sütun adı eşlemesini değiştirmek için kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="f1238-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="f1238-646">`Department`Varlığın kodunda, `Column` özniteliği SQL veri türü eşlemesini değiştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="f1238-647">`Budget`Sütun, veritabanında SQL Server para türü kullanılarak tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="f1238-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="f1238-648">Sütun eşlemesi genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f1238-648">Column mapping is generally not required.</span></span> <span data-ttu-id="f1238-649">EF Core genellikle özelliğin CLR türüne göre uygun SQL Server veri türünü seçer.</span><span class="sxs-lookup"><span data-stu-id="f1238-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="f1238-650">CLR `decimal` türü SQL Server bir `decimal` türe eşlenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="f1238-651">`Budget` para birimi için, para veri türü ise para birimi için daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="f1238-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="f1238-652">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="f1238-653">FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="f1238-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="f1238-654">Bir departman yönetici olabilir veya olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="f1238-655">Yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="f1238-655">An administrator is always an instructor.</span></span> <span data-ttu-id="f1238-656">Bu nedenle, `InstructorID` özelliği VARLıĞA FK olarak dahil edilir `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="f1238-657">Gezinti özelliği adlandırılır `Administrator` ancak bir `Instructor` varlık barındırır:</span><span class="sxs-lookup"><span data-stu-id="f1238-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="f1238-658">Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.</span><span class="sxs-lookup"><span data-stu-id="f1238-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="f1238-659">Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="f1238-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="f1238-660">Note: kurala göre EF Core, null yapılamayan ve çoktan çoğa ilişkiler için art arda silme imkanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="f1238-661">Basamaklı silme, dairesel basamaklı silme kurallarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="f1238-662">Döngüsel basamaklı silme kuralları, bir geçiş eklendiğinde özel duruma neden olur.</span><span class="sxs-lookup"><span data-stu-id="f1238-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="f1238-663">Örneğin, `Department.InstructorID` özellik null yapılamayan olarak tanımlanmışsa:</span><span class="sxs-lookup"><span data-stu-id="f1238-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="f1238-664">EF Core, eğitmen silindiğinde departmanı silmek için bir basamakla silme kuralı yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="f1238-665">Eğitmen silindiğinde departmanı silmek amaçlanan davranış değildir.</span><span class="sxs-lookup"><span data-stu-id="f1238-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="f1238-666">Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) Cascade yerine bir kısıtlama kuralı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="f1238-667">Yukarıdaki kod, departman-eğitmen ilişkisindeki basamaklı silmeyi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="f1238-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="f1238-668">Kayıt varlığını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="f1238-668">Update the Enrollment entity</span></span>

<span data-ttu-id="f1238-669">Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.</span><span class="sxs-lookup"><span data-stu-id="f1238-669">An enrollment record is for one course taken by one student.</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="f1238-671">*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="f1238-672">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="f1238-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="f1238-673">FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="f1238-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="f1238-674">Kayıt kaydı tek bir kurs için olduğundan, `CourseID` FK özelliği ve bir `Course` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="f1238-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="f1238-675">Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve bir `Student` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="f1238-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="f1238-676">Çoktan çoğa Ilişkiler</span><span class="sxs-lookup"><span data-stu-id="f1238-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="f1238-677">Ve varlıkları arasında çoktan çoğa bir ilişki vardır `Student` `Course` .</span><span class="sxs-lookup"><span data-stu-id="f1238-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="f1238-678">`Enrollment`Varlık, veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="f1238-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="f1238-679">"Yükle", `Enrollment` tablonun birleştirilmiş tablolar Için FKs 'ler (Bu durumda, PK ve) gibi ek veriler içerdiği anlamına gelir `Grade` .</span><span class="sxs-lookup"><span data-stu-id="f1238-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="f1238-680">Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="f1238-681">(Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="f1238-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="f1238-682">Diyagram oluşturmak öğreticinin bir parçası değildir.)</span><span class="sxs-lookup"><span data-stu-id="f1238-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Çok fazla ilişki Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="f1238-684">Her ilişki satırında 1 bir sonda ve diğeri de bir yıldız işareti (\*) bulunur. Bu, bire çok ilişkiyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="f1238-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="f1238-685">Tablo, `Enrollment` sınıf bilgileri içermiyorsa, yalnızca Iki FKs ( `CourseID` ve) içermesi gerekir `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="f1238-686">Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="f1238-687">`Instructor`Ve `Course` varlıklarının saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="f1238-688">Note: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir.</span><span class="sxs-lookup"><span data-stu-id="f1238-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="f1238-689">Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f1238-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="f1238-690">Courseatama varlığı</span><span class="sxs-lookup"><span data-stu-id="f1238-690">The CourseAssignment entity</span></span>

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="f1238-692">Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f1238-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="f1238-693">Eğitmenden kurslar</span><span class="sxs-lookup"><span data-stu-id="f1238-693">Instructor-to-Courses</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="f1238-695">Eğitmenin kurslardan çok-çok ilişkisi:</span><span class="sxs-lookup"><span data-stu-id="f1238-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="f1238-696">Bir varlık kümesiyle temsil etmelidir bir JOIN tablosu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f1238-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="f1238-697">, Saf bir JOIN tablosu (yük içermeyen tablo).</span><span class="sxs-lookup"><span data-stu-id="f1238-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="f1238-698">Bir JOIN varlığına ad vermek yaygındır `EntityName1EntityName2` .</span><span class="sxs-lookup"><span data-stu-id="f1238-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="f1238-699">Örneğin, bu model kullanılarak eğitmen-kurslar 'a katılması tablosu `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="f1238-700">Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f1238-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="f1238-701">Veri modelleri basit ve büyümeye başlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-701">Data models start out simple and grow.</span></span> <span data-ttu-id="f1238-702">Yük yükü dahil olmak üzere genellikle yük-yük birleştirmeleri (PJTs) gelişmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="f1238-703">Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="f1238-704">İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="f1238-705">Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="f1238-706">Eğitmenin kursa çok-çok ilişkisi için `CourseAssignment` tercih edilir `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="f1238-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="f1238-707">Bileşik anahtar</span><span class="sxs-lookup"><span data-stu-id="f1238-707">Composite key</span></span>

<span data-ttu-id="f1238-708">FKs null değer atanamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-708">FKs are not nullable.</span></span> <span data-ttu-id="f1238-709">(Ve) içindeki iki FKs, `CourseAssignment` `InstructorID` `CourseID` tablonun her satırını benzersiz bir şekilde tanımlar `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="f1238-710">`CourseAssignment` adanmış bir PK gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="f1238-711">`InstructorID`Ve `CourseID` özellikleri BILEŞIK bir PK olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="f1238-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="f1238-712">EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir.</span><span class="sxs-lookup"><span data-stu-id="f1238-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="f1238-713">Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="f1238-714">Bileşik anahtar şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="f1238-714">The composite key ensures:</span></span>

* <span data-ttu-id="f1238-715">Tek bir kurs için birden çok satıra izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="f1238-716">Birden çok satıra bir eğitmen için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="f1238-717">Aynı eğitmen ve kurs için birden çok satıra izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="f1238-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="f1238-718">`Enrollment`JOIN varlığı kendı PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür.</span><span class="sxs-lookup"><span data-stu-id="f1238-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="f1238-719">Bu tür yinelemeleri engellemek için:</span><span class="sxs-lookup"><span data-stu-id="f1238-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="f1238-720">FK alanlara benzersiz bir dizin ekleyin veya</span><span class="sxs-lookup"><span data-stu-id="f1238-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="f1238-721">`Enrollment`İle benzer bir birincil bileşik anahtarla yapılandırın `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="f1238-722">Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="f1238-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="f1238-723">DB bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="f1238-723">Update the DB context</span></span>

<span data-ttu-id="f1238-724">*Data/SchoolContext. cs*' ye aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f1238-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="f1238-725">Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın BILEŞIK PK 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="f1238-726">Tutarlı API 'nin özniteliklere alternatif</span><span class="sxs-lookup"><span data-stu-id="f1238-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="f1238-727">`OnModelCreating`Önceki koddaki yöntemi EF Core davranışını yapılandırmak için *Fluent API* kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1238-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="f1238-728">Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="f1238-729">[Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="f1238-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="f1238-730">Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını DB eşlemesi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="f1238-731">Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="f1238-732">Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="f1238-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="f1238-733">`MinimumLength` şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="f1238-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="f1238-734">Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="f1238-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="f1238-735">Öznitelikler ve Fluent API karışık olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="f1238-736">Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır.</span><span class="sxs-lookup"><span data-stu-id="f1238-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="f1238-737">Yalnızca özniteliklerle () yapılabilecek bazı konfigürasyonlar vardır `MinimumLength` .</span><span class="sxs-lookup"><span data-stu-id="f1238-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="f1238-738">Fluent API veya özniteliklerini kullanmak için önerilen uygulama:</span><span class="sxs-lookup"><span data-stu-id="f1238-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="f1238-739">Bu iki yaklaşımdan birini seçin.</span><span class="sxs-lookup"><span data-stu-id="f1238-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="f1238-740">Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.</span><span class="sxs-lookup"><span data-stu-id="f1238-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="f1238-741">Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f1238-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="f1238-742">Yalnızca doğrulama (örneğin, `MinimumLength` ).</span><span class="sxs-lookup"><span data-stu-id="f1238-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="f1238-743">Yalnızca yapılandırma EF Core (örneğin, `HasKey` ).</span><span class="sxs-lookup"><span data-stu-id="f1238-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="f1238-744">Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]` ).</span><span class="sxs-lookup"><span data-stu-id="f1238-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="f1238-745">Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="f1238-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="f1238-746">Ilişkileri gösteren varlık diyagramı</span><span class="sxs-lookup"><span data-stu-id="f1238-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="f1238-747">Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="f1238-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="f1238-749">Önceki diyagramda şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="f1238-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="f1238-750">Birden çok çoktan çoğa ilişki satırı (1 ile \* ).</span><span class="sxs-lookup"><span data-stu-id="f1238-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="f1238-751">Ve varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1) `Instructor` `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="f1238-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="f1238-752">Ve varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-\*) `Instructor` `Department` .</span><span class="sxs-lookup"><span data-stu-id="f1238-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="f1238-753">VERITABANıNı test verileriyle çekirdek olarak</span><span class="sxs-lookup"><span data-stu-id="f1238-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="f1238-754">*Data/Dbınizer. cs* dosyasındaki kodu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="f1238-755">Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="f1238-756">Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="f1238-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="f1238-757">Örnek veriler test için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1238-757">The sample data is used for testing.</span></span> <span data-ttu-id="f1238-758">`Enrollments` `CourseAssignments` Birden çok-çok JOIN tablosunun nasıl çalıştırılabilir olduğunu gösteren örnekler için bkz. ve.</span><span class="sxs-lookup"><span data-stu-id="f1238-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="f1238-759">Geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="f1238-759">Add a migration</span></span>

<span data-ttu-id="f1238-760">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="f1238-763">Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f1238-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="f1238-764">`database update`Komut çalıştırıldığında, aşağıdaki hata oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1238-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="f1238-765">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="f1238-765">Apply the migration</span></span>

<span data-ttu-id="f1238-766">Artık var olan bir veritabanınız olduğuna göre, bundan sonraki değişikliklere nasıl uygulanacağını düşünmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="f1238-767">Bu öğreticide iki yaklaşım gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f1238-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="f1238-768">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="f1238-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="f1238-769">[Geçişi mevcut veritabanına uygulayın](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="f1238-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="f1238-770">Bu yöntem daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="f1238-771">**Note**: Bu, öğreticinin isteğe bağlı bir bölümüdür.</span><span class="sxs-lookup"><span data-stu-id="f1238-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="f1238-772">Bırakma ve yeniden oluşturma adımlarını gerçekleştirebilir ve bu bölümü atlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f1238-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="f1238-773">Bu bölümdeki adımları izlemek isterseniz, bırakma ve yeniden oluşturma adımlarını yapmayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="f1238-774">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="f1238-774">Drop and re-create the database</span></span>

<span data-ttu-id="f1238-775">Güncelleştirilmiş kod, `DbInitializer` Yeni varlıklar için tohum verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="f1238-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="f1238-776">Yeni bir VERITABANı oluşturmak için EF Core zorlamak için DB 'yi bırakıp güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f1238-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f1238-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1238-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1238-778">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f1238-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="f1238-779">`Get-Help about_EntityFrameworkCore`Yardım bilgileri almak IÇIN PMC 'den çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1238-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f1238-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f1238-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f1238-781">Bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="f1238-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="f1238-782">Proje klasörü *Startup.cs* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="f1238-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="f1238-783">Komut penceresine şunu girin:</span><span class="sxs-lookup"><span data-stu-id="f1238-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="f1238-784">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f1238-784">Run the app.</span></span> <span data-ttu-id="f1238-785">Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f1238-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="f1238-786">`DbInitializer.Initialize`Yenı DB 'yi doldurur.</span><span class="sxs-lookup"><span data-stu-id="f1238-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="f1238-787">VERITABANıNı SSOX içinde açın:</span><span class="sxs-lookup"><span data-stu-id="f1238-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="f1238-788">Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="f1238-789">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="f1238-789">Expand the **Tables** node.</span></span> <span data-ttu-id="f1238-790">Oluşturulan tablolar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f1238-790">The created tables are displayed.</span></span>

![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="f1238-792">**Courseatama** tablosunu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f1238-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="f1238-793">**Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f1238-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="f1238-794">**Courseatama** tablosunun veri içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-794">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="f1238-796">Geçişi mevcut veritabanına Uygula</span><span class="sxs-lookup"><span data-stu-id="f1238-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="f1238-797">Bu bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f1238-797">This section is optional.</span></span> <span data-ttu-id="f1238-798">Bu adımlar yalnızca önceki [bırakma ve veritabanını yeniden oluşturma](#drop) bölümünü atladıysanız çalışır.</span><span class="sxs-lookup"><span data-stu-id="f1238-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="f1238-799">Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir.</span><span class="sxs-lookup"><span data-stu-id="f1238-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="f1238-800">Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f1238-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="f1238-801">Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1238-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="f1238-802">Bu kod değişikliklerini yedekleme olmadan yapmayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="f1238-803">Önceki bölümü tamamlayıp veritabanını güncelleştirdiyseniz, bu kod değişikliklerini yapmayın.</span><span class="sxs-lookup"><span data-stu-id="f1238-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="f1238-804">*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:</span><span class="sxs-lookup"><span data-stu-id="f1238-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="f1238-805">Yukarıdaki kod, tabloya null yapılamayan bir `DepartmentID` FK ekler `Course` .</span><span class="sxs-lookup"><span data-stu-id="f1238-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="f1238-806">Önceki öğreticideki VERITABANı, içindeki satırları içerir `Course` , böylece tablo geçişler tarafından güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="f1238-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="f1238-807">`ComplexDataModel`Geçişin mevcut verilerle çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="f1238-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="f1238-808">Yeni sütuna () varsayılan değer vermek için kodu değiştirin `DepartmentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="f1238-809">Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f1238-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="f1238-810">Yabancı anahtar kısıtlamalarını çözme</span><span class="sxs-lookup"><span data-stu-id="f1238-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="f1238-811">`ComplexDataModel`Classes metodunu güncelleştirin `Up` :</span><span class="sxs-lookup"><span data-stu-id="f1238-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="f1238-812">*{Timestamp} _ComplexDataModel. cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="f1238-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="f1238-813">Sütunu tabloya ekleyen kod satırını açıklama `DepartmentID` olarak yapın `Course` .</span><span class="sxs-lookup"><span data-stu-id="f1238-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="f1238-814">Aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f1238-814">Add the following highlighted code.</span></span> <span data-ttu-id="f1238-815">Yeni kod bloğundan sonra geçer `.CreateTable( name: "Department"` :</span><span class="sxs-lookup"><span data-stu-id="f1238-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="f1238-816">Önceki değişikliklerle, varolan satırlar, `Course` Yöntem çalıştıktan sonra "geçici" departmanıyla ilişkilendirilir `ComplexDataModel` `Up` .</span><span class="sxs-lookup"><span data-stu-id="f1238-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="f1238-817">Bir üretim uygulaması şöyle olacaktır:</span><span class="sxs-lookup"><span data-stu-id="f1238-817">A production app would:</span></span>

* <span data-ttu-id="f1238-818">`Department`Yeni satırlara satırlar ve ilgili satırlar eklemek için kod veya komut dosyaları ekleyin `Course` `Department` .</span><span class="sxs-lookup"><span data-stu-id="f1238-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="f1238-819">İçin "geçici" Departmanı veya varsayılan değeri kullanmayın `Course.DepartmentID` .</span><span class="sxs-lookup"><span data-stu-id="f1238-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="f1238-820">Sonraki öğreticide ilgili veriler ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f1238-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1238-821">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f1238-821">Additional resources</span></span>

* [<span data-ttu-id="f1238-822">Bu öğreticinin YouTube sürümü (Bölüm 1)</span><span class="sxs-lookup"><span data-stu-id="f1238-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="f1238-823">Bu öğreticinin YouTube sürümü (Bölüm 2)</span><span class="sxs-lookup"><span data-stu-id="f1238-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="f1238-824">[Önceki](xref:data/ef-rp/migrations) 
>  [Sonraki](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="f1238-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
