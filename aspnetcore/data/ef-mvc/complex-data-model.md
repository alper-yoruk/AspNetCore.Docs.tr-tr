---
title: 'Öğretici: Karmaşık bir veri modeli oluşturun - EF Core ile MVC ASP.NET'
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kuralları belirterek veri modelini özelleştirin.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657432"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="e81d3-103">Öğretici: Karmaşık bir veri modeli oluşturun - EF Core ile MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e81d3-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="e81d3-104">Önceki öğreticilerde, üç varlıktan oluşan basit bir veri modeliyle çalıştınız.</span><span class="sxs-lookup"><span data-stu-id="e81d3-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="e81d3-105">Bu öğreticide, daha fazla varlık ve ilişki ekler siniz ve biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirterek veri modelini özelleştireceksiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="e81d3-106">İşi bittiğinde, varlık sınıfları aşağıdaki resimde gösterilen tamamlanmış veri modelini yapar:</span><span class="sxs-lookup"><span data-stu-id="e81d3-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="e81d3-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="e81d3-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e81d3-109">Veri modelini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-109">Customize the Data model</span></span>
> * <span data-ttu-id="e81d3-110">Öğrenci varlığında değişiklik yapma</span><span class="sxs-lookup"><span data-stu-id="e81d3-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="e81d3-111">Eğitmen varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e81d3-111">Create Instructor entity</span></span>
> * <span data-ttu-id="e81d3-112">OfficeAtama tüzel kişiliği oluşturma</span><span class="sxs-lookup"><span data-stu-id="e81d3-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="e81d3-113">Kurs varlığını değiştir</span><span class="sxs-lookup"><span data-stu-id="e81d3-113">Modify Course entity</span></span>
> * <span data-ttu-id="e81d3-114">Bölüm varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e81d3-114">Create Department entity</span></span>
> * <span data-ttu-id="e81d3-115">Kayıt varlığını değiştirin</span><span class="sxs-lookup"><span data-stu-id="e81d3-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="e81d3-116">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-116">Update the database context</span></span>
> * <span data-ttu-id="e81d3-117">Test verileri ile tohum veritabanı</span><span class="sxs-lookup"><span data-stu-id="e81d3-117">Seed database with test data</span></span>
> * <span data-ttu-id="e81d3-118">Geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="e81d3-118">Add a migration</span></span>
> * <span data-ttu-id="e81d3-119">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-119">Change the connection string</span></span>
> * <span data-ttu-id="e81d3-120">Veritabanını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e81d3-121">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="e81d3-121">Prerequisites</span></span>

* [<span data-ttu-id="e81d3-122">EF Core geçişlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="e81d3-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="e81d3-123">Veri modelini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-123">Customize the Data model</span></span>

<span data-ttu-id="e81d3-124">Bu bölümde biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirten öznitelikleri kullanarak veri modelini nasıl özelleştireceğiniz göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="e81d3-125">Ardından, aşağıdaki bölümlerin birkaçında, önceden oluşturduğunuz sınıflara öznitelikler ekleyerek ve modelde kalan varlık türleri için yeni sınıflar oluşturarak tüm Okul veri modelini oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="e81d3-126">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-126">The DataType attribute</span></span>

<span data-ttu-id="e81d3-127">Öğrenci kayıt tarihleri için, tüm web sayfaları şu anda tarihle birlikte saati görüntüler, ancak bu alan için tek umursadığınız tarihtir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="e81d3-128">Veri ek açıklama özniteliklerini kullanarak, verileri gösteren her görünümde görüntü biçimini düzeltecek bir kod değişikliği yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="e81d3-129">Bunun nasıl yapılacağının bir örneğini görmek için, `EnrollmentDate` `Student` sınıftaki özelliğe bir öznitelik eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="e81d3-130">*Modeller/Student.cs'de,* `using` `System.ComponentModel.DataAnnotations` ad alanı için bir `DataType` `DisplayFormat` deyim ekleyin `EnrollmentDate` ve aşağıdaki örnekte gösterildiği gibi özellik eklemek ve öznitelikleri:</span><span class="sxs-lookup"><span data-stu-id="e81d3-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="e81d3-131">Öznitelik, `DataType` veritabanı içsel türünden daha spesifik bir veri türünü belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="e81d3-132">Bu durumda biz sadece tarih ve saat değil, tarih ve saat izlemek istiyorum.</span><span class="sxs-lookup"><span data-stu-id="e81d3-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="e81d3-133">Numaralandırma, `DataType` Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi ve daha fazlası gibi birçok veri türü sağlar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="e81d3-134">Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="e81d3-135">Örneğin, HTML5'i destekleyen tarayıcılarda bir `mailto:` bağlantı oluşturulabilir `DataType.Date` `DataType.EmailAddress`ve tarih seçici kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="e81d3-136">Öznitelik `DataType` HTML 5 `data-` (telaffuz veri tire) html 5 tarayıcılar anlayabileceği öznitelikleri yakar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="e81d3-137">Öznitelikler `DataType` herhangi bir doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="e81d3-138">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="e81d3-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="e81d3-139">Varsayılan olarak, veri alanı sunucunun CultureInfo'suna dayalı varsayılan biçimlere göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="e81d3-140">Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="e81d3-141">Ayar, `ApplyFormatInEditMode` değer düzenleme için bir metin kutusunda görüntülendiğinde biçimlendirmenin de uygulanması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="e81d3-142">(Bazı alanlar için (örneğin, para birimi değerleri için, düzenleme için metin kutusundaki para birimi simgesini istemeyebilirsiniz.)</span><span class="sxs-lookup"><span data-stu-id="e81d3-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="e81d3-143">Özniteliği tek `DisplayFormat` başına kullanabilirsiniz, ancak özniteliği de kullanmak `DataType` genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="e81d3-144">Öznitelik, `DataType` verilerin bir ekranda nasıl işlenir, aksine anlamsallarını iletir ve aşağıdaki avantajları `DisplayFormat`sağlar:</span><span class="sxs-lookup"><span data-stu-id="e81d3-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="e81d3-145">Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları, bazı istemci tarafı giriş doğrulama, vb. göstermek için).</span><span class="sxs-lookup"><span data-stu-id="e81d3-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="e81d3-146">Varsayılan olarak, tarayıcı verileri bulunduğunuz yerin temeline göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="e81d3-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="e81d3-147">Daha fazla bilgi [ \<için, giriş> etiketi yardımcı belgeleri](../../mvc/views/working-with-forms.md#the-input-tag-helper)bakın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="e81d3-148">Uygulamayı çalıştırın, Öğrenci Dizini sayfasına gidin ve kayıt tarihleri için artık saatlerin görüntülenmediğini fark edin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="e81d3-149">Aynı öğrenci modelini kullanan herhangi bir görünüm için de geçerli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-149">The same will be true for any view that uses the Student model.</span></span>

![Tarihlerini saatsiz gösteren öğrenci dizini sayfası](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="e81d3-151">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-151">The StringLength attribute</span></span>

<span data-ttu-id="e81d3-152">Öznitelikleri kullanarak veri doğrulama kuralları ve doğrulama hatası iletileri de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="e81d3-153">Öznitelik `StringLength` veritabanındaki maksimum uzunluğu ayarlar ve ASP.NET Core MVC için istemci tarafı ve sunucu tarafı doğrulama sağlar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="e81d3-154">Bu öznitelikteki minimum dize uzunluğunu da belirtebilirsiniz, ancak minimum değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="e81d3-155">Kullanıcıların bir ad için 50'den fazla karakter girmediğinden emin olmak istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="e81d3-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="e81d3-156">Bu sınırlamayı eklemek `StringLength` için, `LastName` `FirstMidName` aşağıdaki örnekte gösterildiği gibi, öznitelikleri ve özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="e81d3-157">Öznitelik, `StringLength` kullanıcının bir ad için beyaz alan girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="e81d3-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="e81d3-158">Girişe `RegularExpression` kısıtlamalar uygulamak için özniteliği kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="e81d3-159">Örneğin, aşağıdaki kod ilk karakterin büyük harf, kalan karakterlerin ise alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="e81d3-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="e81d3-160">Öznitelik özniteliğe `MaxLength` benzer `StringLength` işlevsellik sağlar, ancak istemci tarafı doğrulaması sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="e81d3-161">Veritabanı modeli artık veritabanı şemasında değişiklik gerektiren bir şekilde değişti.</span><span class="sxs-lookup"><span data-stu-id="e81d3-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="e81d3-162">Uygulama Kullanıcı GI'sini kullanarak veritabanına eklemiş olabileceğiniz verileri kaybetmeden şemayı güncelleştirmek için geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="e81d3-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="e81d3-163">Değişikliklerinizi kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e81d3-163">Save your changes and build the project.</span></span> <span data-ttu-id="e81d3-164">Ardından proje klasöründeki komut penceresini açın ve aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="e81d3-165">Komut, `migrations add` değişiklik iki sütun için maksimum uzunluğu kısalttığından, veri kaybının meydana gelebileceği konusunda uyarır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="e81d3-166">Geçişler \* \<timeStamp>_MaxLengthOnNames.cs\*adlı bir dosya oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="e81d3-167">Bu dosya, geçerli `Up` veri modeliyle eşleşecek şekilde veritabanını güncelleştirecek yöntemde kod içerir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="e81d3-168">Komut `database update` bu kodu çalıştırıyor.</span><span class="sxs-lookup"><span data-stu-id="e81d3-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="e81d3-169">Geçişler dosya adı için önceden belirlenmiş zaman damgası, geçişleri sıralamak için Entity Framework tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="e81d3-170">Güncelleştirme veritabanı komutunu çalıştırmadan önce birden çok geçiş oluşturabilirsiniz ve daha sonra tüm geçişler oluşturuldukları sırada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="e81d3-171">Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin, **Yeni Oluştur'u**tıklatın ve 50 karakterden daha uzun bir ad girmeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="e81d3-172">Uygulama bunu yapmanızı engellemelidir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="e81d3-173">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-173">The Column attribute</span></span>

<span data-ttu-id="e81d3-174">Sınıflarınızın ve özelliklerinizin veritabanına nasıl eşlenebileceğini denetlemek için öznitelikleri de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="e81d3-175">Alan da bir `FirstMidName` göbek adı içerebileceğinden, ilk ad alanı için adı kullandığınızı varsayalım.</span><span class="sxs-lookup"><span data-stu-id="e81d3-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="e81d3-176">Ancak veritabanı sütununa adlandırılmasını `FirstName`istiyorsunuz, çünkü veritabanına karşı geçici sorgular yazacak kullanıcılar bu ada alışkındır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="e81d3-177">Bu eşlemi yapmak için `Column` özniteliği kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="e81d3-178">Öznitelik, `Column` veritabanı oluşturulduğunda, `Student` `FirstMidName` tablonun özelliğieşleyen sütununun adının `FirstName`verolacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="e81d3-179">Başka bir deyişle, kodunuz `Student.FirstMidName`, veriler `FirstName` `Student` tablonun sütunundan gelir veya güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="e81d3-180">Sütun adlarını belirtmezseniz, bunlara özellik adı ile aynı ad verilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="e81d3-181">*Student.cs* dosyasında, aşağıdaki `using` vurgulanan kodda gösterildiği gibi, özellik için `System.ComponentModel.DataAnnotations.Schema` bir deyim ekleyin ve `FirstMidName` özelliğe sütun adı özniteliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="e81d3-182">Öznitelik eklenmesi, `Column` veritabanıyla eşleşmeyemeyeceği için modeli destekleyen `SchoolContext`modeli değiştirir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="e81d3-183">Değişikliklerinizi kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e81d3-183">Save your changes and build the project.</span></span> <span data-ttu-id="e81d3-184">Ardından proje klasöründeki komut penceresini açın ve başka bir geçiş oluşturmak için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="e81d3-185">**SQL Server Object Explorer'da,** **Öğrenci** tablosunu çift tıklatarak Öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Göçlerden sonra SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="e81d3-187">İlk iki geçişi uygulamadan önce, ad sütunları nvarchar (MAX) türündendi.</span><span class="sxs-lookup"><span data-stu-id="e81d3-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="e81d3-188">Onlar şimdi nvarchar (50) konum ve sütun adı FirstMidName firstname olarak değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="e81d3-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="e81d3-189">Aşağıdaki bölümlerdeki tüm varlık sınıflarını oluşturmayı bitirmeden önce derlemeye çalışırsanız, derleyici hataları alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="e81d3-190">Öğrenci varlığında yapılan değişiklikler</span><span class="sxs-lookup"><span data-stu-id="e81d3-190">Changes to Student entity</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="e81d3-192">*Modeller/Student.cs'de,* daha önce eklediğiniz kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="e81d3-193">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="e81d3-194">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="e81d3-194">The Required attribute</span></span>

<span data-ttu-id="e81d3-195">Öznitelik `Required` ad özelliklerini gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="e81d3-196">Öznitelik `Required` değer türleri (DateTime, int, double, float, vb) gibi nullable olmayan türleri için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="e81d3-197">Null olamaz türleri otomatik olarak gerekli alanlar olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="e81d3-198">Öznitelik, `Required` uygulanacak olması `MinimumLength` `MinimumLength` için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="e81d3-199">Görüntü özniteliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-199">The Display attribute</span></span>

<span data-ttu-id="e81d3-200">Öznitelik, `Display` metin kutularıiçin alt yazının her örnekte özellik adı yerine "Ad", "Soyadı", "Tam Ad" ve "Kayıt Tarihi" olması gerektiğini belirtir (sözcükleri bölen boşluk yoktur).</span><span class="sxs-lookup"><span data-stu-id="e81d3-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="e81d3-201">FullName hesaplanan özellik</span><span class="sxs-lookup"><span data-stu-id="e81d3-201">The FullName calculated property</span></span>

<span data-ttu-id="e81d3-202">`FullName`diğer iki özelliği nkiçleme tarafından oluşturulan bir değeri döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="e81d3-203">Bu nedenle yalnızca bir erişime `FullName` sahip ve veritabanında hiçbir sütun oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="e81d3-204">Eğitmen varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e81d3-204">Create Instructor entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="e81d3-206">Şablon kodunu aşağıdaki kodla değiştirerek *Modeller/Instructor.cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e81d3-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="e81d3-207">Öğrenci ve Eğitmen kuruluşlarında çeşitli özelliklerin aynı olduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="e81d3-208">Bu serinin ilerleyen saatlerinde [Devralma](inheritance.md) Uygulama öğreticisinde, artıklığı ortadan kaldırmak için bu kodu yeniden düzenlemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="e81d3-209">Bir satıra birden çok öznitelik koyabilirsiniz, `HireDate` böylece öznitelikleri aşağıdaki gibi de yazabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e81d3-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="e81d3-210">Ders Atamaları ve OfisAtama navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="e81d3-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="e81d3-211">Ve `CourseAssignments` `OfficeAssignment` özellikleri navigasyon özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="e81d3-212">Bir eğitmen herhangi bir sayıda `CourseAssignments` ders verebilir, bu nedenle bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="e81d3-213">Bir gezinti özelliği birden çok varlığı barındırabiliyorsa, türü girişlerin eklenebileceği, silinebileceği ve güncelleştirilebildiği bir liste olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="e81d3-214">Belirtebilirsiniz `ICollection<T>` veya gibi bir `List<T>` `HashSet<T>`tür ya da .</span><span class="sxs-lookup"><span data-stu-id="e81d3-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e81d3-215">Belirtirseniz, `ICollection<T>`EF varsayılan `HashSet<T>` olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="e81d3-216">Bu `CourseAssignment` varlıkların neden çok-çok ilişkiler hakkında bölümde aşağıda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="e81d3-217">Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla `OfficeAssignment` bir ofis te sahip olabileceğini belirtir, bu nedenle özellik tek bir OfficeAssignment tüzel kişiliğine sahiptir (ofis atanmamışsa geçersiz olabilir).</span><span class="sxs-lookup"><span data-stu-id="e81d3-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="e81d3-218">OfficeAtama tüzel kişiliği oluşturma</span><span class="sxs-lookup"><span data-stu-id="e81d3-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment tüzel kişiliği](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="e81d3-220">Aşağıdaki kodla *Modeller/OfficeAssignment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e81d3-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="e81d3-221">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-221">The Key attribute</span></span>

<span data-ttu-id="e81d3-222">Eğitmen ve OfficeAssignment varlıkları arasında bire sıfır veya bir ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="e81d3-223">Bir ofis ataması yalnızca atandığı eğitmenle ilişkili olarak bulunur ve bu nedenle birincil anahtarı aynı zamanda Eğitmen tüzel kişiliğinin yabancı anahtarıdır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="e81d3-224">Ancak Entity Framework, adı Kimlik veya classnameID adlandırma kuralını izlemediği için InstructorID'yi bu varlığın birincil anahtarı olarak otomatik olarak tanıyamaz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="e81d3-225">Bu nedenle, `Key` öznitelik anahtar olarak tanımlamak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="e81d3-226">Varlığın `Key` kendi birincil anahtarı varsa, ancak özelliği classnameID veya ID'den başka bir şey olarak adlandırmak istiyorsanız, özniteliği de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="e81d3-227">Varsayılan olarak, sütun tanımlayıcı bir ilişki için olduğundan, EF anahtarı veritabanı tarafından oluşturulmayan olarak ele alır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="e81d3-228">Eğitmen navigasyon özelliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-228">The Instructor navigation property</span></span>

<span data-ttu-id="e81d3-229">Eğitmen tüzel kişiliğinin `OfficeAssignment` boşsayılabilir bir gezinti özelliği vardır (çünkü bir eğitmenin ofis `Instructor` ataması olmayabilir) ve OfficeAssignment tüzel kişiliğinin geçersiz olmayan bir gezinti özelliği vardır (çünkü bir ofis ataması eğitmen olmadan var olamaz - `InstructorID` geçersizdir).</span><span class="sxs-lookup"><span data-stu-id="e81d3-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="e81d3-230">Bir Eğitmen tüzel kişiliğinin ilgili bir OfficeAssignment tüzel kişiliği varsa, her varlığın gezinti özelliğinde diğerine bir başvurusu olur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="e81d3-231">İlgili bir `[Required]` eğitmen olması gerektiğini belirtmek için Eğitmen navigasyon özelliğine bir öznitelik koyabilirsiniz, ancak `InstructorID` yabancı anahtar (bu tablonun anahtarı da) geçersiz olduğundan bunu yapmanız alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="e81d3-232">Kurs varlığını değiştir</span><span class="sxs-lookup"><span data-stu-id="e81d3-232">Modify Course entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="e81d3-234">*Modeller/Course.cs'de,* daha önce eklediğiniz kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="e81d3-235">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="e81d3-236">Kurs tüzel kişiliği, `DepartmentID` ilgili Bölüm tüzel kişiliğini gösteren `Department` yabancı bir anahtar özelliğine ve navigasyon özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="e81d3-237">Varlık Çerçevesi, ilgili bir varlık için gezinti özelliğiniz olduğunda veri modelinize yabancı bir anahtar özelliği eklemenizi gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="e81d3-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="e81d3-238">EF, ihtiyaç duyulan her yerde veritabanında otomatik olarak yabancı anahtarlar oluşturur ve bunlar için [gölge özellikleri](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="e81d3-239">Ancak veri modelinde yabancı anahtara sahip olmak güncelleştirmeleri daha basit ve daha verimli hale getirebilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="e81d3-240">Örneğin, bir ders tüzel kişiliğini yeniden yüklemek için getirdiğinizde, yüklemezseniz Bölüm varlığı geçersiz olur, bu nedenle kurs varlığını güncelleştirdiğinizde, önce Bölüm varlığını getirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="e81d3-241">Yabancı anahtar özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden önce Departman varlığını almanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e81d3-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="e81d3-242">DatabaseOluşturulan öznitelik</span><span class="sxs-lookup"><span data-stu-id="e81d3-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="e81d3-243">Özellik üzerinde `DatabaseGenerated` `None` parametre ile öznitelik birincil anahtar değerleri veritabanı tarafından oluşturulan yerine kullanıcı tarafından sağlanmaktadır belirtir. `CourseID`</span><span class="sxs-lookup"><span data-stu-id="e81d3-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="e81d3-244">Varsayılan olarak, Varlık Çerçevesi birincil anahtar değerlerinveritabanı tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="e81d3-245">Çoğu senaryoda istediğin bu.</span><span class="sxs-lookup"><span data-stu-id="e81d3-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="e81d3-246">Ancak, Kurs kuruluşları için, bir bölüm için 1000 serisi, başka bir bölüm için 2000 serisi gibi kullanıcı tarafından belirtilen bir kurs numarası kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="e81d3-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="e81d3-247">Öznitelik, `DatabaseGenerated` bir satırın oluşturulduğu veya güncelleştirilme tarihini kaydetmek için kullanılan veritabanı sütunlarında olduğu gibi varsayılan değerleri oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="e81d3-248">Daha fazla bilgi [için, oluşturulan özellikler](/ef/core/modeling/generated-properties)e bakın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e81d3-249">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="e81d3-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="e81d3-250">Ders varlığındaki yabancı anahtar özellikleri ve navigasyon özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="e81d3-251">Bir kurs bir bölüme atanır, `DepartmentID` bu nedenle `Department` yukarıda belirtilen nedenlerle yabancı bir anahtar ve navigasyon özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="e81d3-252">Bir kursa herhangi bir sayıda öğrenci kaydolabilir, bu nedenle `Enrollments` navigasyon özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="e81d3-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="e81d3-253">Bir ders birden fazla eğitmen tarafından `CourseAssignments` öğretilebilir, bu nedenle `CourseAssignment` navigasyon özelliği bir koleksiyondur (türü [daha sonra](#many-to-many-relationships)açıklanır):</span><span class="sxs-lookup"><span data-stu-id="e81d3-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="e81d3-254">Bölüm varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e81d3-254">Create Department entity</span></span>

![Bölüm tüzel kişiliği](complex-data-model/_static/department-entity.png)

<span data-ttu-id="e81d3-256">Aşağıdaki kodile *Modeller/Department.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e81d3-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="e81d3-257">Sütun özniteliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-257">The Column attribute</span></span>

<span data-ttu-id="e81d3-258">Daha önce `Column` sütun adı eşlemi değiştirmek için öznitelik kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="e81d3-259">Bölüm varlığı kodunda, `Column` öznitelik, veritabanındaki SQL Server para türü kullanılarak sütunun tanımlanabilmesi için SQL veri türü eşlemi eşlemi değiştirmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="e81d3-260">Varlık Çerçevesi, özellik için tanımladığınız CLR türüne göre uygun SQL Server veri türünü seçtiğinden, sütun eşleme genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="e81d3-261">CLR `decimal` türü, SQL Server `decimal` türüyle eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="e81d3-262">Ancak bu durumda, sütunun para birimi tutarlarını tutacağını ve para veri türünün bunun için daha uygun olduğunu bilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e81d3-263">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="e81d3-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="e81d3-264">Yabancı anahtar ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="e81d3-265">Bir bölümün bir yöneticisi olabilir veya olmayabilir ve yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="e81d3-266">Bu `InstructorID` nedenle özellik, Eğitmen varlığına yabancı anahtar olarak eklenir ve özelliği `int` nullable olarak işaretlemek için tür atamasonra bir soru işareti eklenir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="e81d3-267">Gezinti özelliği adlandırılmış, `Administrator` ancak bir Eğitmen varlık tutar:</span><span class="sxs-lookup"><span data-stu-id="e81d3-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="e81d3-268">Bir bölümün birçok dersi olabilir, bu nedenle Kurslar navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="e81d3-269">Taraf Çerçevesi, sözleşmeyle, geçersiz olmayan yabancı anahtarlar ve çok-çok ilişkiler için basamaklı silmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="e81d3-270">Bu, geçiş eklemeye çalıştığınızda bir özel durum neden olacak dairesel basamaklı silme kuralları, neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="e81d3-271">Örneğin, Department.InstructorID özelliğini nullable olarak tanımlamadıysanız, EF, eğitmeni sildiğinizde bölümü silmek için basamaklı bir silme kuralını yapılandırır, ki bu olmasını istediğiniz şey değildir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="e81d3-272">İş kurallarınız özelliğin `InstructorID` geçersiz olmasını gerektiriyorsa, ilişkide basamaklı silmeyi devre dışı ksaymak için aşağıdaki akıcı API deyimini kullanmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="e81d3-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="e81d3-273">Kayıt varlığını değiştirin</span><span class="sxs-lookup"><span data-stu-id="e81d3-273">Modify Enrollment entity</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="e81d3-275">*Modeller/Enrollment.cs'de,* daha önce eklediğiniz kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="e81d3-276">Yabancı anahtar ve navigasyon özellikleri</span><span class="sxs-lookup"><span data-stu-id="e81d3-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="e81d3-277">Yabancı anahtar özellikleri ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="e81d3-278">Kayıt kaydı tek bir kurs içindir, `CourseID` bu nedenle yabancı `Course` bir anahtar özelliği ve navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="e81d3-279">Kayıt kaydı tek bir öğrenci içindir, `StudentID` bu nedenle yabancı `Student` bir anahtar özelliği ve navigasyon özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="e81d3-280">Çok-Çok ilişkiler</span><span class="sxs-lookup"><span data-stu-id="e81d3-280">Many-to-Many relationships</span></span>

<span data-ttu-id="e81d3-281">Öğrenci ve Kurs varlıkları arasında çok-çok ilişkisi vardır ve Kayıt varlığı veritabanında *yük yüklü* bir çok-çok birleştirme tablosu olarak işlev görür.</span><span class="sxs-lookup"><span data-stu-id="e81d3-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="e81d3-282">"Yük ile" Kayıt tablosu, birleştirilmiş tablolar için yabancı anahtarların yanı sıra ek veriler içerdiği anlamına gelir (bu durumda, birincil anahtar ve Not özelliği).</span><span class="sxs-lookup"><span data-stu-id="e81d3-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="e81d3-283">Aşağıdaki resimde, bu ilişkilerin varlık diyagramında nasıl göründüğü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="e81d3-284">(Bu diyagram EF 6.x için Entity Framework Power Tools kullanılarak oluşturuldu; diyagramı oluşturmak öğreticinin bir parçası değil, sadece burada bir illüstrasyon olarak kullanılıyor.)</span><span class="sxs-lookup"><span data-stu-id="e81d3-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Öğrenci-Kurs birçok ilişki için çok](complex-data-model/_static/student-course.png)

<span data-ttu-id="e81d3-286">Her ilişki satırının bir ucunda 1, diğerucunda bir yıldız (\*) vardır ve bu da bir-çok ilişkisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="e81d3-287">Kayıt tablosunda not bilgileri içermiyorsa, yalnızca iki yabancı anahtar CourseID ve StudentID içermelidir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="e81d3-288">Bu durumda, veritabanında yük (veya saf birleştirme tablosu) olmayan çok-çok birleştirme tablosu olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="e81d3-289">Eğitmen ve Kurs varlıkları bu tür çok-çok ilişki vardır ve bir sonraki adım yük olmadan bir birleştirme tablosu olarak işlev bir varlık sınıfı oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="e81d3-290">(EF 6.x, çok-çok ilişkiler için örtülü birleştirme tablolarını destekler, ancak EF Core desteklemez.</span><span class="sxs-lookup"><span data-stu-id="e81d3-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="e81d3-291">Daha fazla bilgi [için, EF Core GitHub deposundaki tartışmaya](https://github.com/aspnet/EntityFramework/issues/1368)bakın.)</span><span class="sxs-lookup"><span data-stu-id="e81d3-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="e81d3-292">Ders Atama sı</span><span class="sxs-lookup"><span data-stu-id="e81d3-292">The CourseAssignment entity</span></span>

![Ders Atama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="e81d3-294">Aşağıdaki kodile *Modeller/DersAtama.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e81d3-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="e81d3-295">Varlık adlarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-295">Join entity names</span></span>

<span data-ttu-id="e81d3-296">Bir join tablo eğitmen-to-kurslar çok-çok ilişki için veritabanında gereklidir ve bir varlık kümesi tarafından temsil edilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="e81d3-297">Bu durumda bir birleştirme varlık `EntityName1EntityName2`adlandırmak yaygındır `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="e81d3-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="e81d3-298">Ancak, ilişkiyi açıklayan bir ad seçmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="e81d3-299">Veri modelleri basit başlar ve büyür, yük yok sık sık daha sonra yük alma birleştirir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="e81d3-300">Açıklayıcı bir varlık adı ile başlarsanız, daha sonra adı değiştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="e81d3-301">İdeal olarak, birleştirme varlığı iş alanında kendi doğal (muhtemelen tek kelime) adı olurdu.</span><span class="sxs-lookup"><span data-stu-id="e81d3-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="e81d3-302">Örneğin, Kitaplar ve Müşteriler Derecelendirmeler aracılığıyla bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="e81d3-303">Bu ilişki `CourseAssignment` için, daha `CourseInstructor`iyi bir seçimdir .</span><span class="sxs-lookup"><span data-stu-id="e81d3-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="e81d3-304">Kompozit anahtar</span><span class="sxs-lookup"><span data-stu-id="e81d3-304">Composite key</span></span>

<span data-ttu-id="e81d3-305">Yabancı anahtarlar nullable ve birlikte benzersiz tablonun her satırı tanımlamak olmadığından, ayrı bir birincil anahtar için gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="e81d3-306">*InstructorID* ve *CourseID* özellikleri kompozit birincil anahtar olarak işlev almalıdır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="e81d3-307">EF için bileşik birincil anahtarları tanımlamak için tek yolu *akıcı API* kullanarak (öznitelikleri kullanılarak yapılamaz).</span><span class="sxs-lookup"><span data-stu-id="e81d3-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="e81d3-308">Bileşik birincil anahtarı sonraki bölümde nasıl yapılandıracağınızı görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="e81d3-309">Bileşik anahtar, bir kurs için birden çok satır ve bir eğitmen için birden çok satır olsa da, aynı eğitmen ve kurs için birden çok satır alamamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="e81d3-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="e81d3-310">Birleştirme `Enrollment` varlığı kendi birincil anahtarını tanımlar, bu nedenle bu tür yinelemeler mümkündür.</span><span class="sxs-lookup"><span data-stu-id="e81d3-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="e81d3-311">Bu tür yinelemeleri önlemek için, yabancı anahtar alanlarına benzersiz `Enrollment` bir dizin ekleyebilir `CourseAssignment`veya ''ye benzer bir birincil bileşik anahtarla yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="e81d3-312">Daha fazla bilgi için [Dizinler'e](/ef/core/modeling/indexes)bakın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="e81d3-313">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-313">Update the database context</span></span>

<span data-ttu-id="e81d3-314">*Data/SchoolContext.cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="e81d3-315">Bu kod yeni varlıkları ekler ve Ders Atama kuruluşunun bileşik birincil anahtarını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="e81d3-316">Akıcı bir API alternatifi hakkında</span><span class="sxs-lookup"><span data-stu-id="e81d3-316">About a fluent API alternative</span></span>

<span data-ttu-id="e81d3-317">Sınıfın yöntemindeki `OnModelCreating` kod, EF davranışını yapılandırmak için *akıcı API* kullanır. `DbContext`</span><span class="sxs-lookup"><span data-stu-id="e81d3-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="e81d3-318">API'ye "akıcı" denir, çünkü genellikle ef [core belgelerinden](/ef/core/modeling/#use-fluent-api-to-configure-a-model)bu örnekte olduğu gibi, bir dizi yöntem çağrısını tek bir deyimde bir araya getirerek kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e81d3-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="e81d3-319">Bu öğreticide, akıcı API'yi yalnızca özniteliklerle yapamayacağınız veritabanı eşlemeiçin kullanıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="e81d3-320">Ancak, öznitelikleri kullanarak yapabileceğiniz biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtmek için akıcı API'yi de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="e81d3-321">Akıcı API `MinimumLength` ile uygulanamıyor gibi bazı öznitelikler.</span><span class="sxs-lookup"><span data-stu-id="e81d3-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="e81d3-322">Daha önce de `MinimumLength` belirtildiği gibi, şema değişmez, sadece bir istemci ve sunucu tarafı doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="e81d3-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="e81d3-323">Bazı geliştiriciler, varlık sınıflarını "temiz" tutabilmek için akıcı API'yi yalnızca kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="e81d3-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="e81d3-324">İsterseniz öznitelikleri ve akıcı API karıştırabilirsiniz ve sadece akıcı API kullanılarak yapılabilir birkaç özelleştirmeler vardır, ancak genel olarak önerilen uygulama bu iki yaklaşımbirini seçmek ve mümkün olduğunca tutarlı bir şekilde kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="e81d3-325">Her ikisini de kullanırsanız, çakışma olan her yerde Akıcı API'nin öznitelikleri geçersiz kladığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="e81d3-326">Öznitelikler ve akıcı API hakkında daha fazla bilgi için yapılandırma [yöntemlerine](/ef/core/modeling/)bakın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="e81d3-327">İlişkileri Gösteren Varlık Diyagramı</span><span class="sxs-lookup"><span data-stu-id="e81d3-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="e81d3-328">Aşağıdaki resimde, Tamamlanan Okul modeli için Entity Framework Power Tools'un oluşturduğu diyagram gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="e81d3-330">Bir-çok ilişki satırlarının (1'den 1'e) \*yanı sıra, Burada Eğitmen ve OfficeAtama varlıkları ile Eğitmen ve Bölüm varlıkları arasındaki sıfır veya bir-çok ilişkisi çizgisi (0..1 ila \*) arasındaki birden sıfıra veya birilişki çizgisini (1'den 0.0'a) görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="e81d3-331">Test verileri ile tohum veritabanı</span><span class="sxs-lookup"><span data-stu-id="e81d3-331">Seed database with test data</span></span>

<span data-ttu-id="e81d3-332">Oluşturduğunuz yeni varlıklar için tohum verileri sağlamak için *Data/DbInitializer.cs* dosyasındaki kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="e81d3-333">İlk öğreticide gördüğünüz gibi, bu kodun çoğu yalnızca yeni varlık nesneleri oluşturur ve örnek verileri test için gerekli olan özelliklere yükler.</span><span class="sxs-lookup"><span data-stu-id="e81d3-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="e81d3-334">Birden çok ilişkinin nasıl işlendiğine dikkat edin: kod, varlık `Enrollments` kümelerinde `CourseAssignment` varlıklar oluşturarak ilişkiler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="e81d3-335">Geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="e81d3-335">Add a migration</span></span>

<span data-ttu-id="e81d3-336">Değişikliklerinizi kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e81d3-336">Save your changes and build the project.</span></span> <span data-ttu-id="e81d3-337">Ardından proje klasöründeki komut penceresini `migrations add` açın ve komutu girin (güncelleştirme veritabanı komutunu henüz yapmayın):</span><span class="sxs-lookup"><span data-stu-id="e81d3-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="e81d3-338">Olası veri kaybı hakkında bir uyarı alırsınız.</span><span class="sxs-lookup"><span data-stu-id="e81d3-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="e81d3-339">Bu noktada komutu `database update` çalıştırmayı denediyseniz (henüz yapmayın), aşağıdaki hatayı alırsınız:</span><span class="sxs-lookup"><span data-stu-id="e81d3-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="e81d3-340">ALTER TABLE deyimi FOREIGN KEY kısıtlaması ile çelişen "FK_dbo. Course_dbo. Department_DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="e81d3-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="e81d3-341">Çakışma veritabanı "ContosoUniversity", tablo "dbo oluştu. Bölüm", sütun 'DepartmentID'.</span><span class="sxs-lookup"><span data-stu-id="e81d3-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="e81d3-342">Bazen varolan verilerle geçişleri yürüttüğüzde, yabancı anahtar kısıtlamalarını karşılamak için veritabanına saplama verileri eklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="e81d3-343">`Up` Yöntemde oluşturulan kod, Ders tablosuna nullable olmayan DepartmentID yabancı anahtarı ekler.</span><span class="sxs-lookup"><span data-stu-id="e81d3-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="e81d3-344">Kod çalıştığında Ders tablosunda zaten satırlar varsa, `AddColumn` SQL Server sütuna null olmayan hangi değeri koyacağımı bilmediği için işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="e81d3-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="e81d3-345">Bu öğretici için geçişi yeni bir veritabanında çalıştıracaksınız, ancak bir üretim uygulamasında geçişin varolan verileri işlemesini yapmanız gerekir, bu nedenle aşağıdaki yönler bunun nasıl yapılacağınıgösteren bir örneği gösterir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="e81d3-346">Varolan verilerle bu geçiş çalışması için yeni sütuna varsayılan değer vermek için kodu değiştirmeniz ve varsayılan departman olarak hareket etmek için "Geçici" adlı bir saplama bölümü oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e81d3-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="e81d3-347">Sonuç olarak, mevcut Kurs satırlarının `Up` tümü, yöntem çalıştırıldıktan sonra "Temp" bölümüyle ilgili olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e81d3-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="e81d3-348">*{timestamp}_ComplexDataModel.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="e81d3-349">Ders tablosuna DepartmentID sütununa ekleyen kod satırına yorum yapın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="e81d3-350">Bölüm tablosunu oluşturan koddan sonra aşağıdaki vurgulanan kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e81d3-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="e81d3-351">Bir üretim uygulamasında, Bölüm satırlarını eklemek ve Ders satırlarını yeni Bölüm satırlarıyla ilişkilendirmek için kod veya komut dosyası yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="e81d3-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="e81d3-352">Daha sonra artık "Temp" bölümüne veya Course.DepartmentID sütunundaki varsayılan değere ihtiyacınız olmaz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="e81d3-353">Değişikliklerinizi kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e81d3-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="e81d3-354">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-354">Change the connection string</span></span>

<span data-ttu-id="e81d3-355">Artık sınıfta boş bir `DbInitializer` veritabanına yeni varlıklar için tohum verileri ekleyen yeni kodunuz var.</span><span class="sxs-lookup"><span data-stu-id="e81d3-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="e81d3-356">EF'nin yeni bir boş veritabanı oluşturması için *appsettings.json'daki* bağlantı dizesindeki veritabanının adını ContosoUniversity3 veya kullandığınız bilgisayarda kullanmadığınız başka bir adla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="e81d3-357">Değişikliğinizi *appsettings.json'a*kaydedin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="e81d3-358">Veritabanı adını değiştirmeye alternatif olarak veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e81d3-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="e81d3-359">**SQL Server Object Explorer** (SSOX) veya CLI komutunu `database drop` kullanın:</span><span class="sxs-lookup"><span data-stu-id="e81d3-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="e81d3-360">Veritabanını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-360">Update the database</span></span>

<span data-ttu-id="e81d3-361">Veritabanı adını değiştirdikten veya veritabanını sildikten `database update` sonra, geçişleri yürütmek için komut penceresindeki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="e81d3-362">Yöntemin `DbInitializer.Initialize` çalışmasına ve yeni veritabanını doldurmasına neden olacak şekilde uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="e81d3-363">Veritabanını daha önce yaptığınız gibi SSOX'ta açın ve tüm tabloların oluşturulduğunu görmek için **Tablolar** düğümünün genişletilmesini.</span><span class="sxs-lookup"><span data-stu-id="e81d3-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="e81d3-364">(SSOX'unuzun hala önceki nden itibaren açık olması durumunda, **Yenile** düğmesini tıklatın.)</span><span class="sxs-lookup"><span data-stu-id="e81d3-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX tabloları](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="e81d3-366">Veritabanını oluşturan baş harf kodunu tetiklemek için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e81d3-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="e81d3-367">**Ders Atama** tablosuna sağ tıklayın ve içinde veri olduğunu doğrulamak için **Verileri Görüntüle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX'ta Ders Atama verileri](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="e81d3-369">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="e81d3-369">Get the code</span></span>

[<span data-ttu-id="e81d3-370">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="e81d3-371">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="e81d3-371">Next steps</span></span>

<span data-ttu-id="e81d3-372">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="e81d3-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e81d3-373">Veri modelini özelleştirilmiş</span><span class="sxs-lookup"><span data-stu-id="e81d3-373">Customized the Data model</span></span>
> * <span data-ttu-id="e81d3-374">Öğrenci varlığında değişiklik yapıldı</span><span class="sxs-lookup"><span data-stu-id="e81d3-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="e81d3-375">Oluşturulan Eğitmen varlık</span><span class="sxs-lookup"><span data-stu-id="e81d3-375">Created Instructor entity</span></span>
> * <span data-ttu-id="e81d3-376">Oluşturulan OfficeAssignment tüzel kişiliği</span><span class="sxs-lookup"><span data-stu-id="e81d3-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="e81d3-377">Modifiye Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="e81d3-377">Modified Course entity</span></span>
> * <span data-ttu-id="e81d3-378">Oluşturulan Bölüm varlığı</span><span class="sxs-lookup"><span data-stu-id="e81d3-378">Created Department entity</span></span>
> * <span data-ttu-id="e81d3-379">Değiştirilmiş Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="e81d3-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="e81d3-380">Veritabanı bağlamını güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e81d3-380">Updated the database context</span></span>
> * <span data-ttu-id="e81d3-381">Test verileri ile seri veritabanı</span><span class="sxs-lookup"><span data-stu-id="e81d3-381">Seeded database with test data</span></span>
> * <span data-ttu-id="e81d3-382">Geçiş eklendi</span><span class="sxs-lookup"><span data-stu-id="e81d3-382">Added a migration</span></span>
> * <span data-ttu-id="e81d3-383">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="e81d3-383">Changed the connection string</span></span>
> * <span data-ttu-id="e81d3-384">Veritabanını güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e81d3-384">Updated the database</span></span>

<span data-ttu-id="e81d3-385">İlgili verilere nasıl erişiniriz hakkında daha fazla bilgi edinmek için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="e81d3-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e81d3-386">Sonraki: İlgili verilere erişin</span><span class="sxs-lookup"><span data-stu-id="e81d3-386">Next: Access related data</span></span>](read-related-data.md)
