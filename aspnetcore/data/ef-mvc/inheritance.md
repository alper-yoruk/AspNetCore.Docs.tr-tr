---
title: 'Öğretici: EF Core devralma-ASP.NET MVC uygulama'
description: Bu öğretici, bir ASP.NET Core uygulamasındaki Entity Framework Core kullanarak veri modelinde devralmayı nasıl uygulayacağınızı gösterir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 4883c697e950cac298dec961b4cd5a5096d8e946
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773581"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="0fd03-103">Öğretici: EF Core devralma-ASP.NET MVC uygulama</span><span class="sxs-lookup"><span data-stu-id="0fd03-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="0fd03-104">Önceki öğreticide eşzamanlılık özel durumlarını ele alırsınız.</span><span class="sxs-lookup"><span data-stu-id="0fd03-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="0fd03-105">Bu öğretici, veri modelinde devralmayı nasıl uygulayacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="0fd03-106">Nesne odaklı programlamada, kod yeniden kullanımını kolaylaştırmak için devralmayı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="0fd03-107">Bu `Instructor` öğreticide, ve `Student` sınıflarını, hem Eğitmenler hem de öğrenciler için ortak olan gibi `Person` özellikleri `LastName` içeren bir temel sınıftan türetireceğiz şekilde değiştireceksiniz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="0fd03-108">Herhangi bir Web sayfası eklemez veya değiştirmezsiniz, ancak koddan bazılarını değiştireceksiniz ve bu değişiklikler otomatik olarak veritabanına yansıtılacaktır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="0fd03-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="0fd03-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0fd03-110">Devralmayı veritabanına eşle</span><span class="sxs-lookup"><span data-stu-id="0fd03-110">Map inheritance to database</span></span>
> * <span data-ttu-id="0fd03-111">Kişi sınıfını oluşturma</span><span class="sxs-lookup"><span data-stu-id="0fd03-111">Create the Person class</span></span>
> * <span data-ttu-id="0fd03-112">Eğitmeni ve öğrenci 'yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0fd03-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="0fd03-113">Modele kişi ekleme</span><span class="sxs-lookup"><span data-stu-id="0fd03-113">Add Person to the model</span></span>
> * <span data-ttu-id="0fd03-114">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0fd03-114">Create and update migrations</span></span>
> * <span data-ttu-id="0fd03-115">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="0fd03-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0fd03-116">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0fd03-116">Prerequisites</span></span>

* [<span data-ttu-id="0fd03-117">Eşzamanlılık işle</span><span class="sxs-lookup"><span data-stu-id="0fd03-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="0fd03-118">Devralmayı veritabanına eşle</span><span class="sxs-lookup"><span data-stu-id="0fd03-118">Map inheritance to database</span></span>

<span data-ttu-id="0fd03-119">Okul `Instructor` veri `Student` modelindeki ve sınıflarının özdeş birçok özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="0fd03-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Öğrenci ve eğitmen sınıfları](inheritance/_static/no-inheritance.png)

<span data-ttu-id="0fd03-121">`Instructor` Ve `Student` varlıkları tarafından paylaşılan özellikler için gereksiz kodu ortadan kaldırmak istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="0fd03-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="0fd03-122">Ya da adın bir eğitmenden veya bir öğrenciye ait olup olmadığına bakılmaksızın adları biçimlendirmeden bir hizmet yazmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="0fd03-123">Yalnızca bu paylaşılan özellikleri `Person` içeren bir temel sınıf oluşturabilir `Instructor` ve sonra aşağıdaki çizimde gösterildiği gibi, ve `Student` sınıflarının bu temel sınıftan devralmasını sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0fd03-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Kişi sınıfından türetilen öğrenci ve eğitmen sınıfları](inheritance/_static/inheritance.png)

<span data-ttu-id="0fd03-125">Bu devralma yapısının veritabanında temsil edilebilmesi için birkaç yol vardır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="0fd03-126">Tek bir tabloda hem öğrenciler hem de eğitmenler hakkında bilgi içeren bir kişi tablonuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="0fd03-127">Bazı sütunlar yalnızca Eğitmenler (HireDate), bazıları yalnızca öğrencilerle (kayıttarihi), bazıları ise (soyadı, adı) için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="0fd03-128">Genellikle, her bir satırın temsil ettiği türü belirtmek için bir Ayrıştırıcı sütunu vardır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="0fd03-129">Örneğin, ayrıştırıcı sütununda, Eğitmenler için "eğitmen" ve öğrenciler için "öğrenci" bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Hiyerarşi başına tablo örneği](inheritance/_static/tph.png)

<span data-ttu-id="0fd03-131">Tek bir veritabanı tablosundan bir varlık devralma yapısı oluşturmanın bu düzeni, hiyerarşi başına tablo (TPH) devralma olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="0fd03-132">Alternatif olarak, veritabanının devralma yapısına benzer bir şekilde görünmesini sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="0fd03-133">Örneğin, kişi tablosunda yalnızca ad alanları olabilir ve Tarih alanlarıyla ayrı eğitmen ve öğrenci tabloları vardır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Tablo türü devralma](inheritance/_static/tpt.png)

<span data-ttu-id="0fd03-135">Her varlık sınıfı için bir veritabanı tablosu yapmanın bu düzeni, tür başına tablo (TPT) devralma olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="0fd03-136">Başka bir seçenek de Özet olmayan tüm türleri tek tek tablolarla eşlemenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="0fd03-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="0fd03-137">Devralınan özellikler de dahil olmak üzere bir sınıfın tüm özellikleri karşılık gelen tablonun sütunlarına eşlenir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="0fd03-138">Bu düzene, tablo başına somut sınıf (TPC) devralma adı verilir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="0fd03-139">Daha önce gösterildiği gibi, kişi, öğrenci ve eğitmen sınıfları için TPC devralmayı uyguladıysanız, öğrenci ve eğitmen tabloları, devralındıktan sonra, devralma uygulandıktan sonra farklı şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="0fd03-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="0fd03-140">TPC ve TPH devralma desenleri genellikle TPT devralma desenlerinden daha iyi performans sağlar, çünkü TPT desenleri karmaşık JOIN sorgularına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="0fd03-141">Bu öğreticide, TPH devralmanın nasıl uygulanacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="0fd03-142">TPH Entity Framework Core desteklediği tek devralma modelidir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="0fd03-143">Ne `Person` yapacaklarınız bir sınıf oluşturur, ' den `Instructor` `Student` `Person`türetmek için ve sınıflarını değiştirin, yeni sınıfını öğesine `DbContext`ekleyin ve bir geçiş oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0fd03-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="0fd03-144">Aşağıdaki değişiklikleri yapmadan önce projenin bir kopyasını kaydetmeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="0fd03-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="0fd03-145">Daha sonra sorunlarla karşılaşırsanız ve baştan başlamak gerekirse, bu öğretici için yapılan adımları tersine çevirme veya tüm serinin başlangıcına geri dönme yerine kaydedilen projeden başlamak daha kolay olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="0fd03-146">Kişi sınıfını oluşturma</span><span class="sxs-lookup"><span data-stu-id="0fd03-146">Create the Person class</span></span>

<span data-ttu-id="0fd03-147">Modeller klasöründe Person.cs oluşturun ve şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0fd03-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="0fd03-148">Eğitmeni ve öğrenci 'yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0fd03-148">Update Instructor and Student</span></span>

<span data-ttu-id="0fd03-149">*Instructor.cs*' de, kişi sınıfından eğitmen sınıfını türetirsiniz ve anahtar ve ad alanlarını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0fd03-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="0fd03-150">Kod aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="0fd03-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="0fd03-151">*Student.cs*' de aynı değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="0fd03-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="0fd03-152">Modele kişi ekleme</span><span class="sxs-lookup"><span data-stu-id="0fd03-152">Add Person to the model</span></span>

<span data-ttu-id="0fd03-153">Kişi varlık türünü *SchoolContext.cs*öğesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0fd03-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="0fd03-154">Yeni satırlar vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="0fd03-155">Bu, Entity Framework hiyerarşinin devralma devralınmasını yapılandırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="0fd03-156">Gördüğünüz gibi, veritabanı güncelleştirildiği sırada öğrenci ve eğitmen tablolarının yerine bir kişi tablosu olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="0fd03-157">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0fd03-157">Create and update migrations</span></span>

<span data-ttu-id="0fd03-158">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="0fd03-158">Save your changes and build the project.</span></span> <span data-ttu-id="0fd03-159">Ardından proje klasöründe komut penceresini açın ve aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="0fd03-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="0fd03-160">`database update` Komutu henüz çalıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="0fd03-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="0fd03-161">Bu komut, eğitmen tablosunu bırakacak ve öğrenci tablosunu kişi olarak yeniden adlandırdığı için kayıp veri oluşmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="0fd03-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="0fd03-162">Varolan verileri korumak için özel kod sağlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="0fd03-163">*Geçişleri/\<zaman damgasını>_Inheritance. cs* açın ve `Up` yöntemi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0fd03-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="0fd03-164">Bu kod aşağıdaki veritabanı güncelleştirme görevlerini gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="0fd03-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="0fd03-165">Yabancı anahtar kısıtlamalarını ve öğrenci tablosuna işaret eden dizinleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="0fd03-166">Eğitmen tablosunu kişi olarak yeniden adlandırır ve öğrenci verilerini depolamak için gerekli değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="0fd03-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="0fd03-167">Öğrenciler için Nullable kayıt tarihi ekler.</span><span class="sxs-lookup"><span data-stu-id="0fd03-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="0fd03-168">Bir satırın bir öğrenci mi yoksa bir eğitmen mi olduğunu göstermek için ayrıştırıcı sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="0fd03-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="0fd03-169">Öğrenci satırlarında işe alma tarihleri olmadığından, HireDate null yapılabilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="0fd03-170">Öğrencilere işaret eden yabancı anahtarları güncelleştirmek için kullanılacak geçici bir alan ekler.</span><span class="sxs-lookup"><span data-stu-id="0fd03-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="0fd03-171">Öğrencileri kişi tablosuna kopyaladığınızda, yeni birincil anahtar değerleri alırlar.</span><span class="sxs-lookup"><span data-stu-id="0fd03-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="0fd03-172">Öğrenci tablosundaki verileri kişi tablosuna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="0fd03-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="0fd03-173">Bu, öğrencilerden yeni birincil anahtar değerleri atanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0fd03-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="0fd03-174">Öğrencilere işaret eden yabancı anahtar değerlerini düzeltir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="0fd03-175">Yabancı anahtar kısıtlamalarını ve dizinleri yeniden oluşturur, şimdi bunları kişi tablosuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="0fd03-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="0fd03-176">(Birincil anahtar türü olarak tamsayı yerine GUID kullandıysanız, öğrenci birincil anahtar değerlerinin değiştirilmesi gerekmez ve bu adımların bazıları atlanamaz.)</span><span class="sxs-lookup"><span data-stu-id="0fd03-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="0fd03-177">Şu `database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0fd03-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0fd03-178">(Bir üretim sisteminde, önceki veritabanı sürümüne geri dönmek için bunu `Down` kullanmanız durumunda bu yöntemde ilgili değişiklikleri yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0fd03-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="0fd03-179">Bu öğreticide, `Down` yöntemini kullanmayacağız.)</span><span class="sxs-lookup"><span data-stu-id="0fd03-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="0fd03-180">Varolan verileri içeren bir veritabanında şema değişiklikleri yaparken başka hatalar almak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="0fd03-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="0fd03-181">Çözemiyoruz geçiş hataları alırsanız, bağlantı dizesindeki veritabanı adını değiştirebilir veya veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="0fd03-182">Yeni bir veritabanı ile geçirilecek veri yoktur ve Update-Database komutunun hatasız tamamlanabilmesi daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="0fd03-183">Veritabanını silmek için, SSOX kullanın veya `database drop` CLI komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0fd03-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="0fd03-184">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="0fd03-184">Test the implementation</span></span>

<span data-ttu-id="0fd03-185">Uygulamayı çalıştırın ve çeşitli sayfaları deneyin.</span><span class="sxs-lookup"><span data-stu-id="0fd03-185">Run the app and try various pages.</span></span> <span data-ttu-id="0fd03-186">Her şey, daha önce olduğu gibi çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0fd03-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="0fd03-187">**SQL Server Nesne Gezgini**, **veri bağlantıları/SchoolContext** ve ardından **Tablolar**' ı genişletin ve öğrenci ve eğitmen tablolarının bir kişi tablosu ile değiştirildiğini görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="0fd03-188">Kişi tablosu tasarımcısını açın ve öğrencinin ve eğitmen tablolarında kullanılan tüm sütunları olduğunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="0fd03-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![SSOX 'teki kişi tablosu](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="0fd03-190">Kişi tablosuna sağ tıklayın ve ardından **tablo verilerini göster** ' e tıklayarak ayrıştırıcı sütununu görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="0fd03-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![SSOX tablo verilerinde kişi tablosu](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="0fd03-192">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="0fd03-192">Get the code</span></span>

[<span data-ttu-id="0fd03-193">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="0fd03-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="0fd03-194">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0fd03-194">Additional resources</span></span>

<span data-ttu-id="0fd03-195">Entity Framework Core devralma hakkında daha fazla bilgi için bkz. [Devralma](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="0fd03-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0fd03-196">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="0fd03-196">Next steps</span></span>

<span data-ttu-id="0fd03-197">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="0fd03-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0fd03-198">Veritabanına devralma eşlenmiş</span><span class="sxs-lookup"><span data-stu-id="0fd03-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="0fd03-199">Kişi sınıfı oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="0fd03-199">Created the Person class</span></span>
> * <span data-ttu-id="0fd03-200">Güncelleştirilmiş eğitmen ve öğrenci</span><span class="sxs-lookup"><span data-stu-id="0fd03-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="0fd03-201">Modele kişi eklendi</span><span class="sxs-lookup"><span data-stu-id="0fd03-201">Added Person to the model</span></span>
> * <span data-ttu-id="0fd03-202">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0fd03-202">Created and update migrations</span></span>
> * <span data-ttu-id="0fd03-203">Uygulama test edildi</span><span class="sxs-lookup"><span data-stu-id="0fd03-203">Tested the implementation</span></span>

<span data-ttu-id="0fd03-204">Çeşitli görece gelişmiş Entity Framework senaryolarını nasıl işleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="0fd03-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0fd03-205">İleri: gelişmiş konular</span><span class="sxs-lookup"><span data-stu-id="0fd03-205">Next: Advanced topics</span></span>](advanced.md)
