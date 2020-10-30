---
title: 'Öğretici: EF Core devralma-ASP.NET MVC uygulama'
description: Bu öğretici, bir ASP.NET Core uygulamasındaki Entity Framework Core kullanarak veri modelinde devralmayı nasıl uygulayacağınızı gösterir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 581a31bad4069523699fbbac63862c9dff12034d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054222"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="db18b-103">Öğretici: EF Core devralma-ASP.NET MVC uygulama</span><span class="sxs-lookup"><span data-stu-id="db18b-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="db18b-104">Önceki öğreticide eşzamanlılık özel durumlarını ele alırsınız.</span><span class="sxs-lookup"><span data-stu-id="db18b-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="db18b-105">Bu öğretici, veri modelinde devralmayı nasıl uygulayacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="db18b-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="db18b-106">Nesne odaklı programlamada, kod yeniden kullanımını kolaylaştırmak için devralmayı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db18b-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="db18b-107">Bu öğreticide, `Instructor` ve `Student` sınıflarını, `Person` `LastName` hem Eğitmenler hem de öğrenciler için ortak olan gibi özellikleri içeren bir temel sınıftan türetireceğiz şekilde değiştireceksiniz.</span><span class="sxs-lookup"><span data-stu-id="db18b-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="db18b-108">Herhangi bir Web sayfası eklemez veya değiştirmezsiniz, ancak koddan bazılarını değiştireceksiniz ve bu değişiklikler otomatik olarak veritabanına yansıtılacaktır.</span><span class="sxs-lookup"><span data-stu-id="db18b-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="db18b-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="db18b-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="db18b-110">Devralmayı veritabanına eşle</span><span class="sxs-lookup"><span data-stu-id="db18b-110">Map inheritance to database</span></span>
> * <span data-ttu-id="db18b-111">Kişi sınıfını oluşturma</span><span class="sxs-lookup"><span data-stu-id="db18b-111">Create the Person class</span></span>
> * <span data-ttu-id="db18b-112">Eğitmeni ve öğrenci 'yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="db18b-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="db18b-113">Modele kişi ekleme</span><span class="sxs-lookup"><span data-stu-id="db18b-113">Add Person to the model</span></span>
> * <span data-ttu-id="db18b-114">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="db18b-114">Create and update migrations</span></span>
> * <span data-ttu-id="db18b-115">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="db18b-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="db18b-116">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="db18b-116">Prerequisites</span></span>

* [<span data-ttu-id="db18b-117">Eşzamanlılık işle</span><span class="sxs-lookup"><span data-stu-id="db18b-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="db18b-118">Devralmayı veritabanına eşle</span><span class="sxs-lookup"><span data-stu-id="db18b-118">Map inheritance to database</span></span>

<span data-ttu-id="db18b-119">`Instructor` `Student` Okul veri modelindeki ve sınıflarının özdeş birçok özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="db18b-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Öğrenci ve eğitmen sınıfları](inheritance/_static/no-inheritance.png)

<span data-ttu-id="db18b-121">Ve varlıkları tarafından paylaşılan özellikler için gereksiz kodu ortadan kaldırmak istediğinizi varsayalım `Instructor` `Student` .</span><span class="sxs-lookup"><span data-stu-id="db18b-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="db18b-122">Ya da adın bir eğitmenden veya bir öğrenciye ait olup olmadığına bakılmaksızın adları biçimlendirmeden bir hizmet yazmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db18b-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="db18b-123">`Person`Yalnızca bu paylaşılan özellikleri içeren bir temel sınıf oluşturabilir `Instructor` ve sonra `Student` Aşağıdaki çizimde gösterildiği gibi, ve sınıflarının bu temel sınıftan devralmasını sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="db18b-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Kişi sınıfından türetilen öğrenci ve eğitmen sınıfları](inheritance/_static/inheritance.png)

<span data-ttu-id="db18b-125">Bu devralma yapısının veritabanında temsil edilebilmesi için birkaç yol vardır.</span><span class="sxs-lookup"><span data-stu-id="db18b-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="db18b-126">Tek bir tabloda hem öğrenciler hem de eğitmenler hakkında bilgi içeren bir kişi tablonuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="db18b-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="db18b-127">Bazı sütunlar yalnızca Eğitmenler (HireDate), bazıları yalnızca öğrencilerle (kayıttarihi), bazıları ise (soyadı, adı) için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="db18b-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="db18b-128">Genellikle, her bir satırın temsil ettiği türü belirtmek için bir Ayrıştırıcı sütunu vardır.</span><span class="sxs-lookup"><span data-stu-id="db18b-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="db18b-129">Örneğin, ayrıştırıcı sütununda, Eğitmenler için "eğitmen" ve öğrenciler için "öğrenci" bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="db18b-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Hiyerarşi başına tablo örneği](inheritance/_static/tph.png)

<span data-ttu-id="db18b-131">Tek bir veritabanı tablosundan bir varlık devralma yapısı oluşturmanın bu düzeni, hiyerarşi başına tablo (TPH) devralma olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="db18b-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="db18b-132">Alternatif olarak, veritabanının devralma yapısına benzer bir şekilde görünmesini sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db18b-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="db18b-133">Örneğin, kişi tablosunda yalnızca ad alanları olabilir ve Tarih alanlarıyla ayrı eğitmen ve öğrenci tabloları vardır.</span><span class="sxs-lookup"><span data-stu-id="db18b-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

> [!WARNING]
> <span data-ttu-id="db18b-134">Tür başına tablo (TPT) EF Core 3. x tarafından desteklenmez, ancak bu [EF Core 5,0](/ef/core/what-is-new/ef-core-5.0/plan)' de uygulanmıştır.</span><span class="sxs-lookup"><span data-stu-id="db18b-134">Table Per Type (TPT) is not supported by EF Core 3.x, however it is has been implemented in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan).</span></span>

![Tablo türü devralma](inheritance/_static/tpt.png)

<span data-ttu-id="db18b-136">Her varlık sınıfı için bir veritabanı tablosu yapmanın bu düzeni, tür başına tablo (TPT) devralma olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="db18b-136">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="db18b-137">Başka bir seçenek de Özet olmayan tüm türleri tek tek tablolarla eşlemenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="db18b-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="db18b-138">Devralınan özellikler de dahil olmak üzere bir sınıfın tüm özellikleri karşılık gelen tablonun sütunlarına eşlenir.</span><span class="sxs-lookup"><span data-stu-id="db18b-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="db18b-139">Bu düzene, tablo başına somut sınıf (TPC) devralma adı verilir.</span><span class="sxs-lookup"><span data-stu-id="db18b-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="db18b-140">Daha önce gösterildiği gibi, kişi, öğrenci ve eğitmen sınıfları için TPC devralmayı uyguladıysanız, öğrenci ve eğitmen tabloları, devralındıktan sonra, devralma uygulandıktan sonra farklı şekilde görünür.</span><span class="sxs-lookup"><span data-stu-id="db18b-140">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="db18b-141">TPC ve TPH devralma desenleri genellikle TPT devralma desenlerinden daha iyi performans sağlar, çünkü TPT desenleri karmaşık JOIN sorgularına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="db18b-141">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="db18b-142">Bu öğreticide, TPH devralmanın nasıl uygulanacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="db18b-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="db18b-143">TPH Entity Framework Core desteklediği tek devralma modelidir.</span><span class="sxs-lookup"><span data-stu-id="db18b-143">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="db18b-144">Ne yapacaklarınız bir sınıf oluşturur `Person` , ' `Instructor` `Student` den türetmek için ve sınıflarını değiştirin `Person` , yeni sınıfını öğesine ekleyin `DbContext` ve bir geçiş oluşturun.</span><span class="sxs-lookup"><span data-stu-id="db18b-144">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="db18b-145">Aşağıdaki değişiklikleri yapmadan önce projenin bir kopyasını kaydetmeyi göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="db18b-145">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="db18b-146">Daha sonra sorunlarla karşılaşırsanız ve baştan başlamak gerekirse, bu öğretici için yapılan adımları tersine çevirme veya tüm serinin başlangıcına geri dönme yerine kaydedilen projeden başlamak daha kolay olacaktır.</span><span class="sxs-lookup"><span data-stu-id="db18b-146">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="db18b-147">Kişi sınıfını oluşturma</span><span class="sxs-lookup"><span data-stu-id="db18b-147">Create the Person class</span></span>

<span data-ttu-id="db18b-148">Modeller klasöründe Person.cs oluşturun ve şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="db18b-148">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="db18b-149">Eğitmeni ve öğrenci 'yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="db18b-149">Update Instructor and Student</span></span>

<span data-ttu-id="db18b-150">*Instructor.cs* ' de, kişi sınıfından eğitmen sınıfını türetirsiniz ve anahtar ve ad alanlarını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="db18b-150">In *Instructor.cs* , derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="db18b-151">Kod aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="db18b-151">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="db18b-152">*Student.cs* ' de aynı değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="db18b-152">Make the same changes in *Student.cs* .</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="db18b-153">Modele kişi ekleme</span><span class="sxs-lookup"><span data-stu-id="db18b-153">Add Person to the model</span></span>

<span data-ttu-id="db18b-154">Kişi varlık türünü *SchoolContext.cs* öğesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="db18b-154">Add the Person entity type to *SchoolContext.cs* .</span></span> <span data-ttu-id="db18b-155">Yeni satırlar vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="db18b-155">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="db18b-156">Bu, Entity Framework hiyerarşinin devralma devralınmasını yapılandırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="db18b-156">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="db18b-157">Gördüğünüz gibi, veritabanı güncelleştirildiği sırada öğrenci ve eğitmen tablolarının yerine bir kişi tablosu olacaktır.</span><span class="sxs-lookup"><span data-stu-id="db18b-157">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="db18b-158">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="db18b-158">Create and update migrations</span></span>

<span data-ttu-id="db18b-159">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="db18b-159">Save your changes and build the project.</span></span> <span data-ttu-id="db18b-160">Ardından proje klasöründe komut penceresini açın ve aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="db18b-160">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="db18b-161">`database update`Komutu henüz çalıştırmayın.</span><span class="sxs-lookup"><span data-stu-id="db18b-161">Don't run the `database update` command yet.</span></span> <span data-ttu-id="db18b-162">Bu komut, eğitmen tablosunu bırakacak ve öğrenci tablosunu kişi olarak yeniden adlandırdığı için kayıp veri oluşmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="db18b-162">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="db18b-163">Varolan verileri korumak için özel kod sağlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="db18b-163">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="db18b-164">*Geçişler/ \<timestamp> _Inheritance. cs* ' i açın ve `Up` yöntemi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="db18b-164">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="db18b-165">Bu kod aşağıdaki veritabanı güncelleştirme görevlerini gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="db18b-165">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="db18b-166">Yabancı anahtar kısıtlamalarını ve öğrenci tablosuna işaret eden dizinleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="db18b-166">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="db18b-167">Eğitmen tablosunu kişi olarak yeniden adlandırır ve öğrenci verilerini depolamak için gerekli değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="db18b-167">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="db18b-168">Öğrenciler için Nullable kayıt tarihi ekler.</span><span class="sxs-lookup"><span data-stu-id="db18b-168">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="db18b-169">Bir satırın bir öğrenci mi yoksa bir eğitmen mi olduğunu göstermek için ayrıştırıcı sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="db18b-169">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="db18b-170">Öğrenci satırlarında işe alma tarihleri olmadığından, HireDate null yapılabilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="db18b-170">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="db18b-171">Öğrencilere işaret eden yabancı anahtarları güncelleştirmek için kullanılacak geçici bir alan ekler.</span><span class="sxs-lookup"><span data-stu-id="db18b-171">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="db18b-172">Öğrencileri kişi tablosuna kopyaladığınızda, yeni birincil anahtar değerleri alırlar.</span><span class="sxs-lookup"><span data-stu-id="db18b-172">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="db18b-173">Öğrenci tablosundaki verileri kişi tablosuna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="db18b-173">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="db18b-174">Bu, öğrencilerden yeni birincil anahtar değerleri atanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="db18b-174">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="db18b-175">Öğrencilere işaret eden yabancı anahtar değerlerini düzeltir.</span><span class="sxs-lookup"><span data-stu-id="db18b-175">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="db18b-176">Yabancı anahtar kısıtlamalarını ve dizinleri yeniden oluşturur, şimdi bunları kişi tablosuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="db18b-176">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="db18b-177">(Birincil anahtar türü olarak tamsayı yerine GUID kullandıysanız, öğrenci birincil anahtar değerlerinin değiştirilmesi gerekmez ve bu adımların bazıları atlanamaz.)</span><span class="sxs-lookup"><span data-stu-id="db18b-177">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="db18b-178">Şu `database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="db18b-178">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="db18b-179">(Bir üretim sisteminde, `Down` önceki veritabanı sürümüne geri dönmek için bunu kullanmanız durumunda bu yöntemde ilgili değişiklikleri yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="db18b-179">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="db18b-180">Bu öğreticide, yöntemini kullanmayacağız `Down` .)</span><span class="sxs-lookup"><span data-stu-id="db18b-180">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="db18b-181">Varolan verileri içeren bir veritabanında şema değişiklikleri yaparken başka hatalar almak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="db18b-181">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="db18b-182">Çözemiyoruz geçiş hataları alırsanız, bağlantı dizesindeki veritabanı adını değiştirebilir veya veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db18b-182">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="db18b-183">Yeni bir veritabanı ile geçirilecek veri yoktur ve Update-Database komutunun hatasız tamamlanabilmesi daha olasıdır.</span><span class="sxs-lookup"><span data-stu-id="db18b-183">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="db18b-184">Veritabanını silmek için, SSOX kullanın veya `database drop` CLI komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="db18b-184">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="db18b-185">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="db18b-185">Test the implementation</span></span>

<span data-ttu-id="db18b-186">Uygulamayı çalıştırın ve çeşitli sayfaları deneyin.</span><span class="sxs-lookup"><span data-stu-id="db18b-186">Run the app and try various pages.</span></span> <span data-ttu-id="db18b-187">Her şey, daha önce olduğu gibi çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="db18b-187">Everything works the same as it did before.</span></span>

<span data-ttu-id="db18b-188">**SQL Server Nesne Gezgini** , **veri bağlantıları/SchoolContext** ve ardından **Tablolar** ' ı genişletin ve öğrenci ve eğitmen tablolarının bir kişi tablosu ile değiştirildiğini görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="db18b-188">In **SQL Server Object Explorer** , expand **Data Connections/SchoolContext** and then **Tables** , and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="db18b-189">Kişi tablosu tasarımcısını açın ve öğrencinin ve eğitmen tablolarında kullanılan tüm sütunları olduğunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="db18b-189">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![SSOX 'teki kişi tablosu](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="db18b-191">Kişi tablosuna sağ tıklayın ve ardından **tablo verilerini göster** ' e tıklayarak ayrıştırıcı sütununu görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="db18b-191">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![SSOX tablo verilerinde kişi tablosu](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="db18b-193">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="db18b-193">Get the code</span></span>

[<span data-ttu-id="db18b-194">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="db18b-194">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="db18b-195">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="db18b-195">Additional resources</span></span>

<span data-ttu-id="db18b-196">Entity Framework Core devralma hakkında daha fazla bilgi için bkz. [Devralma](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="db18b-196">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="db18b-197">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="db18b-197">Next steps</span></span>

<span data-ttu-id="db18b-198">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="db18b-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="db18b-199">Veritabanına devralma eşlenmiş</span><span class="sxs-lookup"><span data-stu-id="db18b-199">Mapped inheritance to database</span></span>
> * <span data-ttu-id="db18b-200">Kişi sınıfı oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="db18b-200">Created the Person class</span></span>
> * <span data-ttu-id="db18b-201">Güncelleştirilmiş eğitmen ve öğrenci</span><span class="sxs-lookup"><span data-stu-id="db18b-201">Updated Instructor and Student</span></span>
> * <span data-ttu-id="db18b-202">Modele kişi eklendi</span><span class="sxs-lookup"><span data-stu-id="db18b-202">Added Person to the model</span></span>
> * <span data-ttu-id="db18b-203">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="db18b-203">Created and update migrations</span></span>
> * <span data-ttu-id="db18b-204">Uygulama test edildi</span><span class="sxs-lookup"><span data-stu-id="db18b-204">Tested the implementation</span></span>

<span data-ttu-id="db18b-205">Çeşitli görece gelişmiş Entity Framework senaryolarını nasıl işleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="db18b-205">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="db18b-206">İleri: gelişmiş konular</span><span class="sxs-lookup"><span data-stu-id="db18b-206">Next: Advanced topics</span></span>](advanced.md)