---
title: 'Öğretici: EF Core ile karmaşık veri modeli oluşturma-ASP.NET MVC'
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kurallarını belirterek veri modelini özelleştirin.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 2264ed6f67d2394d17a79e28d1ee4a1273af73cd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401642"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="870db-103">Öğretici: EF Core ile karmaşık veri modeli oluşturma-ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="870db-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="870db-104">Önceki öğreticilerde, üç varlıktan oluşan basit bir veri modeliyle çalıştık.</span><span class="sxs-lookup"><span data-stu-id="870db-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="870db-105">Bu öğreticide, daha fazla varlık ve ilişki ekleyeceksiniz ve biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirterek veri modelini özelleştireceksiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="870db-106">İşiniz bittiğinde, varlık sınıfları aşağıdaki çizimde gösterilen tamamlanmış veri modelini oluşturacak:</span><span class="sxs-lookup"><span data-stu-id="870db-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="870db-108">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="870db-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="870db-109">Veri modelini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="870db-109">Customize the Data model</span></span>
> * <span data-ttu-id="870db-110">Öğrenci varlığında değişiklik yap</span><span class="sxs-lookup"><span data-stu-id="870db-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="870db-111">Eğitmen varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="870db-111">Create Instructor entity</span></span>
> * <span data-ttu-id="870db-112">OfficeAssignment varlığı oluştur</span><span class="sxs-lookup"><span data-stu-id="870db-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="870db-113">Kurs varlığını değiştirme</span><span class="sxs-lookup"><span data-stu-id="870db-113">Modify Course entity</span></span>
> * <span data-ttu-id="870db-114">Departman varlığı oluştur</span><span class="sxs-lookup"><span data-stu-id="870db-114">Create Department entity</span></span>
> * <span data-ttu-id="870db-115">Kayıt varlığını değiştirme</span><span class="sxs-lookup"><span data-stu-id="870db-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="870db-116">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="870db-116">Update the database context</span></span>
> * <span data-ttu-id="870db-117">Test verileriyle çekirdek veritabanı</span><span class="sxs-lookup"><span data-stu-id="870db-117">Seed database with test data</span></span>
> * <span data-ttu-id="870db-118">Geçiş Ekle</span><span class="sxs-lookup"><span data-stu-id="870db-118">Add a migration</span></span>
> * <span data-ttu-id="870db-119">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="870db-119">Change the connection string</span></span>
> * <span data-ttu-id="870db-120">Veritabanını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="870db-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="870db-121">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="870db-121">Prerequisites</span></span>

* [<span data-ttu-id="870db-122">EF Core geçişleri kullanma</span><span class="sxs-lookup"><span data-stu-id="870db-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="870db-123">Veri modelini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="870db-123">Customize the Data model</span></span>

<span data-ttu-id="870db-124">Bu bölümde, biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirten öznitelikleri kullanarak veri modelini nasıl özelleştireceğinizi göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="870db-125">Ardından, aşağıdaki bölümlerde, zaten oluşturduğunuz sınıflara öznitelikler ekleyerek ve modeldeki kalan varlık türleri için yeni sınıflar oluşturarak tüm okul veri modelini oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="870db-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="870db-126">DataType özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-126">The DataType attribute</span></span>

<span data-ttu-id="870db-127">Öğrenci kayıt tarihleri için tüm Web sayfaları Şu anda tarihle birlikte görüntülenir, ancak bu alan için tüm önemli bir tarih olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="870db-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="870db-128">Veri ek açıklaması özniteliklerini kullanarak, verileri gösteren her görünümde görüntü biçimini giderecek bir kod değişikliği yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="870db-129">Bunun nasıl yapılacağını gösteren bir örnek görmek için, sınıfındaki özelliğine bir özniteliği ekleyeceksiniz `EnrollmentDate` `Student` .</span><span class="sxs-lookup"><span data-stu-id="870db-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="870db-130">*Modeller/öğrenci. cs*' de, `using` ad alanı için bir ifade ekleyin `System.ComponentModel.DataAnnotations` ve `DataType` `DisplayFormat` `EnrollmentDate` Aşağıdaki örnekte gösterildiği gibi özelliğe ve özniteliklerini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="870db-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="870db-131">`DataType`Özniteliği, veritabanı iç türünden daha belirgin bir veri türü belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="870db-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="870db-132">Bu durumda, tarihi ve saati değil yalnızca tarihi izlemek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="870db-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="870db-133">`DataType`Sabit listesi, tarih, saat, PhoneNumber, para birimi, Emaadresi gibi birçok veri türünü sağlar.</span><span class="sxs-lookup"><span data-stu-id="870db-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="870db-134">`DataType`Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="870db-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="870db-135">Örneğin, için bir `mailto:` bağlantı oluşturulabilir `DataType.EmailAddress` ve HTML5 'i destekleyen tarayıcılarda için bir tarih seçici sağlaneklenebilir `DataType.Date` .</span><span class="sxs-lookup"><span data-stu-id="870db-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="870db-136">`DataType`ÖZNITELIĞI `data-` HTML 5 tarayıcılarının ANLAYABILMESI için HTML 5 (bir veri Dash) öznitelikleri yayar.</span><span class="sxs-lookup"><span data-stu-id="870db-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="870db-137">`DataType`Öznitelikler hiçbir doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="870db-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="870db-138">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="870db-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="870db-139">Varsayılan olarak, veri alanı sunucunun CultureInfo öğesine göre varsayılan biçimlere göre görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="870db-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="870db-140">`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="870db-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="870db-141">`ApplyFormatInEditMode`Ayar, bir metin kutusunda değer görüntülenmek üzere görüntülendiğinde biçimlendirmenin de uygulanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="870db-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="870db-142">(Bazı alanlar için bunu istemiyor olabilirsiniz; örneğin, para birimi değerleri için metin kutusundaki para birimi sembolünü düzenlemenin değiştirilmesini istemeyebilirsiniz.)</span><span class="sxs-lookup"><span data-stu-id="870db-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="870db-143">`DisplayFormat`Özniteliğini kendisi kullanabilirsiniz, ancak özniteliği de kullanmak iyi bir fikir olabilir `DataType` .</span><span class="sxs-lookup"><span data-stu-id="870db-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="870db-144">`DataType`Özniteliği, bir ekranda nasıl işlenirim aksine, verilerin semantiğini sunar ve aşağıdaki avantajları sağlar `DisplayFormat` :</span><span class="sxs-lookup"><span data-stu-id="870db-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="870db-145">Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını, bazı istemci tarafı giriş doğrulamasını vb. göstermek için).</span><span class="sxs-lookup"><span data-stu-id="870db-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="870db-146">Varsayılan olarak tarayıcı, verileri yerel ayarınızı temel alarak doğru biçimi kullanarak işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="870db-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="870db-147">Daha fazla bilgi için bkz. [ \<input> etiket Yardımcısı belgeleri](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="870db-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="870db-148">Uygulamayı çalıştırın, öğrenciler dizin sayfasına gidin ve kayıt tarihleri için saatlerin artık gösterilmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="870db-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="870db-149">Aynı değer öğrenci modelini kullanan herhangi bir görünüm için de geçerli olacaktır.</span><span class="sxs-lookup"><span data-stu-id="870db-149">The same will be true for any view that uses the Student model.</span></span>

![Öğrenciler Dizin sayfası tarihleri zamansız gösterme](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="870db-151">StringLength özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-151">The StringLength attribute</span></span>

<span data-ttu-id="870db-152">Ayrıca, öznitelikleri kullanarak veri doğrulama kuralları ve doğrulama hata iletileri de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="870db-153">`StringLength`Öznitelik, veritabanında en fazla uzunluğu ayarlar ve ASP.NET Core MVC için istemci tarafı ve sunucu tarafı doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="870db-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="870db-154">Bu öznitelikte en küçük dize uzunluğunu da belirtebilirsiniz, ancak en küçük değerin veritabanı şemasında hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="870db-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="870db-155">Kullanıcıların bir ad için 50 ' den fazla karakter girmemesini istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="870db-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="870db-156">Bu kısıtlamayı eklemek için `StringLength` `LastName` `FirstMidName` Aşağıdaki örnekte gösterildiği gibi öznitelikleri ve özelliklerine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="870db-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="870db-157">Öznitelik, bir `StringLength` kullanıcının ad için boşluk girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="870db-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="870db-158">`RegularExpression`Girişe kısıtlama uygulamak için özniteliğini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="870db-159">Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:</span><span class="sxs-lookup"><span data-stu-id="870db-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="870db-160">`MaxLength`Öznitelik, `StringLength` özniteliğe benzer ancak istemci tarafı doğrulaması sağlamayan işlevselliği sağlar.</span><span class="sxs-lookup"><span data-stu-id="870db-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="870db-161">Veritabanı modeli, veritabanı şemasında değişiklik yapılmasını gerektiren bir şekilde değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="870db-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="870db-162">Uygulama kullanıcı arabirimini kullanarak, veritabanına eklemiş olduğunuz herhangi bir veriyi kaybetmeden Şemayı güncelleştirmek için geçişleri kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="870db-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="870db-163">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="870db-163">Save your changes and build the project.</span></span> <span data-ttu-id="870db-164">Ardından proje klasöründe komut penceresini açın ve aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="870db-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="870db-165">`migrations add`Bu komut, iki sütun için en fazla uzunluğu daha kısa hale yaptığından, veri kaybının gerçekleşebileceğini uyarır.</span><span class="sxs-lookup"><span data-stu-id="870db-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="870db-166">Geçişler \* \<timeStamp> _MaxLengthOnNames. cs\*adlı bir dosya oluşturur.</span><span class="sxs-lookup"><span data-stu-id="870db-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="870db-167">Bu dosya, `Up` geçerli veri modeliyle eşleşecek şekilde veritabanını güncelleştirecek yöntemdeki kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="870db-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="870db-168">`database update`Komut bu kodu çalıştırdı.</span><span class="sxs-lookup"><span data-stu-id="870db-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="870db-169">Geçiş dosyası adının ön eki olan zaman damgası, geçişleri sıralamak için Entity Framework tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="870db-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="870db-170">Update-database komutunu çalıştırmadan önce birden çok geçiş oluşturabilirsiniz ve sonra tüm geçişler oluşturuldukları sırada uygulanır.</span><span class="sxs-lookup"><span data-stu-id="870db-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="870db-171">Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin, **Yeni oluştur**' a tıklayın ve 50 karakterden daha uzun bir ad girmeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="870db-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="870db-172">Uygulamanın bunu yapmasını önleyebilmelidir.</span><span class="sxs-lookup"><span data-stu-id="870db-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="870db-173">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-173">The Column attribute</span></span>

<span data-ttu-id="870db-174">Ayrıca, sınıflarınızın ve özelliklerinin veritabanına nasıl eşlenildiğini denetlemek için özniteliklerini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="870db-175">`FirstMidName`Alan aynı zamanda bir orta ad içerebileceğinden, birinci ad alanı için adı kullandığınızı varsayalım.</span><span class="sxs-lookup"><span data-stu-id="870db-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="870db-176">Ancak veritabanına `FirstName` karşı geçici sorgular yazmayacak olan kullanıcılar bu ada alışkın olduğundan, veritabanı sütununun adlandırılmak istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="870db-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="870db-177">Bu eşlemeyi yapmak için `Column` özniteliğini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="870db-178">`Column`Özniteliği veritabanı oluşturulduğunda, `Student` özelliği ile eşlenen tablonun sütununun `FirstMidName` adlandırılacağını belirtir `FirstName` .</span><span class="sxs-lookup"><span data-stu-id="870db-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="870db-179">Diğer bir deyişle, kodunuz öğesine başvurduğunda `Student.FirstMidName` , veriler tablonun sütununda gelir veya güncelleştirilir `FirstName` `Student` .</span><span class="sxs-lookup"><span data-stu-id="870db-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="870db-180">Sütun adları belirtmezseniz, bunlar Özellik adı ile aynı ada verilir.</span><span class="sxs-lookup"><span data-stu-id="870db-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="870db-181">*Student.cs* dosyasında, için bir ifade ekleyin `using` `System.ComponentModel.DataAnnotations.Schema` ve `FirstMidName` aşağıdaki vurgulanmış kodda gösterildiği gibi, özelliği için bir sütun adı özniteliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="870db-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="870db-182">Özniteliği ekleme modeli, `Column` `SchoolContext` tarafından yedeklendiğinden, veritabanıyla eşleşmeyecek şekilde değişir.</span><span class="sxs-lookup"><span data-stu-id="870db-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="870db-183">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="870db-183">Save your changes and build the project.</span></span> <span data-ttu-id="870db-184">Ardından proje klasöründe komut penceresini açın ve başka bir geçiş oluşturmak için aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="870db-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="870db-185">**SQL Server Nesne Gezgini** **, öğrenci tablosuna** çift tıklayarak öğrenci tablosu tasarımcısını açın.</span><span class="sxs-lookup"><span data-stu-id="870db-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="870db-187">İlk iki geçişi uygulamadan önce ad sütunları nvarchar (MAX) türünde.</span><span class="sxs-lookup"><span data-stu-id="870db-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="870db-188">Artık nvarchar (50) ve sütun adı FirstMidName iken FirstName olarak değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="870db-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="870db-189">Aşağıdaki bölümlerde tüm varlık sınıflarını oluşturmayı bitirmeden önce derlemeyi denerseniz Derleyici hataları alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="870db-190">Öğrenci varlığındaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="870db-190">Changes to Student entity</span></span>

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

<span data-ttu-id="870db-192">*Modeller/öğrenci. cs*' de, daha önce eklediğiniz kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="870db-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="870db-193">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="870db-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="870db-194">Gerekli öznitelik</span><span class="sxs-lookup"><span data-stu-id="870db-194">The Required attribute</span></span>

<span data-ttu-id="870db-195">`Required`Özniteliği, ad özellikleri gerekli alanları yapar.</span><span class="sxs-lookup"><span data-stu-id="870db-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="870db-196">`Required`Değer türleri (DateTime, int, Double, float, vb.) gibi null yapılamayan türler için öznitelik gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="870db-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="870db-197">Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="870db-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="870db-198">`Required`Özniteliğinin zorlanmak üzere ile birlikte kullanılması gerekir `MinimumLength` `MinimumLength` .</span><span class="sxs-lookup"><span data-stu-id="870db-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="870db-199">Display özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-199">The Display attribute</span></span>

<span data-ttu-id="870db-200">`Display`Öznitelik, metin kutularının açıklamalı alt yazısının her örnekteki Özellik adı yerine "ilk ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir (kelimeleri hiçbir alan içermez).</span><span class="sxs-lookup"><span data-stu-id="870db-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="870db-201">FullName hesaplanmış özelliği</span><span class="sxs-lookup"><span data-stu-id="870db-201">The FullName calculated property</span></span>

<span data-ttu-id="870db-202">`FullName`, iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="870db-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="870db-203">Bu nedenle, yalnızca bir get erişimcisine sahiptir ve `FullName` veritabanında hiçbir sütun oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="870db-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="870db-204">Eğitmen varlığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="870db-204">Create Instructor entity</span></span>

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="870db-206">Şablon kodunu aşağıdaki kodla değiştirerek *modeller/eğitmen. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="870db-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="870db-207">Öğrenci ve eğitmen varlıklarında birçok özellik aynı olduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="870db-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="870db-208">Devralma öğreticisini bu serinin ilerleyen kısımlarında [uyguladığınızda](inheritance.md) , artıklığı ortadan kaldırmak için bu kodu yeniden düzenlemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="870db-209">Birden çok özniteliği tek bir satıra koyabilirsiniz, böylece `HireDate` öznitelikleri aşağıdaki şekilde yazabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="870db-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="870db-210">Courseatamalar ve OfficeAssignment gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="870db-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="870db-211">`CourseAssignments`Ve `OfficeAssignment` özellikleri gezinti özellikleridir.</span><span class="sxs-lookup"><span data-stu-id="870db-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="870db-212">Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="870db-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="870db-213">Bir gezinti özelliği birden çok varlık tukiyorsa, türü girişlerin eklenebileceği, silinebileceği ve güncelleştirilemeyebilir bir liste olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="870db-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="870db-214">Veya gibi `ICollection<T>` bir tür belirtebilirsiniz `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="870db-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="870db-215">Belirtirseniz `ICollection<T>` , EF `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="870db-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="870db-216">Bunların `CourseAssignment` varlıkların, çoktan çoğa ilişkiler hakkında bölümünde aşağıda açıklanmasının nedeni aşağıda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="870db-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="870db-217">Contoso Üniversitesi iş kuralları, bir eğitmenin yalnızca en fazla bir ofisin olabileceğini, bu nedenle `OfficeAssignment` özelliğin tek bir OfficeAssignment varlığı (Office atanmamışsa null olabilir) bulundurduğunu belirten bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="870db-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="870db-218">OfficeAssignment varlığı oluştur</span><span class="sxs-lookup"><span data-stu-id="870db-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="870db-220">Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="870db-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="870db-221">Anahtar özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-221">The Key attribute</span></span>

<span data-ttu-id="870db-222">Eğitmen ve OfficeAssignment varlıkları arasında bire sıfır veya-bir ilişki vardır.</span><span class="sxs-lookup"><span data-stu-id="870db-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="870db-223">Office ataması, atandığı eğitmenle ilişkili olarak yalnızca kendi birincil anahtarı da eğitmen varlığına ait yabancı anahtarıdır.</span><span class="sxs-lookup"><span data-stu-id="870db-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="870db-224">Ancak Entity Framework, adı ID veya Classnameıd adlandırma kuralını izlemediği için bu varlığın birincil anahtarı olarak Komutctorıd 'yi otomatik olarak tanıyamaz.</span><span class="sxs-lookup"><span data-stu-id="870db-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="870db-225">Bu nedenle, `Key` özniteliği anahtar olarak tanımlamak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="870db-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="870db-226">`Key`Varlık kendi birincil anahtarına sahip olsa da, özelliği Classnameıd veya ID dışında bir şey olarak adlandırmak istiyorsanız özniteliğini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="870db-227">Varsayılan olarak, tam olarak, sütun tanımlayıcı bir ilişki için olduğundan EF, anahtarı veritabanı olmayan bir şekilde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="870db-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="870db-228">Eğitmen gezintisi özelliği</span><span class="sxs-lookup"><span data-stu-id="870db-228">The Instructor navigation property</span></span>

<span data-ttu-id="870db-229">Eğitmen varlığı, null yapılabilir bir `OfficeAssignment` gezinti özelliğine sahiptir (bir eğitmenin bir Office ataması olmayabilir) ve OfficeAssignment varlığı null atanamaz bir `Instructor` gezinti özelliğine sahiptir (bir Office ataması bir eğitmen olmadan mevcut olamaz-- `InstructorID` null değer atanamaz).</span><span class="sxs-lookup"><span data-stu-id="870db-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="870db-230">Bir eğitmen varlığı ilgili bir OfficeAssignment varlığına sahip olduğunda, her varlığın gezinti özelliğinde diğer birine bir başvurusu olur.</span><span class="sxs-lookup"><span data-stu-id="870db-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="870db-231">`[Required]`İlgili bir eğitmen olması gerektiğini belirtmek Için eğitmen gezinti özelliğine bir öznitelik koyabilirsiniz, ancak `InstructorID` yabancı anahtar (aynı zamanda bu tabloya yönelik olan anahtar) null değer atanamaz olduğundan bunu yapmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="870db-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="870db-232">Kurs varlığını değiştirme</span><span class="sxs-lookup"><span data-stu-id="870db-232">Modify Course entity</span></span>

![Kurs varlığı](complex-data-model/_static/course-entity.png)

<span data-ttu-id="870db-234">*Modeller/kurs. cs*' de, daha önce eklediğiniz kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="870db-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="870db-235">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="870db-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="870db-236">Kurs varlığının, `DepartmentID` Ilgili departman varlığını işaret eden ve bir gezinti özelliği olan yabancı anahtar özelliği vardır `Department` .</span><span class="sxs-lookup"><span data-stu-id="870db-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="870db-237">Entity Framework, ilgili varlık için bir gezinti özelliği olduğunda, veri modelinize yabancı anahtar özelliği eklemenizi gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="870db-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="870db-238">EF, gerektiğinde otomatik olarak yabancı anahtarlar oluşturur ve bunlar için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="870db-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="870db-239">Ancak veri modelinde yabancı anahtar olması, güncelleştirmeleri daha basit ve daha verimli hale getirir.</span><span class="sxs-lookup"><span data-stu-id="870db-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="870db-240">Örneğin, düzenlemek üzere bir kurs varlığı aldığınızda, bunu yüklemezseniz departman varlığı null olur, bu nedenle kurs varlığını güncelleştirdiğinizde öncelikle departman varlığını almanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="870db-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="870db-241">Yabancı anahtar özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden önce bölüm varlığını almanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="870db-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="870db-242">DatabaseGenerated özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="870db-243">`DatabaseGenerated`Özelliğindeki parametresi olan özniteliği, `None` `CourseID` birincil anahtar değerlerinin veritabanı tarafından oluşturulması yerine Kullanıcı tarafından sağlandığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="870db-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="870db-244">Varsayılan olarak, Entity Framework birincil anahtar değerlerinin veritabanı tarafından oluşturulduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="870db-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="870db-245">Bu, Çoğu senaryoda istediğiniz şeydir.</span><span class="sxs-lookup"><span data-stu-id="870db-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="870db-246">Ancak, kurs varlıkları için bir departman için 1000 serisi, başka bir departman için 2000 serisi vb. gibi kullanıcı tarafından belirtilen bir kurs numarası kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="870db-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="870db-247">`DatabaseGenerated`Özniteliği, bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için kullanılan veritabanı sütunlarında olduğu gibi varsayılan değerleri oluşturmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="870db-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="870db-248">Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="870db-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="870db-249">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="870db-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="870db-250">Kurs varlığındaki yabancı anahtar özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="870db-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="870db-251">Bir kurs bir departmana atanır, bu nedenle `DepartmentID` yukarıda bahsedilen nedenlerden dolayı bir yabancı anahtar ve bir `Department` gezinti özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="870db-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="870db-252">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:</span><span class="sxs-lookup"><span data-stu-id="870db-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="870db-253">Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur (tür `CourseAssignment` [daha sonra](#many-to-many-relationships)açıklanmıştır):</span><span class="sxs-lookup"><span data-stu-id="870db-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="870db-254">Departman varlığı oluştur</span><span class="sxs-lookup"><span data-stu-id="870db-254">Create Department entity</span></span>

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

<span data-ttu-id="870db-256">Aşağıdaki kodla *modeller/departman. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="870db-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="870db-257">Column özniteliği</span><span class="sxs-lookup"><span data-stu-id="870db-257">The Column attribute</span></span>

<span data-ttu-id="870db-258">Daha önce, `Column` sütun adı eşlemesini değiştirmek için özniteliğini kullandınız.</span><span class="sxs-lookup"><span data-stu-id="870db-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="870db-259">Bölüm varlığının kodunda, `Column` sütun, veritabanının SQL Server para türü kullanılarak tanımlanacak şekılde SQL veri türü eşlemesini değiştirmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="870db-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="870db-260">Entity Framework, özellik için tanımladığınız CLR türüne göre uygun SQL Server veri türünü seçtiği için sütun eşlemesi genellikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="870db-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="870db-261">CLR `decimal` türü SQL Server bir `decimal` türe eşlenir.</span><span class="sxs-lookup"><span data-stu-id="870db-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="870db-262">Ancak bu durumda, sütunun para birimi tutarlarını tutuını ve para veri türü bunun için daha uygun olduğunu bilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="870db-263">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="870db-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="870db-264">Yabancı anahtar ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="870db-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="870db-265">Bir departman yönetici olabilir veya olmayabilir ve yönetici her zaman bir eğitmendir.</span><span class="sxs-lookup"><span data-stu-id="870db-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="870db-266">Bu nedenle, `InstructorID` özelliği, eğitmen varlığına yabancı anahtar olarak dahil edilir ve `int` tür atamadan sonra özelliği null yapılabilir olarak işaretlemek için bir soru işareti eklenir.</span><span class="sxs-lookup"><span data-stu-id="870db-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="870db-267">Gezinti özelliği adlandırılır `Administrator` ancak bir eğitmen varlığı tutar:</span><span class="sxs-lookup"><span data-stu-id="870db-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="870db-268">Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="870db-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="870db-269">Kurala göre Entity Framework, null olamayan yabancı anahtarlar ve çoktan çoğa ilişkiler için art arda silme imkanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="870db-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="870db-270">Bu, bir geçiş eklemeye çalıştığınızda bir özel duruma neden olacak dairesel basamaklı silme kurallarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="870db-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="870db-271">Örneğin, Department. Komutctorıd özelliğini null yapılabilir olarak tanımlamadıysanız, bu, bir basamak silme kuralını, eğitmeni sildiğinizde, ne yapmak istediğinize ilişkin olmayan bir şeyi silmek için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="870db-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="870db-272">İş kurallarınız `InstructorID` özelliği null değer atanamaz olarak gerektiriyorsa, ilişkide basamaklı silmeyi devre dışı bırakmak için aşağıdaki Fluent API ifadesini kullanmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="870db-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="870db-273">Kayıt varlığını değiştirme</span><span class="sxs-lookup"><span data-stu-id="870db-273">Modify Enrollment entity</span></span>

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="870db-275">*Modeller/kayıt. cs*' de, daha önce eklediğiniz kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="870db-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="870db-276">Yabancı anahtar ve gezinti özellikleri</span><span class="sxs-lookup"><span data-stu-id="870db-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="870db-277">Yabancı anahtar özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:</span><span class="sxs-lookup"><span data-stu-id="870db-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="870db-278">Kayıt kaydı tek bir kurs için olduğundan, `CourseID` yabancı anahtar özelliği ve bir `Course` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="870db-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="870db-279">Kayıt kaydı tek bir öğrenci içindir, bu nedenle bir `StudentID` yabancı anahtar özelliği ve bir `Student` gezinti özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="870db-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="870db-280">Çoktan çoğa ilişkiler</span><span class="sxs-lookup"><span data-stu-id="870db-280">Many-to-Many relationships</span></span>

<span data-ttu-id="870db-281">Öğrenci ve kurs varlıkları arasında çok-çok ilişkisi vardır ve kayıt varlığı, veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="870db-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="870db-282">"Yük ile", kayıt tablosunun birleştirilmiş tablolar için yabancı anahtarlar (Bu durumda bir birincil anahtar ve bir sınıf özelliği) yanında ek veriler içerdiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="870db-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="870db-283">Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="870db-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="870db-284">(Bu diyagram EF 6. x için Entity Framework güç araçları kullanılarak oluşturulmuştur; diyagramı oluşturmak öğreticinin bir parçası değildir, burada yalnızca bir çizim olarak kullanılmaktadır.)</span><span class="sxs-lookup"><span data-stu-id="870db-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

<span data-ttu-id="870db-286">Her ilişki satırında 1 bir sonda ve diğeri de bir yıldız işareti (\*) bulunur. Bu, bire çok ilişkiyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="870db-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="870db-287">Kayıt tablosu, sınıf bilgilerini içermiyorsa, yalnızca iki yabancı anahtar olan CourseID ve Studentitıd 'yi içermelidir.</span><span class="sxs-lookup"><span data-stu-id="870db-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="870db-288">Bu durumda, veritabanına yük (veya saf bir JOIN tablosu) olmadan çok-çok arasında bir JOIN tablosu olacaktır.</span><span class="sxs-lookup"><span data-stu-id="870db-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="870db-289">Eğitmen ve kurs varlıklarının bu tür çok-çok ilişkisi vardır ve bir sonraki adımınız, yük olmadan bir JOIN tablosu olarak çalışacak bir varlık sınıfı oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="870db-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="870db-290">(EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core.</span><span class="sxs-lookup"><span data-stu-id="870db-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="870db-291">Daha fazla bilgi için, [EF Core GitHub deposundaki tartışmaya](https://github.com/aspnet/EntityFramework/issues/1368)bakın.)</span><span class="sxs-lookup"><span data-stu-id="870db-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="870db-292">Courseatama varlığı</span><span class="sxs-lookup"><span data-stu-id="870db-292">The CourseAssignment entity</span></span>

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="870db-294">Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="870db-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="870db-295">Varlık adlarını Birleştir</span><span class="sxs-lookup"><span data-stu-id="870db-295">Join entity names</span></span>

<span data-ttu-id="870db-296">Eğitim--çok ilişkisi için veritabanında bir JOIN tablosu gereklidir ve bir varlık kümesi tarafından temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="870db-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="870db-297">Bu örnekte olduğu gibi bir JOIN varlığı adı yaygın olarak `EntityName1EntityName2` kullanılır `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="870db-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="870db-298">Ancak, ilişkiyi açıklayan bir ad seçmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="870db-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="870db-299">Veri modelleri, daha sonra yükleri daha sonra almak için, yük olmayan birleşimler olmadan basit ve büyümeye başlar.</span><span class="sxs-lookup"><span data-stu-id="870db-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="870db-300">Açıklayıcı bir varlık adıyla başladıysanız, daha sonra adı değiştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="870db-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="870db-301">İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="870db-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="870db-302">Örneğin, kitaplar ve müşteriler derecelendirmeler aracılığıyla bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="870db-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="870db-303">Bu ilişki için, `CourseAssignment` daha iyi bir seçenektir `CourseInstructor` .</span><span class="sxs-lookup"><span data-stu-id="870db-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="870db-304">Bileşik anahtar</span><span class="sxs-lookup"><span data-stu-id="870db-304">Composite key</span></span>

<span data-ttu-id="870db-305">Yabancı anahtarlar null değer atanmadığından ve tablodaki her satırı benzersiz bir şekilde tanımladığından, ayrı bir birincil anahtar gerekmez.</span><span class="sxs-lookup"><span data-stu-id="870db-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="870db-306">*Komutctorıd* ve *CourseID* özellikleri bir bileşik birincil anahtar olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="870db-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="870db-307">EF için bileşik birincil anahtarları tanımlamanın tek yolu *Fluent API* kullanmaktır (öznitelikleri kullanılarak gerçekleştirilemez).</span><span class="sxs-lookup"><span data-stu-id="870db-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="870db-308">Bir sonraki bölümde bileşik birincil anahtarı nasıl yapılandıracağınızı göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="870db-309">Bileşik anahtar, tek bir kurs için birden çok satır ve bir eğitmen için birden fazla satır, aynı eğitmen ve kurs için birden çok satıra sahip olmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="870db-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="870db-310">`Enrollment`JOIN varlığı kendi birincil anahtarını tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür.</span><span class="sxs-lookup"><span data-stu-id="870db-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="870db-311">Bu tür yinelemeleri engellemek için yabancı anahtar alanlarına benzersiz bir dizin ekleyebilir veya `Enrollment` benzer bir birincil bileşik anahtarla yapılandırabilirsiniz `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="870db-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="870db-312">Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="870db-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="870db-313">Veritabanı bağlamını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="870db-313">Update the database context</span></span>

<span data-ttu-id="870db-314">*Data/SchoolContext. cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="870db-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="870db-315">Bu kod yeni varlıkları ekler ve Courseatama varlığının bileşik birincil anahtarını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="870db-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="870db-316">Fluent API alternatifi hakkında</span><span class="sxs-lookup"><span data-stu-id="870db-316">About a fluent API alternative</span></span>

<span data-ttu-id="870db-317">Sınıf yöntemindeki kod, `OnModelCreating` `DbContext` EF davranışını yapılandırmak için *Fluent API* kullanır.</span><span class="sxs-lookup"><span data-stu-id="870db-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="870db-318">Bu örnekte, [EF Core belgelerinden](/ef/core/modeling/#use-fluent-api-to-configure-a-model)farklı olarak, bir dizi yöntemi çağıran tek bir bildirimde dize tarafından KULLANıLDıĞıNDAN, API "floent" olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="870db-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="870db-319">Bu öğreticide, yalnızca öznitelikleri ile yapaamıyoruz veritabanı eşlemesi için Fluent API kullanıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="870db-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="870db-320">Ancak, özniteliklerini kullanarak yapabileceğiniz biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtmek için Fluent API de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="870db-321">Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="870db-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="870db-322">Daha önce belirtildiği gibi, `MinimumLength` Şemayı değiştirmez, yalnızca bir istemci ve sunucu tarafı doğrulama kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="870db-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="870db-323">Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="870db-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="870db-324">İsterseniz öznitelikleri ve Fluent API karıştırabilir ve yalnızca Fluent API kullanılarak gerçekleştirilebilecek bazı özelleştirmeler de vardır ancak genel olarak önerilen uygulama, bu iki yaklaşımdan birini seçmek ve bunları mümkün olduğunca tutarlı bir şekilde kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="870db-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="870db-325">Her ikisini de kullanırsanız, bir çakışma olduğunda, akıcı API 'nin öznitelikleri geçersiz kıldığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="870db-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="870db-326">Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="870db-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="870db-327">Ilişkileri gösteren varlık diyagramı</span><span class="sxs-lookup"><span data-stu-id="870db-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="870db-328">Aşağıdaki çizimde, Entity Framework Power Tools 'un tamamlanmış okul modeli için kullandığı diyagram gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="870db-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![Varlık diyagramı](complex-data-model/_static/diagram.png)

<span data-ttu-id="870db-330">Bire çok ilişki çizgilerinin yanı sıra (1 ' den fazla \* ), eğitmen ve OfficeAssignment varlıkları arasında (1 ila 0.. 1) ve eğitmen ve departman varlıkları arasında sıfır veya-bire çok ilişki satırı (0.. 1 ile \*) arasında bir-sıfır veya-bir ilişki satırını görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="870db-331">Test verileriyle çekirdek veritabanı</span><span class="sxs-lookup"><span data-stu-id="870db-331">Seed database with test data</span></span>

<span data-ttu-id="870db-332">*Veri/Dbınınitializer. cs* dosyasındaki kodu, oluşturduğunuz yeni varlıklar için çekirdek verileri sağlamak üzere aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="870db-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="870db-333">İlk öğreticide gördüğünüz gibi, bu kodun çoğu yalnızca yeni varlık nesneleri oluşturur ve test için gereken şekilde, örnek verileri özelliklere yükler.</span><span class="sxs-lookup"><span data-stu-id="870db-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="870db-334">Çoktan çoğa ilişkilerin nasıl işlendiği hakkında dikkat edin: kod, `Enrollments` ve varlık kümelerinde varlıklar oluşturarak ilişkiler oluşturur `CourseAssignment` .</span><span class="sxs-lookup"><span data-stu-id="870db-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="870db-335">Geçiş Ekle</span><span class="sxs-lookup"><span data-stu-id="870db-335">Add a migration</span></span>

<span data-ttu-id="870db-336">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="870db-336">Save your changes and build the project.</span></span> <span data-ttu-id="870db-337">Ardından proje klasöründe komut penceresini açın ve `migrations add` komutu girin (henüz Update-database komutunu yapmayın):</span><span class="sxs-lookup"><span data-stu-id="870db-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="870db-338">Olası veri kaybı hakkında bir uyarı alırsınız.</span><span class="sxs-lookup"><span data-stu-id="870db-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="870db-339">`database update`Komutu bu noktada çalıştırmayı denediyseniz (henüz yapmayın), şu hatayı alırsınız:</span><span class="sxs-lookup"><span data-stu-id="870db-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="870db-340">ALTER TABLE ifadesi, "FK_dbo yabancı anahtar kısıtlaması ile çakışıyor. Course_dbo. Department_DepartmentID ".</span><span class="sxs-lookup"><span data-stu-id="870db-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="870db-341">"ContosoUniversity" veritabanında, "dbo" tablosunda çakışma oluştu. Bölüm ", sütun ' DepartmentID '.</span><span class="sxs-lookup"><span data-stu-id="870db-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="870db-342">Bazı durumlarda, mevcut verilerle geçişleri yürüttüğünüzde, yabancı anahtar kısıtlamalarını karşılamak için saplama verilerini veritabanına eklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="870db-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="870db-343">Yönteminde oluşturulan kod, `Up` Kurs tablosuna null yapılamayan bir DepartmentID yabancı anahtar ekler.</span><span class="sxs-lookup"><span data-stu-id="870db-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="870db-344">Kurs tablosunda kod çalıştırıldığında zaten satırlar varsa, `AddColumn` SQL Server null olmayan sütuna hangi değerin yerleştirileceğini bilmediği için işlem başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="870db-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="870db-345">Bu öğreticide, geçişi yeni bir veritabanında çalıştıracaksınız, ancak bir üretim uygulamasında geçiş, mevcut verileri işleyeceğinizden, bu sayede bunun nasıl yapılacağını gösteren bir örnek gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="870db-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="870db-346">Bu geçişi mevcut verilerle birlikte çalışarak, yeni sütuna varsayılan bir değer vermek için kodu değiştirmeniz ve varsayılan departman görevi gören "Temp" adlı bir saplama departmanı oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="870db-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="870db-347">Sonuç olarak, mevcut kurs satırları, yöntem çalıştıktan sonra "geçici" departmanla ilgili olacaktır `Up` .</span><span class="sxs-lookup"><span data-stu-id="870db-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="870db-348">*{Timestamp} _ComplexDataModel. cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="870db-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="870db-349">DepartmentID sütununu kurs tablosuna ekleyen kod satırını açıklama satırı yapın.</span><span class="sxs-lookup"><span data-stu-id="870db-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="870db-350">Departman tablosunu oluşturan koddan sonra aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="870db-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="870db-351">Bir üretim uygulamasında, departman satırları eklemek ve kurs satırlarını yeni departman satırlarıyla ilişkilendirmek için kod veya komut dosyaları yazın.</span><span class="sxs-lookup"><span data-stu-id="870db-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="870db-352">Bundan sonra "geçici" Departmanı veya kurs. DepartmentID sütununda Varsayılan değer gerekmez.</span><span class="sxs-lookup"><span data-stu-id="870db-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="870db-353">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="870db-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="870db-354">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="870db-354">Change the connection string</span></span>

<span data-ttu-id="870db-355">Artık `DbInitializer` sınıfta yeni varlıklar için tohum verilerini boş bir veritabanına ekleyen yeni kodunuz var.</span><span class="sxs-lookup"><span data-stu-id="870db-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="870db-356">EF 'in yeni boş bir veritabanı oluşturmasını sağlamak için, *appsettings.jsüzerindeki* bağlantı dizesinde veritabanının adını ContosoUniversity3 veya kullandığınız bilgisayarda kullanmadığınız başka bir adla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="870db-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="870db-357">*appsettings.js*değişikliklerinizi kaydedin.</span><span class="sxs-lookup"><span data-stu-id="870db-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="870db-358">Veritabanı adını değiştirmeye alternatif olarak, veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="870db-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="870db-359">**SQL Server Nesne Gezgini** (ssox) veya `database drop` CLI komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="870db-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="870db-360">Veritabanını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="870db-360">Update the database</span></span>

<span data-ttu-id="870db-361">Veritabanı adını değiştirdikten veya veritabanını sildikten sonra, `database update` geçişleri yürütmek için komut penceresinde komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="870db-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="870db-362">`DbInitializer.Initialize`Yöntemi çalıştırmak ve yeni veritabanını doldurmak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="870db-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="870db-363">Veritabanını daha önce yaptığınız gibi SSOX içinde açın ve tabloların tümünün oluşturulduğunu görmek için **Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="870db-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="870db-364">(Yine de bir daha önceki zamanda SSOX açıksa **Yenile** düğmesine tıklayın.)</span><span class="sxs-lookup"><span data-stu-id="870db-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="870db-366">Veritabanını gösteren Başlatıcı kodunu tetiklemek için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="870db-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="870db-367">**Courseatama** tablosuna sağ tıklayın ve verileri **görüntüle** ' yi seçerek veri içerdiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="870db-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="870db-369">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="870db-369">Get the code</span></span>

[<span data-ttu-id="870db-370">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="870db-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="870db-371">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="870db-371">Next steps</span></span>

<span data-ttu-id="870db-372">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="870db-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="870db-373">Veri modeli özelleştirildi</span><span class="sxs-lookup"><span data-stu-id="870db-373">Customized the Data model</span></span>
> * <span data-ttu-id="870db-374">Öğrenci varlığında değişiklikler yapıldı</span><span class="sxs-lookup"><span data-stu-id="870db-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="870db-375">Eğitmen varlığı oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="870db-375">Created Instructor entity</span></span>
> * <span data-ttu-id="870db-376">OfficeAssignment varlığı oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="870db-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="870db-377">Değiştirilen kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="870db-377">Modified Course entity</span></span>
> * <span data-ttu-id="870db-378">Departman varlığı oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="870db-378">Created Department entity</span></span>
> * <span data-ttu-id="870db-379">Değiştirilen kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="870db-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="870db-380">Veritabanı bağlamı güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="870db-380">Updated the database context</span></span>
> * <span data-ttu-id="870db-381">Test verileriyle birlikte sağlanan veritabanı</span><span class="sxs-lookup"><span data-stu-id="870db-381">Seeded database with test data</span></span>
> * <span data-ttu-id="870db-382">Geçiş eklendi</span><span class="sxs-lookup"><span data-stu-id="870db-382">Added a migration</span></span>
> * <span data-ttu-id="870db-383">Bağlantı dizesi değiştirildi</span><span class="sxs-lookup"><span data-stu-id="870db-383">Changed the connection string</span></span>
> * <span data-ttu-id="870db-384">Veritabanı güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="870db-384">Updated the database</span></span>

<span data-ttu-id="870db-385">İlgili verilere erişme hakkında daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="870db-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="870db-386">Sonraki: ilgili verilere erişin</span><span class="sxs-lookup"><span data-stu-id="870db-386">Next: Access related data</span></span>](read-related-data.md)
