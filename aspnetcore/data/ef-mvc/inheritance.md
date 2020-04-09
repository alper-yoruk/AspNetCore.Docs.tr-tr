---
title: 'Öğretici: Devralma uygulayın - EF Core ile MVC ASP.NET'
description: Bu öğretici, ASP.NET Core uygulamasında Entity Framework Core'u kullanarak veri modelinde devralmayı nasıl uygulayacağınızı gösterecektir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657243"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="8bd56-103">Öğretici: Devralma uygulayın - EF Core ile MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="8bd56-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="8bd56-104">Önceki öğreticide, eşzamanlılık özel durumlarını işlettiniz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="8bd56-105">Bu öğretici, veri modelinde devralmayı nasıl uygulayacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="8bd56-106">Nesne yönelimli programlamada, kodun yeniden kullanımını kolaylaştırmak için devralmayı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="8bd56-107">Bu öğreticide, sınıfları `Instructor` ve `Student` sınıfları, hem eğitmenler `Person` hem de öğrenciler `LastName` için ortak olan özellikler içeren bir taban sınıftan türeecek şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="8bd56-108">Web sayfaları eklemez veya değiştirmezsiniz, ancak kodun bir kısmını değiştirirsiniz ve bu değişiklikler otomatik olarak veritabanına yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="8bd56-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="8bd56-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8bd56-110">Veritabanına harita kalıbı</span><span class="sxs-lookup"><span data-stu-id="8bd56-110">Map inheritance to database</span></span>
> * <span data-ttu-id="8bd56-111">Kişi sınıfını oluşturma</span><span class="sxs-lookup"><span data-stu-id="8bd56-111">Create the Person class</span></span>
> * <span data-ttu-id="8bd56-112">Eğitmen ve Öğrenciyi Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="8bd56-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="8bd56-113">Modele Kişi Ekle</span><span class="sxs-lookup"><span data-stu-id="8bd56-113">Add Person to the model</span></span>
> * <span data-ttu-id="8bd56-114">Geçişler oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8bd56-114">Create and update migrations</span></span>
> * <span data-ttu-id="8bd56-115">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="8bd56-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8bd56-116">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8bd56-116">Prerequisites</span></span>

* [<span data-ttu-id="8bd56-117">Eşzamanlılığı Tut</span><span class="sxs-lookup"><span data-stu-id="8bd56-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="8bd56-118">Veritabanına harita kalıbı</span><span class="sxs-lookup"><span data-stu-id="8bd56-118">Map inheritance to database</span></span>

<span data-ttu-id="8bd56-119">Okul `Instructor` `Student` veri modelindeki ve sınıfların aynı olan birkaç özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="8bd56-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Öğrenci ve Eğitmen sınıfları](inheritance/_static/no-inheritance.png)

<span data-ttu-id="8bd56-121">Varlıklar `Instructor` ve `Student` varlıklar tarafından paylaşılan özellikler için gereksiz kodu ortadan kaldırmak istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="8bd56-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="8bd56-122">Ya da adın bir eğitmenden mi yoksa bir öğrenciden mi geldiğini umursamadan isimleri biçimlendirebilen bir hizmet yazmak istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="8bd56-123">Yalnızca paylaşılan `Person` özellikleri içeren bir taban sınıf oluşturabilir, `Instructor` `Student` ardından aşağıdaki resimde gösterildiği gibi, bu taban sınıftan ve sınıflardan devralabilir:</span><span class="sxs-lookup"><span data-stu-id="8bd56-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Kişi sınıfından kaynaklanan öğrenci ve eğitmen sınıfları](inheritance/_static/inheritance.png)

<span data-ttu-id="8bd56-125">Bu devralma yapısıveritabanında temsil edilebilir çeşitli yolları vardır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="8bd56-126">Tek bir tabloda hem öğrenciler hem de eğitmenler hakkında bilgi içeren bir Kişi tablosu na sahip olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="8bd56-127">Sütunlardan bazıları yalnızca eğitmenler (HireDate), bazıları sadece öğrenciler (EnrollmentDate), bazıları her ikisi için de (Soyadı, İlkAd) için geçerli olabilir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="8bd56-128">Genellikle, her satırın hangi türü temsil ettiğini belirtmek için bir ayrımcı sütununuzun olur.</span><span class="sxs-lookup"><span data-stu-id="8bd56-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="8bd56-129">Örneğin, ayrımcı sütunda eğitmenler için "Eğitmen" ve öğrenciler için "Öğrenci" olabilir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Tablo başına hiyerarşi örneği](inheritance/_static/tph.png)

<span data-ttu-id="8bd56-131">Tek bir veritabanı tablosundan varlık kalıtım yapısı oluşturma bu desen tablo başına hiyerarşi (TPH) kalıtım denir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="8bd56-132">Alternatif veritabanı devralma yapısı gibi görünmesini sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="8bd56-133">Örneğin, yalnızca Kişi tablosundaki ad alanlarına sahip olabilir ve tarih alanlarıyla birlikte ayrı Eğitmen ve Öğrenci tablolarınız olabilir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Tablo başına tür devralma](inheritance/_static/tpt.png)

<span data-ttu-id="8bd56-135">Her varlık sınıfı için bir veritabanı tablosu yapma bu desen tür başına tablo (TPT) kalıtım denir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="8bd56-136">Ancak başka bir seçenek tek tek tablolar için tüm soyut olmayan türleri haritalamaktır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="8bd56-137">Devralınan özellikler de dahil olmak üzere bir sınıfın tüm özellikleri, ilgili tablonun sütunlarına eşlenir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="8bd56-138">Bu desen, Beton Başına Tablo Sınıfı (TPC) kalıtım olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="8bd56-139">Daha önce gösterildiği gibi Kişi, Öğrenci ve Eğitmen sınıfları için TPC kalıtım ını uygularsanız, Öğrenci ve Eğitmen tabloları, kalıtım uygulandıktan sonra öncekinden farklı görünmeyecek.</span><span class="sxs-lookup"><span data-stu-id="8bd56-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="8bd56-140">TPT desenleri karmaşık birleştirme sorgularına neden olabileceğinden, TPC ve TPH kalıtım desenleri genellikle TPT kalıtım desenlerinden daha iyi performans sağlar.</span><span class="sxs-lookup"><span data-stu-id="8bd56-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="8bd56-141">Bu öğretici, TPH kalıtım nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="8bd56-142">TPH, Varlık Çerçeve Çekirdeği'nin desteklediği tek kalıtım desenidir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="8bd56-143">Yapacağınız şey bir `Person` sınıf oluşturmak, `Instructor` sınıfları ve `Student` sınıfları değiştirmek, `Person`yeni `DbContext`sınıfı eklemek ve bir geçiş oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="8bd56-144">Aşağıdaki değişiklikleri yapmadan önce projenin bir kopyasını kaydetmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="8bd56-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="8bd56-145">Sonra sorunlarla karşınıza çıkmak ve baştan başlamak gerekiyorsa, bu öğretici için yapılan adımları tersine çevirmek veya tüm serinin başına geri dönmek yerine kaydedilen projeden başlamak daha kolay olacaktır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="8bd56-146">Kişi sınıfını oluşturma</span><span class="sxs-lookup"><span data-stu-id="8bd56-146">Create the Person class</span></span>

<span data-ttu-id="8bd56-147">Modeller klasöründe, Person.cs oluşturun ve şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="8bd56-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="8bd56-148">Eğitmen ve Öğrenciyi Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="8bd56-148">Update Instructor and Student</span></span>

<span data-ttu-id="8bd56-149">*Instructor.cs,* Eğitmen sınıfını Kişi sınıfından türetin ve anahtar ve ad alanlarını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="8bd56-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="8bd56-150">Kod aşağıdaki örnek gibi görünecektir:</span><span class="sxs-lookup"><span data-stu-id="8bd56-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="8bd56-151">*Student.cs*aynı değişiklikleri yapın.</span><span class="sxs-lookup"><span data-stu-id="8bd56-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="8bd56-152">Modele Kişi Ekle</span><span class="sxs-lookup"><span data-stu-id="8bd56-152">Add Person to the model</span></span>

<span data-ttu-id="8bd56-153">*SchoolContext.cs'a*Kişi varlık türünü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8bd56-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="8bd56-154">Yeni satırlar vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="8bd56-155">Tablo başına hiyerarşi kalıtımını yapılandırmak için Varlık Çerçevesi'nin ihtiyacı olan tek şey budur.</span><span class="sxs-lookup"><span data-stu-id="8bd56-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="8bd56-156">Göreceğiniz gibi, veritabanı güncelleştirildiğinde, Öğrenci ve Eğitmen tablolarının yerine bir Kişi tablosu olacaktır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="8bd56-157">Geçişler oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8bd56-157">Create and update migrations</span></span>

<span data-ttu-id="8bd56-158">Değişikliklerinizi kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8bd56-158">Save your changes and build the project.</span></span> <span data-ttu-id="8bd56-159">Ardından proje klasöründeki komut penceresini açın ve aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="8bd56-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="8bd56-160">Henüz komutu `database update` çalıştırma.</span><span class="sxs-lookup"><span data-stu-id="8bd56-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="8bd56-161">Bu komut, Instructor tablosunu düşüreceği ve Öğrenci tablosunu Kişiye yeniden adlandıracağı için verilerin kaybolmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="8bd56-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="8bd56-162">Varolan verileri korumak için özel kod sağlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="8bd56-163">*Geçişleri/\<zaman damgasını>_Inheritance.cs* açın ve yöntemi aşağıdaki kodla değiştirin: `Up`</span><span class="sxs-lookup"><span data-stu-id="8bd56-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="8bd56-164">Bu kod, aşağıdaki veritabanı güncelleştirme görevleri ilgilenir:</span><span class="sxs-lookup"><span data-stu-id="8bd56-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="8bd56-165">Öğrenci tablosuna işaret eden yabancı anahtar kısıtlamalarını ve dizinleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="8bd56-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="8bd56-166">Eğitmen tablosunu Kişi olarak yeniden adlandırır ve Öğrenci verilerini depolaması için gerekli değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="8bd56-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="8bd56-167">Öğrenciler için geçersiz kayıt tarihi ekler.</span><span class="sxs-lookup"><span data-stu-id="8bd56-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="8bd56-168">Bir satırın öğrenci veya eğitmen için olup olmadığını belirtmek için Ayrımcı sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="8bd56-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="8bd56-169">Öğrenci satırları işe alma tarihleri olmayacağından HireDate'i geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="8bd56-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="8bd56-170">Öğrencilere işaret eden yabancı anahtarları güncelleştirmek için kullanılacak geçici bir alan ekler.</span><span class="sxs-lookup"><span data-stu-id="8bd56-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="8bd56-171">Öğrencileri Kişi tablosuna kopyaladiğinizde yeni temel anahtar değerleri elde ederler.</span><span class="sxs-lookup"><span data-stu-id="8bd56-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="8bd56-172">Öğrenci tablosundaki verileri Kişi tablosuna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="8bd56-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="8bd56-173">Bu, öğrencilerin yeni birincil anahtar değerleri atanmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="8bd56-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="8bd56-174">Öğrencileri işaret eden yabancı anahtar değerlerini düzeltir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="8bd56-175">Yabancı anahtar kısıtlamalarını ve dizinlerini yeniden oluşturur ve şimdi bunları Kişi tablosuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="8bd56-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="8bd56-176">(Birincil anahtar türü olarak tamsayı yerine GUID kullansaydınız, öğrenci birincil anahtar değerlerinin değişmesi gerekmezdi ve bu adımlardan birkaçı atlanmış olabilir.)</span><span class="sxs-lookup"><span data-stu-id="8bd56-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="8bd56-177">Komutu `database update` çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8bd56-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="8bd56-178">(Bir üretim sisteminde, önceki veritabanı `Down` sürümüne geri dönmek için bunu kullanmanız gerekme ihtimaline karşı yöntemde karşılık gelen değişiklikler yaparsınız.</span><span class="sxs-lookup"><span data-stu-id="8bd56-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="8bd56-179">Bu öğretici için `Down` yöntemi kullanmayacaksınız.)</span><span class="sxs-lookup"><span data-stu-id="8bd56-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="8bd56-180">Varolan verilere sahip bir veritabanında şema değişiklikleri yaparken başka hatalar da almak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="8bd56-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="8bd56-181">Çözemediğiniz geçiş hataları alırsanız, bağlantı dizesinde veritabanı adını değiştirebilir veya veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="8bd56-182">Yeni bir veritabanında, geçirilen veri yoktur ve güncelleştirme veritabanı komutu hatasız olarak tamamlanma olasılığı daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="8bd56-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="8bd56-183">Veritabanını silmek için SSOX'ı kullanın veya CLI komutunu çalıştırın. `database drop`</span><span class="sxs-lookup"><span data-stu-id="8bd56-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="8bd56-184">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="8bd56-184">Test the implementation</span></span>

<span data-ttu-id="8bd56-185">Uygulamayı çalıştırın ve çeşitli sayfaları deneyin.</span><span class="sxs-lookup"><span data-stu-id="8bd56-185">Run the app and try various pages.</span></span> <span data-ttu-id="8bd56-186">Her şey eskisi gibi çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="8bd56-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="8bd56-187">**SQL Server Object Explorer'da**Veri **Bağlantılarını/SchoolContext'ı** ve ardından **Tabloları**genişletin ve Öğrenci ve Eğitmen tablolarının Bir Kişi tablosuyla değiştirildiğini görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="8bd56-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="8bd56-188">Kişi tablosu tasarımcısını açın ve öğrenci ve eğitmen tablolarında kullanılan tüm sütunlara sahip olduğunu görün.</span><span class="sxs-lookup"><span data-stu-id="8bd56-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![SSOX'ta kişi tablosu](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="8bd56-190">Kişi tablosunu sağ tıklatın ve sonra ayırıcı sütunu görmek için **Tablo Verilerini Göster'i** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="8bd56-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![SSOX'ta kişi tablosu - tablo verileri](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="8bd56-192">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="8bd56-192">Get the code</span></span>

[<span data-ttu-id="8bd56-193">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="8bd56-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="8bd56-194">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8bd56-194">Additional resources</span></span>

<span data-ttu-id="8bd56-195">Entity Framework Core'da devralma hakkında daha fazla bilgi için [bkz.](/ef/core/modeling/inheritance)</span><span class="sxs-lookup"><span data-stu-id="8bd56-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="8bd56-196">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="8bd56-196">Next steps</span></span>

<span data-ttu-id="8bd56-197">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="8bd56-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8bd56-198">Veritabanına eşlenen devralma</span><span class="sxs-lookup"><span data-stu-id="8bd56-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="8bd56-199">Kişi sınıfı oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="8bd56-199">Created the Person class</span></span>
> * <span data-ttu-id="8bd56-200">Güncellenen Eğitmen ve Öğrenci</span><span class="sxs-lookup"><span data-stu-id="8bd56-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="8bd56-201">Modele Kişi Eklendi</span><span class="sxs-lookup"><span data-stu-id="8bd56-201">Added Person to the model</span></span>
> * <span data-ttu-id="8bd56-202">Geçişleri oluşturma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8bd56-202">Created and update migrations</span></span>
> * <span data-ttu-id="8bd56-203">Uygulama test edildi</span><span class="sxs-lookup"><span data-stu-id="8bd56-203">Tested the implementation</span></span>

<span data-ttu-id="8bd56-204">Göreceli olarak gelişmiş Varlık Çerçevesi senaryolarının çeşitli işlemek için öğrenmek için bir sonraki öğretici için ilerlemek.</span><span class="sxs-lookup"><span data-stu-id="8bd56-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8bd56-205">Sonraki: Gelişmiş konular</span><span class="sxs-lookup"><span data-stu-id="8bd56-205">Next: Advanced topics</span></span>](advanced.md)
