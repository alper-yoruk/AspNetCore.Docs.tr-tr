---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Eşzamanlılık - 8/8
author: rick-anderson
description: Bu öğretici, birden çok kullanıcı aynı varlığı aynı anda güncelleştirdiğinde çakışmaları nasıl işleyeceğini gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656914"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="6489a-103">ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Eşzamanlılık - 8/8</span><span class="sxs-lookup"><span data-stu-id="6489a-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="6489a-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), ve [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="6489a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6489a-105">Bu öğretici, birden çok kullanıcı bir varlığı aynı anda güncelleştirdiğinde (aynı anda) çakışmaları nasıl işleyeceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="6489a-106">Eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="6489a-106">Concurrency conflicts</span></span>

<span data-ttu-id="6489a-107">Bir eşzamanlılık çakışması oluşur:</span><span class="sxs-lookup"><span data-stu-id="6489a-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="6489a-108">Kullanıcı, bir varlığın edin'le mecasayfasına doğru yol alar.</span><span class="sxs-lookup"><span data-stu-id="6489a-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="6489a-109">Başka bir kullanıcı, ilk kullanıcı değişikliği veritabanına yazılmadan önce aynı varlığı güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="6489a-110">Eşzamanlılık algılaması etkinleştirilemezse, veritabanını en son güncelleren kişi diğer kullanıcının değişikliklerinin üzerine yazar.</span><span class="sxs-lookup"><span data-stu-id="6489a-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="6489a-111">Bu risk kabul edilebilirse, eşzamanlılık için programlama maliyeti yarar ağır basabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="6489a-112">Kötümser eşzamanlılık (kilitleme)</span><span class="sxs-lookup"><span data-stu-id="6489a-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="6489a-113">Eşzamanlılık çakışmalarını önlemenin bir yolu veritabanı kilitlerini kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="6489a-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="6489a-114">Buna kötümser eşzamanlılık denir.</span><span class="sxs-lookup"><span data-stu-id="6489a-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="6489a-115">Uygulama, güncelleştirmeyi planladığı bir veritabanı satırını okumadan önce bir kilit ister.</span><span class="sxs-lookup"><span data-stu-id="6489a-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="6489a-116">Güncelleştirme erişimi için bir satır kilitlendikten sonra, ilk kilit serbest bırakılıncaya kadar başka hiçbir kullanıcının satırı kilitlemesine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="6489a-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="6489a-117">Kilitleri yönetmenin dezavantajları vardır.</span><span class="sxs-lookup"><span data-stu-id="6489a-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="6489a-118">Program karmaşık olabilir ve kullanıcı sayısı arttıkça performans sorunlarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="6489a-119">Entity Framework Core bunun için yerleşik destek sağlamaz ve bu öğretici bunu nasıl uygulayacağımı göstermez.</span><span class="sxs-lookup"><span data-stu-id="6489a-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="6489a-120">İyimser eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="6489a-120">Optimistic concurrency</span></span>

<span data-ttu-id="6489a-121">İyimser eşzamanlılık eşzamanlılık eşzamanlılık çakışmaların gerçekleşmesine izin verir ve bunu yaptığında uygun şekilde tepki verir.</span><span class="sxs-lookup"><span data-stu-id="6489a-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="6489a-122">Örneğin, Jane Bölüm'ün edit sayfasını ziyaret eder ve İngilizce bölümü bütçesini 350.000,00 TL'den 0,00 TL'ye değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget30.png)

<span data-ttu-id="6489a-124">Jane **Kaydet'i**tıklatmadan önce, John aynı sayfayı ziyaret eder ve Başlangıç Tarihi alanını 9/1/2007'den 9/1/2013'e değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date30.png)

<span data-ttu-id="6489a-126">Jane önce **Kaydet'i** tıklatıyor ve tarayıcı Bütçe tutarı olarak Dizin sayfasını sıfırla gösterdiğinden, değişikliğinin etkili olduğunu görür.</span><span class="sxs-lookup"><span data-stu-id="6489a-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="6489a-127">John, 350.000,00 TL bütçeyi gösteren bir Edit sayfasında **Kaydet'i** tıklatıyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="6489a-128">Bundan sonra ne olacağı eşzamanlılık çakışmalarını nasıl ele aldığınıza göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="6489a-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="6489a-129">Bir kullanıcının hangi özelliği değiştirdiğini izleyebilir ve yalnızca veritabanındaki ilgili sütunları güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="6489a-130">Senaryoda, hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="6489a-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="6489a-131">Farklı özellikler iki kullanıcı tarafından güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="6489a-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="6489a-132">Bir dahaki sefere birisi İngilizce bölümüne göz atsa, hem Jane'in hem de John'un değişikliklerini görecek.</span><span class="sxs-lookup"><span data-stu-id="6489a-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="6489a-133">Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışma sayısını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="6489a-134">Bu yaklaşımın bazı dezavantajları vardır:</span><span class="sxs-lookup"><span data-stu-id="6489a-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="6489a-135">Aynı özellikte rakip değişiklikler yapılırsa veri kaybını önleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="6489a-136">Genellikle bir web uygulamasında pratik değildir.</span><span class="sxs-lookup"><span data-stu-id="6489a-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="6489a-137">Getirilen tüm değerleri ve yeni değerleri izlemek için önemli bir durum korumayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="6489a-138">Büyük miktarda durum bakımı uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="6489a-139">Bir varlık üzerinde eşzamanlılık algılamaile karşılaştırıldığında uygulama karmaşıklığını artırabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="6489a-140">John'un değişmesine izin verebilirsin Jane'in bozuk paralarını yazabilirsin.</span><span class="sxs-lookup"><span data-stu-id="6489a-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="6489a-141">Birisi İngilizce bölümüne bir daha ki sefere göz atanın, 9/1/2013 ve getirilen 350.000,00 $ değerini göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="6489a-142">Bu *yaklaşıma, Kazananların Sonuncusu* veya Son *Kazananlar* senaryosu olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="6489a-143">(İstemcinin tüm değerleri veri deposunda bulunanlardan önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, İstemci Kazançları otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="6489a-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="6489a-144">John'un değişikliğinin veritabanında güncelleştirilen olmasını engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="6489a-145">Genellikle, uygulama:</span><span class="sxs-lookup"><span data-stu-id="6489a-145">Typically, the app would:</span></span>

  * <span data-ttu-id="6489a-146">Bir hata iletisi görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-146">Display an error message.</span></span>
  * <span data-ttu-id="6489a-147">Verilerin geçerli durumunu gösterin.</span><span class="sxs-lookup"><span data-stu-id="6489a-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="6489a-148">Kullanıcının değişiklikleri yeniden uygulamasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="6489a-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="6489a-149">Buna Mağaza *Kazançları* senaryosu denir.</span><span class="sxs-lookup"><span data-stu-id="6489a-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="6489a-150">(Veri depolama değerleri istemci tarafından gönderilen değerlerden önceliklidir.) Bu öğreticide Mağaza Kazançları senaryosunu uygularsınız.</span><span class="sxs-lookup"><span data-stu-id="6489a-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="6489a-151">Bu yöntem, kullanıcı uyarılmadan hiçbir değişikliğin üzerine yazılmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="6489a-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="6489a-152">EF Core'da çakışma algılama</span><span class="sxs-lookup"><span data-stu-id="6489a-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="6489a-153">EF Core `DbConcurrencyException` çakışmaları algıladığında özel durumlar atar.</span><span class="sxs-lookup"><span data-stu-id="6489a-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="6489a-154">Veri modeli çakışma algılamayı etkinleştirmek için yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6489a-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="6489a-155">Çakışma algılamayı etkinleştirme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6489a-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="6489a-156">EF Core'u, Güncelleştirme ve Sil komutlarının Nerede yan tümcesi'nde [eşzamanlılık belirteçleri](/ef/core/modeling/concurrency) olarak yapılandırılan sütunların özgün değerlerini içerecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6489a-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="6489a-157">Çağrıldığında, `SaveChanges` Where yan tümcesi [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) özniteliği ile açıklamalı tüm özelliklerin özgün değerlerini arar.</span><span class="sxs-lookup"><span data-stu-id="6489a-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="6489a-158">Güncelleştirme deyimi, satır ilk okundundan beri eşzamanlılık belirteç özelliklerinden herhangi biri değiştirilirse güncelleştirilen bir satır bulamaz.</span><span class="sxs-lookup"><span data-stu-id="6489a-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="6489a-159">EF Core bunu eşzamanlılık çatışması olarak yorumluyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="6489a-160">Çok sayıda sütunu olan veritabanı tabloları için bu yaklaşım, çok büyük Where yan tümcelerine neden olabilir ve büyük miktarda durum gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="6489a-161">Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.</span><span class="sxs-lookup"><span data-stu-id="6489a-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="6489a-162">Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemek için kullanılabilecek bir izleme sütunu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="6489a-163">BIR SQL Server veritabanında, izleme sütununun `rowversion`veri türü .</span><span class="sxs-lookup"><span data-stu-id="6489a-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="6489a-164">Değer, `rowversion` satır her güncelleştiride artılan sıralı bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="6489a-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="6489a-165">Bir Güncelleştirme veya Sil komutunda, Where yan tümcesi izleme sütununun (özgün satır sürüm numarası) özgün değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6489a-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="6489a-166">Güncelleştirilen satır başka bir kullanıcı tarafından değiştirildiyse, `rowversion` sütundaki değer özgün değerden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="6489a-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="6489a-167">Bu durumda, Güncelleştirme veya Sil deyimi, Where yan tümcesi nedeniyle güncelleştirilemeyecek satırı bulamaz.</span><span class="sxs-lookup"><span data-stu-id="6489a-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="6489a-168">EF Core, güncelleştirme veya silme komutundan hiçbir satır etkilenmediğinde eşzamanlılık özel durumu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6489a-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="6489a-169">İzleme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="6489a-169">Add a tracking property</span></span>

<span data-ttu-id="6489a-170">*Modeller/Department.cs*olarak, RowVersion adlı bir izleme özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6489a-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="6489a-171">[Zaman damgası](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) özniteliği, sütunu eşzamanlı izleme sütunu olarak tanımlayan şeydir.</span><span class="sxs-lookup"><span data-stu-id="6489a-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="6489a-172">Akıcı API izleme özelliğini belirtmek için alternatif bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="6489a-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="6489a-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6489a-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6489a-174">BIR SQL Server veritabanı `[Timestamp]` için, bayt dizisi olarak tanımlanan bir varlık özelliğindeki öznitelik:</span><span class="sxs-lookup"><span data-stu-id="6489a-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="6489a-175">Sütunun DELETE ve UPDATE WHERE yan tümcelerine eklenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="6489a-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="6489a-176">Veritabanındaki sütun türünü [satır sürümüne](/sql/t-sql/data-types/rowversion-transact-sql)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6489a-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="6489a-177">Veritabanı, satır her güncelleştiride artımlı bir sıralı satır sürüm numarası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6489a-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="6489a-178">Bir `Update` veya `Delete` komutta, `Where` yan tümce getirilen satır sürüm değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6489a-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="6489a-179">Güncelleştirilen satır, getirilen satırdan sonra değiştiyse:</span><span class="sxs-lookup"><span data-stu-id="6489a-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="6489a-180">Geçerli satır sürüm değeri getirilen değerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="6489a-181">Yan tümce getirilen satır sürüm değerini `Update` aradığından, veya `Delete` komutları satır bulamaz. `Where`</span><span class="sxs-lookup"><span data-stu-id="6489a-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="6489a-182">A `DbUpdateConcurrencyException` atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="6489a-183">Aşağıdaki kod, Bölüm adı güncelleştirildiğinde EF Core tarafından oluşturulan T-SQL'in bir bölümünü gösterir:</span><span class="sxs-lookup"><span data-stu-id="6489a-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="6489a-184">Önceki vurgulanan kod, `WHERE` 'yi `RowVersion`içeren yan tümceyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="6489a-185">Veritabanı `RowVersion` `RowVersion` parametreye eşit değilse (`@p2`), satır güncelleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="6489a-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="6489a-186">Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirilen T-SQL'i gösterir:</span><span class="sxs-lookup"><span data-stu-id="6489a-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="6489a-187">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) son ifadeden etkilenen satır sayısını döndürür.</span><span class="sxs-lookup"><span data-stu-id="6489a-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="6489a-188">Hiçbir satır güncelleştirmezse, EF Core `DbUpdateConcurrencyException`bir .</span><span class="sxs-lookup"><span data-stu-id="6489a-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6489a-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6489a-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6489a-190">Bir SQLite veritabanı `[Timestamp]` için, bayt dizisi olarak tanımlanan bir varlık özelliğindeki öznitelik:</span><span class="sxs-lookup"><span data-stu-id="6489a-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="6489a-191">Sütunun DELETE ve UPDATE WHERE yan tümcelerine eklenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="6489a-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="6489a-192">BLOB sütun türüne eşler.</span><span class="sxs-lookup"><span data-stu-id="6489a-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="6489a-193">Veritabanı, rowVersion sütununa, bir satır güncelleştirildiğinde yeni bir rasgele bayt dizisiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="6489a-194">Bir `Update` veya `Delete` komutta, `Where` yan tümce RowVersion sütununun getirilen değerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6489a-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="6489a-195">Güncelleştirilen satır, getirilen satırdan sonra değiştiyse:</span><span class="sxs-lookup"><span data-stu-id="6489a-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="6489a-196">Geçerli satır sürüm değeri getirilen değerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="6489a-197">Yan tümce özgün satır sürüm değerini `Update` aradığından, orkomut `Delete` satır bulamaz. `Where`</span><span class="sxs-lookup"><span data-stu-id="6489a-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="6489a-198">A `DbUpdateConcurrencyException` atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="6489a-199">Veritabanını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6489a-199">Update the database</span></span>

<span data-ttu-id="6489a-200">`RowVersion` Özellik ekleme, geçiş gerektiren veri modelini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="6489a-201">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="6489a-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6489a-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6489a-203">PMC'de aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6489a-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6489a-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6489a-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6489a-205">Bir terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6489a-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="6489a-206">Şu komut:</span><span class="sxs-lookup"><span data-stu-id="6489a-206">This command:</span></span>

* <span data-ttu-id="6489a-207">*Geçişler/{zaman damgası}_RowVersion.cs* geçiş dosyasını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6489a-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="6489a-208">*Geçişler/SchoolContextModelSnapshot.cs* dosyasını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="6489a-209">Güncelleştirme, `BuildModel` yönteme aşağıdaki vurgulanmış kodu ekler:</span><span class="sxs-lookup"><span data-stu-id="6489a-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="6489a-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6489a-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6489a-211">PMC'de aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6489a-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6489a-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6489a-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6489a-213">Dosyayı `Migrations/<timestamp>_RowVersion.cs` açın ve vurgulanan kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6489a-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="6489a-214">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6489a-214">The preceding code:</span></span>

  * <span data-ttu-id="6489a-215">Varolan satırları rasgele blob değerleriyle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="6489a-216">RowVersion sütununa bir satır güncelleştirildiğinde rasgele bir blob değeriayarveritabanı tetikleyicileri ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="6489a-217">Bir terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6489a-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="6489a-218">İskele Bölümü sayfaları</span><span class="sxs-lookup"><span data-stu-id="6489a-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6489a-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6489a-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6489a-220">Aşağıdaki istisnalar dışında [İskele Öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları izleyin:</span><span class="sxs-lookup"><span data-stu-id="6489a-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="6489a-221">*Sayfalar/Bölümler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6489a-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="6489a-222">Model `Department` sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6489a-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="6489a-223">Yeni bir bağlam oluşturmak yerine varolan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="6489a-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6489a-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6489a-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6489a-225">*Sayfalar/Bölümler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6489a-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="6489a-226">Bölüm sayfalarını iskeleye getirmek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6489a-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="6489a-227">**Windows'da:**</span><span class="sxs-lookup"><span data-stu-id="6489a-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="6489a-228">**Linux veya macOS'ta:**</span><span class="sxs-lookup"><span data-stu-id="6489a-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="6489a-229">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="6489a-230">Dizin sayfasını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6489a-230">Update the Index page</span></span>

<span data-ttu-id="6489a-231">İskele aracı Dizin `RowVersion` sayfası için bir sütun oluşturdu, ancak bu alan bir üretim uygulamasında görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="6489a-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="6489a-232">Bu öğreticide, eşzamanlılık işlemenin `RowVersion` nasıl çalıştığını göstermeye yardımcı olmak için son bayt görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="6489a-233">Son bayt ın tek başına benzersiz olacağı garanti değildir.</span><span class="sxs-lookup"><span data-stu-id="6489a-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="6489a-234">*Sayfaları Güncelleştir\Bölümler\Index.cshtml* sayfası:</span><span class="sxs-lookup"><span data-stu-id="6489a-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="6489a-235">Dizini Bölümlerle Değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="6489a-236">Bayt dizisinin son baytını göstermek için içeren `RowVersion` kodu değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="6489a-237">FirstMidName'i FullName ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="6489a-238">Aşağıdaki kod güncelleştirilmiş sayfayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="6489a-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="6489a-239">Sayfayı Güncelle modelini güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-239">Update the Edit page model</span></span>

<span data-ttu-id="6489a-240">*Sayfaları Güncelleştir\Bölümler\Edit.cshtml.cs* aşağıdaki kodla:</span><span class="sxs-lookup"><span data-stu-id="6489a-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="6489a-241">`OnGet` [OriginalValue,](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) yöntemde `rowVersion` getirilirken varlığın değeriyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="6489a-242">EF Core, özgün `RowVersion` değeri içeren bir WHERE yan tümcesi içeren bir SQL UPDATE komutu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6489a-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="6489a-243">GÜNCELLEME komutundan hiçbir satır etkilenmezse (hiçbir `RowVersion` satır `DbUpdateConcurrencyException` orijinal değere sahip değilse), bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="6489a-244">Önceki vurgulanan kodda:</span><span class="sxs-lookup"><span data-stu-id="6489a-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="6489a-245">`Department.RowVersion` Değer, edit sayfası için Al isteğinde ilk olarak getirilen varlıkta ne olduğudur.</span><span class="sxs-lookup"><span data-stu-id="6489a-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="6489a-246">Değer, düzenlenecek varlığı `OnPost` görüntüleyen Razor sayfasındaki gizli bir alan tarafından yönteme sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6489a-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="6489a-247">Gizli alan değeri model `Department.RowVersion` bağlayıcısı tarafından kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="6489a-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="6489a-248">`OriginalValue`EF Core'un Where yan tümcesinde kullanacağı şeydir.</span><span class="sxs-lookup"><span data-stu-id="6489a-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="6489a-249">Vurgulanan kod satırı yürütülmeden önce, `OriginalValue` bu yöntemde `FirstOrDefaultAsync` çağrıldığında veritabanında bulunan ve Düzenle sayfasında görüntülenenden farklı olabilecek değere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6489a-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="6489a-250">Vurgulanan kod, EF Core'un `RowVersion` SQL UPDATE `Department` deyiminin Where yan tümcesi'nde görüntülenen varlığın özgün değerini kullandığından emin olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="6489a-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="6489a-251">Eşzamanlılık hatası olduğunda, aşağıdaki vurgulanan kod istemci değerlerini (bu yönteme deftere nakledilen değerler) ve veritabanı değerlerini alır.</span><span class="sxs-lookup"><span data-stu-id="6489a-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="6489a-252">Aşağıdaki kod, veritabanı değerlerine sahip her sütun için, deftere `OnPostAsync`nakledilenden farklı bir özel hata iletisi ekler:</span><span class="sxs-lookup"><span data-stu-id="6489a-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="6489a-253">Aşağıdaki vurgulanan kod `RowVersion` veritabanından alınan yeni değerin değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6489a-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="6489a-254">Kullanıcı **Kaydet'i**bir sonraki tıklatıldığında, yalnızca Edit sayfasının son ekranından oluşan eşzamanlılık hataları yakalanır.</span><span class="sxs-lookup"><span data-stu-id="6489a-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="6489a-255">Eski `ModelState.Remove` `RowVersion` değere `ModelState` sahip olduğundan ifade gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="6489a-256">Razor Page'de, `ModelState` bir alanın değeri, her ikisi de mevcut olduğunda model özellik değerlerinin üzerinde önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="6489a-257">Razor sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="6489a-257">Update the Razor page</span></span>

<span data-ttu-id="6489a-258">*Sayfaları/Bölümleri/Edit.cshtml'i* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6489a-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="6489a-259">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6489a-259">The preceding code:</span></span>

* <span data-ttu-id="6489a-260">Yönergeyi `page` `@page` `@page "{id:int}"`' den .</span><span class="sxs-lookup"><span data-stu-id="6489a-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="6489a-261">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-261">Adds a hidden row version.</span></span> <span data-ttu-id="6489a-262">`RowVersion`post back değeri bağlar böylece eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="6489a-263">Hata ayıklama amacıyla `RowVersion` son baytını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6489a-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="6489a-264">Güçlü `ViewData` bir şekilde yazılanla `InstructorNameSL`değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="6489a-265">Eşpara birimi çakışmalarını Edit sayfasıyla test edin</span><span class="sxs-lookup"><span data-stu-id="6489a-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="6489a-266">İngilizce bölümünde Edit'in iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="6489a-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="6489a-267">Uygulamayı çalıştırın ve Bölümleri seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="6489a-268">İngilizce bölümü için **Edit** köprüye sağ tıklayın ve **yeni sekmede Aç'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="6489a-269">İlk sekmede, İngilizce bölümü için **Edit** köprübağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="6489a-270">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6489a-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="6489a-271">İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-271">Change the name in the first browser tab and click **Save**.</span></span>

![Bölüm Değiştir'den sonra sayfa 1'i edit](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="6489a-273">Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="6489a-274">Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="6489a-275">İkinci tarayıcı sekmesinde farklı bir alanı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-275">Change a different field in the second browser tab.</span></span>

![Bölüm Değiştir'den sonra sayfa 2'yi edit](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="6489a-277">**Kaydet**'e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6489a-277">Click **Save**.</span></span> <span data-ttu-id="6489a-278">Veritabanı değerleriyle eşleşmeyan tüm alanlar için hata iletileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="6489a-278">You see error messages for all fields that don't match the database values:</span></span>

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/edit-error30.png)

<span data-ttu-id="6489a-280">Bu tarayıcı penceresi Ad alanını değiştirmek niyetinde değildi.</span><span class="sxs-lookup"><span data-stu-id="6489a-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="6489a-281">Geçerli değeri (Diller) Ad alanına kopyalayın ve yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6489a-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="6489a-282">Sekme dışarı. İstemci tarafı doğrulama hata iletisini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6489a-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="6489a-283">Yeniden **Kaydet'i** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-283">Click **Save** again.</span></span> <span data-ttu-id="6489a-284">İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="6489a-285">Dizin sayfasında kaydedilen değerleri görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="6489a-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="6489a-286">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-286">Update the Delete page</span></span>

<span data-ttu-id="6489a-287">*Sayfaları/Bölümleri/Delete.cshtml.cs'leri* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="6489a-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="6489a-288">Sil sayfası, varlık alındıktan sonra değiştiğinde eşzamanlılık çakışmalarını algılar.</span><span class="sxs-lookup"><span data-stu-id="6489a-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="6489a-289">`Department.RowVersion`varlığın getirili satır sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="6489a-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="6489a-290">EF Core SQL DELETE komutunu oluşturduğunda, `RowVersion`'.</span><span class="sxs-lookup"><span data-stu-id="6489a-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="6489a-291">SQL DELETE komutu etkilenen sıfır satırla sonuçlanırsa:</span><span class="sxs-lookup"><span data-stu-id="6489a-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="6489a-292">`RowVersion` SQL DELETE komutu veritabanında `RowVersion` eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="6489a-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="6489a-293">Bir DbUpdateConcurrencyException özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="6489a-294">`OnGetAsync`ile `concurrencyError`denir.</span><span class="sxs-lookup"><span data-stu-id="6489a-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="6489a-295">Razor'u sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-295">Update the Delete Razor page</span></span>

<span data-ttu-id="6489a-296">*Sayfaları/Bölümleri/Delete.cshtml'i* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6489a-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="6489a-297">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="6489a-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="6489a-298">Yönergeyi `page` `@page` `@page "{id:int}"`' den .</span><span class="sxs-lookup"><span data-stu-id="6489a-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="6489a-299">Bir hata iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-299">Adds an error message.</span></span>
* <span data-ttu-id="6489a-300">Yönetici **alanında** FirstMidName'i FullName ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="6489a-301">Son `RowVersion` bayt görüntülemek için değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="6489a-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="6489a-302">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-302">Adds a hidden row version.</span></span> <span data-ttu-id="6489a-303">`RowVersion`postgit değeri bağlar geri eklemek böylece eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="6489a-304">Eşzamanlılık çakışmalarını test edin</span><span class="sxs-lookup"><span data-stu-id="6489a-304">Test concurrency conflicts</span></span>

<span data-ttu-id="6489a-305">Bir test departmanı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6489a-305">Create a test department.</span></span>

<span data-ttu-id="6489a-306">Test bölümünde Sil'in iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="6489a-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="6489a-307">Uygulamayı çalıştırın ve Bölümleri seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="6489a-308">Test bölümü için **Sil** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="6489a-309">Test bölümü için **Edit** köprübağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="6489a-310">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6489a-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="6489a-311">İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="6489a-312">Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="6489a-313">Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="6489a-314">İkinci sekmeden test bölümünü silin. Eşzamanlılık hatası veritabanındaki geçerli değerlerle görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="6489a-315">**Sil'i** tıklattığınızda, `RowVersion` güncelleştirilmedikçe varlığı siler.bölüm silinir.</span><span class="sxs-lookup"><span data-stu-id="6489a-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6489a-316">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6489a-316">Additional resources</span></span>

* [<span data-ttu-id="6489a-317">EF Core'da Eşzamanlılık Belirteçleri</span><span class="sxs-lookup"><span data-stu-id="6489a-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="6489a-318">EF Core'da eşzamanlılık işleme</span><span class="sxs-lookup"><span data-stu-id="6489a-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="6489a-319">Core 2.x kaynak ASP.NET hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="6489a-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="6489a-320">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="6489a-320">Next steps</span></span>

<span data-ttu-id="6489a-321">Bu serinin son öğretici.</span><span class="sxs-lookup"><span data-stu-id="6489a-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="6489a-322">Ek konular bu [öğretici serisinin MVC sürümünde](xref:data/ef-mvc/index)ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="6489a-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6489a-323">Önceki öğretici</span><span class="sxs-lookup"><span data-stu-id="6489a-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6489a-324">Bu öğretici, birden çok kullanıcı bir varlığı aynı anda güncelleştirdiğinde (aynı anda) çakışmaları nasıl işleyeceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="6489a-325">Çözemediğiniz sorunlarla karşılaştıysanız, [tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="6489a-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="6489a-326">[Talimatları indirin.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6489a-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="6489a-327">Eşzamanlılık çakışmaları</span><span class="sxs-lookup"><span data-stu-id="6489a-327">Concurrency conflicts</span></span>

<span data-ttu-id="6489a-328">Bir eşzamanlılık çakışması oluşur:</span><span class="sxs-lookup"><span data-stu-id="6489a-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="6489a-329">Kullanıcı, bir varlığın edin'le mecasayfasına doğru yol alar.</span><span class="sxs-lookup"><span data-stu-id="6489a-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="6489a-330">Başka bir kullanıcı, ilk kullanıcı değişikliği DB'ye yazılmadan önce aynı varlığı güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="6489a-331">Eşzamanlılık algılaması etkinleştirilemezse, eşzamanlı güncelleştirmeler oluştuğunda:</span><span class="sxs-lookup"><span data-stu-id="6489a-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="6489a-332">Son güncelleştirme kazanır.</span><span class="sxs-lookup"><span data-stu-id="6489a-332">The last update wins.</span></span> <span data-ttu-id="6489a-333">Diğer bir süre, son güncelleştirme değerleri DB'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="6489a-334">Geçerli güncelleştirmelerin ilki kaybolur.</span><span class="sxs-lookup"><span data-stu-id="6489a-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="6489a-335">İyimser eşzamanlılık</span><span class="sxs-lookup"><span data-stu-id="6489a-335">Optimistic concurrency</span></span>

<span data-ttu-id="6489a-336">İyimser eşzamanlılık eşzamanlılık eşzamanlılık çakışmaların gerçekleşmesine izin verir ve bunu yaptığında uygun şekilde tepki verir.</span><span class="sxs-lookup"><span data-stu-id="6489a-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="6489a-337">Örneğin, Jane Bölüm'ün edit sayfasını ziyaret eder ve İngilizce bölümü bütçesini 350.000,00 TL'den 0,00 TL'ye değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

<span data-ttu-id="6489a-339">Jane **Kaydet'i**tıklatmadan önce, John aynı sayfayı ziyaret eder ve Başlangıç Tarihi alanını 9/1/2007'den 9/1/2013'e değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

<span data-ttu-id="6489a-341">Jane önce **Kaydet'i** tıklatıyor ve tarayıcı Dizin sayfasını görüntülediğinde değişikliğini görür.</span><span class="sxs-lookup"><span data-stu-id="6489a-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Bütçe sıfıra değiştirildi](concurrency/_static/budget-zero.png)

<span data-ttu-id="6489a-343">John, 350.000,00 TL bütçeyi gösteren bir Edit sayfasında **Kaydet'i** tıklatıyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="6489a-344">Bundan sonra ne olacağı eşzamanlılık çakışmaları nasıl ele aldığınıza göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="6489a-345">İyimser eşzamanlılık aşağıdaki seçenekleri içerir:</span><span class="sxs-lookup"><span data-stu-id="6489a-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="6489a-346">Bir kullanıcının hangi özelliği değiştirdiğini izleyebilir ve yalnızca DB'deki ilgili sütunları güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="6489a-347">Senaryoda, hiçbir veri kaybolmaz.</span><span class="sxs-lookup"><span data-stu-id="6489a-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="6489a-348">Farklı özellikler iki kullanıcı tarafından güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="6489a-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="6489a-349">Bir dahaki sefere birisi İngilizce bölümüne göz atsa, hem Jane'in hem de John'un değişikliklerini görecek.</span><span class="sxs-lookup"><span data-stu-id="6489a-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="6489a-350">Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışma sayısını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="6489a-351">Bu yaklaşım:</span><span class="sxs-lookup"><span data-stu-id="6489a-351">This approach:</span></span>
 
  * <span data-ttu-id="6489a-352">Aynı özellikte rakip değişiklikler yapılırsa veri kaybını önleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="6489a-353">Genellikle bir web uygulamasında pratik değildir.</span><span class="sxs-lookup"><span data-stu-id="6489a-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="6489a-354">Getirilen tüm değerleri ve yeni değerleri izlemek için önemli bir durum korumayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="6489a-355">Büyük miktarda durum bakımı uygulama performansını etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="6489a-356">Bir varlık üzerinde eşzamanlılık algılamaile karşılaştırıldığında uygulama karmaşıklığını artırabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="6489a-357">John'un değişmesine izin verebilirsin Jane'in bozuk paralarını yazabilirsin.</span><span class="sxs-lookup"><span data-stu-id="6489a-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="6489a-358">Birisi İngilizce bölümüne bir daha ki sefere göz atanın, 9/1/2013 ve getirilen 350.000,00 $ değerini göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="6489a-359">Bu *yaklaşıma, Kazananların Sonuncusu* veya Son *Kazananlar* senaryosu olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="6489a-360">(İstemcinin tüm değerleri veri deposunda bulunanlardan önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, İstemci Kazançları otomatik olarak gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="6489a-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="6489a-361">John'un değişikliğinin DB'de güncelleştirilen olmasını engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="6489a-362">Genellikle, uygulama:</span><span class="sxs-lookup"><span data-stu-id="6489a-362">Typically, the app would:</span></span>

  * <span data-ttu-id="6489a-363">Bir hata iletisi görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-363">Display an error message.</span></span>
  * <span data-ttu-id="6489a-364">Verilerin geçerli durumunu gösterin.</span><span class="sxs-lookup"><span data-stu-id="6489a-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="6489a-365">Kullanıcının değişiklikleri yeniden uygulamasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="6489a-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="6489a-366">Buna Mağaza *Kazançları* senaryosu denir.</span><span class="sxs-lookup"><span data-stu-id="6489a-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="6489a-367">(Veri depolama değerleri istemci tarafından gönderilen değerlerden önceliklidir.) Bu öğreticide Mağaza Kazançları senaryosunu uygularsınız.</span><span class="sxs-lookup"><span data-stu-id="6489a-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="6489a-368">Bu yöntem, kullanıcı uyarılmadan hiçbir değişikliğin üzerine yazılmamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="6489a-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="6489a-369">Eşzamanlılık işleme</span><span class="sxs-lookup"><span data-stu-id="6489a-369">Handling concurrency</span></span> 

<span data-ttu-id="6489a-370">Bir özellik [eşzamanlılık belirteci](/ef/core/modeling/concurrency)olarak yapılandırıldığınızda:</span><span class="sxs-lookup"><span data-stu-id="6489a-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="6489a-371">EF Core, özelliğin alındıktan sonra değiştirilmediğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="6489a-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="6489a-372">Denetim, [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) veya [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında oluşur.</span><span class="sxs-lookup"><span data-stu-id="6489a-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="6489a-373">Özellik alındıktan sonra değiştirildiyse, bir [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="6489a-374">DB ve veri modeli atma `DbUpdateConcurrencyException`desteklemek için yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6489a-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="6489a-375">Bir özelliküzerinde eşzamanlılık çakışmalarını algılama</span><span class="sxs-lookup"><span data-stu-id="6489a-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="6489a-376">Eşzamanlılık çakışmaları [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) özniteliği ile özellik düzeyinde algılanabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="6489a-377">Öznitelik modeldeki birden çok özelliğe uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="6489a-378">Daha fazla bilgi için [Bkz. Veri Ek Açıklamaları-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="6489a-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="6489a-379">Öznitelik `[ConcurrencyCheck]` bu öğreticide kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="6489a-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="6489a-380">Bir satırda eşzamanlılık çakışmalarını algılama</span><span class="sxs-lookup"><span data-stu-id="6489a-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="6489a-381">Eşzamanlılık çakışmalarını algılamak için modele bir [satır çevirme](/sql/t-sql/data-types/rowversion-transact-sql) izleme sütunu eklenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="6489a-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="6489a-382">`rowversion` :</span></span>

* <span data-ttu-id="6489a-383">SQL Server'a özgüdür.</span><span class="sxs-lookup"><span data-stu-id="6489a-383">Is SQL Server specific.</span></span> <span data-ttu-id="6489a-384">Diğer veritabanları benzer bir özellik sağlamayabilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="6489a-385">DB'den getirilen bir varlığın değiştirilmediğini belirlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="6489a-386">DB, satır her `rowversion` güncelleştiride artımlı sıralı bir sayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6489a-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="6489a-387">Bir `Update` veya `Delete` komutta, `Where` yan tümce. `rowversion`</span><span class="sxs-lookup"><span data-stu-id="6489a-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="6489a-388">Güncelleştirilen satır değiştiyse:</span><span class="sxs-lookup"><span data-stu-id="6489a-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="6489a-389">`rowversion`getirilen değerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="6489a-390">`Where` Yan `Update` `Delete` tümcesi getirilen `rowversion`ilerlediği için satır veya komutlar bulmuyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="6489a-391">A `DbUpdateConcurrencyException` atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="6489a-392">EF Core'da, bir `Update` satır veya `Delete` komut tarafından güncelleştirildiğinde, eşzamanlılık özel durumu atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="6489a-393">Bölüm kuruluşuna izleme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="6489a-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="6489a-394">*Modeller/Department.cs*olarak, RowVersion adlı bir izleme özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6489a-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="6489a-395">[Zaman Damgası](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) özniteliği, bu sütunun `Where` `Update` yan tümcesi ve `Delete` komutları dahil olduğunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="6489a-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="6489a-396">SQL Server'ın `Timestamp` önceki sürümleri, SQL `rowversion` türü `timestamp` değiştirmeden önce bir SQL veri türü kullandığından öznitelik çağrılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="6489a-397">Akıcı API izleme özelliğini de belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="6489a-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="6489a-398">Aşağıdaki kod, Bölüm adı güncelleştirildiğinde EF Core tarafından oluşturulan T-SQL'in bir bölümünü gösterir:</span><span class="sxs-lookup"><span data-stu-id="6489a-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="6489a-399">Önceki vurgulanan kod, `WHERE` 'yi `RowVersion`içeren yan tümceyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="6489a-400">DB `RowVersion` `RowVersion` parametreye eşit değilse (`@p2`), satır güncelleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="6489a-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="6489a-401">Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirilen T-SQL'i gösterir:</span><span class="sxs-lookup"><span data-stu-id="6489a-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="6489a-402">[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) son ifadeden etkilenen satır sayısını döndürür.</span><span class="sxs-lookup"><span data-stu-id="6489a-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="6489a-403">Hiçbir satırda güncellenir, EF Core `DbUpdateConcurrencyException`atar.</span><span class="sxs-lookup"><span data-stu-id="6489a-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="6489a-404">Visual Studio'nun çıkış penceresinde T-SQL EF Core'un oluşturduğunu görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6489a-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="6489a-405">DB'yi güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-405">Update the DB</span></span>

<span data-ttu-id="6489a-406">`RowVersion` Özellik ekleme, geçiş gerektiren DB modelini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="6489a-407">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-407">Build the project.</span></span> <span data-ttu-id="6489a-408">Komut penceresine aşağıdakileri girin:</span><span class="sxs-lookup"><span data-stu-id="6489a-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="6489a-409">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="6489a-409">The preceding commands:</span></span>

* <span data-ttu-id="6489a-410">*Geçişler/{zaman damgası}_RowVersion.cs* geçiş dosyası ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="6489a-411">*Geçişler/SchoolContextModelSnapshot.cs* dosyasını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="6489a-412">Güncelleştirme, `BuildModel` yönteme aşağıdaki vurgulanmış kodu ekler:</span><span class="sxs-lookup"><span data-stu-id="6489a-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="6489a-413">DB'yi güncelleştirmek için geçişleri çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="6489a-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="6489a-414">İskele Bölümler modeli</span><span class="sxs-lookup"><span data-stu-id="6489a-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6489a-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6489a-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="6489a-416">[İskele'deki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları uygulayın öğrenci modeli `Department` ve model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6489a-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6489a-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6489a-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="6489a-418">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6489a-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="6489a-419">Önceki komut `Department` modeli iskeleler.</span><span class="sxs-lookup"><span data-stu-id="6489a-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="6489a-420">Projeyi Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="6489a-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="6489a-421">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="6489a-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="6489a-422">Departmanlar Dizini sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-422">Update the Departments Index page</span></span>

<span data-ttu-id="6489a-423">İskele altyapısı Dizin `RowVersion` sayfası için bir sütun oluşturdu, ancak bu alan görüntülenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="6489a-424">Bu öğreticide, eşzamanlılığı anlamalarına yardımcı olmak için son bayt `RowVersion` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="6489a-425">Son baytın benzersiz olacağı garanti değildir.</span><span class="sxs-lookup"><span data-stu-id="6489a-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="6489a-426">Gerçek bir uygulama görüntülenmezdi `RowVersion` veya son `RowVersion`bayt.</span><span class="sxs-lookup"><span data-stu-id="6489a-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="6489a-427">Dizin sayfasını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6489a-427">Update the Index page:</span></span>

* <span data-ttu-id="6489a-428">Dizini Bölümlerle Değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="6489a-429">İçinde `RowVersion` ki işaretlemeyi son bayt `RowVersion`ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="6489a-430">FirstMidName'i FullName ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="6489a-431">Aşağıdaki biçimlendirme güncelleştirilmiş sayfayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="6489a-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="6489a-432">Sayfayı Güncelle modelini güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-432">Update the Edit page model</span></span>

<span data-ttu-id="6489a-433">*Sayfaları Güncelleştir\Bölümler\Edit.cshtml.cs* aşağıdaki kodla:</span><span class="sxs-lookup"><span data-stu-id="6489a-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="6489a-434">Eşzamanlılık sorununu algılamak için [OriginalValue,](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) `rowVersion` getirilen varlıktan gelen değerle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="6489a-435">EF Core, özgün `RowVersion` değeri içeren bir WHERE yan tümcesi içeren bir SQL UPDATE komutu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6489a-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="6489a-436">GÜNCELLEME komutundan hiçbir satır etkilenmezse (hiçbir `RowVersion` satır `DbUpdateConcurrencyException` orijinal değere sahip değilse), bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="6489a-437">Önceki kodda, `Department.RowVersion` varlık getirili değeridir.</span><span class="sxs-lookup"><span data-stu-id="6489a-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="6489a-438">`OriginalValue`bu yöntemde çağrıldığında `FirstOrDefaultAsync` DB değeridir.</span><span class="sxs-lookup"><span data-stu-id="6489a-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="6489a-439">Aşağıdaki kod istemci değerlerini (bu yönteme deftere nakledilen değerler) ve DB değerlerini alır:</span><span class="sxs-lookup"><span data-stu-id="6489a-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="6489a-440">Aşağıdaki kod, gönderilenden farklı DB değerleri olan her sütun için `OnPostAsync`özel bir hata iletisi ekler:</span><span class="sxs-lookup"><span data-stu-id="6489a-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="6489a-441">Aşağıdaki vurgulanan kod, `RowVersion` DB'den alınan yeni değerin değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6489a-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="6489a-442">Kullanıcı **Kaydet'i**bir sonraki tıklatıldığında, yalnızca Edit sayfasının son ekranından oluşan eşzamanlılık hataları yakalanır.</span><span class="sxs-lookup"><span data-stu-id="6489a-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="6489a-443">Eski `ModelState.Remove` `RowVersion` değere `ModelState` sahip olduğundan ifade gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="6489a-444">Razor Page'de, `ModelState` bir alanın değeri, her ikisi de mevcut olduğunda model özellik değerlerinin üzerinde önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="6489a-445">Edit sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-445">Update the Edit page</span></span>

<span data-ttu-id="6489a-446">*Sayfaları/Bölümleri/Edit.cshtml'i* aşağıdaki biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6489a-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="6489a-447">Önceki biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="6489a-447">The preceding markup:</span></span>

* <span data-ttu-id="6489a-448">Yönergeyi `page` `@page` `@page "{id:int}"`' den .</span><span class="sxs-lookup"><span data-stu-id="6489a-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="6489a-449">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-449">Adds a hidden row version.</span></span> <span data-ttu-id="6489a-450">`RowVersion`post back değeri bağlar böylece eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="6489a-451">Hata ayıklama amacıyla `RowVersion` son baytını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6489a-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="6489a-452">Güçlü `ViewData` bir şekilde yazılanla `InstructorNameSL`değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="6489a-453">Eşpara birimi çakışmalarını Edit sayfasıyla test edin</span><span class="sxs-lookup"><span data-stu-id="6489a-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="6489a-454">İngilizce bölümünde Edit'in iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="6489a-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="6489a-455">Uygulamayı çalıştırın ve Bölümleri seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="6489a-456">İngilizce bölümü için **Edit** köprüye sağ tıklayın ve **yeni sekmede Aç'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="6489a-457">İlk sekmede, İngilizce bölümü için **Edit** köprübağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="6489a-458">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6489a-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="6489a-459">İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-459">Change the name in the first browser tab and click **Save**.</span></span>

![Bölüm Değiştir'den sonra sayfa 1'i edit](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="6489a-461">Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="6489a-462">Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="6489a-463">İkinci tarayıcı sekmesinde farklı bir alanı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6489a-463">Change a different field in the second browser tab.</span></span>

![Bölüm Değiştir'den sonra sayfa 2'yi edit](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="6489a-465">**Kaydet**'e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6489a-465">Click **Save**.</span></span> <span data-ttu-id="6489a-466">DB değerleriyle eşleşmeyan tüm alanlar için hata iletileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="6489a-466">You see error messages for all fields that don't match the DB values:</span></span>

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/edit-error.png)

<span data-ttu-id="6489a-468">Bu tarayıcı penceresi Ad alanını değiştirmek niyetinde değildi.</span><span class="sxs-lookup"><span data-stu-id="6489a-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="6489a-469">Geçerli değeri (Diller) Ad alanına kopyalayın ve yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6489a-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="6489a-470">Sekme dışarı. İstemci tarafı doğrulama hata iletisini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6489a-470">Tab out. Client-side validation removes the error message.</span></span>

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/cv.png)

<span data-ttu-id="6489a-472">Yeniden **Kaydet'i** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-472">Click **Save** again.</span></span> <span data-ttu-id="6489a-473">İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6489a-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="6489a-474">Dizin sayfasında kaydedilen değerleri görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="6489a-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="6489a-475">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-475">Update the Delete page</span></span>

<span data-ttu-id="6489a-476">Sayfasil modelini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6489a-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="6489a-477">Sil sayfası, varlık alındıktan sonra değiştiğinde eşzamanlılık çakışmalarını algılar.</span><span class="sxs-lookup"><span data-stu-id="6489a-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="6489a-478">`Department.RowVersion`varlığın getirili satır sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="6489a-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="6489a-479">EF Core SQL DELETE komutunu oluşturduğunda, `RowVersion`'.</span><span class="sxs-lookup"><span data-stu-id="6489a-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="6489a-480">SQL DELETE komutu etkilenen sıfır satırla sonuçlanırsa:</span><span class="sxs-lookup"><span data-stu-id="6489a-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="6489a-481">`RowVersion` SQL DELETE komutu DB `RowVersion` ile eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6489a-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="6489a-482">Bir DbUpdateConcurrencyException özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="6489a-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="6489a-483">`OnGetAsync`ile `concurrencyError`denir.</span><span class="sxs-lookup"><span data-stu-id="6489a-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="6489a-484">Sil sayfasını güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6489a-484">Update the Delete page</span></span>

<span data-ttu-id="6489a-485">*Sayfaları/Bölümleri/Delete.cshtml'i* aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6489a-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="6489a-486">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="6489a-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="6489a-487">Yönergeyi `page` `@page` `@page "{id:int}"`' den .</span><span class="sxs-lookup"><span data-stu-id="6489a-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="6489a-488">Bir hata iletisi ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-488">Adds an error message.</span></span>
* <span data-ttu-id="6489a-489">Yönetici **alanında** FirstMidName'i FullName ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6489a-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="6489a-490">Son `RowVersion` bayt görüntülemek için değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="6489a-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="6489a-491">Gizli satır sürümü ekler.</span><span class="sxs-lookup"><span data-stu-id="6489a-491">Adds a hidden row version.</span></span> <span data-ttu-id="6489a-492">`RowVersion`post back değeri bağlar böylece eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="6489a-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="6489a-493">Sil sayfasıyla eşzamanlılık çakışmalarını test edin</span><span class="sxs-lookup"><span data-stu-id="6489a-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="6489a-494">Bir test departmanı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6489a-494">Create a test department.</span></span>

<span data-ttu-id="6489a-495">Test bölümünde Sil'in iki tarayıcı örneğini açın:</span><span class="sxs-lookup"><span data-stu-id="6489a-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="6489a-496">Uygulamayı çalıştırın ve Bölümleri seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="6489a-497">Test bölümü için **Sil** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6489a-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="6489a-498">Test bölümü için **Edit** köprübağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="6489a-499">İki tarayıcı sekmesi aynı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6489a-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="6489a-500">İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet'i**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6489a-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="6489a-501">Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6489a-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="6489a-502">Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="6489a-503">İkinci sekmeden test bölümünü silin. Eşzamanlılık hatası, DB'nin geçerli değerleriyle görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6489a-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="6489a-504">**Sil'i** tıklattığınızda, `RowVersion` güncelleştirilmedikçe varlığı siler.bölüm silinir.</span><span class="sxs-lookup"><span data-stu-id="6489a-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="6489a-505">Bir [Inheritance](xref:data/ef-mvc/inheritance) veri modelinin nasıl devralilenene ilişkin bkz.</span><span class="sxs-lookup"><span data-stu-id="6489a-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="6489a-506">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6489a-506">Additional resources</span></span>

* [<span data-ttu-id="6489a-507">EF Core'da Eşzamanlılık Belirteçleri</span><span class="sxs-lookup"><span data-stu-id="6489a-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="6489a-508">EF Core'da eşzamanlılık işleme</span><span class="sxs-lookup"><span data-stu-id="6489a-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="6489a-509">Bu öğreticinin YouTube sürümü (Eşzamanlılık Çakışmalarını Taşıma)</span><span class="sxs-lookup"><span data-stu-id="6489a-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="6489a-510">Bu öğretici YouTube sürümü (Bölüm 2)</span><span class="sxs-lookup"><span data-stu-id="6489a-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="6489a-511">Bu öğretici YouTube sürümü (Bölüm 3)</span><span class="sxs-lookup"><span data-stu-id="6489a-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="6489a-512">Önceki</span><span class="sxs-lookup"><span data-stu-id="6489a-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

